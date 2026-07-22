# 🚨 CRITICAL DATABASE MIGRATION PLAN
## Agent Sales Activity Manager (ASAM) - Referential Integrity Fix

### **PHASE 1A: IMMEDIATE DATA BACKUP**
```bash
# Create complete database backup before any changes
pg_dump $DATABASE_URL > backup_pre_migration_$(date +%Y%m%d_%H%M%S).sql
```

### **PHASE 1B: DATABASE SCHEMA MIGRATION SEQUENCE**

#### **Migration 1: Add Missing Tables and Enums**
```sql
-- Add new enums
CREATE TYPE "ActivityType" AS ENUM ('CALL', 'MEETING', 'EMAIL', 'FOLLOW_UP', 'SALE', 'QUOTE');
CREATE TYPE "InsuranceType" AS ENUM ('AUTO', 'LIFE_HEALTH', 'PROPERTY_FIRE');
CREATE TYPE "TaskStatus" AS ENUM ('PENDING', 'IN_PROGRESS', 'COMPLETED', 'CANCELLED');
CREATE TYPE "Priority" AS ENUM ('LOW', 'MEDIUM', 'HIGH');

-- Add PerformanceTarget table (completely missing)
CREATE TABLE "PerformanceTarget" (
    "id" SERIAL NOT NULL,
    "userId" INTEGER NOT NULL,
    "weekStartDate" TIMESTAMP(3) NOT NULL,
    "peopleContacted" INTEGER NOT NULL DEFAULT 0,
    "lifeSalesConversations" INTEGER NOT NULL DEFAULT 0,
    "sales" INTEGER NOT NULL DEFAULT 0,
    "referralRequests" INTEGER NOT NULL DEFAULT 0,
    "premiumAmount" DECIMAL(12,2) NOT NULL DEFAULT 0,
    "createdAt" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "updatedAt" TIMESTAMP(3) NOT NULL,
    "setById" INTEGER,
    CONSTRAINT "PerformanceTarget_pkey" PRIMARY KEY ("id")
);

-- Add ClientAssignment table (for proper client-user relationships)
CREATE TABLE "ClientAssignment" (
    "id" SERIAL NOT NULL,
    "userId" INTEGER NOT NULL,
    "clientId" INTEGER NOT NULL,
    "assignedAt" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "isActive" BOOLEAN NOT NULL DEFAULT true,
    CONSTRAINT "ClientAssignment_pkey" PRIMARY KEY ("id")
);
```

#### **Migration 2: Fix Existing Tables**
```sql
-- Add missing fields to User table
ALTER TABLE "User" ADD COLUMN "isActive" BOOLEAN NOT NULL DEFAULT true;

-- Add missing fields to Client table
ALTER TABLE "Client" ADD COLUMN "isActive" BOOLEAN NOT NULL DEFAULT true;

-- Add missing fields to Activity table
ALTER TABLE "Activity" ADD COLUMN "clientId" INTEGER;
ALTER TABLE "Activity" ADD COLUMN "value" DOUBLE PRECISION;
ALTER TABLE "Activity" ADD COLUMN "insuranceType" "InsuranceType";
ALTER TABLE "Activity" ADD COLUMN "status" TEXT;

-- Fix Task table - replace string fields with proper relationships
ALTER TABLE "Task" ADD COLUMN "clientId" INTEGER;
ALTER TABLE "Task" ADD COLUMN "status" "TaskStatus" NOT NULL DEFAULT 'PENDING';
ALTER TABLE "Task" ALTER COLUMN "estimatedValue" TYPE DECIMAL(12,2);

-- Fix SalesMetric table - make userId required
UPDATE "SalesMetric" SET "userId" = 1 WHERE "userId" IS NULL; -- Assign to admin temporarily
ALTER TABLE "SalesMetric" ALTER COLUMN "userId" SET NOT NULL;
ALTER TABLE "SalesMetric" ALTER COLUMN "revenue" TYPE DECIMAL(12,2);
```

