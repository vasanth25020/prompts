# prompts
all type of prompt 

Improve the ui/ux design 

"""Act as an expert UX/UI designer. Propose 5 innovative and modern design improvements for a [App/Website Category] interface to increase user engagement and streamline the primary workflow. Focus on typography, color contrast, responsive layout patterns, and intuitive Call-to-Actions (CTAs)"""

"""i want responsive design support for all device"""

isometric world

# MASTER PROMPT: Isometric World — Fully Functional 2D & 3D Interactive Map

## Role

Act as a senior frontend engineer, software architect, visualization specialist, and testing engineer. Your task is to design and implement a production-ready **Isometric World** module with fully functional 2D and 3D visualization modes.

Work within the existing project's framework, folder structure, coding standards, dependencies, and architecture. Inspect the repository before making changes.

Do not build only a static visual mockup. Every visible control, map interaction, vehicle animation, view switch, and selection must work correctly.

# 1. Project Objective

Build an interactive world map that represents an operational environment containing:

- Roads and road intersections.
- Trees, vegetation, and landscaping.
- Buildings, facilities, and landmarks.
- Warehouses, hubs, and operational locations.
- Trucks and other vehicles.
- Location markers and label chips.
- Configurable vehicle routes and destinations.
- Map navigation and visualization controls.

The application must support two distinct visualization modes:

**2D Top-Down View:** A conventional overhead map with roads, buildings, trees, vehicles, and location markers.

**3D Isometric View:** A depth-rendered isometric world with visible building heights, raised structures, tree canopies, road surfaces, shadows, and appropriately projected vehicles.

Both views must represent the same underlying world, layout, locations, road network, and vehicle state.

The core architectural principle is:

**One world data model → One pure layout engine → Two rendering modes → One shared interaction and vehicle system.**

# 2. Repository Inspection and Integration

Before implementation:

1. Inspect the existing repository, framework, package manager, dependencies, and directory structure.
2. Identify existing map components, design systems, state management, routing logic, and testing frameworks.
3. Reuse existing components and dependencies wherever appropriate.
4. Identify the correct integration point for the Isometric World module.
5. Preserve existing application functionality, styles, APIs, and coding conventions.
6. Avoid unnecessary dependency installation or unrelated refactoring.
7. Create a short implementation plan before modifying files.

If the repository already contains a map implementation, extend it rather than creating a disconnected replacement.

# 3. Architecture: Pure Layout Engine

Create a framework-independent layout engine responsible for the world geometry and object positions.

Suggested module:

`worldLayout.ts`

The layout engine must not depend on React, the DOM, SVG elements, canvas rendering, or UI state.

## Responsibilities

- Generate deterministic world layouts.
- Calculate world coordinates and grid positions.
- Define road segments, intersections, and connectivity.
- Position buildings, trees, hubs, and landmarks.
- Define vehicle routes, starting positions, and destinations.
- Calculate object dimensions and spatial bounds.
- Maintain stable object identifiers.
- Validate layout configuration.
- Detect or prevent unintended object overlaps.
- Keep objects within the configured world boundaries.
- Return immutable, structured layout data.

## Suggested data model

Define typed interfaces or equivalent types for:

- `WorldConfig`
- `WorldLayout`
- `WorldPoint`
- `WorldObject`
- `RoadSegment`
- `RoadIntersection`
- `Building`
- `Tree`
- `Location`
- `Vehicle`
- `VehicleRoute`
- `RouteWaypoint`
- `WorldBounds`
- `LabelConfig`
- `ViewMode`
- `CameraState`

Each object should have a stable identifier, world position, relevant dimensions, and the properties needed by its renderers.

## Pure layout API

Implement an API similar to:

- `generateWorldLayout(config)`
- `validateWorldLayout(layout)`
- `getWorldBounds(layout)`
- `getRouteById(layout, routeId)`
- `getObjectById(layout, objectId)`
- `findRouteBetween(layout, originId, destinationId)`, if route finding is required.

