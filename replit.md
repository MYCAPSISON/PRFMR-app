# PRFMR - Sustainable Fat Loss Tracker

## Overview

PRFMR is a mobile-first web application designed for sustainable fat loss for active individuals. The app provides calorie and macro tracking, weight trend visualization, and educational content about nutrition. It emphasizes conservative, science-based approaches to fat loss without extreme dieting.

**Key Features:**
- User onboarding with body metrics collection
- BMR/TDEE calculation using Mifflin-St Jeor equation
- Personalized calorie and macro targets
- Daily food logging and weight tracking
- Progress visualization with charts
- Educational "Playbook" with nutrition principles
- **Supplement Tracker** - Personal supplement inventory, stacks, and reminders

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture
- **Framework:** React 18 with TypeScript
- **Build Tool:** Vite with hot module replacement
- **Routing:** Wouter (lightweight React router)
- **State Management:** TanStack React Query for server state
- **Styling:** Tailwind CSS with shadcn/ui component library
- **Animations:** Framer Motion for page transitions and micro-interactions
- **Charts:** Recharts for data visualization

**Design Pattern:** The frontend uses a pages-based structure with shared components. Custom hooks abstract data fetching logic (`use-user.ts`, `use-logs.ts`). Local storage manages simple session state (username persistence).

### Backend Architecture
- **Runtime:** Node.js with Express
- **Language:** TypeScript with ESM modules
- **API Design:** RESTful endpoints defined in `shared/routes.ts`
- **Build:** esbuild for server bundling, Vite for client

**Key Design Decisions:**
- Shared schema and route definitions between client/server for type safety
- In-memory storage implementation with interface for future database migration
- Calculation logic (BMR, TDEE, macros) lives server-side in `routes.ts`

### Data Storage
- **ORM:** Drizzle ORM with PostgreSQL dialect
- **Schema Location:** `shared/schema.ts`
- **Current Implementation:** MemStorage class (in-memory) with IStorage interface
- **Database Ready:** Drizzle config points to PostgreSQL via DATABASE_URL

**Schema includes:**
- `users` - User profiles with onboarding data and calculated targets
- `logs` - Daily weight and macro entries
- `foodEntries` - Individual food items logged per day
- `supplementCatalog` - Predefined supplements with micronutrient data (seeded with ~30 common supplements)
- `supplements` - User's personal supplement inventory (with catalogId, doseAmount, doseUnit)
- `supplementIntakes` - Daily supplement intake tracking for AMQS integration
- `supplementStacks` - Groups of supplements (e.g., "Morning Routine")
- `stackSupplements` - Links supplements to stacks (many-to-many)
- `stackReminders` - Reminders with time and days of week
- `supplementLogs` - Tracks when supplements are taken (reminder UI)
- `disclaimerAcceptances` - Records user acceptance of legal disclaimers

### Supplement Tracker
The supplement tracker allows users to:
1. Add supplements from a predefined catalog or as custom entries
2. Specify dosage amounts and units (mcg, mg, g, IU, etc.)
3. Create "stacks" to group supplements together
4. Set reminders for stacks (time + days of week)
5. Track taken/not-taken status on dashboard
6. View supplement micronutrient contributions in AMQS score

**Catalog-based Tracking:** Supplements linked to the catalog have their micronutrient data automatically integrated into daily and weekly AMQS calculations. The catalog includes common supplements like Vitamin D3, Magnesium, Zinc, B12, Iron, and more, with their respective micronutrient contributions mapped to AMQS nutrient keys.

**Important:** This feature includes legal disclaimers stating it's for personal tracking only and does not provide medical advice. Users must accept the disclaimer before enabling reminders. Server-side enforcement prevents reminder creation without acceptance.

**Security Note:** The app uses localStorage-based session management without server-side authentication. Data is isolated by username in API paths, but proper resource ownership enforcement would require implementing session-based authentication (a future enhancement).

### Calculation Engine
The app calculates personalized targets using:
1. **BMR:** Mifflin-St Jeor equation based on gender, weight, height, age
2. **TDEE:** BMR × activity multiplier (1.2 to 1.9)
3. **Deficit:** Conservative 500kcal below TDEE
4. **Protein:** 2g per kg body weight
5. **Fat:** 0.9g per kg body weight
6. **Carbs:** Remaining calories after protein and fat allocation

## External Dependencies

### UI Component Library
- **shadcn/ui:** Pre-built accessible components using Radix UI primitives
- **Configuration:** `components.json` defines aliases and Tailwind setup

### Key NPM Packages
- `@tanstack/react-query` - Data fetching and caching
- `drizzle-orm` / `drizzle-zod` - Database ORM and schema validation
- `zod` - Runtime type validation
- `recharts` - Charting library
- `framer-motion` - Animation library
- `date-fns` - Date manipulation
- `wouter` - Client-side routing

### Database
- PostgreSQL (configured via DATABASE_URL environment variable)
- `connect-pg-simple` for session storage (available but not currently used)

### Development Tools
- `@replit/vite-plugin-runtime-error-modal` - Error overlay in development
- `@replit/vite-plugin-cartographer` - Replit-specific development features