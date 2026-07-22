# 🌱 Database Seed Guide - Agent Sales Activity Manager (ASAM)

## Overview

This guide documents the database seed implementation for the Agent Sales Activity Manager (ASAM) application. The seed data system provides environment-specific data while maintaining consistency across all environments.

**🔄 UPDATED**: As of December 2024, seed data includes comprehensive **Data Integrity Service** integration to ensure real-time data quality and consistency across all operations.

## Single Source of Truth

**IMPORTANT:** There is ONE seed file (`prisma/seed-enhanced.ts`) that handles all environments. This ensures consistency and prevents confusion.

**🆕 DATA INTEGRITY ENFORCEMENT**: The seed file now incorporates `DataIntegrityService` for:
- **Real-time data validation** during seeding
- **Automatic data consistency checks**
- **Comprehensive current week WeeklyActivity generation**
- **No duplicate user prevention**
- **Hierarchical data relationship validation**

## Environment-Specific Data

### 1. **asam_d (Development)** and **asam_t (Test)** Databases

- ✅ **Comprehensive organizational hierarchy** with data integrity enforcement:
  - 1 Admin (Angela Holloway) - System Administrator
  - 1 Manager (Jay Cee) - Office Manager reporting to Admin
  - 1 Sales Lead (Brittany Dollar) with 2 Sales Agents (Alianna, Sarah)
  - 1 Service Lead (Nickcol Scott) with 3 Service Agents (Austin, Emily, James)
- ✅ **Comprehensive Activity data** (calls, meetings, emails, follow-ups) from January 2, 2025 to June 5, 2025
- ✅ **Current Week WeeklyActivity records** for ALL agents (preventing dashboard/daily activity inconsistencies)
- ✅ **Realistic performance patterns** (8-15 activities/day for sales, 5-10 for service)
- ✅ **Enhanced Data Features**:
  - Tasks with realistic priorities and categories
  - Sales metrics with proper trending
  - Performance targets aligned with role expectations
  - Weekly forecasts with variance modeling
  - Complete client database (819 clients)
- ✅ **Data Integrity Validation**:
  - No duplicate users (cleaned during seeding)
  - Consistent userId references across all tables
  - Current week data for dashboard consistency
  - Proper role hierarchy enforcement

### 2. **asam (Production)** Database

- ✅ Only essential data:
  - Default admin user only (Angela Holloway)
  - No additional users or activity data
  - Clean slate for production use

## File Structure

```
prisma/
└── seed-enhanced.ts        # THE ONLY seed file with DataIntegrityService integration

src/lib/services/
└── dataIntegrityService.ts # Data integrity and quality enforcement service

scripts/
└── db-seed-manager.sh     # Database seed management tool
```

## Quick Start Commands

### Using npm scripts:

```bash
# Seed the database (environment auto-detected with data integrity)
npm run db:seed

# Load seed data to development
npm run db:load:dev

# Load seed data to test
npm run db:load:test

# Load seed data to production (admin only)
npm run db:load:prod

# Check database status
npm run db:status

# Reload data (clear then load with data integrity checks)
npm run db:reload

# Clear all data
npm run db:unload
```

### Using the seed manager directly:

```bash
# Show help and all options
./scripts/db-seed-manager.sh help

# Load data to specific environment (with data integrity enforcement)
./scripts/db-seed-manager.sh load development
./scripts/db-seed-manager.sh load test
./scripts/db-seed-manager.sh load production

# Check status of any database
./scripts/db-seed-manager.sh status development
./scripts/db-seed-manager.sh status test
./scripts/db-seed-manager.sh status production

# Clear data (with confirmation)
./scripts/db-seed-manager.sh unload development
```

## User Hierarchy (Development/Test) - **DATA INTEGRITY VERIFIED**

```
Angela Holloway (angela) - ADMIN (Top Level)
└── Jay Cee (jay) - OFFICE_MANAGER
    ├── Brittany Dollar (brittany) - SALES_LEAD
    │   ├── Alianna Harrilal (alianna) - SALES Agent
    │   └── Sarah Williams (sales_agent2) - SALES Agent
    └── Nickcol Scott (nickcol) - SERVICE_LEAD
        ├── Austin Holloway (austin) - SERVICE Agent
        ├── Emily Rodriguez (service_agent1) - SERVICE Agent
        └── James Taylor (service_agent2) - SERVICE Agent
```

