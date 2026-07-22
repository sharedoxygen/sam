# 🚨 CREDENTIAL EXPOSURE REMEDIATION GUIDE

**CRITICAL SECURITY INCIDENT**  
**Date:** October 14, 2025  
**Status:** IMMEDIATE ACTION REQUIRED

---

## 📋 QUICK ACTION CHECKLIST

This is your step-by-step remediation plan. Follow in order:

- [ ] **Step 1:** Review the security audit report
- [ ] **Step 2:** Rotate all exposed credentials (DO THIS FIRST!)
- [ ] **Step 3:** Sanitize Git history
- [ ] **Step 4:** Force push cleaned repository
- [ ] **Step 5:** Notify team and coordinate re-cloning
- [ ] **Step 6:** Update CI/CD systems
- [ ] **Step 7:** Verify and test
- [ ] **Step 8:** Implement prevention measures

---

## 🔥 STEP 1: REVIEW SECURITY AUDIT

Read the full security audit report:
```bash
cat SECURITY_AUDIT_REPORT.md
```

**Key findings:**
- ✅ PostgreSQL credentials exposed in Git history since May 18, 2025
- ✅ Hardcoded credentials in 5 shell scripts
- ✅ NextAuth secrets exposed
- ✅ RDS endpoint exposed

---

## 🔑 STEP 2: ROTATE CREDENTIALS (DO FIRST!)

**WHY FIRST?** Anyone with access to Git history can use the old credentials. Changing them immediately stops any potential breach.

### 2.1 Generate New Credentials

Run the credential rotation script:
```bash
./scripts/rotate-credentials.sh
```

This will generate:
- New `NEXTAUTH_SECRET`
- New NextAuth preview keys
- New database password

**Save the output** - you'll need these values!

### 2.2 Change PostgreSQL Password

#### Option A: Local PostgreSQL
```bash
psql -U postgres -c "ALTER USER postgres WITH PASSWORD 'NEW_PASSWORD_HERE';"
```

#### Option B: AWS RDS
1. Go to AWS RDS Console
2. Select your database instance
3. Click "Modify"
4. Set new master password
5. Apply immediately

### 2.3 Update Local Environment Files

Update `.env` and `.env.local` with new credentials:
```bash
# Copy template
cp .env.example .env

# Edit with new credentials
nano .env  # or vim, code, etc.
```

Required updates:
```env
DATABASE_PASSWORD="<NEW_PASSWORD_FROM_SCRIPT>"
DB_PASSWORD="<NEW_PASSWORD_FROM_SCRIPT>"
NEXTAUTH_SECRET="<NEW_SECRET_FROM_SCRIPT>"

# Update all DATABASE_URL values with new password
DATABASE_URL="postgresql://postgres:<NEW_PASSWORD>@host:5432/asam_d?schema=public"
```

### 2.4 Test New Credentials

Verify database connectivity:
```bash
# Test Prisma connection
npx prisma db execute --command "SELECT 1;"

# Test application
npm run dev
```

---

## 🧹 STEP 3: SANITIZE GIT HISTORY

**WARNING:** This rewrites Git history. Coordinate with your team!

### 3.1 Prerequisites

Install `git-filter-repo`:
```bash
# macOS
brew install git-filter-repo

# Linux/Windows
pip install git-filter-repo
```

### 3.2 Backup Repository

Create a complete backup:
```bash
cp -r . ../agent-sales-activity-manager-backup-$(date +%Y%m%d)
```

### 3.3 Run Sanitization Script

```bash
./scripts/sanitize-git-history.sh
```

Follow the prompts. This will:
- Remove `.env`, `.env.backup`, `.env.local` from ALL history
- Rewrite Git history
- Clean up repository

### 3.4 Verify Cleanup

Check that .env files are gone from history:
```bash
# Should return nothing
git log --all --full-history -- .env
git log --all --full-history -- .env.backup
git log --all --full-history -- .env.local
```

---

## 🚀 STEP 4: FORCE PUSH CLEANED HISTORY

**CRITICAL:** This breaks existing clones. Coordinate with team!

### 4.1 Notify Team BEFORE Pushing

Send this message to all team members:

```
🚨 CRITICAL: Git History Rewrite in Progress

We are sanitizing the Git repository to remove exposed credentials.

REQUIRED ACTIONS:
1. Commit and push ALL your current work NOW
2. Backup any uncommitted work
3. Wait for the "all clear" message
4. Delete your local repository
5. Clone fresh from remote

Timeline: This will happen in the next 30 minutes.
```

### 4.2 Force Push

Once team is notified and ready:
```bash
# Verify current remote
git remote -v

# Force push all branches
git push --force --all origin

# Force push all tags
git push --force --tags origin
```

### 4.3 Send "All Clear" Message

```
✅ Repository sanitization complete!

NEXT STEPS:
1. Delete your local clone
2. Clone fresh: git clone <repo-url>
3. Copy your .env.example to .env
4. Contact DevOps for new credentials
5. Test your local setup

Do NOT try to pull/merge - you must re-clone!
```

---

## 👥 STEP 5: TEAM COORDINATION

### 5.1 Each Team Member Must:

1. **Delete local repository:**
   ```bash
   cd /path/to/parent/directory
   rm -rf agent-sales-activity-manager
   ```

2. **Clone fresh:**
   ```bash
   git clone <repository-url>
   cd agent-sales-activity-manager
   ```

3. **Set up environment:**
   ```bash
   cp .env.example .env
   # Get new credentials from team lead
   nano .env
   ```

4. **Install dependencies:**
   ```bash
   npm install
   npx prisma generate
   ```

5. **Verify setup:**
   ```bash
   npm run dev
   ```

---

## 🔧 STEP 6: UPDATE CI/CD SYSTEMS

