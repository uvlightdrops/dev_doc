---
project: dev_doc
scope: guides
purpose: Textual terminal UI architecture and implementation patterns
created: 2026-11-05 11
agent_project: ki-ecosystem
status: active
semantic_blocks:
- overview
- architecture-component-design
- common-patterns
- resources
- see-also
updated: 2026-11-10 23
update_reason: Validate YAML metadata and align semantic blocks with section headings
---
# Textual TUI Best Practices

**Status:** Reference guide for Textual framework development  
**Last Updated:** 2026-10-20  
**Scope:** Cross-project reference - use with project-specific adaptations

<a id="overview"></a>

## Overview [overview]

Textual is a Python framework for building full-featured Terminal User Interfaces (TUIs). This guide documents best practices learned from production TUI development.

<a id="architecture--component-design"></a>

## Architecture & Component Design [architecture-component-design]

### 1. Widget Hierarchy & Composition

**Best Practice:** Build reusable, single-responsibility widgets rather than monolithic screens.

```python
# Good: Composed widgets
class LoggingPanel(Container):
    """Logs display with pagination and filtering"""
    
class SettingsPanel(Container):
    """Configuration editor"""

class App(Screen):
    def compose(self):
        yield Header()
        yield TabbedContent(
            TabPane("Logs", LoggingPanel()),
            TabPane("Settings", SettingsPanel()),
        )
        yield Footer()

# Avoid: Single monolithic widget with all logic
```

**Benefits:**
- Easier to test (unit test individual widgets)
- Reusable across screens
- Cleaner separation of concerns
- Easier to maintain state

### 2. State Management

**Best Practice:** Use reactive attributes for state that affects UI.

```python
from textual.reactive import reactive

class ContentViewer(Widget):
    current_file: reactive[str] = reactive("")
    line_number: reactive[int] = reactive(1)
    
    def watch_current_file(self, old_value: str, new_value: str):
        """Triggered when current_file changes"""
        self.reload_content()
    
    def watch_line_number(self, old_value: int, new_value: int):
        """Triggered when line_number changes"""
        self.highlight_line()
```

**Avoid:** Manual state tracking with callbacks.

**Benefits:**
- Automatic UI updates on state change
- Decoupled from event handling
- Clean reactive pattern

### 3. Data Loading & Async Operations

**Best Practice:** Use `work()` decorator for background tasks.

```python
from textual.work import work
from textual.message import Message

class FileLoader(Container):
    class FileLoaded(Message):
        def __init__(self, content: str):
            super().__init__()
            self.content = content
    
    @work(exclusive=True)
    async def load_file(self, path: str):
        """Load file asynchronously, don't block UI"""
        try:
            content = await asyncio.to_thread(open(path).read)
            self.post_message(self.FileLoaded(content))
        except Exception as e:
            self.notify(f"Error: {e}", severity="error")
    
    def on_file_loaded(self, message: FileLoaded):
        """Handle message from background task"""
        self.update_display(message.content)
```

**Avoid:** Synchronous file I/O or network calls blocking the UI thread.

**Benefits:**
- UI remains responsive
- No frozen screens during slow operations
- Clean message-driven communication

### 4. Event Handling & Message Passing

**Best Practice:** Use Textual's message system for inter-widget communication.

```python
class SearchBar(Widget):
    class Search(Message):
        def __init__(self, query: str):
            super().__init__()
            self.query = query
    
    def on_input_submitted(self, event):
        self.post_message(self.Search(self.input.value))

class ResultsPanel(Widget):
    def on_search_bar_search(self, message: SearchBar.Search):
        """Receive search query from SearchBar"""
        self.perform_search(message.query)
```

**Avoid:** Direct widget references and direct method calls between widgets.

**Benefits:**
- Decoupled widgets
- Easier to test
- Easier to refactor UI layout

### 5. Keyboard & Mouse Handling

**Best Practice:** Bind common actions in App or Screen level.

```python
class AppScreen(Screen):
    BINDINGS = [
        ("q", "quit", "Quit"),
        ("ctrl+s", "save", "Save"),
        ("f1", "help", "Help"),
        ("tab", "focus_next", "Next"),
    ]
    
    def action_save(self):
        """Called when Ctrl+S pressed"""
        self.notify("Saving...")
    
    def action_help(self):
        """Called when F1 pressed"""
        self.show_help_modal()
```

**Avoid:** Catching key events at widget level and duplicating bindings.

**Benefits:**
- Global hotkeys work everywhere
- Single source of truth for keybindings
- Easier to document available actions

### 6. Modal Dialogs & Popups

**Best Practice:** Use Textual's `ModalScreen` for dialogs returning values.

