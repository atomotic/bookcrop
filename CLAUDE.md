# Page Viewer - Margin Selector

A browser-based tool for selecting margins on scanned book pages and exporting crop coordinates for batch processing with VIPS.

## Overview

This web application provides an interactive interface for defining crop margins on scanned book pages. It uses the File System Access API to work directly with local directories, allowing you to mark content areas across multiple pages and export the coordinates for automated batch cropping.

## Features

### Core Functionality

- **Directory Picker**: Select local directories containing scanned page images using Chrome's File System Access API
- **Image Browsing**: Navigate through pages with Previous/Next buttons or arrow keys (←/→)
- **Interactive Selection**: Draw rectangular selections to mark page content areas
- **Real-time Coordinates**: Display selection coordinates (upper-left x/y, lower-right x/y) in image pixel space
- **Multi-Directory Support**: Work on multiple scan projects simultaneously with isolated selections

### Selection Tools

- **Draw**: Click and drag to create a new selection rectangle
- **Move**: Drag inside the selection to reposition it
- **Resize**: Drag corner handles to adjust selection size
- **Lock/Unlock**: Toggle between resize-enabled and move-only modes
  - Locked: Can move but cannot resize or create new selections
  - Unlocked: Full editing capability with visible corner handles
- **Visual Overlay**: Semi-transparent mask outside selection area for better visibility

### Navigation & Progress

- **Page Indicator Bar**: Visual bars showing all pages at bottom of screen
  - Gray bars: Pages without selections
  - Green bars: Pages with selections defined
  - Blue bar: Current page
  - Split blue/green: Current page with selection
  - Click any bar to jump to that page
  - Hover for page number and filename tooltip

### Data Persistence

- **LocalStorage**: Selections automatically saved per directory
- **Directory-Specific**: Each directory maintains its own selection data
- **Auto-Save**: Selections saved when navigating between pages or modifying selection
- **Session Persistence**: Data survives browser restarts

### Export Options

#### CSV Export
- Downloads: `[directory-name]-selections.csv`
- Format: `filename,ulx,uly,lrx,lry`
- Use case: Import into spreadsheets, databases, or custom processing scripts

#### VIPS Script Export
- Downloads: `[directory-name]-crop.sh`
- Generates ready-to-run bash script with `vips extract_area` commands
- Creates `cropped/` output directory automatically
- Format: `vips extract_area "input.jpg" "cropped/output.jpg" left top width height`
- Includes directory reference in script header

## Requirements

- **Browser**: Chrome, Edge, Opera, or any Chromium-based browser (version 86+)
- **API**: File System Access API support required
- **VIPS**: For running exported crop scripts (optional, `libvips` package)

## Usage

### Getting Started

1. Open `page-viewer.html` in a Chromium-based browser
2. Click **Select Directory** and choose your scans folder
3. The first image loads with page indicators at the bottom

### Defining Margins

1. **Draw Selection**: Click and drag on the image to create a selection rectangle
   - The green rectangle marks your content area
   - Dark overlay shows what will be cropped out
2. **Adjust Position**: Drag inside the selection to move it
3. **Adjust Size**: Drag corner handles to resize (when unlocked)
4. **Lock Selection**: Click **Lock Selection** to prevent accidental resizing
   - Useful when applying same margins across multiple pages
   - Can still move, but cannot resize or redraw

### Navigating Pages

- **Buttons**: Use Previous/Next buttons
- **Keyboard**: Arrow keys (←/→)
- **Indicator Bar**: Click any bar at the bottom to jump to that page
- **Auto-Save**: Current page selection is saved before switching pages

### Tracking Progress

- Green indicators show which pages have selections defined
- Directory name displayed in brackets: `[directory-name] 5 / 50 - page005.jpg`
- Coordinate display updates in real-time as you adjust selection

### Exporting Results

#### For CSV Processing
1. Click **Export CSV** button
2. Downloads `[directory-name]-selections.csv`
3. Import into your preferred processing tool

#### For VIPS Batch Cropping
1. Click **Export VIPS Script** button
2. Downloads `[directory-name]-crop.sh`
3. Place script in your images directory
4. Make executable: `chmod +x [directory-name]-crop.sh`
5. Run: `./[directory-name]-crop.sh`
6. Cropped images saved to `cropped/` subdirectory

## File Structure

```
scantailorweb/
├── page-viewer.html    # Single-file web application
└── Claude.md          # This documentation file
```

## Technical Details

### Coordinate System

- Coordinates are in **actual image pixel space** (using `naturalWidth`/`naturalHeight`)
- Format: `ulx, uly, lrx, lry` (upper-left x/y, lower-right x/y)
- Origin: Top-left corner (0,0)
- Compatible with VIPS `extract_area` command

### Storage Format

```javascript
{
  "directory-name-1": {
    "page001.jpg": { ulx: 150, uly: 200, lrx: 2000, lry: 3000 },
    "page002.jpg": { ulx: 150, uly: 200, lrx: 2000, lry: 3000 }
  },
  "directory-name-2": {
    "scan01.tif": { ulx: 100, uly: 150, lrx: 1800, lry: 2800 }
  }
}
```

### Supported Image Formats

- JPEG (`.jpg`, `.jpeg`)
- PNG (`.png`)
- GIF (`.gif`)
- BMP (`.bmp`)
- WebP (`.webp`)
- TIFF (`.tif`, `.tiff`)

### Canvas Overlay Implementation

- HTML5 Canvas positioned absolutely over image element
- Canvas dimensions match image natural size for accurate pixel coordinates
- Mouse event scaling accounts for displayed vs. actual image size
- Selection rendered with semi-transparent overlay and green border

## Keyboard Shortcuts

- `←` Previous page
- `→` Next page

## Version History

### v1.3 - Page Indicator Bar
- Added visual page indicator bar with color-coded progress
- Click-to-navigate functionality
- Hover tooltips with page info
- Real-time updates on selection changes

### v1.2 - Multi-Directory Support
- Directory-specific selection storage
- Directory name in UI and exports
- Support for working on multiple projects

### v1.1 - Selection Tools & Export
- Interactive rectangular selection with move/resize
- Lock/unlock toggle
- Real-time coordinate display
- CSV and VIPS script export
- LocalStorage persistence
- Fixed coordinate system using naturalWidth/Height

### v1.0 - Initial Release
- Basic image viewer
- Directory picker
- Keyboard navigation

## Browser Compatibility

| Browser | Version | Status |
|---------|---------|--------|
| Chrome  | 86+     | ✅ Fully supported |
| Edge    | 86+     | ✅ Fully supported |
| Opera   | 72+     | ✅ Fully supported |
| Firefox | -       | ❌ File System Access API not supported |
| Safari  | -       | ❌ File System Access API not supported |

## Installation

No installation required. Simply open `page-viewer.html` in a supported browser.

For VIPS command-line tool:
```bash
# Ubuntu/Debian
sudo apt install libvips-tools

# macOS
brew install vips

# Arch Linux
sudo pacman -S libvips
```

## License

Generated with [Claude Code](https://claude.com/claude-code)

## Contributing

This is a single-file web application designed for simplicity and portability. All HTML, CSS, and JavaScript are contained in `page-viewer.html`.
