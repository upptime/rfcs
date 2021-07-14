# How to create an RFC

Start by creating a new branch with your first name and a slugified version of your RFC separated by a `/` (slash). For example, if your first name is Anand and the title is "New feature", create a new git branch with the name `anand/new-feature`. Then, create a new file in your new branch, not in `main`.

Create a new markdown file in the `rfcs/{year}` folder, replacing `{year}` with the current year. The name of the file should be a slugified version of your title with the `.md` file extension. For example, if the title is "New feature" and you are publishing this RFC in 2021, the file name should be `rfcs/2021/new-feature.md`.

The contents of this markdown file should be in the following format, the first line should have a title and then paragraphs and content:

```md
# Title

Body goes here...
```

Create a pull request from your branch. It should be a draft pull request if you are still working on the RFC. You can merge it to `main` when your document is ready.

After your branch has been merged, GitHub Actions workflow will automatically create an issue for your RFC which will be used for discussion. You don't need to do that manually.