#### **Migration 3: Add Foreign Key Constraints**
```sql
-- Activity table foreign keys
ALTER TABLE "Activity" ADD CONSTRAINT "Activity_userId_fkey" 
    FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE CASCADE ON UPDATE CASCADE;
ALTER TABLE "Activity" ADD CONSTRAINT "Activity_clientId_fkey" 
    FOREIGN KEY ("clientId") REFERENCES "Client"("id") ON DELETE SET NULL ON UPDATE CASCADE;

-- Task table foreign keys
ALTER TABLE "Task" ADD CONSTRAINT "Task_userId_fkey" 
    FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE CASCADE ON UPDATE CASCADE;
ALTER TABLE "Task" ADD CONSTRAINT "Task_clientId_fkey" 
    FOREIGN KEY ("clientId") REFERENCES "Client"("id") ON DELETE SET NULL ON UPDATE CASCADE;

-- PerformanceTarget foreign keys
ALTER TABLE "PerformanceTarget" ADD CONSTRAINT "PerformanceTarget_userId_fkey" 
    FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE CASCADE ON UPDATE CASCADE;
ALTER TABLE "PerformanceTarget" ADD CONSTRAINT "PerformanceTarget_setById_fkey" 
    FOREIGN KEY ("setById") REFERENCES "User"("id") ON DELETE SET NULL ON UPDATE CASCADE;

-- ClientAssignment foreign keys
ALTER TABLE "ClientAssignment" ADD CONSTRAINT "ClientAssignment_userId_fkey" 
    FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE CASCADE ON UPDATE CASCADE;
ALTER TABLE "ClientAssignment" ADD CONSTRAINT "ClientAssignment_clientId_fkey" 
    FOREIGN KEY ("clientId") REFERENCES "Client"("id") ON DELETE CASCADE ON UPDATE CASCADE;

-- Fix existing foreign keys to add cascade behaviors
ALTER TABLE "WeeklyActivity" DROP CONSTRAINT "WeeklyActivity_userId_fkey";
ALTER TABLE "WeeklyActivity" ADD CONSTRAINT "WeeklyActivity_userId_fkey" 
    FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE "WeeklyForecast" DROP CONSTRAINT "WeeklyForecast_userId_fkey";
ALTER TABLE "WeeklyForecast" ADD CONSTRAINT "WeeklyForecast_userId_fkey" 
    FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE "SalesMetric" DROP CONSTRAINT "SalesMetric_userId_fkey";
ALTER TABLE "SalesMetric" ADD CONSTRAINT "SalesMetric_userId_fkey" 
    FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE CASCADE ON UPDATE CASCADE;
```

#### **Migration 4: Add Indexes for Performance**
```sql
-- User indexes
CREATE INDEX "User_role_idx" ON "User"("role");
CREATE INDEX "User_managerId_idx" ON "User"("managerId");
CREATE INDEX "User_isActive_idx" ON "User"("isActive");

-- Client indexes
CREATE INDEX "Client_name_idx" ON "Client"("name");
CREATE INDEX "Client_email_idx" ON "Client"("email");
CREATE INDEX "Client_isActive_idx" ON "Client"("isActive");

-- Activity indexes
CREATE INDEX "Activity_userId_date_idx" ON "Activity"("userId", "date");
CREATE INDEX "Activity_type_idx" ON "Activity"("type");
CREATE INDEX "Activity_clientId_idx" ON "Activity"("clientId");

-- Task indexes
CREATE INDEX "Task_userId_dueDate_idx" ON "Task"("userId", "dueDate");
CREATE INDEX "Task_status_idx" ON "Task"("status");
CREATE INDEX "Task_clientId_idx" ON "Task"("clientId");
CREATE INDEX "Task_priority_idx" ON "Task"("priority");

-- Performance Target indexes
CREATE INDEX "PerformanceTarget_userId_weekStartDate_idx" ON "PerformanceTarget"("userId", "weekStartDate");
CREATE INDEX "PerformanceTarget_setById_idx" ON "PerformanceTarget"("setById");

-- Client Assignment indexes
CREATE INDEX "ClientAssignment_userId_idx" ON "ClientAssignment"("userId");
CREATE INDEX "ClientAssignment_clientId_idx" ON "ClientAssignment"("clientId");

-- Sales Metric indexes
CREATE INDEX "SalesMetric_userId_date_idx" ON "SalesMetric"("userId", "date");

-- Weekly Activity indexes
CREATE INDEX "WeeklyActivity_userId_weekStartDate_idx" ON "WeeklyActivity"("userId", "weekStartDate");

-- Weekly Forecast indexes
CREATE INDEX "WeeklyForecast_userId_weekStartDate_idx" ON "WeeklyForecast"("userId", "weekStartDate");
```

