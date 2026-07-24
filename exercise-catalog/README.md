# LifeTracker Exercise Catalog

Published exercise data consumed by the [LifeTracker](https://github.com) Android app's
"Check for exercise updates" feature (Settings > Exercise Library). The app fetches
`exercises.json` from this repo's `main` branch and reconciles it against each user's local
exercise library — new exercises are added, exercises the user hasn't modified are updated
automatically, and exercises the user *has* edited or removed are only ever changed with their
explicit confirmation.

## Updating an exercise

Edit the exercise's object in `exercises.json` and **increment its `version` field by 1**. The
app only treats an exercise as changed when the remote `version` is higher than what a device
last synced — editing content without bumping `version` will be silently ignored by every
installed app.

## Adding a new exercise

Append a new object to the `exercises` array. `sourceId` must be a unique, stable, URL-safe slug
(lowercase, hyphenated) — it's the permanent identity of the exercise across every future update,
so never change an existing `sourceId` or reuse one for a different exercise.

## Schema

```jsonc
{
  "catalogVersion": 1,           // bump when the schema itself changes shape (rare)
  "exercises": [
    {
      "sourceId": "barbell-bench-press",   // stable slug, never reused/changed
      "name": "Barbell Bench Press",
      "activityType": "WEIGHTLIFTING",     // see ActivityType.kt in the app for the full list
      "shortDescription": "...",
      "longDescription": "...",
      "difficultyLevel": "INTERMEDIATE",   // BEGINNER | INTERMEDIATE | ADVANCED
      "version": 1,                        // bump on every content change to this exercise
      "muscles": [
        {"muscleName": "Chest", "isPrimary": true}
      ],
      "equipment": [
        {"equipment": "BARBELL", "isRequired": true}   // see Equipment.kt for the full list
      ],
      "links": [
        {"label": "Form guide", "url": "https://..."}
      ],
      "media": [
        {"mediaType": "IMAGE", "url": "https://...", "caption": "Starting position"}
      ]
    }
  ]
}
```

`muscleName` values must exactly match a name in the app's built-in muscle reference table
(Chest, Lats, Trapezius, Rhomboids, Lower Back, Front/Side/Rear Deltoid, Rotator Cuff, Biceps,
Triceps, Forearms, Hands, Abs, Obliques, Deep Core, Quadriceps, Hamstrings, Glutes, Hip Flexors,
Adductors, Abductors, Hip Rotators, Calves, Shins, Neck, Cardiovascular System, Full Body) — an
unrecognized name is silently skipped for that one muscle rather than failing the whole sync.

`media` is URL-only — the catalog can't ship local files, so every entry needs a hosted image or
video link.
