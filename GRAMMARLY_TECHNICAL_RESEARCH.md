# Grammarly Technical Implementation Strategy — Research Findings

> Research conducted: 2026-01-23
> Sources: Stack Overflow, Microsoft Documentation, pywinauto docs

---

## 🔍 Executive Summary

Based on extensive research, Grammarly uses a **multi-layered approach** combining:

1. **Windows UI Automation** — For reading text from native applications
2. **Layered Transparent Windows** — For overlay UI (icon, underlines, tooltips)
3. **Focus tracking** — To know when user is in a text field
4. **Text insertion** — Via UI Automation patterns or clipboard simulation

---

## 1️⃣ TEXT CAPTURE — Windows UI Automation

### What Is UI Automation?

Microsoft UI Automation is an accessibility framework that provides:

- Programmatic access to UI elements across applications
- Text content extraction
- Caret/cursor position tracking
- Focus change notifications

### Two Backends Available (pywinauto terminology):

| Backend   | Name             | Best For                                     |
| --------- | ---------------- | -------------------------------------------- |
| **win32** | Windows Messages | MFC, VB6, VCL, legacy apps                   |
| **uia**   | UI Automation    | WinForms, WPF, Store apps, Qt5, **browsers** |

**Grammarly uses the `uia` backend** — this is confirmed by its ability to work in modern apps and browsers.

### Key Interfaces for Text Capture:

```
IUIAutomation (Root interface)
    │
    ├── IUIAutomationElement (Any UI element)
    │       │
    │       └── GetCurrentPattern() → ITextProvider
    │
    ├── ITextProvider (For text controls)
    │       │
    │       ├── DocumentRange → Full document text
    │       ├── GetSelection() → Selected text ranges
    │       ├── GetVisibleRanges() → Text currently visible
    │       └── GetCaretRange() → Caret/cursor position
    │
    └── ITextRangeProvider (Text range object)
            │
            ├── GetText() → Actual text content
            ├── GetBoundingRectangles() → Screen position of text
            └── Move(), Expand() → Navigate through text
```

### How Grammarly Captures Text:

```python
# Pseudocode based on research findings
from pywinauto import Desktop

# 1. Get the focused element across ALL applications
desktop = Desktop(backend="uia")

# 2. Find THE element with keyboard focus
for window in desktop.windows():
    for element in window.descendants():
        if element.has_keyboard_focus():
            focused_element = element
            break

# 3. Check if it's a text control (Edit, Document, etc.)
if focused_element.control_type in ["Edit", "Document", "Text"]:

    # 4. Get the TextPattern
    text_pattern = focused_element.get_pattern("Text")

    # 5. Get full document text
    document_range = text_pattern.DocumentRange
    full_text = document_range.GetText(-1)  # -1 = all text

    # 6. Get caret/cursor position
    caret_range = text_pattern.GetCaretRange()
    caret_position = caret_range.GetBoundingRectangles()

    # 7. Get selected text if any
    selections = text_pattern.GetSelection()
    if selections:
        selected_text = selections[0].GetText(-1)
```

### Focus Tracking:

```python
# Grammarly monitors focus changes across ALL windows
from comtypes.client import CreateObject
from UIAutomationClient import *

uia = CreateObject(CUIAutomation)

# Subscribe to focus changed events
def on_focus_changed(sender, element):
    """Called whenever focus changes anywhere in Windows"""
    if element.CurrentControlType in [UIA_EditControlTypeId, UIA_DocumentControlTypeId]:
        # User focused on a text field!
        show_grammarly_icon()
        start_monitoring_text()
    else:
        hide_grammarly_icon()

uia.AddFocusChangedEventHandler(None, on_focus_changed)
```

---

## 2️⃣ OVERLAY UI — Layered Transparent Windows

### How Grammarly Shows Its Icon/Tooltips Over Other Apps:

Grammarly creates a **transparent, always-on-top window** that floats above all applications.

