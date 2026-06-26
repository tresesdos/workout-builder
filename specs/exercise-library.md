# Exercise Library — Module Specification

**Project:** workout-builder  
**Module:** Exercise Library  
**Version:** 1.0  
**Status:** Ready for prototype

---

## Purpose

A searchable, filterable, and sortable catalog of individual exercises that serves as the foundational data layer for building workout sessions. The exercise library is the first module of the workout-builder application.

---

## Out of Scope

The following are explicitly excluded from this module:

- Sets and reps (belong in workout builder)
- Workout routines or training splits
- User accounts or authentication
- Social features or sharing
- Exercise creation or editing by the user
- Smart / AI-powered search suggestions (future feature)
- Embedded video player (future feature — video URL link only for prototype)
- Per-exercise thumbnails (future feature — muscle group placeholder icons only for prototype)

---

## Data Model

Each exercise is a single record with the following attributes:

```json
{
  "id": "unique-string-identifier",
  "name": "Barbell Back Squat",
  "type": "Strength",
  "primary_muscle": "Quadriceps",
  "secondary_muscles": ["Glutes", "Hamstrings", "Core"],
  "equipment": ["Barbell", "Squat Rack"],
  "difficulty": "Intermediate",
  "description": "A compound lower body movement performed with a barbell across the upper back.",
  "instructions": "Stand with feet shoulder-width apart. Brace core, hinge at hips and knees simultaneously, descend until thighs are parallel to floor, drive through heels to return to standing.",
  "keywords": ["squat", "legs", "quads", "compound", "lower body"],
  "video_url": "https://example.com/barbell-back-squat"
}
```

### Controlled Vocabularies

These fields use fixed value sets. No variations or synonyms permitted in data entry.

**Type**
- Strength
- Cardio
- Mobility

**Primary Muscle / Secondary Muscles**
- Chest
- Back
- Shoulders
- Biceps
- Triceps
- Forearms
- Core
- Glutes
- Quadriceps
- Hamstrings
- Calves
- Full Body

**Difficulty**
- Beginner
- Intermediate
- Advanced

### Data Notes

- `equipment` may be an empty array for bodyweight exercises
- `secondary_muscles` may be an empty array
- `video_url` is optional and may be null
- Exercises with `primary_muscle: "Full Body"` must have specific muscles listed in `secondary_muscles`
- Each equipment variation (e.g. Dumbbell Curl, Barbell Curl, Hammer Curl) is stored as a separate exercise record

---

## Seed Dataset

For the prototype, Claude will generate approximately 30 exercises covering all muscle groups and all three types. Dataset will be provided as a JSON array matching the data model above.

Target for production: hundreds of exercises.

---

## User Stories

| # | When I... | I expect... |
|---|-----------|-------------|
| 1 | Open the exercise library | An unfiltered alphabetical list of all exercises with no active search or filters |
| 2 | Search for an exercise | Results list filtered to matching exercises, sorted alphabetically by name, showing name and short description |
| 3 | Select a muscle group filter | List reloads showing matching exercises, sorted by muscle group first then name |
| 4 | Tap on an exercise | A slide-in drawer opens showing full exercise detail |
| 5 | Search returns no results | A plain message: "No exercises found. Try a different search term or clear your filters." |
| 6 | Dismiss the detail drawer | Drawer closes, list returns to exact previous state including scroll position, filters, and search term |

---

## Component Inventory

### 1. Search Bar
- Text input with placeholder: "Search exercises"
- Inline clear (×) button visible when field has content
- Submit triggers search (button tap or keyboard return)
- Visible search submit button — do not rely on keyboard return alone

### 2. Filter Bar
- Horizontal scrollable row of muscle group filter chips
- Each chip represents one muscle group from the controlled vocabulary
- Multiple chips can be selected simultaneously
- Selected chips display in active/highlighted state
- Each active filter displays as a dismissible chip — tap to deselect
- "Clear all" control visible when any filter is active

### 3. Exercise List
- Displays 10 exercises per page
- Lazy load: next 10 load as user scrolls to the last visible item
- Each list item shows:
  - Muscle group placeholder icon
  - Exercise name
  - Short description (truncated to 2 lines)
  - Primary muscle group label

### 4. Sort Control
- Default sort: alphabetical by name (no active filter or search)
- When filter active: sort by muscle group, then name
- User-accessible sort options: Name (A–Z), Muscle Group, Difficulty, Type

### 5. Exercise Detail Drawer
- Slides in from the right (or bottom on mobile)
- Displays full exercise record:
  - Name
  - Type badge
  - Difficulty badge
  - Primary muscle
  - Secondary muscles
  - Equipment required (or "Bodyweight" if equipment array is empty)
  - Description
  - Instructions
  - Keywords (displayed as tags)
  - Video link (if video_url present)
- Close control (×) in drawer header
- Tap outside drawer to dismiss
- Dismissing preserves full list state

---

## Behavior Specifications

### Search
- Search executes on submit only (button tap or keyboard return)
- Search matches against: name, description, keywords
- Search and filters work together using AND logic (results must match search term AND selected filters)
- Active search term persists until user clears the field

### Filtering
- Muscle group filter matches against primary_muscle AND secondary_muscles
- Multiple muscle group filters use OR logic (exercise matches if it belongs to ANY selected group)
- Filters and search work together using AND logic
- Bodyweight filter option returns exercises where equipment array is empty

### Empty States
- No results from search or filter: "No exercises found. Try a different search term or clear your filters."
- Library loads with no data: "No exercises available."

### List State Preservation
- Opening and closing the detail drawer preserves:
  - Scroll position
  - Active filters
  - Active search term
  - Sort order

### Lazy Loading
- Initial load: 10 exercises
- Trigger: user scrolls to last visible exercise
- Load increment: 10 additional exercises
- Loading indicator displayed during fetch

---

## Visual Direction

Clean, functional, low-distraction. This is a utility interface used during or before a workout — speed of scanning matters more than visual richness.

- High information density in the list view — compact list items, not cards
- Strong typographic hierarchy: exercise name prominent, description secondary
- Muscle group icons serve as quick visual anchors, not decoration
- Drawer detail view can breathe more — more whitespace, larger type for instructions
- Muted color palette with a single functional accent color for active filter states and interactive elements
- No gratuitous animation — drawer slide-in is the only motion

---

## Future Features (Not in Scope for Prototype)

- Embedded video player
- Per-exercise thumbnail images
- Smart search suggestions ("Did you mean...")
- AI-powered recommendations on empty results
- Programmatic training split filters (Leg Day, Push/Pull, etc.)
- Exercise creation and editing
- Favoriting or tagging exercises
- Back / Upper Back split in muscle group taxonomy
