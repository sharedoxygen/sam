# 🔐 Login Credentials Reference

**Last Updated:** October 14, 2025  
**Status:** ✅ VERIFIED WORKING

---

## 📋 Current Login Credentials

### Admin Account
```
Username: angela
Password: adminpass
Role: ADMIN
Name: Angela Holloway
Email: angela.holloway@asam.local
```

### Office Manager
```
Username: jay
Password: agent123
Role: OFFICE_MANAGER
Name: Jay Cee
Email: jay.cee@asam.local
Reports To: angela (Admin)
```

### Sales Lead
```
Username: brittany
Password: agent123
Role: SALES_LEAD
Name: Brittany Dollar
Email: brittany.dollar@asam.local
Reports To: jay (Office Manager)
```

### Service Lead
```
Username: nickcol
Password: agent123
Role: SERVICE_LEAD
Name: Nickcol Scott
Email: nickcol.scott@asam.local
Reports To: jay (Office Manager)
```

### Sales Agents

#### Agent 1
```
Username: alianna
Password: agent123
Role: SALES
Name: Alianna Harrilal
Email: alianna.harrilal@asam.local
Reports To: brittany (Sales Lead)
```

#### Agent 2
```
Username: sales_agent2
Password: agent123
Role: SALES
Name: Sarah Williams
Email: sarah.williams@asam.local
Reports To: brittany (Sales Lead)
```

### Service Agents

#### Agent 1
```
Username: austin
Password: agent123
Role: SERVICE
Name: Austin Holloway
Email: austin.holloway@asam.local
Reports To: nickcol (Service Lead)
```

#### Agent 2
```
Username: service_agent1
Password: agent123
Role: SERVICE
Name: Emily Rodriguez
Email: emily.rodriguez@asam.local
Reports To: nickcol (Service Lead)
```

#### Agent 3
```
Username: service_agent2
Password: agent123
Role: SERVICE
Name: James Taylor
Email: james.taylor@asam.local
Reports To: nickcol (Service Lead)
```

---

## 🔄 Password Reset

If passwords are not working, reseed the database:

```bash
npm run db:seed
```

This will:
- Update all user passwords to the documented values above
- Preserve all activity and client data
- Update email addresses to @asam.local domain

---

## 🔧 Technical Details

### Password Hashing
- All passwords are hashed using `bcrypt` with 10 salt rounds
- Passwords are updated during upsert operations
- Admin password: `adminpass` (special)
- All other users: `agent123` (standard)

### Seed File Location
`prisma/seed-enhanced.ts`

### Environment Variables Required
- `DATABASE_URL` - PostgreSQL connection string
- `DATABASE_NAME` - Database name (default: `asam_d`)
- `NODE_ENV` - Environment (default: `development`)

---

## 📊 Organizational Hierarchy

```
Angela Holloway (Admin)
  └─ Jay Cee (Office Manager)
      ├─ Brittany Dollar (Sales Lead)
      │   ├─ Alianna Harrilal (Sales Agent)
      │   └─ Sarah Williams (Sales Agent)
      │
      └─ Nickcol Scott (Service Lead)
          ├─ Austin Holloway (Service Agent)
          ├─ Emily Rodriguez (Service Agent)
          └─ James Taylor (Service Agent)
```

---

## ⚠️ Important Notes

1. **Production Warning**: These are development/test credentials only
2. **Email Domain**: All emails use `@asam.local` (safe local domain)
3. **Password Security**: Change admin password in production
4. **Reseed Safety**: Reseeding is safe and preserves existing data structure

---

## 🐛 Troubleshooting

### Login Not Working?

1. **Verify credentials** - Check you're using the correct username/password
2. **Reseed database** - Run `npm run db:seed` to update passwords
3. **Check database** - Ensure DATABASE_URL is set in `.env.local`
4. **Clear browser cache** - Sometimes session data can interfere

### Password Changed Unexpectedly?

- Reseeding will reset all passwords to documented values
- Check if anyone ran the seed script recently
- Verify you're using the correct environment (dev/test/prod)

---

## ✅ Quick Test

To verify login is working:

```bash
# Start the application
npm run dev

# Navigate to http://localhost:3000

# Try logging in with:
# Username: angela
# Password: adminpass
```

---

*These credentials are for development and testing purposes only. Never use these in a production environment.*

