---
output:
  html_document: default
  pdf_document: default
---
# Starting an R Project with a GitHub Repository

**A practical vignette for reproducible research workflows**
  
  ---
  
  ## Prerequisites -- do this first 
  
  - R and RStudio installed
- Git installed (`git --version` in your terminal to check)
- A GitHub account
- The `usethis` package (`install.packages("usethis")`)

---
  
  ## Step 1: Configure Git and GitHub credentials (one-time setup)
  
  If you haven't done this before, tell Git who you are and connect it to GitHub.
```r
library(usethis)

# Set your Git identity
use_git_config(
  user.name  = "Your Name",
  user.email = "you@example.com"
)

# Create a GitHub Personal Access Token (PAT)
# This opens a browser window to GitHub — select repo, workflow, gist scopes
create_github_token()

# Store the token securely in your .Renviron
gitcreds::gitcreds_set()
```

Run `gh_token_help()` if you run into trouble.

---

## Step 2: Create the R Project

In RStudio: **File → New Project → New Directory → New Project**

Or from the console:
```r
usethis::create_project("~/projects/my-edna-analysis")
```

This creates the directory, an `.Rproj` file, and opens a fresh RStudio session.

---

## Step 3: Initialize Git
```r
usethis::use_git()
```

`usethis` will:
1. Initialize a local Git repository
2. Stage and commit all existing files
3. Offer to restart RStudio (say yes)

You now have a local repo with an initial commit.

---

## Step 4: Create a sensible `.gitignore`

R projects generate files you don't want tracked. `usethis` handles most of this
automatically, but check `.gitignore` and add anything project-specific:
  ```
# Added by usethis
.Rproj.user
.Rhistory
.RData
.Ruserdata

# Data files (track with DVC or document separately)
data/raw/
  *.csv
*.rds

# Rendered outputs (regenerate from code)
docs/
  ```

Commit the updated `.gitignore`:
  ```bash
git add .gitignore
git commit -m "Update .gitignore"
```

---
  
  ## Step 5: Push to GitHub
  ```r
usethis::use_github(private = FALSE)   # or private = TRUE
```

This will:
  1. Create a new repository on GitHub under your account
2. Set it as the `origin` remote
3. Push your commits

Alternatively, create the repo on GitHub first and connect manually:
  ```bash
git remote add origin https://github.com/yourusername/my-edna-analysis.git
git branch -M main
git push -u origin main
```

---
  
  ## Step 6: Add standard project infrastructure
  
  With the repo live, add the scaffolding for a reproducible project:
  ```r
# A README rendered from R Markdown
usethis::use_readme_rmd()

# An MIT (or other) license
usethis::use_mit_license("Your Name")

# Folder structure
dir.create("data/raw",       recursive = TRUE)
dir.create("data/processed", recursive = TRUE)
dir.create("R")
dir.create("output/figures", recursive = TRUE)

# Optional: renv for package reproducibility
renv::init()
```

Commit and push:
  ```bash
git add -A
git commit -m "Add project scaffolding"
git push
```

---
  
  ## Step 7: The everyday Git loop
  
  From here, your workflow is:
  ```bash
# Check what's changed
git status

# Stage specific files (or use . for everything)
git add R/clean_data.R

# Commit with a meaningful message
git commit -m "Add function to filter low-read-depth samples"

# Push to GitHub
git push
```

In RStudio, the **Git pane** (top-right) lets you do all of this with clicks
instead of the terminal.

---
  
  ## Resulting project structure
  ```
my-edna-analysis/
  ├── my-edna-analysis.Rproj
├── README.Rmd
├── LICENSE
├── .gitignore
├── renv.lock
├── R/
  │   └── functions.R
├── data/
  │   ├── raw/        # never modified after download
  │   └── processed/
  └── output/
  └── figures/
  ```

---
  
  ## Tips
  
  | Habit | Why it matters |
  |---|---|
  | Commit early and often | Small commits are easier to understand and revert |
  | Write commit messages in the imperative ("Add", "Fix", "Remove") | Consistent, readable history |
  | Never commit credentials or raw sensitive data | Use `.gitignore` and `.Renviron` |
  | Use `renv::snapshot()` after adding packages | Locks dependency versions for collaborators |
  | Tag releases (`git tag v1.0`) | Anchors the code to a manuscript submission |
  
  ---
  
  *For more, see Jenny Bryan's [Happy Git with R](https://happygitwithr.com) —
the definitive guide for this workflow.*