```python
class ConfirmDialog(ModalScreen):
    def compose(self):
        yield Container(
            Static("Are you sure?"),
            Horizontal(
                Button("Yes", id="yes"),
                Button("No", id="no"),
            ),
        )
    
    def on_button_pressed(self, event: Button.Pressed):
        self.dismiss(result=event.button.id == "yes")

class MainScreen(Screen):
    async def show_confirm(self):
        result = await self.app.push_screen_wait(ConfirmDialog())
        if result:
            self.perform_action()
```

**Benefits:**
- Modal blocks interaction with main screen
- Returns value (async/await pattern)
- Clean lifecycle management

### 7. Styling & Theming

**Best Practice:** Use CSS in separate files, avoid inline styles.

```
# app.tcss (Textual CSS)
Screen {
    layout: vertical;
}

#header {
    dock: top;
    height: 3;
    background: $surface;
    color: $text;
}

#content {
    border: solid $accent;
}

Button {
    margin: 1 2;
    background: $primary;
}

Button:focus {
    background: $accent;
}
```

```python
class App(Textual):
    CSS_PATH = "app.tcss"
```

**Avoid:** Inline `styles.*` in Python code for large projects.

**Benefits:**
- Consistent theming
- Easy to modify UI without code changes
- Familiar CSS-like syntax
- Separation of concerns

### 8. Performance: Large Data Sets

**Best Practice:** Use `OptionList` or paginated widgets for large lists.

```python
from textual.widgets import OptionList

class LargeFileList(Container):
    def compose(self):
        options = [
            (f"File {i}", i) for i in range(10000)  # Don't render all at once
        ]
        yield OptionList(*options[:100])  # Virtual scrolling in OptionList
    
    def on_option_list_selected(self, event):
        file_id = event.option.id
        self.load_file(file_id)
```

**Avoid:** Adding 10,000 widgets to a container.

**Benefits:**
- Smooth scrolling with many items
- Lower memory footprint
- Better UI responsiveness

### 9. Logging & Debugging

**Best Practice:** Use Python's `logging` module, not `print()`.

```python
import logging

log = logging.getLogger(__name__)

class DataProcessor(Widget):
    def process(self, data):
        log.debug(f"Processing {len(data)} items")
        try:
            result = self.compute(data)
            log.info(f"Processing complete: {len(result)} results")
            return result
        except Exception as e:
            log.error(f"Processing failed", exc_info=True)
            raise
```

**Configure in your app:**

```python
import logging.config

logging.config.dictConfig({
    'version': 1,
    'handlers': {
        'file': {
            'class': 'logging.FileHandler',
            'filename': 'app.log',
        },
    },
    'root': {'level': 'INFO', 'handlers': ['file']},
})
```

**Avoid:** `print()` for debugging - output interferes with TUI rendering.

**Benefits:**
- Logs don't interfere with UI
- Can be toggled on/off without code changes
- Standard Python logging practices

### 10. Testing Textual Apps

**Best Practice:** Use Textual's testing utilities.

```python
from textual.pilot import Pilot

async def test_button_click():
    app = MyApp()
    async with app.run_test() as pilot:
        # Simulate user actions
        await pilot.press("tab")  # Move focus
        await pilot.press("enter")  # Click button
        
        # Assert app state changed
        assert app.counter == 1
```

**Benefits:**
- Test user interactions programmatically
- No need to actually render terminal
- Fast test execution

---

<a id="common-patterns"></a>

## Common Patterns [common-patterns]

### Loading State with Spinner

```python
from textual.widgets import Static

class LoadingSpinner(Static):
    def render(self) -> str:
        frames = ["⠋", "⠙", "⠹", "⠸", "⠼", "⠴", "⠦", "⠧", "⠇", "⠏"]
        return frames[int(time.time() * 10) % len(frames)]
```

### Tabbed Interface with Lazy Loading

```python
from textual.containers import TabbedContent, TabPane

class LazyLoadedPane(TabPane):
    def on_show(self):
        """Load content when tab becomes visible"""
        if not self.loaded:
            self.load_data()
            self.loaded = True
```

### Command Palette

Textual provides `OptionList` for command palette pattern.

---

<a id="resources"></a>

## Resources [resources]

- **Official:** https://textual.textualize.io
- **API Reference:** https://textual.textualize.io/api
- **Tutorial:** https://textual.textualize.io/tutorial

---

<a id="see-also"></a>

## See Also [see-also]

- Project-specific implementations of these patterns (see your project docs)
- Related: Web framework best practices in DOCUMENTATION_ORGANIZATION_GUIDE.md