```c++
// Windows API approach
HWND hwnd = CreateWindowEx(
    WS_EX_LAYERED |        // Transparent window support
    WS_EX_TRANSPARENT |    // Click-through (mouse passes to app below)
    WS_EX_TOPMOST |        // Always on top
    WS_EX_TOOLWINDOW,      // Don't show in taskbar
    "GrammarlyOverlay",    // Window class
    NULL,                  // No window title
    WS_POPUP,              // Popup style (no borders)
    x, y, width, height,   // Position (follows cursor)
    NULL, NULL, hInstance, NULL
);

// Make it transparent
SetLayeredWindowAttributes(
    hwnd,
    RGB(255, 0, 255),      // Color key (this color becomes transparent)
    255,                    // Alpha (full opacity for non-transparent parts)
    LWA_COLORKEY           // Use color key transparency
);
```

### Python Equivalent (with pywin32):

```python
import win32gui
import win32con

# Extended window styles
ex_style = (
    win32con.WS_EX_LAYERED |
    win32con.WS_EX_TRANSPARENT |
    win32con.WS_EX_TOPMOST |
    win32con.WS_EX_TOOLWINDOW
)

# Create the overlay window
hwnd = win32gui.CreateWindowEx(
    ex_style,
    "Grammarly Overlay",
    None,
    win32con.WS_POPUP,
    x, y, width, height,
    None, None, None, None
)

# Set transparency
win32gui.SetLayeredWindowAttributes(
    hwnd,
    win32api.RGB(255, 0, 255),  # Magenta = transparent
    0,
    win32con.LWA_COLORKEY
)

win32gui.ShowWindow(hwnd, win32con.SW_SHOWNOACTIVATE)
```

### Key Properties:

| Property            | Purpose                          |
| ------------------- | -------------------------------- |
| `WS_EX_LAYERED`     | Enables transparency features    |
| `WS_EX_TRANSPARENT` | Clicks pass through to app below |
| `WS_EX_TOPMOST`     | Window stays above all others    |
| `WS_EX_TOOLWINDOW`  | Doesn't appear in taskbar        |
| `WS_POPUP`          | No border, title bar, etc.       |

### Making Icon/Tooltip Clickable:

The **entire window is click-through** except for specific regions:

```python
# Technique: Remove WS_EX_TRANSPARENT temporarily for clickable areas
def on_mouse_enter_icon():
    # Make this region clickable
    current_style = win32gui.GetWindowLong(hwnd, win32con.GWL_EXSTYLE)
    win32gui.SetWindowLong(
        hwnd,
        win32con.GWL_EXSTYLE,
        current_style & ~win32con.WS_EX_TRANSPARENT  # Remove transparent flag
    )

def on_mouse_leave_icon():
    # Make click-through again
    current_style = win32gui.GetWindowLong(hwnd, win32con.GWL_EXSTYLE)
    win32gui.SetWindowLong(
        hwnd,
        win32con.GWL_EXSTYLE,
        current_style | win32con.WS_EX_TRANSPARENT  # Add transparent flag
    )
```

---

## 3️⃣ TEXT INSERTION — How Suggestions Are Applied

### Method 1: UI Automation ValuePattern (Best)

```python
# If the control supports ValuePattern
value_pattern = element.get_pattern("Value")
value_pattern.SetValue("New text here")
```

**Pros:** Direct, fast, reliable
**Cons:** Not all controls support it

### Method 2: UI Automation TextPattern

```python
# For rich text controls
text_pattern = element.get_pattern("Text")
# Select text to replace
selection = text_pattern.GetSelection()
# No direct "SetText" - need to use clipboard
```

### Method 3: Clipboard + Paste Simulation (Most Universal)