#### **Migration 5: Add Unique Constraints**
```sql
-- Performance Target unique constraint
ALTER TABLE "PerformanceTarget" ADD CONSTRAINT "PerformanceTarget_userId_weekStartDate_key" 
    UNIQUE ("userId", "weekStartDate");

-- Client Assignment unique constraint
ALTER TABLE "ClientAssignment" ADD CONSTRAINT "ClientAssignment_userId_clientId_key" 
    UNIQUE ("userId", "clientId");

-- Sales Metric unique constraint
ALTER TABLE "SalesMetric" ADD CONSTRAINT "SalesMetric_userId_weekNumber_year_key" 
    UNIQUE ("userId", "weekNumber", "year");
```

### **PHASE 1C: DATA MIGRATION & CLEANUP**

#### **Step 1: Migrate Client Data from Tasks**
```sql
-- Extract unique clients from Task table and create Client records
INSERT INTO "Client" ("name", "email", "phone", "createdAt", "updatedAt")
SELECT DISTINCT 
    "clientName" as "name",
    "clientEmail" as "email", 
    "clientPhone" as "phone",
    CURRENT_TIMESTAMP as "createdAt",
    CURRENT_TIMESTAMP as "updatedAt"
FROM "Task" 
WHERE "clientName" IS NOT NULL 
AND "clientName" != ''
AND NOT EXISTS (SELECT 1 FROM "Client" WHERE "name" = "Task"."clientName");

-- Update Task records to reference Client table
UPDATE "Task" SET "clientId" = (
    SELECT "Client"."id" 
    FROM "Client" 
    WHERE "Client"."name" = "Task"."clientName"
) WHERE "clientName" IS NOT NULL AND "clientName" != '';

-- Create ClientAssignment records for task owners
INSERT INTO "ClientAssignment" ("userId", "clientId", "assignedAt", "isActive")
SELECT DISTINCT "Task"."userId", "Task"."clientId", CURRENT_TIMESTAMP, true
FROM "Task" 
WHERE "Task"."clientId" IS NOT NULL
AND NOT EXISTS (
    SELECT 1 FROM "ClientAssignment" 
    WHERE "ClientAssignment"."userId" = "Task"."userId" 
    AND "ClientAssignment"."clientId" = "Task"."clientId"
);
```

#### **Step 2: Clean Up Legacy String Fields**
```sql
-- Remove redundant string fields from Task table (after migration verification)
ALTER TABLE "Task" DROP COLUMN "clientName";
ALTER TABLE "Task" DROP COLUMN "clientEmail";  
ALTER TABLE "Task" DROP COLUMN "clientPhone";
```

### **PHASE 1D: API UPDATE REQUIREMENTS**

The following APIs need updates to work with the new schema:

1. **Tasks API** - Update to use clientId instead of string fields
2. **Activities API** - Add support for new fields (value, insuranceType, status)
3. **Weekly Forecast API** - Add premiumAmount support
4. **Performance Targets API** - Create new API for PerformanceTarget table
5. **Clients API** - Add ClientAssignment management
6. **Team Performance API** - Update aggregation logic for new schema

### **PHASE 1E: VERIFICATION STEPS**

1. **Data Integrity Check**
   ```sql
   -- Verify no orphaned records
   SELECT COUNT(*) FROM "Activity" WHERE "userId" NOT IN (SELECT "id" FROM "User");
   SELECT COUNT(*) FROM "Task" WHERE "userId" NOT IN (SELECT "id" FROM "User");
   SELECT COUNT(*) FROM "SalesMetric" WHERE "userId" NOT IN (SELECT "id" FROM "User");
   ```

2. **Relationship Verification**
   ```sql
   -- Verify foreign key constraints work
   SELECT constraint_name, table_name FROM information_schema.table_constraints 
   WHERE constraint_type = 'FOREIGN KEY' AND table_schema = 'public';
   ```

3. **Performance Verification**
   ```sql
   -- Verify indexes exist
   SELECT indexname, tablename FROM pg_indexes WHERE schemaname = 'public';
   ```

### **🔒 ROLLBACK PLAN**

If migration fails at any step:
```bash
# Restore from backup
psql $DATABASE_URL < backup_pre_migration_YYYYMMDD_HHMMSS.sql
```

### **✅ SUCCESS CRITERIA**

- [ ] All foreign key constraints properly implemented with cascade behaviors
- [ ] No orphaned records in any table
- [ ] Client data properly normalized and linked
- [ ] Performance Target functionality fully database-backed
- [ ] All APIs updated to work with new schema
- [ ] Full test suite passes
- [ ] Performance benchmarks meet requirements 