These functions must produce predictable outputs for identical inputs.

Separate deterministic world generation from rendering and runtime animation.

# 4. Coordinate Systems and Projection

Establish one consistent world-coordinate system for both views.

## World coordinates

Store world positions independently of their screen representation.

For example:

`WorldPoint = { x, y, z }`

Use `z` for elevation or object height where relevant. In a flat 2D layout, it may be zero.

## 2D projection

Implement `projectTo2D()` to convert world positions into top-down screen coordinates.

Requirements:
- Preserve horizontal and vertical road geometry.
- Apply consistent scaling and viewport offsets.
- Support camera transformations.
- Keep object positions and route geometry consistent.

## Isometric projection

Implement `projectToIsometric()` to convert world coordinates into isometric screen coordinates.

Use a consistent mathematical projection, such as:

- Screen horizontal position derived from the difference between world X and Y.
- Screen vertical position derived from their sum, adjusted by elevation.

For configurable tile dimensions:

`screenX = originX + (x - y) * tileWidth / 2`

`screenY = originY + (x + y) * tileHeight / 2 - z * elevationScale`

Adapt these equations to the selected world-coordinate conventions.

Requirements:
- Maintain consistent projection for every object.
- Account for object height and ground contact points.
- Support configurable tile dimensions and elevation scaling.
- Keep vehicles aligned with their road routes.
- Prevent projection logic from being duplicated throughout UI components.

# 5. 2D Top-Down View

Create a fully interactive 2D map renderer.

Suggested component: `WorldMap2D`

## Visual requirements

Render:

- Road surfaces, intersections, junctions, and connecting paths.
- Buildings and facilities.
- Trees and landscaping.
- Operational hubs and location markers.
- Trucks and other vehicles.
- Location labels and contextual indicators.
- Selected, hovered, active, and disabled states.

Use clean geometry, consistent spacing, clear contrast, and a professional operational-dashboard visual style.

## Functional requirements

- Render the generated world layout accurately.
- Support pan and zoom.
- Support object selection and hover interactions.
- Show location details when a location is selected.
- Display truck routes and current positions.
- Support route highlighting.
- Provide a reset or fit-to-screen function.
- Adapt to desktop, tablet, and mobile viewports.

The 2D mode must be a true overhead representation, not an isometric image with its perspective flattened.

# 6. 3D Isometric View

Create a fully interactive isometric renderer.

Suggested component: `WorldMapIsometric`

## Visual requirements

Render:

- Isometric road surfaces and intersections.
- Buildings with visible side faces and roof surfaces.
- Trees with visible height and canopy depth.
- Hubs, warehouses, and other structures.
- Trucks with appropriate orientation and visible depth.
- Location markers and label chips.
- Shadows, highlights, and subtle depth cues.
- Selected and hovered objects.

Use a consistent isometric projection and visual hierarchy.

## Rendering technology

Prefer the simplest appropriate technology compatible with the existing project:

- SVG for vector-based isometric geometry and moderate scene complexity.
- A suitable WebGL-based library only when genuine 3D geometry, lighting, camera control, or scene complexity justifies it.

Do not use a fake 3D effect as a substitute for correct isometric geometry.

If the implementation uses projected SVG, describe it accurately as an isometric 2.5D representation rather than claiming it is a fully volumetric 3D engine.

## Functional requirements

- Pan, zoom, select, hover, and reset.
- Render the same world objects used by the 2D view.
- Project roads, buildings, trees, and vehicles consistently.
- Keep vehicles grounded on their routes.
- Support route highlighting and location details.
- Maintain readable labels at supported zoom levels.
- Ensure objects are rendered in an appropriate depth order.
- Avoid incorrect overlaps between buildings, trees, trucks, and road surfaces.

Where practical, support depth sorting or appropriate rendering layers to keep nearer objects in front of farther objects.

# 7. 2D / 3D View Mode Switcher

Add a visible, accessible view selector in the map toolbar.

Example:

`[ 2D Top-Down ] [ 3D Isometric ]`

