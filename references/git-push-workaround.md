# Git Push Workaround

## Problem

GitHub HTTPS and SSH pushes timeout intermittently due to firewall restrictions.

## Symptoms

- `ssh -T git@github.com` succeeds (authentication works)
- `git push origin main` times out (SSH or HTTPS blocked)
- GitLab HTTPS works fine

## Workaround Sequence

### 1. Try GitLab HTTPS first

```bash
git remote set-url origin https://github.com/USER/REPO.git
git push origin main
```

### 2. If GitLab works, push to a feature branch

```bash
git checkout -b vX.Y.Z-changelog
git push gitlab vX.Y.Z-changelog
# Then merge via GitLab web UI
```

### 3. GitHub Main Branch Protected

If main is protected and force-push is rejected:

```bash
git checkout -b release-vX.Y.Z
git push origin release-vX.Y.Z
# Create PR via GitHub web UI
```

## Quick Reference

| Remote | HTTPS | SSH | Works? |
|--------|-------|-----|--------|
| GitHub | ✅ | ✅ (auth OK) | ❌ Push timeout |
| GitLab | ✅ | ✅ | ✅ |

## Verification

```bash
# Test GitHub auth (not push)
ssh -T git@github.com

# Test GitLab auth
ssh -T git@gitlab.com

# Check remote URLs
git remote -v
```
