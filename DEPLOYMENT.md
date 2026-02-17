# Deployment Guide - Nagpur Urban Planning System

## Quick Start (Development)

```bash
# Install dependencies
pnpm install

# Start development server
pnpm dev

# Open browser to http://localhost:3000
```

## Production Deployment

### Option 1: Deploy to Vercel (Recommended)

1. **Push to GitHub**:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git push origin main
   ```

2. **Deploy to Vercel**:
   - Go to [vercel.com/new](https://vercel.com/new)
   - Import your repository
   - Click "Deploy"

3. **Set Environment Variables** (if using PostgreSQL):
   - Go to Project Settings → Environment Variables
   - Add `DATABASE_URL=your_postgres_connection_string`

### Option 2: Self-Hosted Deployment

#### Prerequisites
- Node.js 18+
- PostgreSQL 14+ (with PostGIS extension)
- Linux/macOS server or Windows with WSL

#### Steps

1. **Clone repository**:
   ```bash
   git clone your-repo-url
   cd nagpur-urban-planning
   ```

2. **Install dependencies**:
   ```bash
   pnpm install --frozen-lockfile
   ```

3. **Build application**:
   ```bash
   pnpm build
   ```

4. **Set environment variables** (`.env.local`):
   ```
   DATABASE_URL=postgresql://user:password@localhost:5432/nagpur_planning
   NODE_ENV=production
   ```

5. **Start production server**:
   ```bash
   pnpm start
   ```

6. **Set up reverse proxy** (Nginx example):
   ```nginx
   upstream app {
     server 127.0.0.1:3000;
   }

   server {
     listen 80;
     server_name your-domain.com;

     location / {
       proxy_pass http://app;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection 'upgrade';
       proxy_set_header Host $host;
       proxy_cache_bypass $http_upgrade;
     }
   }
   ```

## Database Setup

### PostgreSQL with PostGIS

1. **Install PostgreSQL & PostGIS**:
   ```bash
   # Ubuntu/Debian
   sudo apt-get install postgresql postgresql-contrib postgis

   # macOS
   brew install postgresql postgis
   ```

2. **Create database**:
   ```bash
   createdb nagpur_planning
   psql nagpur_planning -c "CREATE EXTENSION postgis;"
   ```

3. **Initialize schema**:
   ```bash
   psql nagpur_planning < scripts/01-init-database.sql
   ```

4. **Populate data**:
   ```bash
   python3 scripts/02-fetch-nagpur-data.py
   psql nagpur_planning < scripts/03-populate-nagpur-data.sql
   ```

5. **Train ML models**:
   ```bash
   python3 scripts/04-ml-models.py
   ```

### Using Cloud PostgreSQL

#### Supabase (Recommended for beginners)

1. Go to [supabase.com](https://supabase.com)
2. Create new project
3. Run migrations in SQL editor:
   ```sql
   -- Copy contents from scripts/01-init-database.sql
   ```
4. Get connection string from project settings
5. Add to `.env.local`:
   ```
   DATABASE_URL=postgresql://[user]:[password]@[host]:5432/[database]
   ```

#### AWS RDS

1. Create RDS PostgreSQL instance with PostGIS
2. Get endpoint from RDS dashboard
3. Add to `.env.local`:
   ```
   DATABASE_URL=postgresql://admin:password@instance.c9akciq32.us-east-1.rds.amazonaws.com:5432/nagpur_planning
   ```

#### Google Cloud SQL

1. Create Cloud SQL PostgreSQL instance
2. Add PostGIS extension:
   ```sql
   CREATE EXTENSION postgis;
   ```
3. Create service account and download JSON key
4. Connect via Cloud SQL Proxy
5. Add connection string to `.env.local`

## Performance Optimization

### Image Optimization
- Images are optimized using Next.js Image component
- All assets use modern formats (WebP with fallbacks)

### Caching Strategy
```typescript
// Browser caching headers (next.config.js)
{
  headers: [
    {
      source: '/data/:path*',
      headers: [{ key: 'Cache-Control', value: 'public, max-age=3600' }]
    }
  ]
}
```

### Database Optimization
- Create indexes on frequently queried columns:
  ```sql
  CREATE INDEX idx_wards_ward_number ON wards(ward_number);
  CREATE INDEX idx_recommendations_ward_id ON recommendations(ward_id);
  CREATE INDEX idx_recommendations_category ON recommendations(category);
  ```

### CDN Setup (Vercel automatic)
- All static assets served via Vercel Edge Network
- API responses cached with ISR (Incremental Static Regeneration)

## Monitoring & Logging

### Application Monitoring

**Vercel Analytics** (built-in):
- Go to Project → Analytics
- Monitor Web Vitals, page load times, errors

**Custom Logging**:
```typescript
// app/lib/logger.ts
export const log = (message: string, data?: any) => {
  if (process.env.NODE_ENV === 'production') {
    // Send to external logging service
  }
  console.log(`[${new Date().toISOString()}] ${message}`, data);
};
```

### Database Monitoring

**PostgreSQL logs**:
```bash
# View slow queries
tail -f /var/log/postgresql/postgresql.log | grep "duration"
```

**Monitoring tools**:
- [PgAdmin](https://www.pgadmin.org/) - GUI for PostgreSQL
- [CloudBeaver](https://cloudbeaver.io/) - Universal database IDE

## Security Checklist

- [ ] Set `NODE_ENV=production`
- [ ] Enable HTTPS only
- [ ] Set secure database password
- [ ] Enable database backups
- [ ] Configure firewall rules
- [ ] Set up DDoS protection
- [ ] Enable rate limiting
- [ ] Use environment variables for secrets
- [ ] Regular security updates
- [ ] Database encryption at rest

## Backup & Recovery

### PostgreSQL Backups

```bash
# Full backup
pg_dump nagpur_planning > backup_$(date +%Y%m%d).sql