## Switching requirements

- Switch modes without reloading the page.
- Clearly indicate the selected mode.
- Provide a smooth transition when practical.
- Preserve the world layout and object identifiers.
- Preserve vehicle positions and animation progress.
- Preserve selected objects and active routes.
- Preserve visibility preferences and relevant filters.
- Keep the viewport centered on the same logical world location.
- Adapt the zoom appropriately for each projection.
- Prevent duplicate animation loops or event listeners.
- Support keyboard and touch interaction.

Changing the rendering mode must not regenerate the world or reset the vehicles.

The two modes must use the same underlying world data, even though their projected positions and visual styles differ.

# 8. Pan, Zoom, and Camera Controls

Implement a reusable navigation system shared by both views.

## Pan

- Drag to move around the map.
- Support pointer events and touch where practical.
- Prevent accidental panning when interacting with controls.
- Maintain predictable pointer behavior.

## Zoom

- Support zoom-in and zoom-out buttons.
- Support mouse-wheel zoom.
- Zoom toward the pointer position where practical.
- Set configurable minimum and maximum zoom levels.
- Avoid sudden jumps or incorrect coordinate transformations.

## Reset and fit

Provide:

- Reset view.
- Fit entire world to viewport.
- Center on a selected location where appropriate.

## Camera state

Maintain a consistent camera-state model containing the required translation, scale, and projection-specific settings.

Keep camera state separate from the generated world layout and vehicle positions.

Test navigation behavior in both visualization modes.

# 9. Label Chips and Location Markers

Create a reusable `LocationLabel` component.

## Requirements

- Show configurable labels for buildings, hubs, destinations, and operational locations.
- Display short names, identifiers, status, or other relevant information.
- Support compact label chips with consistent typography and spacing.
- Provide selected, hovered, active, and inactive states.
- Support click and keyboard interaction.
- Keep labels anchored to their associated world objects.
- Update label positions correctly during pan and zoom.
- Keep labels readable in both 2D and isometric views.
- Allow configurable label visibility and zoom thresholds.
- Avoid label collisions where practical.
- Provide tooltips or detail panels for additional information.

Use a clear separation between world-space coordinates and screen-space label placement.

Labels must not drift away from their objects when the user changes views or moves the camera.

# 10. Roads and Route Network

Create a road model that supports both visual rendering and vehicle movement.

## Requirements

- Represent roads as connected graph edges or equivalent structured route geometry.
- Represent intersections and junctions explicitly where necessary.
- Define road widths and visual properties.
- Support straight roads, turns, junctions, and route segments.
- Maintain consistent connectivity between road segments.
- Avoid disconnected or invalid routes.
- Support route highlighting.
- Keep road geometry independent of the rendering mode.

Define a single logical route representation and derive its 2D and isometric visual paths from the same route data.

If automatic route planning is implemented, use a graph-based pathfinding algorithm and validate that the resulting route follows connected roads.

# 11. Animated Trucks and Vehicles

Create a reusable vehicle-rendering and animation system.

Suggested components:

- `TruckRenderer`
- `VehicleLayer`
- `TruckAnimationController`

## Vehicle data

Each vehicle should support:

- Stable vehicle ID.
- Display name or registration identifier.
- Current route ID.
- Origin and destination.
- Current progress along the route.
- Configurable speed.
- Direction of travel.
- Current status.
- Playback state.

## Animation requirements

- Move trucks smoothly along the actual route geometry.
- Calculate vehicle positions from route progress.
- Orient vehicles according to their direction of movement.
- Project the same logical position into both views.
- Support multiple independently animated vehicles.
- Avoid random movement or teleporting between waypoints.
- Keep trucks aligned with roads and turns.
- Handle route completion and reset behavior.
- Avoid animation jumps when switching between 2D and 3D.
- Clean up animation resources when no longer required.

Use a time-based animation mechanism, such as `requestAnimationFrame`, where appropriate. Animation should be based on elapsed time rather than assuming a fixed number of frames per second.

