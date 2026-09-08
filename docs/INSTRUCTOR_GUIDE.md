# Instructor Guide

## 1. Prepare the starter repository

Create the project students should fork.

Add:

```text
.course/course-updates.json
tools/CourseUpdater/
```

Keep `.course/applied/` empty in the starter.

A typical manifest begins with all planned lesson entries, even if the corresponding tags have not been published yet.

## 2. Create the instructor remote model

Students normally have:

```text
origin    -> their fork
upstream  -> instructor repository
```

For example:

```bash
git remote add upstream git@github.com:YourOrg/YourCourseProject.git
```

Set `"remote": "upstream"` in the manifest.

## 3. Establish a starter release

Once the starter repository has been verified by CI:

```bash
git tag -a starter-v1 -m "Verified starter project v1"
git push origin starter-v1
```

Treat that tag as immutable once students begin using it.

## 4. Create the course-updates branch

Create the branch from the verified starter state:

```bash
git switch -c course-updates starter-v1
git push -u origin course-updates
```

Do not merge this branch back into `main`.

## 5. Build a lesson payload

For Lesson 1, add only the material students should receive at the beginning of the lesson. Examples include:

- new tests;
- a lesson-specific CI workflow;
- README status badge;
- starter files;
- assets;
- dependencies;
- assignment-support files.

Also create the marker:

```text
.course/applied/lesson-01
```

The marker may contain a human-readable description.

## 6. Commit one lesson as one commit

A lesson tag should identify exactly one complete update payload.

```bash
git add .
git diff --cached
git commit -m "Add Lesson 1 update"
git push
```

Avoid mixing unrelated maintenance into a lesson release commit.

## 7. Verify before tagging

Run the project's normal build and test commands.

It is acceptable, and often desirable, for newly delivered acceptance tests to fail before students implement the lesson. The important requirements are:

- the project still builds;
- the new tests are discoverable;
- failures are caused by missing lesson behavior rather than broken test infrastructure;
- previously completed test groups remain independently runnable.

## 8. Tag the lesson

```bash
git tag -a lesson-01-update -m "Lesson 1 update"
git push origin lesson-01-update
```

Do not move a published lesson tag after students have used it. If a release needs correction, publish a new corrective update or versioned tag.

## 9. Student update workflow

Students begin with a clean repository:

```bash
git status
git pull
```

Then run:

```bash
dotnet run --project tools/CourseUpdater
```

The resulting cherry-pick is already committed. Students normally follow with their project-specific validation command and:

```bash
git push
```

## 10. Versioning between semesters

Keep historical starter and lesson tags immutable.

If the starter changes significantly:

```text
starter-v1
starter-v2
```

For a redesigned lesson sequence, consider versioned lesson tags:

```text
v2-lesson-01-update
v2-lesson-02-update
```

or use a new project repository when the assignment has materially changed.

## CI status dashboards

One useful pattern is one workflow per lesson or milestone. That allows students to see regression status independently:

```text
Starter     passing
Lesson 1    passing
Lesson 2    failing
```

When future tests are installed just-in-time through CourseUpdater, students never see failures for lessons they have not reached yet.
