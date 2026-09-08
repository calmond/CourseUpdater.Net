# CourseUpdater

CourseUpdater is a small, reusable tool for delivering incremental course-project updates through Git.

It is designed for classes where students begin from a starter repository and continue building the same project across multiple lessons. Instructors publish each lesson as an immutable tagged Git commit. Students run one command to retrieve and apply the next update.

The approach keeps normal student work in Git while hiding the more specialized `fetch` + `cherry-pick` mechanics behind a safe, repeatable workflow.

## Why use it?

CourseUpdater is useful when you want students to:

- fork one starter repository and keep it for an entire project;
- receive new tests, CI workflows, starter files, assets, or instructions over time;
- keep their own Git history intact;
- receive lessons in sequence;
- avoid manually copying files from new starter ZIP archives;
- use the same project year after year with immutable release points.

## How it works

The instructor repository has:

- a stable starter branch, usually `main`;
- a `course-updates` branch used to build lesson releases;
- one immutable tag per lesson update;
- a `.course/course-updates.json` manifest;
- one marker file added by each lesson update.

Example release history:

```text
starter-v1
   |
   +-- lesson-01-update
   |
   +-- lesson-02-update
   |
   +-- lesson-03-update
```

A student runs:

```bash
dotnet run --project tools/CourseUpdater
```

CourseUpdater then:

1. finds the Git repository root;
2. reads `.course/course-updates.json`;
3. refuses to run if the working tree is dirty;
4. verifies the configured instructor remote exists;
5. selects the next unapplied lesson;
6. fetches instructor tags;
7. verifies prerequisites are installed;
8. cherry-picks the lesson tag;
9. aborts and restores the previous state if the cherry-pick conflicts;
10. verifies the lesson marker file was installed.

## Student commands

Install the next lesson:

```bash
dotnet run --project tools/CourseUpdater
```

Show lesson status:

```bash
dotnet run --project tools/CourseUpdater -- --list
```

Install a specific lesson, if prerequisites are already present:

```bash
dotnet run --project tools/CourseUpdater -- 2
```

## Manifest

Each project defines its releases in:

```text
.course/course-updates.json
```

Example:

```json
{
  "course": "CS 220",
  "project": "Example Project",
  "remote": "upstream",
  "nextSteps": [
    "dotnet test",
    "git push"
  ],
  "lessons": [
    {
      "number": 1,
      "name": "First Feature",
      "tag": "lesson-01-update",
      "marker": ".course/applied/lesson-01"
    },
    {
      "number": 2,
      "name": "Validation",
      "tag": "lesson-02-update",
      "marker": ".course/applied/lesson-02"
    }
  ]
}
```

`nextSteps` is optional and lets each project tell students what to do after an update.

## Instructor workflow

For each lesson:

1. Start from the existing `course-updates` branch.
2. Add only the files students should receive for that lesson.
3. Add the lesson marker.
4. Update the manifest and any status badges.
5. Commit the entire lesson payload as one commit.
6. Test it.
7. Tag that exact commit with the manifest's tag.
8. Push the branch and tag.

Example:

```bash
git add .
git commit -m "Add Lesson 2 update"
git push
git tag -a lesson-02-update -m "Lesson 2 update"
git push origin lesson-02-update
```

Do not merge lesson payloads back into the starter branch.

See [docs/INSTRUCTOR_GUIDE.md](docs/INSTRUCTOR_GUIDE.md) for a complete walkthrough.

## Repository integration

A course project typically contains:

```text
project/
├── .course/
│   ├── course-updates.json
│   └── applied/
├── tools/
│   └── CourseUpdater/
├── src/
├── tests/
└── README.md
```

Copy `src/CourseUpdater` from this repository into your course project as `tools/CourseUpdater`, or adapt it to your preferred project layout.

## Design principles

CourseUpdater intentionally keeps the mechanism small:

- Git remains the transport and history system.
- Lesson releases are immutable.
- Student changes remain in the student's own repository.
- Updates are deterministic and sequential.
- Conflicts fail safely instead of leaving beginners in a half-completed cherry-pick.
- Project-specific information lives in configuration rather than updater code.

## Current implementation

The reference implementation is written in C# and targets .NET 10. The underlying release model is language-independent; other launchers or implementations can use the same manifest/tag/marker convention.

A Gradle/Java implementation and IDE integrations are natural future extensions.

## Origin

CourseUpdater grew out of an ASP.NET Core teaching workflow in which students fork a starter project, receive lesson-specific automated tests and GitHub Actions workflows, and progressively build one application throughout the course.

The working example is the <a href="https://github.com/WVUP/UnitConverter" target=_blank>WVUP UnitConverter project</a>.

## License

MIT License. See [LICENSE](LICENSE).
