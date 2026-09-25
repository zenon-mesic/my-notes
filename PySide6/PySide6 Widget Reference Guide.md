

# PySide6 Widget Reference Guide

## Core Building Blocks

```python
from PySide6.QtWidgets import QWidget, QApplication, QMainWindow, QFrame, QSizePolicy
```

| Widget | Purpose |
|--------|---------|
| `QWidget` | Base class for all UI objects; top-level windows inherit from this |
| `QMainWindow` | Application main window with menu bar, toolbar, status bar support |
| `QFrame` | Simple rectangular container with border styles |
| `QSizeGrip` | Corner grip for resizing dialog windows |


## Text Input & Display

```python
from PySide6.QtWidgets import (
    QLabel, QLineEdit, QTextEdit, QPlainTextEdit, 
    QAbstractSpinBox, QSpinBox, QDoubleSpinBox, QDateTimeEdit
)
```

| Widget | Purpose | Common Use Cases |
|--------|---------|------------------|
| `QLabel` | Display read-only text or images | Labels, icons, static info |
| `QLineEdit` | Single-line text input | Search boxes, form fields |
| `QTextEdit` | Rich text multi-line editing | HTML editors, formatted notes |
| `QPlainTextEdit` | Plain text multi-line editing | Code editors, logs, console output |
| `QSpinBox` | Integer input with up/down controls | Quantity selectors, counters |
| `QDoubleSpinBox` | Float input with precision control | Measurements, prices |
| `QDateTimeEdit` | Date/time input with spinner | Timestamps, scheduling |
| `QDateEdit` | Date-only input | Birth dates, deadlines |
| `QTimeEdit` | Time-only input | Alarms, timers |


## Buttons & Selection Controls

```python
from PySide6.QtWidgets import (
    QPushButton, QCheckBox, QRadioButton, QButtonGroup,
    QToggle, QToolButton
)
```

| Widget | Purpose | Key Notes |
|--------|---------|-----------|
| `QPushButton` | Standard clickable button | Most common trigger widget |
| `QCheckBox` | Boolean on/off selection | Independent choices |
| `QRadioButton` | Mutually exclusive selection | Use with `QButtonGroup` |
| `QButtonGroup` | Groups radio buttons logically | Not a visual widget itself |
| `QToggle` | On/off toggle switch (Qt6) | Modern toggle UI |
| `QToolButton` | Compact button, often in toolbars | Icons + optional text |


## Combo Boxes & Lists

```python
from PySide6.QtWidgets import (
    QComboBox, QListWidget, QListWidgetItem,
    QMenu, QAction
)
```

| Widget | Purpose | Model View vs Widget Style |
|--------|---------|---------------------------|
| `QComboBox` | Dropdown selection list | Either editable or read-only |
| `QListWidget` | Scrollable list of items | Easy widget-style API |
| `QListWidgetItem` | Individual list item | Used with `QListWidget` |
| `QMenu` | Popup context/dropdown menu | Context menus, menubars |
| `QAction` | Abstract command/action | Reusable actions across menus/toolbars |


## Advanced List/Table/Tree Views (Model/View Architecture)

```python
from PySide6.QtWidgets import (
    QListView, QTableView, QTreeView,
    QTableWidgetItem, QTreeWidgetItem, QHeaderView
)
```

| Widget | Purpose | Best For |
|--------|---------|----------|
| `QListView` | Scrollable single-column view | Item lists, galleries |
| `QTableView` | Spreadsheet-like grid | Data tables, matrices |
| `QTreeView` | Hierarchical tree structure | File browsers, categories |
| `QTableWidget` | Table with built-in items | Quick prototypes (widget style) |
| `QTreeWidget` | Tree with built-in items | Quick hierarchies (widget style) |
| `QHeaderView` | Column/row headers | Customize table/tree headers |

> **Note:** Model/View architecture (`QListView`, `QTableView`, `QTreeView`) is preferred for production apps; Widget-style (`QTableWidget`, etc.) is easier for quick prototypes.


## Sliders & Progress Indicators

```python
from PySide6.QtWidgets import (
    QSlider, QProgressBar, QLCDNumber, QScrollBar, QDial
)
```

