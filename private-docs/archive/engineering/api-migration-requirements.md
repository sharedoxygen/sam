# API MIGRATION REQUIREMENTS - SCHEMA TRANSITION

## **🚨 CRITICAL API UPDATES NEEDED**

### **1. Tasks API (`src/app/api/tasks/route.ts`)**
**Issues:**
- Remove: `clientName`, `clientEmail`, `clientPhone` fields (deprecated)
- Add: `clientId` foreign key relationship to Client table
- Fix: `priority` field to use `Priority` enum (HIGH/MEDIUM/LOW)
- Add: `status` field using `TaskStatus` enum (PENDING/IN_PROGRESS/COMPLETED/CANCELLED)  
- Fix: `estimatedValue` as Decimal type instead of string

**Required Changes:**
```typescript
// Replace client string fields with proper relationship
clientId: data.clientId || null,
// Use enum values
priority: data.priority || 'MEDIUM', 
status: data.status || 'PENDING',
// Handle decimal properly
estimatedValue: data.estimatedValue ? new Decimal(data.estimatedValue) : null,
```

### **2. Activities API (`src/app/api/activities/route.ts`)**
**Issues:**
- `type` field now uses `ActivityType` enum (CALL/MEETING/EMAIL/FOLLOW_UP/SALE/QUOTE)
- Remove: `clientName` field (deprecated)
- Add: `clientId` foreign key relationship
- Add: `value`, `insuranceType`, `status` fields

### **3. Team APIs**
**Files:** `src/app/api/team/activities/route.ts`, `src/app/api/team/performance/route.ts`
**Issues:**
- Activity type comparisons using lowercase strings instead of enum values
- References to deprecated `clientName` field

### **4. Reports API (`src/app/api/reports/activity/route.ts`)**
**Issues:**
- Duration field now nullable, needs null checks

### **5. Scripts and Seeds**
**Files:** `prisma/seed-enhanced.ts`, `scripts/export-seed-data.ts`
**Issues:**
- All seed data using old schema structure
- Client relationships not properly established
- Enum values in lowercase instead of uppercase

## **🔧 MIGRATION STRATEGY**

1. **Phase 1:** Update enum references (ActivityType, Priority, TaskStatus)
2. **Phase 2:** Replace client string fields with foreign key relationships  
3. **Phase 3:** Add missing fields (value, insuranceType, status)
4. **Phase 4:** Update all seed data and scripts
5. **Phase 5:** Comprehensive testing

## **📋 COMPLETION STATUS**
- ❌ Tasks API (partially started)
- ❌ Activities API  
- ❌ Team APIs
- ❌ Reports API
- ❌ Seed scripts
- ❌ Export scripts 