## Vehicle controls

Provide working controls for:

- Play.
- Pause.
- Resume.
- Reset.
- Show or hide routes.
- Select a truck.
- View truck details.

Where appropriate, support selecting a destination and assigning a valid route.

Ensure the UI accurately reflects each vehicle's playback state.

# 12. State Management and Synchronization

Separate state into three categories.

## Static world state

Includes:
- Generated world layout.
- Roads and intersections.
- Buildings and trees.
- Locations and route definitions.

## Runtime simulation state

Includes:
- Vehicle positions and progress.
- Playback and speed settings.
- Active routes.
- Selected vehicles and locations.

## View state

Includes:
- Current mode.
- Camera position and zoom.
- Label visibility.
- Selected visual layers.
- Hovered objects.

Avoid duplicating state unnecessarily.

When switching modes, transform the existing world and runtime state into the new projection. Do not create a second independent simulation for the 3D view.

# 13. Layer and Visibility Controls

Add optional layer controls if they fit the existing application's design.

Possible layers:

- Roads.
- Buildings.
- Trees and vegetation.
- Locations.
- Trucks.
- Vehicle routes.
- Labels.

Requirements:
- Each supported layer can be shown or hidden.
- Changes apply to both views.
- Layer toggles must not modify the underlying layout.
- Selected objects and active routes should remain consistent.
- Provide a sensible default configuration.

# 14. Responsive Design and Accessibility

The module must work on desktop, tablet, and mobile devices.

Requirements:

- Responsive map container.
- Usable toolbar and view switcher.
- Touch-friendly controls.
- Appropriate pointer and keyboard interactions.
- Accessible names for interactive controls.
- Visible keyboard focus indicators.
- Sufficient contrast for labels and important map objects.
- Reduced-motion support.
- Graceful handling of small viewports.

Do not make important controls inaccessible behind the map or outside the visible viewport.

# 15. Performance and Resource Management

Optimize the implementation for a growing number of map objects and vehicles.

Requirements:

- Generate the static layout only when its configuration changes.
- Keep static geometry separate from frequently updated vehicle positions.
- Avoid unnecessary component re-renders.
- Use memoization when it provides a measurable benefit.
- Avoid recalculating the entire layout during pan, zoom, or vehicle animation.
- Clean up animation frames, event listeners, and subscriptions.
- Avoid duplicated animation loops after view switching.
- Handle empty and large layouts gracefully.
- Keep interaction responsive during animation.

Do not introduce complex optimization techniques without a demonstrated need.

# 16. Testing Strategy

Use the existing project's test framework. Add tests at the appropriate levels.

## Pure layout unit tests

Test:
- Deterministic generation.
- Coordinate conversion.
- World bounds.
- Stable identifiers.
- Road connectivity.
- Route validity.
- Object placement.
- Configuration validation.
- Empty and minimal layouts.
- Multiple vehicle routes.

## Projection tests

Test:
- 2D coordinate conversion.
- Isometric coordinate conversion.
- Elevation handling.
- World-to-screen consistency.
- Route waypoint projection.
- Consistent ground contact points for vehicles.

## Component and integration tests

Test:
- Rendering of both views.
- 2D/3D switching.
- Preservation of selected objects.
- Preservation of vehicle progress.
- Pan, zoom, and reset.
- Label positioning and interactions.
- Layer visibility.
- Truck play, pause, resume, and reset.
- Multiple simultaneous vehicles.
- Cleanup of animation resources.

## Build validation

Run the available:
- Unit tests.
- Integration tests.
- Type checks.
- Linter.
- Production build.

Resolve errors caused by the implementation before considering the work complete.

Do not claim a test passed unless it was actually executed and passed.

# 17. Suggested Project Structure

Adapt these modules to the repository's existing conventions rather than enforcing this exact structure.

