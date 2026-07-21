# Troubleshooting

## Issue 1

### Problem

README.md was showing **U** inside VS Code.

### Root Cause

The file was not tracked by Git.

### Solution

```powershell
git add .
```

### Result

The file moved from the Working Directory to the Staging Area.

---

## Issue 2

### Problem

git remote -v returned no output.

### Root Cause

No remote repository was configured.

### Solution

```powershell
git remote add origin https://github.com/yuvraj1120/devops-portfolio.git
```

### Result

Successfully connected the local repository with GitHub.