```python
import pyperclip
import pyautogui

def insert_text(text):
    # 1. Save original clipboard
    original = pyperclip.paste()

    try:
        # 2. Copy new text to clipboard
        pyperclip.copy(text)

        # 3. Simulate Ctrl+V
        pyautogui.hotkey('ctrl', 'v')

        # 4. Wait for paste to complete
        time.sleep(0.05)

    finally:
        # 5. Restore original clipboard
        pyperclip.copy(original)
```

**This is likely what Grammarly uses** — it's the most reliable across all applications.

### Method 4: pywinauto's Built-in Methods

```python
# For Edit controls
element.set_edit_text("New text")

# For controls with Value pattern
element.set_value("New text")

# Type character by character (slow but works everywhere)
element.type_keys("New text", with_spaces=True)
```

---

## 4️⃣ GETTING CURSOR/CARET POSITION

For showing the overlay in the right place:

### Method 1: UI Automation GetCaretRange

```python
text_pattern = element.get_pattern("Text")
caret_range = text_pattern.GetCaretRange()
bounding_rects = caret_range.GetBoundingRectangles()
# Returns screen coordinates of the caret
```

### Method 2: Windows GetCaretPos API

```python
import ctypes
from ctypes import wintypes

class POINT(ctypes.Structure):
    _fields_ = [("x", wintypes.LONG), ("y", wintypes.LONG)]

point = POINT()
ctypes.windll.user32.GetCaretPos(ctypes.byref(point))

# Convert to screen coordinates
hwnd = ctypes.windll.user32.GetForegroundWindow()
ctypes.windll.user32.ClientToScreen(hwnd, ctypes.byref(point))

print(f"Caret at: ({point.x}, {point.y})")
```

---

## 5️⃣ APPLICATION DETECTION

Knowing which app is active to customize behavior:

```python
import win32gui
import win32process
import psutil

def get_active_app_info():
    # Get foreground window
    hwnd = win32gui.GetForegroundWindow()

    # Get window title
    window_title = win32gui.GetWindowText(hwnd)

    # Get process ID
    _, pid = win32process.GetWindowThreadProcessId(hwnd)

    # Get process name
    try:
        process = psutil.Process(pid)
        process_name = process.name()
        exe_path = process.exe()
    except:
        process_name = "Unknown"
        exe_path = None

    return {
        "hwnd": hwnd,
        "title": window_title,
        "process_name": process_name,
        "exe_path": exe_path,
        "pid": pid
    }

# Example output:
# {
#     "hwnd": 12345,
#     "title": "Document1 - Microsoft Word",
#     "process_name": "WINWORD.EXE",
#     "exe_path": "C:\\Program Files\\Microsoft Office\\...",
#     "pid": 5678
# }
```

---

