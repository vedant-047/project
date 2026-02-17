# Nagpur Urban Planning - Decision Support System

An AI-powered sustainable urban development planning system built with Next.js, React, and Python. This system provides data-driven insights and recommendations for sustainable urban planning in Nagpur's wards.

## Features

### 📊 Dashboard
- Real-time analytics and key performance indicators
- Ward-level sustainability metrics
- Population and demographic data
- Interactive status monitoring

### 🗺️ Ward Maps & Visualization
- Geographic visualization of all 5 Nagpur wards
- Sustainability score overlay
- Ward selection and detailed information
- Coordinates and boundary data

### 💡 Recommendations Engine
- AI-generated improvement recommendations
- Categorized by environmental, infrastructure, social, and economic factors
- Priority-based filtering (high, medium, low)
- Cost and impact estimates
- Implementation timelines

### 📈 Scenario Planning
- 5-year sustainability projections
- Multiple scenario modeling:
  - Current Trajectory (no investment)
  - Conservative (2% annual growth)
  - Moderate (3.5% annual growth)
  - Aggressive (5% annual growth)
- Impact analysis and comparison

## Tech Stack

- **Frontend**: Next.js 16, React 19, TypeScript, Tailwind CSS
- **Data Visualization**: Recharts, Leaflet (GIS ready)
- **Backend**: Python (data pipeline, ML models)
- **Database**: PostgreSQL with PostGIS (ready for integration)
- **UI Components**: shadcn/ui

## Project Structure

```
├── app/
│   ├── page.tsx              # Main dashboard
│   ├── maps/page.tsx         # Ward maps visualization
│   ├── recommendations/page.tsx  # Recommendations engine
│   ├── scenarios/page.tsx    # Scenario planning
│   └── layout.tsx            # Root layout
├── components/
│   ├── dashboard-header.tsx
│   ├── stats-cards.tsx
│   ├── ward-selector.tsx
│   ├── sustainability-chart.tsx
│   ├── recommendations-list.tsx
│   └── ward-details-panel.tsx
├── public/data/
│   └── nagpur-wards.json     # Ward data (5 wards)
├── scripts/
│   ├── 01-init-database.sql  # Database schema
│   ├── 02-fetch-nagpur-data.py  # Data generation
│   ├── 03-populate-nagpur-data.sql  # Data population
│   └── 04-ml-models.py       # ML models and insights
└── styles/
    └── globals.css           # Global styles and design tokens
```

## Ward Data

The system includes data for 5 Nagpur wards:
1. **East Nagpur** - Population: 85K, Score: 68.75/100
2. **Central Nagpur** - Population: 102K, Score: 75.5/100
3. **South Nagpur** - Population: 79K, Score: 70.25/100
4. **West Nagpur** - Population: 96K, Score: 67.75/100
5. **North Nagpur** - Population: 89K, Score: 73.5/100

**Total Population**: ~451K across all wards

## Sustainability Metrics

Each ward is evaluated on 4 key dimensions:

- **Environmental**: Green space %, air quality, water quality
- **Infrastructure**: Public transport, waste management, water systems
- **Social**: Literacy rate, employment, community facilities
- **Economic**: Economic development, business opportunities

## Recommendations

Each ward has 1-2 tailored recommendations with:
- Clear objectives and impact projections
- Cost estimates in Indian Rupees
- Implementation timelines (6-24 months)
- Priority classification

Examples:
- Expand green spaces (+15 impact score)
- Improve public transport (+18 impact score)
- Enhance waste management (+12 impact score)
- Upgrade water infrastructure (+14 impact score)
- Renewable energy integration (+16 impact score)

## Getting Started

### Prerequisites
- Node.js 18+
- pnpm (recommended) or npm

### Installation

1. **Clone and install dependencies**:
   ```bash
   cd nagpur-urban-planning
   pnpm install
   ```

2. **Run development server**:
   ```bash
   pnpm dev
   ```

3. **Open browser**:
   - Navigate to http://localhost:3000
   - System automatically loads ward data from `/public/data/nagpur-wards.json`

### Using with PostgreSQL Backend

To connect to a real PostgreSQL database with PostGIS:

1. **Set up environment variables** in `.env.local`:
   ```
   DATABASE_URL=postgresql://user:password@localhost:5432/nagpur_planning
   ```

2. **Run database migrations**:
   ```bash
   # Create schema
   psql -d nagpur_planning -f scripts/01-init-database.sql
   
   # Populate data
   psql -d nagpur_planning -f scripts/03-populate-nagpur-data.sql
   ```

3. **Update API endpoints** to fetch from PostgreSQL instead of JSON

## Python Scripts

### Data Generation (`02-fetch-nagpur-data.py`)
Generates realistic Nagpur ward data including:
- Geographic coordinates
- Population metrics
- Sustainability scores
- Infrastructure data
- Environmental metrics
- AI-generated recommendations

### ML Models (`04-ml-models.py`)
Implements machine learning models for:
- Sustainability prediction
- Gap analysis
- Recommendation generation
- Impact scoring

## API Endpoints (Ready for Backend Implementation)

```
GET /api/wards              # List all wards
GET /api/wards/:id          # Ward details
GET /api/wards/:id/metrics  # Ward metrics
GET /api/recommendations    # All recommendations
POST /api/scenarios         # Generate scenario
```

## Design System

- **Primary Color**: #3b82f6 (Blue)
- **Neutrals**: White, gray shades, black
- **Typography**: Geist font family
- **Spacing**: Tailwind scale (4px base)
- **Responsive**: Mobile-first design with md/lg breakpoints

## Performance Optimizations

- Server-side rendering for fast initial loads
- Image optimization with next/image
- CSS modules for component styling
- Efficient data fetching with React hooks
- Client-side caching with SWR (ready for implementation)

## Future Enhancements

- Real-time data updates from sensors
- Machine learning model improvements
- Advanced geospatial analysis
- Mobile app version
- Multi-language support
- Real-time collaboration features
- Advanced reporting and export

## Contributing

This project is part of the Nagpur Urban Development Initiative. Contributions are welcome for:
- Data accuracy improvements
- Algorithm refinements
- UI/UX enhancements
- Performance optimization

## License

All rights reserved - Nagpur Urban Planning System

## Support

For questions or issues, refer to the system documentation or contact the development team.

---

Built with Next.js, React, and Python for sustainable urban planning in Nagpur.