**🔒 DATA INTEGRITY GUARANTEES:**
- ✅ No duplicate users (automatic detection and cleanup)
- ✅ Consistent manager-report relationships
- ✅ All agents have current week WeeklyActivity data
- ✅ Dashboard and daily activity page show consistent metrics
- ✅ Proper role-based data distribution

## Default Credentials

### All Environments:

| Role  | Username | Password  | Full Name       | Email Domain           |
| ----- | -------- | --------- | --------------- | ---------------------- |
| Admin | angela   | adminpass | Angela Holloway | @angelaholloway.com    |

### Development/Test Only:

| Role           | Username       | Password | Full Name        | Email Domain           |
| -------------- | -------------- | -------- | ---------------- | ---------------------- |
| Office Manager | jay            | agent123 | Jay Cee          | @angelaholloway.com    |
| Sales Lead     | brittany       | agent123 | Brittany Dollar  | @angelaholloway.com    |
| Service Lead   | nickcol        | agent123 | Nickcol Scott    | @angelaholloway.com    |
| Sales Agent    | alianna        | agent123 | Alianna Harrilal | @angelaholloway.com    |
| Sales Agent    | sales_agent2   | agent123 | Sarah Williams   | @angelaholloway.com    |
| Service Agent  | austin         | agent123 | Austin Holloway  | @angelaholloway.com    |
| Service Agent  | service_agent1 | agent123 | Emily Rodriguez  | @angelaholloway.com    |
| Service Agent  | service_agent2 | agent123 | James Taylor     | @angelaholloway.com    |

## Activity Data (Dev/Test Only) - **ENHANCED WITH DATA INTEGRITY**

- **Date Range**: January 2, 2025 to June 5, 2025
- **Activity Types**: calls, meetings, emails, follow-ups
- **Insurance Products**: Auto, Home, Life, Health, Business, Umbrella, Renters, Disability
- **Client Base**: 819 clients with realistic names and proper data relationships
- **Volume**: 28,603+ activities distributed equitably across agents
- **Weekly Activities**: 207 records ensuring dashboard/daily activity consistency
- **Weekly Forecasts**: 161 records with realistic variance modeling
- **Data Quality**: Real-time validation and integrity enforcement

## Client Examples (Dev/Test Only)

**Individual Clients:**
- Robert Johnson, Patricia Williams, Michael Brown, Jennifer Davis
- David Miller, Elizabeth Wilson, Christopher Moore, Barbara Taylor

**Business Clients:**
- Anderson Construction LLC, Premier Health Services
- TechVision Solutions, Green Valley Farms
- Riverside Manufacturing, Summit Financial Group

## Environment Variables

The seed script (`prisma/seed-enhanced.ts`) detects the current environment based on `DATABASE_NAME` and `NODE_ENV` environment variables with **enhanced data integrity validation**.

The `db-seed-manager.sh` script sets these variables appropriately:

- **Development**: `DATABASE_NAME=asam_d`, `NODE_ENV=development`
- **Test**: `DATABASE_NAME=asam_t`, `NODE_ENV=test`  
- **Production**: `DATABASE_NAME=asam`, `NODE_ENV=production`

## Safety Features - **ENHANCED & POST-INCIDENT**

1. **🚨 MANDATORY BACKUP PROTOCOL**: ALL destructive operations require explicit user permission and backup creation
2. **Production Protection**: Minimal data only (admin user)
3. **Environment Detection**: Automatic application of appropriate seed data
4. **Idempotent Operations**: Uses upsert operations to prevent duplicate data
5. **Clear Confirmation**: Requires explicit confirmation before destructive operations
6. **🆕 Data Integrity Enforcement**:
   - Real-time duplicate detection and prevention
   - Comprehensive data relationship validation
   - Current week data generation for all agents
   - Automatic data consistency checks
7. **🆕 DataIntegrityService Integration**:
   - Validates all user creation operations
   - Ensures WeeklyActivity data consistency
   - Prevents data integrity violations during seeding

### 🚨 CRITICAL DATABASE SAFETY REQUIREMENTS

