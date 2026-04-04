---
name: add-publication
description: Add a new publication entry to the al-folio Jekyll academic portfolio site. Use this skill whenever the user wants to add a paper, publication, conference paper, journal article, or BibTeX entry to their website. Trigger when the user mentions adding a publication, pasting a DOI, sharing BibTeX, or says things like "I have a new paper" or "add this to my publications page", even if they don't use the word "publication" explicitly.
---

# Add Publication

Add a new publication to Jiale Zhang's academic portfolio site (al-folio theme, Jekyll + jekyll-scholar).

## What this skill does

When the user provides publication details (typically a DOI/URL and raw BibTeX from the publisher), this skill:

1. Parses and enriches the BibTeX entry with al-folio-specific fields
2. Prepends the entry to `_bibliography/papers.bib` (newest first)
3. Links a preview image **the user provides** (you cannot generate or retrieve images)
4. Optionally updates `_data/venues.yml` with a new venue abbreviation

**Critical:** You ONLY process the BibTeX and metadata the user gives you. Never fabricate, guess, or hallucinate paper titles, authors, DOIs, or any publication details. If something is missing, ask the user.

## Expected user input

The user will typically provide:
- A **DOI or publisher URL** (ACM Digital Library, IEEE Xplore, etc.)
- **Raw BibTeX** copied from the publisher

They may also specify:
- Whether the paper should be `selected` (featured on homepage) — **default is NOT selected**
- A venue abbreviation for the badge
- An award designation

## Step-by-step workflow

### 1. Parse the BibTeX

Read the BibTeX the user provides. Identify:
- Entry type (`@inproceedings`, `@article`, `@inbook`, etc.)
- The citation key
- All standard fields (author, title, year, booktitle/journal, doi, abstract, etc.)

If key fields are missing (e.g., `abstract`, `author`, `doi`), ask the user to provide them. The abstract is important — al-folio shows it in an expandable section on the publications page. If the publisher BibTeX doesn't include an abstract, ask the user to paste it separately.

### 2. Ask for the preview image

You **cannot** generate, download, or retrieve images. The user must provide the image themselves.

Tell them:

> **Preview image — you need to provide this:**
> - Save your image to `HCIMaker.github.io/assets/img/publication_preview/`
> - Name it something short and descriptive, all lowercase, no spaces (e.g., `myproject.jpg`)
> - Best format: **JPG or PNG, approximately 400x400px (square)**. Square images look most consistent in the publication list since the thumbnail column is ~200px wide.
> - Good sources: a figure from your paper, a system diagram, or a teaser image.

Ask: **"What's the filename of your preview image?"** Wait for the user to confirm the filename before proceeding. Do not assume or invent a filename.

### 3. Enrich the BibTeX entry

Add these al-folio custom fields to the BibTeX entry before inserting:

- `preview = {filename.jpg}` — the preview image filename they provided
- `selected={true}` — only if the user explicitly asks for it. **Do not add this field by default.**
- `abbr` — if the user wants a venue badge, add `abbr={VENUE}`. Check `_data/venues.yml` to see if it already exists; if not, offer to add it.
- `award` and `award_name` — only if the user mentions an award (e.g., best paper)

If the DOI field is missing or empty, ask the user for a publisher URL and add it as `html = {url}` so readers can still access the paper.

Do NOT add these unless the user asks:
- `bibtex_show` — shows raw BibTeX toggle
- `google_scholar_id` — requires manual lookup
- `pdf`, `code`, `video`, `poster`, `slides`, `supp`, `blog`, `website` — only if user provides links

### 4. Format and insert into papers.bib

Read `HCIMaker.github.io/_bibliography/papers.bib`.

Format the new entry cleanly:
- Keep the existing style: fields indented, one per line
- Preserve any HTML in the title field (the user may have `<strong>` tags for awards)
- Keep the abstract on one line (no line breaks within the abstract value)

**Prepend** the new entry to `papers.bib` — place it after the `---\n---` YAML front matter but before the first existing `@` entry. This keeps newest publications at the top.

### 5. Update venues (if needed)

If the user specified an `abbr` value and it's not in `_data/venues.yml`, ask if they'd like to add it with a color and URL.

Example venue entry:
```yaml
"CHI":
  url: https://chi.acm.org
  color: "#b31b1b"
```

### 6. Confirm and summarize

After making changes, summarize what was done:
- The BibTeX key added
- Which file(s) were modified
- Remind about the preview image if they haven't confirmed it's in place

### 7. Launch local preview

After all changes are made, start the Jekyll dev server via WSL so the user can preview:

```bash
wsl bash -c "cd /mnt/c/Jiale/personal_web/HCIMaker.github.io && bundle exec jekyll serve"
```

Run this in the background. Tell the user the site will be available at `http://localhost:4000` once it finishes building.

### 8. Get user approval

Ask the user to check the publication page at `http://localhost:4000/publications/` and confirm:

- **If approved**: proceed to step 9 (commit & push).
- **If changes needed**: the user will describe what to fix. Make the modifications, then repeat from step 7 (relaunch preview for re-check).

### 9. Commit, push, and stop the server

Once the user approves:

1. **Stop the Jekyll server** by killing the background WSL process.
2. **Git commit** the changed files (typically `_bibliography/papers.bib`, the preview image, and optionally `_data/venues.yml`). Use a descriptive commit message like `Add publication: <short paper title>`.
3. **Git push** to deploy to GitHub Pages.

## Example

**User provides:**
```
DOI: 10.1145/3736425.3770100

BibTeX:
@inproceedings{10.1145/3736425.3770100,
author = {Chang, Yen Cheng and Codling, Jesse and ...},
title = {ViLA: Leveraging General-Purpose Audio...},
year = {2025},
booktitle = {Proceedings of the 12th ACM International Conference on Systems for Energy-Efficient Buildings, Cities, and Transportation},
series = {BuildSys '25},
}
```

**Skill produces** (added to papers.bib):
```bibtex
@inproceedings{10.1145/3736425.3770100,
author = {Chang, Yen Cheng and Codling, Jesse and ...},
title = {ViLA: Leveraging General-Purpose Audio...},
year = {2025},
booktitle = {Proceedings of the 12th ACM International Conference on Systems for Energy-Efficient Buildings, Cities, and Transportation},
series = {BuildSys '25},
preview = {vila.jpg}
}
```

## Important notes

- The site's `_config.yml` has `scholar.last_name: [Einstein]` and `scholar.first_name: [Albert, A.]` — these are the al-folio defaults that control which author name gets **bolded**. If these haven't been updated to `[Zhang]` / `[Jiale, J.]`, mention this to the user as a one-time fix.
- The `papers.bib` file starts with `---\n---` (empty YAML front matter) — this is required by Jekyll. Do not remove it.
- Preview images go in `HCIMaker.github.io/assets/img/publication_preview/`.
- Publications display newest-first by default (jekyll-scholar sorts by year descending).
