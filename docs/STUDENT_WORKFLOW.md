# Student Workflow

Your instructor may use CourseUpdater to add new lesson material to an existing project.

## Before beginning

Make sure your work is committed:

```bash
git status
```

Your working tree should be clean.

Get any changes from your own GitHub repository:

```bash
git pull
```

## Install the next lesson

```bash
dotnet run --project tools/CourseUpdater
```

CourseUpdater will:

- check that your repository is safe to update;
- retrieve the instructor's lesson release;
- apply the next lesson in sequence;
- restore your repository if the update conflicts.

## Check installed lessons

```bash
dotnet run --project tools/CourseUpdater -- --list
```

## After an update

Follow the next steps printed by CourseUpdater. Your instructor may configure commands such as:

```bash
dotnet test
git push
```

A newly installed lesson may intentionally include tests that fail until you complete the assignment.