**Following a data loss incident, these protocols are now MANDATORY:**

#### Before ANY Destructive Database Operation:
1. **EXPLICIT USER PERMISSION** - Never execute without clear approval
2. **BACKUP CREATION** - Export current data state before changes
3. **IMPACT ASSESSMENT** - Clearly explain what data will be affected
4. **RECOVERY PLAN** - Provide tested restoration procedures

#### Prohibited Commands Without Permission:
- `npx prisma migrate reset`
- `npx prisma migrate reset --force`
- `npx prisma db push --force-reset`
- `npm run db:unload`
- `./scripts/db-seed-manager.sh unload`

#### Required Communication Format:
```
⚠️  DATABASE OPERATION REQUIRED ⚠️

Operation: [specific command]
Risk Level: HIGH
Data at Risk: ALL user accounts, activities, and configurations
Backup Required: YES
Recovery Time: 10-30 minutes

This operation will delete all existing data.
I will create a backup first and verify recovery procedures.

Do you give explicit permission to proceed? (YES/NO)
```

## Data Quality Standards

### **MANDATORY VERIFICATION CHECKLIST:**

Before any database operation, verify:

- [ ] ✅ All agents have WeeklyActivity records for current week
- [ ] ✅ Dashboard and daily activity pages show consistent metrics
- [ ] ✅ No duplicate users exist
- [ ] ✅ All manager-report relationships are valid
- [ ] ✅ Activity data is equitably distributed among agents
- [ ] ✅ All data relationships maintain referential integrity

### **POST-SEEDING VALIDATION:**

After seeding, automatically verify:

1. **User Count Validation**: 8 users total (1 admin + 7 agents/managers)
2. **Activity Distribution**: All agents have realistic activity volumes
3. **Current Week Data**: All agents have WeeklyActivity for current week
4. **Hierarchy Validation**: All reporting relationships are correct
5. **Data Consistency**: Dashboard and daily activity metrics align

## Troubleshooting

### Connection Issues

```bash
# Check database connection with data integrity validation
./scripts/db-seed-manager.sh status
```

### Data Integrity Issues

```bash
# Run comprehensive data integrity check
npm run db:reload    # Clears and reloads with full validation
```

### Missing Dependencies

```bash
# Install dependencies including DataIntegrityService
npm install

# Generate Prisma client
npx prisma generate
```

## Best Practices - **UPDATED**

1. **Development Workflow**:
   - Use `npm run db:reload` for clean start with data integrity checks
   - Verify dashboard consistency after any data changes
   - Always check both dashboard and daily activity pages for consistency

2. **Testing**:
   - Always reload test database before integration tests
   - Use consistent seed data for reliable test results
   - Validate data integrity in test scenarios

3. **Production**:
   - Only admin user is seeded
   - Never run full seed in production
   - Always backup before any data operations
   - Use DataIntegrityService for all user creation

4. **🆕 Data Integrity**:
   - All new users created through DataIntegrityService
   - Validate WeeklyActivity generation for new agents
   - Monitor dashboard/daily activity consistency
   - Use data integrity validation in all environments

## Recent Updates (December 2024)

### **✅ Data Integrity Service Integration**
- Comprehensive real-time data validation
- Automatic duplicate prevention
- Current week data generation
- Dashboard consistency enforcement

### **✅ Enhanced Seed Data Quality**
- Fixed user hierarchy inconsistencies
- Added current week WeeklyActivity for all agents
- Implemented comprehensive data relationship validation
- Enhanced client and activity data realism

### **✅ Improved Documentation**
- Updated with data integrity standards
- Added validation checklists
- Enhanced troubleshooting guides
- Documented DataIntegrityService integration

## Important Notes

- There is **ONE** seed file (`prisma/seed-enhanced.ts`) with DataIntegrityService integration
- The seed script automatically detects environment and enforces data integrity
- **Production gets minimal data** (admin only) while dev/test get full validated data
- All passwords in dev/test are `agent123` except admin which is `adminpass`
- **🔒 Data integrity is enforced** at every level of the seeding process
- **Current week WeeklyActivity data** is automatically generated for all agents
- Dashboard and daily activity consistency is **automatically validated**
