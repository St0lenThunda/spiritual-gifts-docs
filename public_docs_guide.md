# Public Documentation Management

This guide explains how to manage and preview the public-facing documentation for **Called & Equipped**.

## How It Works

The documentation system uses a "Single Source of Truth" approach. All documents live in the main vault, but only those marked as **public** are included in the generated MkDocs site.

The filtering is handled by a custom script: `scripts/generate_public_docs.py`.

## Marking a Document as Public

To make a document visible on the public site, you must add `visibility: public` to its YAML frontmatter:

```yaml
---
visibility: public
---
```

!!! important
    If a document does not have `visibility: public`, it will be **excluded** from the public site build.

## How to Preview Public Docs

You can easily preview exactly what the public will see using the built-in workflow.

1.  Run the `/mkdocs` workflow.
2.  The script will automatically scan all your notes.
3.  Any note with `visibility: public` will be copied to a temporary `docs/` directory for MkDocs.
4.  The server will start at [http://localhost:8080](http://localhost:8080).

## Tagging Strategy

We use a canonical set of tags to further organize public content (see `tags.md`). 
Common public tags include:
- `#public/core`
- `#public/pastor`
- `#public/safeguards`

!!! tip
    Use these tags to help users find relevant content within the public site.
