# SOACS DaggerBridge v1.1 — Geo-Fencing Capability Overview

> **Current production release:** DaggerBridge v1.1  
> **Release status:** Released / Production  
> **Major v1.1 capability:** Geo-Fencing

Geo-Fencing is a released capability in **SOACS DaggerBridge v1.1**. It adds geographic filtering and map-based operating-area management to the existing keyword, coordinate, alert, CoT, and KMZ workflow.

The capability lets an operator define **where** a message is operationally relevant without changing the underlying keyword logic. A keyword can remain globally monitored while its geospatial output and/or popup behavior is constrained to one or more operator-defined areas.

## Operational concept

```mermaid
flowchart LR
    A[Message Received] --> B[Keyword Match]
    B --> C[Coordinate Detection]
    C --> D{Geo-Fence Enabled?}
    D -- No --> E[Normal DaggerBridge Processing]
    D -- Yes --> F{Inside Any Assigned Area?}
    F -- No --> G[Log Geo-Fence Reject]
    F -- Yes --> H[Geo-Fence Pass]
    H --> I[Operator Alert / CoT / KMZ Workflow]
```

Geo-Fencing does not replace the existing DaggerBridge keyword and coordinate workflow. It adds an optional geographic decision layer between coordinate validation and downstream operator/output actions.

## Geo-Fence types

### Radius

Radius fences use a geographic center plus distance. Operators can define the center by coordinate entry or map selection and use common distance units including nautical miles, statute miles, kilometers, and meters.

Radius geometry is stored geographically and rendered as a geodesic ring rather than a screen-space ellipse so map pan and zoom do not distort the saved area.

### Freehand Area

Operators can trace an irregular closed polygon directly on the offline map. The resulting area is stored using WGS-84 geographic coordinates and can represent operating areas that do not fit a simple circular radius.

### Multi-Area

A single saved Geo-Fence can contain multiple disconnected polygon components. This allows one logical operating area to contain separate geographic regions without including the space between them.

## Reusable and keyword-only areas

DaggerBridge v1.1 supports two complementary workflows:

- **Reusable saved Geo-Fences** — create an area once and assign it to multiple keywords.
- **Keyword-only / one-time Geo-Fences** — create a temporary area that belongs only to one keyword and is not added to the shared library.

A keyword can use multiple saved fences plus a keyword-only fence at the same time.

## Multiple-fence evaluation

Multiple assigned Geo-Fences use **OR / union** logic. A coordinate passes when it is inside or on the boundary of any valid assigned area.

This supports geographically separate areas without creating one oversized fence around all of them. If the same coordinate falls inside overlapping assigned fences, DaggerBridge generates only one downstream event for that coordinate.

An enabled Geo-Fence with no valid assigned or keyword-only areas fails closed.

## Geo-Fence library

The Geo-Fences workspace provides a master library for saved operating areas. v1.1 supports:

- Creating and naming reusable fences
- Editing existing fences
- Renaming shared fences while preserving keyword references
- Deleting saved fences and cleaning up their assignments
- Duplicating an existing fence and editing the copy
- Identifying whether a fence is user-created or supplied as an out-of-the-box reference
- Resetting supplied reference fences to their original starting geometry

Editing a shared saved fence intentionally changes the geometry used by every keyword assigned to that fence, so the application warns the operator before shared edits.

## Geo-Fence Overview

The combined **Geo-Fence Overview** provides a single operating-area picture across active fences.

Released v1.1 capabilities include:

- Distinct colors for visible fences
- Fence names drawn on the map
- Show/hide controls
- **FIT ALL**
- **FIT SELECTED**
- Direct fence selection from the map or legend
- Overlap highlighting
- Overlap hit reporting and selection cycling
- **EDIT SELECTED**
- **DUPLICATE & EDIT**
- In-place boundary editing while neighboring fences remain visible

Normal Overview interaction remains read-only until the operator intentionally enters an edit workflow.

## Boundary editing and snapping

v1.1 includes direct geometry adjustment tools:

- Draggable polygon vertices
- Midpoint insertion handles
- Whole-area movement
- Multi-Area component selection
- Point deletion
- Undo
- Save / cancel edit
- Radius center and radius handles
- **Snap to Fences** for aligning edited vertices or midpoints to nearby visible fence edges

These controls make adjacent operating areas easier to maintain without repeated manual coordinate entry.

## Offline map interaction

Geo-Fence authoring and review are designed for offline use.

Normal map interaction is:

- **Click-drag:** pan
- **Mouse wheel:** zoom
- **Double-click in Radius mode:** select center
- **Draw Area mode:** trace polygon geometry

Click-drag is the permanent normal pan behavior; a separate PAN MAP button is not required.

## Map and geometry integrity

DaggerBridge v1.1 reprojects saved WGS-84 geometry on map pan and zoom instead of stretching screen-space shapes.

The released implementation includes:

- Longitude wrapping across the ±180-degree seam
- Prevention of world-spanning polygon line artifacts
- Geodesic radius-ring rendering
- Dateline-aware fitting behavior
- Detection of impossible/corrupt radius values rather than silently clamping them
- Read-only Overview navigation outside explicit editing

Moving or zooming the map does not change the underlying saved Geo-Fence geometry.

## Popup behavior

v1.1 separates broad keyword awareness from geography-specific alerting:

- **Popup All** — alert on every enabled keyword match.
- **Popup In Geo-Fence** — alert only when a valid coordinate passes an enabled assigned Geo-Fence.
- Both controls off — no popup for that keyword.

The controls are independent, allowing the operator to choose broad awareness, Geo-Fence-only alerting, both, or neither.

## Summary View

The compact DaggerBridge Summary View includes a read-only world thumbnail showing active Geo-Fences currently in use. Selecting the thumbnail opens the full Geo-Fence Overview.

## Out-of-the-box reference fences

v1.1 includes operator-adjustable SOC reference starting areas. They are intended as editable starting templates, not authoritative command-boundary products.

Important behavior:

- Reference fences are not automatically assigned to keywords.
- Operators explicitly choose whether to use them.
- They can be adjusted, duplicated, replaced, or ignored.
- User-defined fences remain fully supported.

Operational/customer-specific boundary data is not published in this public repository.

## Configuration and portability

Geo-Fence definitions and assignments travel with DaggerBridge configuration profiles. Import-preview awareness includes saved reusable areas, keyword-only areas, assignments, and Geo-Fence-enabled keywords.

This allows a configuration package to describe not only message rules but also the geographic areas associated with those rules.

## Operator visibility and troubleshooting

DaggerBridge records Geo-Fence pass/reject decisions so the operator can determine why a coordinate did or did not proceed through the Geo-Fence-controlled workflow.

The design emphasizes visible operator state rather than hidden automation.

## Release status

**DaggerBridge v1.1 is the current released production version.** Geo-Fencing is part of that production capability set, not a preview or test-only feature.

The private source repository remains the controlled location for implementation, maintenance, test artifacts, and future development.

---

**SOACS DaggerBridge v1.1**  
**Geo-Fencing — Released Production Capability**