| Widget | Purpose | Orientation Options |
|--------|---------|---------------------|
| `QSlider` | Continuous value adjustment | Horizontal/Vertical |
| `QProgressBar` | Task completion indicator | Horizontal/Vertical, text % |
| `QLCDNumber` | LCD-style digital display | Seven-segment appearance |
| `QScrollBar` | Scrollbar control | Usually embedded in views |
| `QDial` | Circular rotary dial | Knob-style input |


## Container & Layout Containers

```python
from PySide6.QtWidgets import (
    QGroupBox, QScrollArea, QSplitter,
    QStackedWidget, QTabWidget, QToolBox,
    QMdiArea, QMdiSubWindow
)
```

| Widget | Purpose | Typical Use |
|--------|---------|-------------|
| `QGroupBox` | Labeled container for related controls | Form sections, settings panels |
| `QScrollArea` | Makes widgets scrollable | Large forms, image viewers |
| `QSplitter` | Resizable split panes | Editor+preview, file explorer |
| `QStackedWidget` | Multiple pages, one visible | Wizard steps, tab alternatives |
| `QTabWidget` | Tabbed interface | Settings panels, document tabs |
| `QToolBox` | Collapsible toolbox panels | Tool palettes, property editors |
| `QMdiArea` | Multiple document interface | IDE-like multi-window workspace |


## Dialog Windows

```python
from PySide6.QtWidgets import (
    QDialog, QMessageBox, QFileDialog, QColorDialog,
    QFontDialog, QInputDialog, QErrorMessage, QProgressDialog
)
```

| Widget | Purpose | Return Value Example |
|--------|---------|---------------------|
| `QDialog` | Base class for dialog windows | Custom dialogs inherit this |
| `QMessageBox` | Alerts, confirmations, warnings | `Yes/No/Cancel` returns |
| `QFileDialog` | Open/save file picker | Selected file path |
| `QColorDialog` | Color selection | Chosen color |
| `QFontDialog` | Font selection | Selected font |
| `QInputDialog` | Simple value prompts | Entered text, number, combo choice |
| `QErrorMessage` | Error message popup | Non-modal error display |
| `QProgressDialog` | Cancelable long operation | Progress + cancel option |


## Application Components

```python
from PySide6.QtWidgets import (
    QMenuBar, QMenu, QToolBar, QStatusBar,
    QDockWidget, QSystemTrayIcon
)
```

| Widget | Purpose | Attached To |
|--------|---------|-------------|
| `QMenuBar` | Top menu bar | `QMainWindow` |
| `QToolBar` | Icon/button toolbar | `QMainWindow` |
| `QStatusBar` | Bottom status line | `QMainWindow` |
| `QDockWidget` | Floating/dockable panel | `QMainWindow` |
| `QSystemTrayIcon` | Background tray icon | Desktop system tray |


## Specialized/Advanced Widgets

```python
from PySide6.QtWidgets import (
    QOpenGLWidget, QGraphicsView, QWebView,
    QCalendarWidget, QSplashScreen, QWizard, QWizardPage
)
```

| Widget | Purpose | Requirements |
|--------|---------|--------------|
| `QOpenGLWidget` | OpenGL rendering surface | OpenGL support |
| `QGraphicsView` | Graphics scene view | For `QGraphicsScene` |
| `QCalendarWidget` | Interactive calendar picker | Built-in (Qt6) |
| `QSplashScreen` | Startup splash screen | Temporary startup display |
| `QWizard` | Step-by-step wizard framework | Multi-page guided flows |
| `QWebView` / `QWebEngineView` | Embedded web content | Requires QtWebEngine module |


## Learning Path Recommendation

1. **Foundation** → `QWidget`, `QMainWindow`, layouts (`QVBoxLayout`, `QHBoxLayout`, `QGridLayout`)
2. **Text & Buttons** → `QLabel`, `QLineEdit`, `QPushButton`
3. **Selection Controls** → `QCheckBox`, `QRadioButton`, `QComboBox`
4. **Containers** → `QGroupBox`, `QScrollArea`, `QTabWidget`
5. **Data Display** → `QListView`, `QTableView` (learn Model/View)
6. **Dialogs** → `QMessageBox`, `QFileDialog`
7. **Advanced** → `QGraphicsView`, `QOpenGLWidget`
