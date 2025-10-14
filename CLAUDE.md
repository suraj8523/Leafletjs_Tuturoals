# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository contains **Leaflet.js Japanese Tutorials** - a comprehensive collection of 10 standalone tutorial documents covering Leaflet.js from beginner to advanced level. Each tutorial is a self-contained markdown file with complete, working code samples.

**Target Version:** Leaflet.js v1.9.4

## Repository Structure

```
.
├── README.md                      # Main entry point with tutorial index
├── 01_quick_start.md              # ⭐ Beginner
├── 02_markers_custom_icons.md     # ⭐⭐ Beginner
├── 03_mobile_map.md               # ⭐⭐ Beginner
├── 04_geojson.md                  # ⭐⭐⭐ Intermediate
├── 05_layer_groups_control.md     # ⭐⭐⭐ Intermediate
├── 06_choropleth_map.md           # ⭐⭐⭐ Intermediate
├── 07_wms_tms.md                  # ⭐⭐⭐⭐ Advanced
├── 08_overlays.md                 # ⭐⭐⭐⭐ Advanced
├── 09_map_panes.md                # ⭐⭐⭐⭐⭐ Advanced
└── 10_extending_leaflet.md        # ⭐⭐⭐⭐⭐ Advanced
```

## Content Architecture

### Tutorial Progression
Tutorials are designed to be followed sequentially but can be used independently. Each builds on concepts from earlier chapters:

1. **Foundation** (01-03): Basic map creation, markers, mobile support
2. **Data Handling** (04): GeoJSON integration
3. **Layer Management** (05-06): Complex layer systems and data visualization
4. **External Services** (07): WMS/TMS integration
5. **Advanced Techniques** (08-10): Overlays, panes, extending Leaflet

### Tutorial Structure Pattern
Each tutorial follows a consistent structure:
1. **Header**: Title, difficulty level (⭐ rating), learning objectives
2. **Prerequisites**: Required knowledge from previous tutorials
3. **Concept Introduction**: Explanation of the topic
4. **Incremental Examples**: Code samples building in complexity
5. **Complete Working Example**: Full HTML page ready to run
6. **FAQ Section**: Common issues and solutions
7. **Next Steps**: Links to related tutorials
8. **Reference Links**: Official documentation and resources

## Working with Tutorials

### Adding a New Tutorial
When adding a new tutorial file:

1. **Follow the naming convention**: `##_descriptive_name.md` (two-digit number)
2. **Include all required sections**:
   - Difficulty rating with ⭐ symbols
   - Learning objectives list
   - Prerequisites section referencing other tutorials by filename
   - Complete working code examples
   - FAQ section
   - "Next Steps" with links to related tutorials
   - Reference links section

3. **Code samples must**:
   - Use Leaflet v1.9.4 CDN links with integrity hashes
   - Be complete and runnable (copy-paste ready)
   - Use Japanese comments and explanations
   - Include proper HTML structure with viewport meta tag
   - Specify map container height in CSS

4. **Update README.md**:
   - Add tutorial to appropriate difficulty section
   - Include learning objectives summary
   - Update the progression flowchart if needed
   - Add to update history section

### Modifying Existing Tutorials

When updating a tutorial:
- Maintain the existing structure and section order
- Keep code examples complete and self-contained
- Ensure all internal links (to other tutorials) remain valid
- Update the "📅 更新履歴" (Update History) section in README.md
- Test that all code samples still work with Leaflet v1.9.4

### Standard Code Template

All tutorials use this HTML template structure:

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>[Tutorial Title]</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>
    <style>
        #map { height: 600px; width: 100%; }
    </style>
</head>
<body>
    <div id="map"></div>
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        // Tutorial code here
    </script>
</body>
</html>
```

## Key Technical Details

### Leaflet.js Usage Patterns

**Coordinate Format**: Always `[latitude, longitude]` for Leaflet (note: GeoJSON uses `[longitude, latitude]`)

**Common Tokyo Coordinates** (used throughout tutorials):
- Tokyo Station: `[35.6812, 139.7671]`
- Tokyo Tower: `[35.6586, 139.7454]`
- Tokyo Skytree: `[35.7101, 139.8107]`

**Standard Map Tiles**:
- OpenStreetMap: `https://tile.openstreetmap.org/{z}/{x}/{y}.png`
- GSI (Japan): `https://cyberjapandata.gsi.go.jp/xyz/std/{z}/{x}/{y}.png`

### Cross-References Between Tutorials

Tutorials reference each other using relative markdown links:
```markdown
[01_quick_start.md](01_quick_start.md)
```

When adding cross-references:
- Use filename-based links (not section anchors)
- Typically reference earlier tutorials in prerequisites
- Reference related tutorials in "Next Steps" section

## Language and Terminology

All content is in **Japanese**:
- Code comments: Japanese
- Explanations: Japanese
- Variable names: English (standard practice)
- UI text in examples: Japanese

**Key terminology translations**:
- Map → 地図 (chizu)
- Marker → マーカー (mākā)
- Layer → レイヤー (reiyā)
- Overlay → オーバーレイ (ōbārei)
- Tutorial → チュートリアル (chūtoriaru)

## Quality Standards

Each tutorial must:
1. **Be self-contained**: Reader can complete it without external dependencies
2. **Include working code**: All examples must run without modification
3. **Provide context**: Explain *why* not just *how*
4. **Address common issues**: FAQ section with typical problems
5. **Link appropriately**: Cross-reference related concepts in other tutorials
6. **Use consistent formatting**: Follow established markdown structure

## Git Commit Conventions

When committing changes:
- Use conventional commit format: `feat:`, `fix:`, `docs:`
- Write commit messages in Japanese
- Include detailed body for tutorial additions/major changes
- Add `🤖 Generated with [Claude Code]` footer

Example:
```
feat: 新しいプラグイン開発チュートリアルを追加

Leaflet.jsプラグインの開発方法を解説する新しい上級チュートリアルを追加。

- プラグインアーキテクチャの説明
- 実装例とベストプラクティス
- README.mdを更新

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

## Resources Referenced Throughout

**Official Leaflet Resources**:
- API Reference: https://leafletjs.com/reference.html
- Official Examples: https://leafletjs.com/examples.html
- GitHub: https://github.com/Leaflet/Leaflet

**Japanese Map Data**:
- GSI (Geospatial Information Authority): https://maps.gsi.go.jp/development/ichiran.html
- e-Stat (Statistics): https://www.e-stat.go.jp/

**Tools Mentioned**:
- GeoJSON.io: https://geojson.io/
- ColorBrewer: https://colorbrewer2.org/
- Font Awesome: https://fontawesome.com/

## Educational Philosophy

These tutorials emphasize:
- **Progressive disclosure**: Start simple, add complexity gradually
- **Learning by doing**: Every concept has hands-on code
- **Real-world relevance**: Examples mirror actual use cases
- **Self-sufficiency**: Teach debugging and problem-solving approaches

When adding content, prioritize practical understanding over theoretical completeness.