- `world/types.ts` — shared type definitions.
- `world/worldLayout.ts` — pure layout generation.
- `world/coordinates.ts` — coordinate projection.
- `world/roadNetwork.ts` — road graph and route geometry.
- `world/vehicleSimulation.ts` — vehicle progress and movement calculations.
- `components/WorldMap.tsx` — shared map container.
- `components/WorldMap2D.tsx` — top-down renderer.
- `components/WorldMapIsometric.tsx` — isometric renderer.
- `components/ViewModeSwitcher.tsx` — view selector.
- `components/MapControls.tsx` — pan, zoom, and reset controls.
- `components/LocationLabel.tsx` — location chips.
- `components/TruckRenderer.tsx` — vehicle rendering.
- `components/VehicleControls.tsx` — playback controls.
- `tests/worldLayout.test.ts` — layout tests.
- `tests/coordinates.test.ts` — projection tests.
- `tests/worldMap.integration.test.tsx` — interaction tests.

# 18. Visual Design Direction

Create a polished, modern operational visualization rather than a basic wireframe.

## 2D visual style

- Clean overhead road network.
- Clear road hierarchy.
- Readable buildings and trees.
- Minimal, well-positioned labels.
- Distinct vehicle symbols.
- Restrained shadows and borders.
- Clear route and selection highlighting.

## 3D isometric visual style

- Consistent isometric angles.
- Visible building roofs and sides.
- Layered trees and landscaping.
- Roads with convincing perspective and depth.
- Subtle shadows and lighting.
- Properly oriented trucks.
- Clear foreground and background ordering.
- Readable location chips.

Maintain consistent design tokens for typography, spacing, colors, status indicators, and interaction states.

Use subtle animation and visual effects only when they improve clarity.

# 19. Implementation Workflow

Execute the work in this order:

1. Inspect the repository and document the current architecture.
2. Define shared types and world-coordinate conventions.
3. Implement the pure layout engine.
4. Write and run layout unit tests.
5. Implement coordinate projection functions.
6. Build the 2D top-down renderer.
7. Build the isometric renderer.
8. Implement shared pan, zoom, and camera controls.
9. Implement label chips and object selection.
10. Implement the vehicle simulation and route-based animation.
11. Add the 2D/3D view switcher.
12. Synchronize camera, labels, selection, and vehicle state across modes.
13. Add layer controls and responsive behavior.
14. Write and run component and integration tests.
15. Run linting, type checks, and the production build.
16. Fix regressions and document the final implementation.

If an existing project requires a different order, adapt the workflow while preserving the separation between layout, simulation, and rendering.

# 20. Definition of Done

The implementation is complete only when:

- The world layout is generated by a pure, independently testable module.
- The same world data renders correctly in 2D and isometric views.
- Roads, buildings, trees, and routes remain spatially consistent.
- Both views support functional pan, zoom, and reset.
- The view switcher works without reloading the application.
- Switching views preserves vehicle progress and selection.
- Labels remain anchored to the correct locations.
- Trucks follow real route geometry and turn correctly.
- Multiple trucks can move independently.
- Playback controls work correctly.
- The UI is responsive and accessible.
- No duplicate animation loops or resource leaks are introduced.
- Automated tests and production build pass, where supported by the repository.
- Existing application features continue to work.
- The implementation contains no fake buttons, placeholder interactions, or unfinished core functionality.

# Final Instructions

Implement the actual feature in the existing repository, not merely a proposal or a sample code snippet.

Inspect existing files before editing. Reuse the project's conventions and dependencies. Make focused, maintainable changes. Validate the implementation with the available tests and build tools.

When finished, provide a concise report containing:

1. Files created and modified.
2. Architecture and key implementation decisions.
3. How the 2D and 3D modes share the same world state.
4. Implemented map interactions and vehicle controls.
5. Tests and build commands executed, with actual results.
6. Any remaining limitations or work that could not be completed.

**Priority order:** Correctness → Shared architecture → Functional interactions → Testability → Performance → Visual polish.

The final result must be a reliable, interactive, extensible 2D and 3D Isometric World module that integrates with the existing application.