Update all CI/CD platforms with new credentials:

### GitHub Actions
1. Go to repository Settings → Secrets and variables → Actions
2. Update secrets:
   - `DATABASE_URL`
   - `DATABASE_PASSWORD`
   - `NEXTAUTH_SECRET`

### Jenkins
1. Update credentials in Jenkins credential store
2. Rebuild all pipelines
3. Clear any cached builds

### AWS/Cloud Deployments
1. Update environment variables in:
   - ECS task definitions
   - Lambda functions
   - Elastic Beanstalk environments
2. Redeploy all services

---

## ✅ STEP 7: VERIFY AND TEST

### 7.1 Verify Git History is Clean

```bash
# Should show no results
git log --all --full-history -S "postgres-cbr" --source
git log --all --full-history -- .env
```

### 7.2 Verify Local Files

```bash
# Should show environment variable usage, not hardcoded passwords
grep -r "postgres-cbr" scripts/
# Should return nothing or show variable references only
```

### 7.3 Test Application

```bash
# Development
npm run dev

# Production build
npm run build
npm run start

# Database connectivity
npx prisma studio
```

### 7.4 Test All Environments

- [ ] Local development works
- [ ] Test database accessible
- [ ] Production database accessible (if applicable)
- [ ] Authentication works
- [ ] All scripts function correctly

---

## 🛡️ STEP 8: IMPLEMENT PREVENTION MEASURES

### 8.1 Install Pre-commit Hooks

```bash
# Install detect-secrets
pip install detect-secrets

# Create pre-commit hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
detect-secrets scan --baseline .secrets.baseline
if [ $? -ne 0 ]; then
    echo "⛔️ Secrets detected! Commit blocked."
    exit 1
fi
EOF

chmod +x .git/hooks/pre-commit
```

### 8.2 Set Up Secret Scanning

Add to your CI/CD pipeline:
```yaml
# .github/workflows/security.yml
name: Security Scan
on: [push, pull_request]
jobs:
  secret-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Secret Scan
        uses: trufflesecurity/trufflehog@main
```

### 8.3 Implement Secrets Management

Consider using:
- **AWS Secrets Manager** (recommended for AWS users)
- **HashiCorp Vault**
- **Azure Key Vault**
- **Google Secret Manager**

### 8.4 Update Documentation

Create/update:
- [ ] `docs/SECURITY_GUIDELINES.md`
- [ ] `.env.example` (already done ✅)
- [ ] Team onboarding documentation
- [ ] Incident response playbook

### 8.5 Regular Security Reviews

Schedule:
- [ ] **Weekly:** Review access logs
- [ ] **Monthly:** Credential rotation check
- [ ] **Quarterly:** Full security audit
- [ ] **Quarterly:** Rotate all credentials

---

## 📞 INCIDENT CONTACTS

| Role | Name | Contact |
|------|------|---------|
| Security Lead | [ASSIGN] | [EMAIL/PHONE] |
| Database Admin | [ASSIGN] | [EMAIL/PHONE] |
| DevOps Lead | [ASSIGN] | [EMAIL/PHONE] |
| Project Manager | [ASSIGN] | [EMAIL/PHONE] |

---

## 🔍 VERIFICATION CHECKLIST

After completing all steps, verify:

- [ ] All credentials have been rotated
- [ ] Git history contains no sensitive data
- [ ] All team members have re-cloned repository
- [ ] CI/CD systems updated and working
- [ ] All environments tested and functional
- [ ] Pre-commit hooks installed
- [ ] `.gitignore` updated to prevent future exposure
- [ ] Documentation updated
- [ ] Incident logged and reviewed
- [ ] Prevention measures implemented

---

## 📚 ADDITIONAL RESOURCES

### Scripts Created
- ✅ `scripts/sanitize-git-history.sh` - Remove sensitive files from history
- ✅ `scripts/rotate-credentials.sh` - Generate new secure credentials
- ✅ `.env.example` - Template for environment variables

### Documentation
- ✅ `SECURITY_AUDIT_REPORT.md` - Detailed audit findings
- ✅ `REMEDIATION_GUIDE.md` - This document

### Commands Reference

```bash
# Check for hardcoded secrets
grep -r "password\|secret\|api_key" --include="*.sh" scripts/

# Verify .env files are ignored
git check-ignore .env .env.local .env.backup

# Check current git status
git status

# View recent commits
git log --oneline -10

# Check remote configuration
git remote -v
```

---

## ⚠️ IMPORTANT NOTES

1. **Time Sensitivity:** Complete Steps 1-4 within 24 hours
2. **Communication:** Keep team informed at each step
3. **Documentation:** Document every action taken
4. **Verification:** Test thoroughly before declaring complete
5. **Learning:** Conduct post-incident review

---

## 🎯 SUCCESS CRITERIA

This incident is resolved when:
- ✅ All exposed credentials have been rotated
- ✅ Git history is clean (verified)
- ✅ All systems are functional with new credentials
- ✅ Team members have fresh clones and working setups
- ✅ Prevention measures are in place
- ✅ No evidence of unauthorized access in logs

---

## 📝 POST-INCIDENT ACTIONS

After completion:
1. **Incident Report:** Document what happened, actions taken, timeline
2. **Team Meeting:** Discuss lessons learned
3. **Process Update:** Update development guidelines
4. **Training:** Security best practices training for team
5. **Monitoring:** Enhanced monitoring for 30 days

---

**Document Version:** 1.0  
**Last Updated:** October 14, 2025  
**Next Review:** After incident completion

---

## 🆘 NEED HELP?

If you encounter issues:
1. **Stop** and document the issue
2. **Don't proceed** with remaining steps
3. **Contact** the Security Lead
4. **Backup** current state before retrying

Remember: It's better to pause and get help than to make things worse!