## 6️⃣ COMPLETE GRAMMARLY-LIKE ARCHITECTURE

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         GRAMMARLY DESKTOP APP                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    EVENT LOOP (Main Thread)                         │   │
│  │                                                                     │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌──────────────┐  │   │
│  │  │Focus Change │ │  Keyboard   │ │  Clipboard  │ │   Timer      │  │   │
│  │  │  Events     │ │   Hook      │ │   Monitor   │ │  (periodic)  │  │   │
│  │  └──────┬──────┘ └──────┬──────┘ └──────┬──────┘ └──────┬───────┘  │   │
│  │         │               │               │               │          │   │
│  │         └───────────────┴───────────────┴───────────────┘          │   │
│  │                                    │                                │   │
│  └────────────────────────────────────┼────────────────────────────────┘   │
│                                       ↓                                     │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    CONTEXT MANAGER                                  │   │
│  │                                                                     │   │
│  │  ┌─────────────────────────────────────────────────────────────┐   │   │
│  │  │ UI Automation Client                                        │   │   │
│  │  │                                                             │   │   │
│  │  │  • Get focused element                                      │   │   │
│  │  │  • Get TextPattern → DocumentRange, Selection, CaretRange   │   │   │
│  │  │  • Get element bounding rectangles                          │   │   │
│  │  │  • Monitor for text changes                                 │   │   │
│  │  └─────────────────────────────────────────────────────────────┘   │   │
│  │                                                                     │   │
│  │  ┌─────────────────────────────────────────────────────────────┐   │   │
│  │  │ Application Detector                                        │   │   │
│  │  │                                                             │   │   │
│  │  │  • Process name (WINWORD.EXE, chrome.exe, etc.)             │   │   │
│  │  │  • Window title parsing                                     │   │   │
│  │  │  • App-specific configurations                              │   │   │
│  │  └─────────────────────────────────────────────────────────────┘   │   │
│  │                                                                     │   │
│  └─────────────────────────────────────┼───────────────────────────────┘   │
│                                        ↓                                    │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    NLP/AI ENGINE                                    │   │
│  │                                                                     │   │
│  │  • Grammar checking                                                 │   │
│  │  • Spelling correction                                              │   │
│  │  • Style suggestions                                                │   │
│  │  • Tone detection                                                   │   │
│  │  • Sentence completion (AI)                                         │   │
│  │                                                                     │   │
│  └─────────────────────────────────────┼───────────────────────────────┘   │
│                                        ↓                                    │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    OVERLAY RENDERER                                 │   │
│  │                                                                     │   │
│  │  Transparent Window (WS_EX_LAYERED | WS_EX_TOPMOST)                │   │
│  │                                                                     │   │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐              │   │
│  │  │  Grammarly   │  │  Underlines  │  │  Tooltip /   │              │   │
│  │  │    Icon      │  │  (errors)    │  │  Suggestion  │              │   │
│  │  └──────────────┘  └──────────────┘  └──────────────┘              │   │
│  │                                                                     │   │
│  │  Position = Caret position from UI Automation                      │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    TEXT INSERTION                                   │   │
│  │                                                                     │   │
│  │  When user accepts a suggestion:                                    │   │
│  │  1. Save current clipboard                                          │   │
│  │  2. Copy suggestion to clipboard                                    │   │
│  │  3. Simulate Ctrl+V (paste)                                         │   │
│  │  4. Restore original clipboard                                      │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 7️⃣ KEY LIBRARIES TO USE

| Purpose           | Python Library         | Notes                    |
| ----------------- | ---------------------- | ------------------------ |
| UI Automation     | `pywinauto`            | Use `backend="uia"`      |
| Low-level Windows | `pywin32`              | For overlay windows      |
| Keyboard hooks    | `pynput` or `keyboard` | Global keystroke capture |
| Clipboard         | `pyperclip`            | Cross-platform clipboard |
| Input simulation  | `pyautogui`            | Simulate keystrokes      |
| Process info      | `psutil`               | Get process details      |

---

## 8️⃣ BROWSER-SPECIFIC NOTES

### For Chrome/Edge (Chromium-based):

- Requires `--force-renderer-accessibility` flag for full UI Automation support
- OR use browser extension for richer DOM access

### For Firefox:

- Has better native accessibility support
- UI Automation works without special flags

### For Electron Apps (VS Code, Slack, Discord):

- Limited UI Automation support
- Text content may be exposed, but structure is limited
- Grammarly likely uses heuristics + window title parsing

---

## 9️⃣ SUMMARY: GRAMMARLY'S STRATEGY

| Component               | Technology Used                             |
| ----------------------- | ------------------------------------------- |
| **Text capture**        | Windows UI Automation (ITextProvider)       |
| **Focus tracking**      | UI Automation focus change events           |
| **Caret position**      | GetCaretRange() + GetBoundingRectangles()   |
| **Overlay UI**          | Layered transparent windows (WS_EX_LAYERED) |
| **Text insertion**      | Clipboard + Ctrl+V simulation               |
| **App detection**       | Process name + window title                 |
| **Keyboard monitoring** | Low-level keyboard hooks                    |

---

_Research completed: 2026-01-23 09:44 IST_
