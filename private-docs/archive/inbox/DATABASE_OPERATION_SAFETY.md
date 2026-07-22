# Database Operation Safety Protocols

## 🚨 CRITICAL: Data Protection First

**This document was created in response to a catastrophic data loss incident. ALL database operations must follow these protocols without exception.**

---

## ⛔ NEVER RUN WITHOUT EXPLICIT USER PERMISSION

The following commands **MUST NEVER** be executed by AI assistants or developers without explicit user approval AND proper backup procedures:

### 🔴 DESTRUCTIVE COMMANDS - ABSOLUTE PROHIBITION
```bash
# NEVER RUN THESE WITHOUT USER PERMISSION AND BACKUP:
npx prisma migrate reset
npx prisma migrate reset --force
npx prisma db push --force-reset
DROP DATABASE
TRUNCATE TABLE
DELETE FROM [table] WHERE [condition affects multiple records]
```

### 🟡 CAUTION COMMANDS - REQUIRE PERMISSION
```bash
# ALWAYS ASK PERMISSION FIRST:
npx prisma migrate dev
npx prisma db push
npm run db:unload
npm run db:reload
./scripts/db-seed-manager.sh unload
```

---

## 📋 MANDATORY PRE-OPERATION CHECKLIST

Before ANY database operation that could cause data loss:

### ✅ STEP 1: USER CONSENT
- [ ] **Explicitly ask user for permission**
- [ ] **Warn about potential data loss**
- [ ] **Explain exactly what will happen**
- [ ] **Wait for clear "YES" confirmation**

### ✅ STEP 2: BACKUP CREATION
- [ ] **Create database backup**
- [ ] **Export current seed data state**
- [ ] **Verify backup integrity**
- [ ] **Document backup location**

### ✅ STEP 3: IMPACT ASSESSMENT
- [ ] **Identify what data will be affected**
- [ ] **List all tables that will be modified/deleted**
- [ ] **Estimate recovery time if needed**
- [ ] **Confirm user understands the scope**

### ✅ STEP 4: RECOVERY PLAN
- [ ] **Document rollback procedure**
- [ ] **Prepare restoration commands**
- [ ] **Test recovery process**
- [ ] **Have restoration data ready**

---

## 🛡️ SAFE DATABASE OPERATION PROCEDURES

### For Schema Changes:
1. **Create Migration Branch**: Never modify main/production schemas directly
2. **Local Testing**: Test all migrations on local development database first  
3. **Backup First**: Always backup before running migrations
4. **Incremental Changes**: Prefer additive migrations over destructive ones
5. **Rollback Plan**: Have a tested rollback strategy

### For Data Operations:
1. **Read-Only First**: Use SELECT statements to verify data before modifications
2. **Transaction Wrapping**: Wrap operations in transactions for rollback capability
3. **Small Batches**: Process data in small, manageable batches
4. **Validation**: Verify data integrity after each operation
5. **Documentation**: Log all changes with before/after states

---

## 💾 BACKUP PROCEDURES

### Automatic Backup Creation:
```bash
# Create timestamped database backup
./scripts/create-database-backup.sh

# Export current seed data state  
npx ts-node scripts/export-current-seed-data.ts
```

### Manual Backup Verification:
```bash
# Verify backup file exists and is valid
ls -la backups/
./scripts/verify-backup.sh [backup-file]
```

### Backup Retention:
- **Keep 7 daily backups**
- **Keep 4 weekly backups** 
- **Keep 12 monthly backups**
- **Archive major release backups permanently**

---

## 🔄 RECOVERY PROCEDURES

### Immediate Recovery (if data loss detected):
1. **STOP ALL OPERATIONS** immediately
2. **Identify most recent valid backup**
3. **Restore from backup following tested procedure**
4. **Verify data integrity post-recovery**
5. **Document incident and lessons learned**

### Recovery Commands:
```bash
# Restore from backup
./scripts/restore-database-backup.sh [backup-file]

# Restore from exported seed data
npm run db:restore:[environment]
```

---

## 🚫 AI ASSISTANT RESTRICTIONS

### ABSOLUTE PROHIBITIONS:
- ❌ **NEVER run destructive database commands without permission**
- ❌ **NEVER assume data loss is acceptable**
- ❌ **NEVER skip backup procedures "to save time"**
- ❌ **NEVER force migrations without user approval**
- ❌ **NEVER reset databases without explicit consent**

### MANDATORY ACTIONS:
- ✅ **ALWAYS ask permission for any database operation**
- ✅ **ALWAYS explain potential data loss risks**
- ✅ **ALWAYS create backups before changes**
- ✅ **ALWAYS provide recovery options**
- ✅ **ALWAYS document what was done**

### REQUIRED COMMUNICATION:
When proposing any database operation:

```
⚠️  DATABASE OPERATION REQUIRED ⚠️

Operation: [specific command/operation]
Risk Level: [HIGH/MEDIUM/LOW]
Data at Risk: [list affected data]
Backup Required: YES
Recovery Time: [estimated time]

This operation could cause data loss. 
I will create a backup first and verify recovery procedures.

Do you give explicit permission to proceed? (YES/NO)
```

---

## 📊 MONITORING & VALIDATION

### Post-Operation Checks:
- [ ] **Data integrity validation**
- [ ] **Record count verification**
- [ ] **Relationship consistency check**
- [ ] **Application functionality test**
- [ ] **Dashboard consistency verification**

### Continuous Monitoring:
- [ ] **Database health checks**
- [ ] **Backup verification**
- [ ] **Storage capacity monitoring**
- [ ] **Performance baseline tracking**

---

## 🔍 INCIDENT RESPONSE

### If Data Loss Occurs:
1. **Immediate**: Stop all operations, assess scope
2. **Notify**: Inform affected users immediately
3. **Restore**: Use most recent valid backup
4. **Verify**: Confirm full restoration
5. **Document**: Record incident details and root cause
6. **Improve**: Update procedures to prevent recurrence

### Incident Documentation:
- **Timestamp of incident**
- **Commands executed that caused loss**
- **Data affected and scope**
- **Recovery actions taken**
- **Time to full restoration**
- **Lessons learned and procedure updates**

---

## 📈 CONTINUOUS IMPROVEMENT

### Regular Reviews:
- **Monthly backup procedure validation**
- **Quarterly recovery testing**
- **Annual safety protocol updates**
- **Post-incident procedure improvements**

### Training Requirements:
- **All developers must understand these procedures**
- **AI assistants must comply with all restrictions**
- **Regular safety protocol training for team**
- **Emergency response drill execution**

---

## 🎯 SUCCESS METRICS

### Database Safety KPIs:
- **Zero unauthorized data loss incidents**
- **100% backup success rate**
- **Recovery time < 30 minutes**
- **100% compliance with permission protocols**

---

**Last Updated**: December 2024 (Post-Incident)  
**Next Review**: March 2025  
**Incident Level**: CRITICAL - Created after data loss event  
**Approval Required**: ALL database operations affecting data

---

*This document was created in response to an AI assistant executing `npx prisma migrate reset --force` without user permission, resulting in complete data loss. These procedures must be followed to prevent future incidents.* 