# Restore backup
psql nagpur_planning < backup_20240217.sql

# Automated daily backup (crontab)
0 2 * * * pg_dump nagpur_planning | gzip > /backups/db_$(date +\%Y\%m\%d).sql.gz
```

### Application Backups

- Vercel automatically backs up deployments
- GitHub manages code history
- Configure external backup storage for additional security

## Scaling

### Horizontal Scaling

**Load Balancing**:
```nginx
upstream app_servers {
  server app1.example.com:3000;
  server app2.example.com:3000;
  server app3.example.com:3000;
}

server {
  location / {
    proxy_pass http://app_servers;
  }
}
```

### Vertical Scaling

- Increase server resources (CPU, RAM)
- Upgrade PostgreSQL instance class
- Add read replicas for read-heavy workloads

### Database Scaling

```sql
-- Create read replica
CREATE PUBLICATION all_tables FOR ALL TABLES;

-- Connection pooling (with PgBouncer)
-- Configure in pgbouncer.ini
```

## Troubleshooting

### Common Issues

**Build fails with missing dependencies**:
```bash
pnpm install --force
pnpm build
```

**Database connection timeout**:
- Check firewall rules
- Verify DATABASE_URL format
- Ensure database is running

**Performance issues**:
- Check database query performance
- Enable caching headers
- Analyze bundle size: `next/bundle-analyzer`

**Memory leaks**:
```bash
# Monitor Node.js process
node --inspect app.js

# Use Chrome DevTools or clinic.js
```

## Rollback Procedure

### Vercel Rollback
1. Go to Project → Deployments
2. Click on previous stable deployment
3. Click "Promote to Production"

### Manual Rollback
```bash
git revert <commit-hash>
git push origin main
pnpm build
pnpm start
```

## Support & Documentation

- Next.js Docs: [nextjs.org/docs](https://nextjs.org/docs)
- PostgreSQL Docs: [postgresql.org/docs](https://www.postgresql.org/docs/)
- PostGIS Docs: [postgis.net/documentation](https://postgis.net/documentation/)
- Vercel Docs: [vercel.com/docs](https://vercel.com/docs)

---

Last Updated: February 2024
