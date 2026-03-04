# Product Requirements Document (PRD)
# Chinese Recipe Assistant - MVP 1.0

**Version:** 1.0  
**Date:** March 4, 2026  
**Author:** Product Manager  
**Status:** Ready for Development  

---

## 📋 Document Overview

This PRD defines the requirements for MVP 1.0 of the Chinese Recipe Assistant - an AI-powered meal planning application designed for Singaporean households with domestic helpers.

**Target Launch:** 4-6 weeks from kickoff  
**Target Users:** 100-500 households in Singapore  
**Success Metric:** 30+ daily active users within 2 weeks

---

## 🎯 Executive Summary

### The Problem

Singaporean families with domestic helpers spend **30-45 minutes daily** searching YouTube for Chinese recipe videos, planning meals, creating shopping lists, and communicating cooking instructions to their helpers. This is:
- Time-consuming
- Mentally exhausting
- Repetitive
- Language barriers (Chinese videos → Burmese/English instructions)

### The Solution

An AI-powered web application that:
1. Generates personalized Chinese meal plans in seconds
2. Creates consolidated shopping lists automatically
3. Provides video links with instructions in multiple languages
4. Handles dietary preferences and constraints
5. Optimizes leftover ingredients

### Key Value Proposition

**"From 30 minutes to 2 minutes: Plan your week's Chinese meals instantly"**

---

## 👥 Target Users

### Primary User (The Employer)
- **Demographics:** Singapore households earning SGD 8,000+/month
- **Age:** 30-55 years old
- **Tech Savviness:** Medium (comfortable with smartphones/web apps)
- **Pain Points:** 
  - Time-constrained (working professionals/parents)
  - Want authentic Chinese food at home
  - Tired of repetitive meal planning
  - Limited cooking knowledge
  
### Secondary User (The Domestic Helper)
- **Demographics:** Myanmar, Philippines, Indonesia nationals
- **Age:** 25-45 years old
- **Tech Savviness:** Low to Medium
- **Language:** Burmese, Tagalog, Bahasa (limited English/Chinese)
- **Pain Points:**
  - Language barrier with recipe videos
  - Unfamiliar with Chinese ingredients
  - Needs clear, simple instructions

---

## 🎯 Goals & Success Metrics

### Business Goals
1. Validate product-market fit
2. Acquire 100+ beta users
3. Achieve 30% weekly retention
4. Gather user feedback for MVP 2.0

### User Goals
1. Reduce meal planning time by 90% (30 min → 3 min)
2. Increase variety in home-cooked meals
3. Reduce food waste through leftover optimization
4. Improve helper's cooking consistency

### Success Metrics (MVP 1.0)

| Metric | Target | Measurement |
|--------|--------|-------------|
| **User Acquisition** | 100 users | 2 weeks post-launch |
| **Daily Active Users** | 30+ | Week 2-4 average |
| **Retention (Week 1)** | 50% | Users who return Day 2-7 |
| **Queries per User** | 3+ per day | Average active user |
| **Success Rate** | 80% | Meals actually cooked |
| **Time Saved** | 25+ min/day | Self-reported survey |
| **NPS Score** | 40+ | Post-usage survey |

---

## 📖 User Stories

### Story 1: Daily Meal Planning
```
As a busy working mother
I want to get a Chinese dinner suggestion in under 2 minutes
So that I can focus on work without worrying about meal planning

Acceptance Criteria:
- Can input preferences in natural language
- Receives 1-3 meal suggestions with videos
- Gets complete shopping list
- Takes <2 minutes end-to-end
```

### Story 2: Weekly Meal Planning
```
As a household manager
I want to plan a full week of Chinese dinners on Sunday
So that I can shop once and my helper knows the weekly menu

Acceptance Criteria:
- Can specify "plan 7 dinners"
- Receives varied cuisines (Sichuan, Cantonese, etc.)
- Gets consolidated shopping list for entire week
- Can export/print for helper
```

### Story 3: Dietary Constraints
```
As a health-conscious parent
I want meal plans that respect our dietary restrictions
So that everyone in the family can eat safely

Acceptance Criteria:
- Can specify allergies (e.g., no peanuts, no seafood)
- Can set preferences (vegetarian, low-carb, halal)
- All suggestions respect constraints
- System remembers preferences
```

### Story 4: Leftover Management
```
As a cost-conscious household
I want to use leftover ingredients before they spoil
So that we reduce food waste and save money

Acceptance Criteria:
- Can input "I have leftover rice and chicken"
- Receives 3-5 recipe suggestions using those ingredients
- Shows which recipes use the most leftovers
```

### Story 5: Helper Instructions
```
As a domestic helper employer
I want cooking instructions my Burmese helper can understand
So that she can cook the meal correctly

Acceptance Criteria:
- Instructions in simple English
- Link to YouTube video with auto-translate guide
- Step-by-step format
- Ingredient measurements in metric (grams, ml)
```

---

## 🔧 Feature Requirements

### Must Have (P0 - MVP 1.0)

#### F1: AI Meal Planner
**Description:** Natural language meal planning with personalized suggestions

**Functional Requirements:**
- Accept natural language input (e.g., "Plan tonight's dinner, something spicy")
- Support constraints:
  - Cuisine type (Sichuan, Cantonese, Hunan, Shanghai, Beijing, etc.)
  - Spice level (mild, medium, hot, very hot)
  - Cooking time (<30 min, 30-60 min, >60 min)
  - Difficulty (easy, medium, hard)
  - Dietary restrictions (vegetarian, no seafood, no nuts, halal, etc.)
  - Number of meals (1 or 7)
- Return 1-3 meal suggestions with:
  - Recipe name (English + Chinese characters)
  - Brief description
  - Ingredients list
  - Cooking time & difficulty
  - YouTube video link
  - Cuisine type & spice level
  
**Non-Functional Requirements:**
- Response time: <3 seconds
- Accuracy: Suggestions match stated preferences 95%+ of time
- Variety: No repeat suggestions within same day

**API Endpoint:**
```
POST /api/plan-meal
Request:
{
  "query": "Plan dinner tonight, Sichuan style, medium spicy",
  "preferences": {
    "cuisine": ["sichuan"],
    "spice_level": "medium",
    "cooking_time_max": 45,
    "dietary_restrictions": ["no_seafood"]
  },
  "num_suggestions": 3
}

Response:
{
  "suggestions": [
    {
      "id": "recipe_001",
      "name": "Kung Pao Chicken",
      "name_chinese": "宫保鸡丁",
      "cuisine": "Sichuan",
      "description": "Classic Sichuan dish with tender chicken...",
      "spice_level": "medium",
      "cooking_time": 35,
      "difficulty": "medium",
      "video_url": "https://youtube.com/watch?v=...",
      "ingredients": [...],
      "servings": 4
    }
  ],
  "timestamp": "2026-03-04T10:30:00Z"
}
```

---

#### F2: Shopping List Generator
**Description:** Consolidated shopping list from meal plans

**Functional Requirements:**
- Generate shopping list from 1-7 recipes
- Group by category:
  - Proteins (chicken, pork, beef, seafood, tofu)
  - Vegetables & Herbs
  - Sauces & Condiments
  - Dry Goods (noodles, rice, etc.)
  - Fresh Produce
- Consolidate quantities (if 3 recipes need ginger, combine)
- Show metric measurements (grams, ml)
- Estimate total cost (SGD)
- Mark items commonly stocked ("check if you have")
- Export options: Text, PDF, WhatsApp-friendly

**API Endpoint:**
```
POST /api/generate-shopping-list
Request:
{
  "recipe_ids": ["recipe_001", "recipe_002", "recipe_003"]
}

Response:
{
  "shopping_list": {
    "proteins": [
      {"item": "Chicken thigh", "quantity": "800g", "cost_est": 8.50}
    ],
    "vegetables": [
      {"item": "Ginger", "quantity": "50g", "cost_est": 1.20}
    ],
    "sauces": [
      {"item": "Soy sauce", "quantity": "100ml", "check_pantry": true}
    ]
  },
  "total_cost_sgd": 45.30,
  "formatted_text": "SHOPPING LIST\n\nProteins:\n- Chicken..."
}
```

---

#### F3: Recipe Database with RAG Search
**Description:** Semantic search for Chinese recipes using vector embeddings

**Functional Requirements:**
- Database of 200-500 Chinese recipes
- Each recipe contains:
  - Name (English + Chinese)
  - Cuisine origin
  - Ingredients with quantities
  - Step-by-step instructions
  - YouTube video URL
  - Cooking time, difficulty, spice level
  - Tags (vegetarian, quick, authentic, etc.)
- Semantic search using RAG (ChromaDB)
- Search by:
  - Natural language ("quick weeknight dinner")
  - Ingredients ("recipes with chicken and ginger")
  - Cuisine type
  - Metadata filters (time, difficulty, spice)

**Data Model:**
```json
{
  "recipe_id": "rec_001",
  "name_en": "Kung Pao Chicken",
  "name_zh": "宫保鸡丁",
  "cuisine": "Sichuan",
  "region": "Southwest China",
  "description": "A classic Sichuan dish featuring tender chicken...",
  "spice_level": 3,
  "difficulty": 2,
  "cooking_time_min": 35,
  "prep_time_min": 15,
  "servings": 4,
  "ingredients": [
    {
      "item": "Chicken thigh",
      "quantity": 500,
      "unit": "g",
      "notes": "Cut into 1-inch cubes"
    },
    {
      "item": "Dried red chilies",
      "quantity": 8,
      "unit": "pieces"
    }
  ],
  "instructions": [
    {
      "step": 1,
      "action": "Cut chicken into 1-inch cubes",
      "time_min": 3
    }
  ],
  "video_url": "https://youtube.com/watch?v=xyz",
  "video_channel": "Chinese Cooking Demystified",
  "tags": ["authentic", "spicy", "stir-fry", "chicken", "peanuts"],
  "dietary_info": {
    "vegetarian": false,
    "vegan": false,
    "gluten_free": false,
    "contains_nuts": true,
    "halal_adaptable": true
  },
  "cost_estimate_sgd": 12.50,
  "created_at": "2026-03-01T00:00:00Z"
}
```

**Technical Requirements:**
- Use ChromaDB for vector storage
- Use sentence-transformers for embeddings (all-MiniLM-L6-v2)
- Index on: name, description, ingredients, cuisine, tags
- Retrieval: Top 10-15 most relevant recipes per query

---

#### F4: Leftover Optimizer
**Description:** Suggest recipes using leftover ingredients

**Functional Requirements:**
- Accept list of leftover ingredients
- Search recipes that use those ingredients
- Prioritize recipes using MORE of the leftover items
- Show what additional ingredients needed
- Support common leftovers:
  - Rice (white, fried, day-old)
  - Proteins (chicken, pork, beef)
  - Vegetables (bok choy, cabbage, carrots)
  - Tofu

**API Endpoint:**
```
POST /api/leftover-recipes
Request:
{
  "leftovers": ["rice", "chicken", "bok choy"],
  "num_suggestions": 5
}

Response:
{
  "suggestions": [
    {
      "recipe": {...},
      "leftovers_used": ["rice", "chicken"],
      "leftovers_coverage": "66%",
      "additional_ingredients": ["soy sauce", "eggs", "scallions"]
    }
  ]
}
```

---

#### F5: Simple Web Interface
**Description:** Clean, mobile-friendly web UI

**Functional Requirements:**

**Pages:**
1. **Home / Meal Planner** (Main page)
   - Large search/input box for natural language query
   - Quick action buttons:
     - "Plan Tonight's Dinner"
     - "Plan This Week (7 Days)"
     - "Use My Leftovers"
   - Preset preference chips:
     - Cuisines: Sichuan | Cantonese | Hunan | Shanghai
     - Spice: Mild | Medium | Hot
     - Time: <30min | 30-60min
     - Diet: Vegetarian | No Seafood | No Nuts

2. **Results Page**
   - Recipe cards showing:
     - Recipe photo (from video thumbnail)
     - Name (EN + 中文)
     - Cuisine badge
     - Spice indicator (🌶️🌶️)
     - Time & difficulty icons
     - "Select" button
   - Shopping list panel (right sidebar on desktop, bottom sheet on mobile)
   - "Send to WhatsApp" button
   - "Print" button

3. **Recipe Detail Page**
   - Full recipe information
   - Embedded YouTube video
   - Ingredients list with checkboxes
   - Step-by-step instructions
   - "How to enable subtitles" instruction box
   - "Add to Meal Plan" button

4. **Settings Page**
   - Save dietary preferences
   - Set default constraints
   - Language preference (English/Chinese)

**UI/UX Requirements:**
- Mobile-first design (70% of users on mobile)
- Support iOS Safari, Chrome, Samsung Internet
- Fast loading (<2s)
- Offline-friendly (cache recipes)
- Clean, minimal design (like Airbnb/Notion)
- Large touch targets (for helpers with less tech experience)

**Design Tokens:**
```
Colors:
- Primary: #E63946 (Red - Chinese traditional)
- Secondary: #F4A261 (Warm orange)
- Success: #06D6A0
- Background: #FFFFFF
- Text: #1D1D1F
- Secondary Text: #86868B

Typography:
- Headers: Inter, SF Pro Display
- Body: Inter, SF Pro Text
- Chinese: Noto Sans SC

Spacing:
- Base unit: 8px
- Card padding: 16px
- Section gap: 32px
```

---

### Should Have (P1 - Nice to Have in MVP 1.0, OK to defer)

#### F6: User Accounts (Simple)
- No login required for trial (localStorage preferences)
- Optional email signup to sync across devices
- Save meal history
- Favorite recipes

#### F7: Recipe Rating & Feedback
- After meal is cooked, ask "How was it?"
- 5-star rating
- Simple feedback form
- Use to improve future suggestions

---

### Won't Have (Out of Scope for MVP 1.0)

- ❌ Video processing / screenshot extraction
- ❌ Custom subtitle generation
- ❌ Nutrition calculations
- ❌ Cost tracking over time
- ❌ Social features / recipe sharing
- ❌ Mobile app (web only)
- ❌ Multi-language UI (English only)
- ❌ Helper-specific interface
- ❌ Voice input
- ❌ Recipe photo upload
- ❌ Meal plan calendar view

---

## 🏗️ Technical Architecture

### System Architecture

```
┌─────────────────────────────────────────────────────┐
│                   USER DEVICES                      │
│         (iOS Safari, Chrome, Samsung Internet)      │
└─────────────────┬───────────────────────────────────┘
                  │ HTTPS
                  ↓
┌─────────────────────────────────────────────────────┐
│              FRONTEND (Next.js / React)             │
│  - Responsive web app                               │
│  - State management (Zustand/Redux)                 │
│  - API client                                       │
└─────────────────┬───────────────────────────────────┘
                  │ REST API
                  ↓
┌─────────────────────────────────────────────────────┐
│             BACKEND API (FastAPI)                   │
│                                                     │
│  Endpoints:                                         │
│  - POST /api/plan-meal                              │
│  - POST /api/generate-shopping-list                 │
│  - POST /api/leftover-recipes                       │
│  - GET  /api/recipes/{id}                           │
│  - POST /api/search-recipes                         │
│                                                     │
└───┬─────────────────────┬───────────────────────────┘
    │                     │
    ↓                     ↓
┌─────────────┐   ┌──────────────────────────────┐
│  ChromaDB   │   │    DeepSeek API              │
│  (Vector    │   │    (LLM for planning)        │
│   Store)    │   │                              │
│             │   │    Input: Augmented prompt   │
│  - 500      │   │    Output: Meal suggestions  │
│    recipes  │   │    Cost: ~$0.001 per query   │
│  - Semantic │   │                              │
│    search   │   └──────────────────────────────┘
│             │
└─────────────┘
```

### Tech Stack

**Frontend:**
- Framework: Next.js 14 (React 18)
- Styling: Tailwind CSS
- State: Zustand or React Context
- Forms: React Hook Form
- HTTP: Axios or Fetch
- Hosting: Vercel (free tier)

**Backend:**
- Framework: FastAPI (Python 3.10+)
- Vector DB: ChromaDB 0.4.x
- Embeddings: sentence-transformers (all-MiniLM-L6-v2)
- LLM: DeepSeek API
- Hosting: Railway, Render, or Fly.io ($5-10/month)

**Database:**
- Vector Store: ChromaDB (embedded, file-based)
- User Data: PostgreSQL or SQLite (for MVP)

**DevOps:**
- Version Control: Git + GitHub
- CI/CD: GitHub Actions
- Monitoring: Simple logging (stdout)
- Analytics: PostHog or Mixpanel (free tier)

---

## 🔄 User Flows

### Flow 1: Quick Daily Meal Planning

```
User Journey:
1. User opens app homepage
2. Sees large input box: "What would you like to eat today?"
3. Types: "Sichuan dinner tonight, medium spicy"
4. Clicks "Plan Meal" or presses Enter

System Processing:
5. Frontend sends to: POST /api/plan-meal
6. Backend:
   a. Parses query for constraints (cuisine: Sichuan, spice: medium)
   b. Generates query embedding
   c. Searches ChromaDB for relevant recipes
   d. Retrieves top 15 Sichuan recipes
   e. Builds augmented prompt for DeepSeek
   f. Sends to DeepSeek API
   g. DeepSeek selects best 3 recipes
   h. Returns JSON response

7. Frontend displays:
   - 3 recipe cards
   - Shopping list panel (collapsed initially)
   
User Actions:
8. User clicks "Kung Pao Chicken" card
9. Sees recipe detail page with:
   - YouTube video
   - Ingredients
   - Instructions
   - "Generate Shopping List" button
   
10. User clicks "Generate Shopping List"
11. Shopping list appears
12. User clicks "Send to WhatsApp"
13. Opens WhatsApp with pre-filled message

Total time: <2 minutes
```

### Flow 2: Weekly Meal Planning

```
1. User clicks "Plan This Week" button
2. Modal opens: "Any preferences for this week?"
3. User selects:
   - Varied cuisines ✓
   - No seafood ✓
   - Medium difficulty ✓
4. Clicks "Plan 7 Meals"

System:
5. Backend retrieves 35-50 recipes (diverse)
6. DeepSeek selects best 7 (balancing variety)
7. Returns meal plan with:
   - Monday: Kung Pao Chicken (Sichuan)
   - Tuesday: Cantonese Steamed Fish (Cantonese)
   - ...
   - Sunday: Beijing Zhajiangmian (Beijing)

8. Frontend shows:
   - 7-day calendar view
   - Each day has recipe card
   - Combined shopping list
   
9. User clicks "Download Meal Plan"
10. PDF generated with:
    - Weekly schedule
    - Shopping list
    - YouTube links
    - Simple instructions
    
11. User prints and gives to helper

Total time: 3-4 minutes
```

### Flow 3: Leftover Optimization

```
1. User clicks "Use My Leftovers"
2. Input field: "What ingredients do you have?"
3. User types: "rice, chicken, bok choy"
4. Clicks "Find Recipes"

System:
5. Searches recipes containing those ingredients
6. Ranks by coverage (how many leftovers used)
7. Returns 5 suggestions

8. Frontend shows:
   - Recipe cards with "Uses: rice, chicken" tags
   - "66% of your leftovers" indicator
   - "Need to buy: eggs, soy sauce" list

9. User selects "Soy Sauce Fried Rice"
10. Generates shopping list for missing items only
11. Minimal shopping needed

Total time: 1-2 minutes
```

---

## 📊 Data Requirements

### Recipe Data Collection (Setup Phase)

**Sources:**
1. YouTube channels:
   - Chinese Cooking Demystified
   - Wang Gang (王刚)
   - The Woks of Life
   - Souped Up Recipes
   
2. Recipe websites:
   - The Woks of Life blog
   - Red House Spice
   - China Sichuan Food

**Data to Extract:**
- Recipe name (EN + ZH)
- Ingredients with quantities
- Instructions
- Video URL
- Cuisine type
- Difficulty, time

**Target:** 200-500 recipes covering:
- Sichuan: 30%
- Cantonese: 25%
- Hunan: 15%
- Shanghai: 10%
- Beijing: 10%
- Other: 10%

**Scraping Tools:**
- BeautifulSoup (Python)
- YouTube API
- Manual curation for quality

**Setup Time:** 1-2 weeks (40-80 hours)

---

## 🎨 UI Mockups & Wireframes

### Screen 1: Homepage

```
┌────────────────────────────────────────────┐
│  🍜 Recipe Assistant        ⚙️ Settings   │
├────────────────────────────────────────────┤
│                                            │
│   What would you like to eat today?        │
│  ┌──────────────────────────────────────┐ │
│  │ e.g., "Sichuan dinner, medium spicy" │ │
│  └──────────────────────────────────────┘ │
│              [Plan Meal 🔍]                │
│                                            │
│   Quick Actions:                           │
│  ┌─────────────┐  ┌─────────────┐        │
│  │  📅 Plan     │  │  🍚 Use My  │        │
│  │  Tonight's   │  │  Leftovers  │        │
│  │  Dinner      │  │             │        │
│  └─────────────┘  └─────────────┘        │
│  ┌─────────────────────────────────────┐  │
│  │  📆 Plan This Week (7 Dinners)      │  │
│  └─────────────────────────────────────┘  │
│                                            │
│   Preferences:                             │
│   Cuisine: [Sichuan] [Cantonese] [Hunan]  │
│   Spice:   [Mild] [Medium] [Hot]          │
│   Time:    [<30min] [30-60min]            │
│                                            │
│   Recent Plans:                            │
│   • Kung Pao Chicken - Yesterday          │
│   • Mapo Tofu - 2 days ago                │
│                                            │
└────────────────────────────────────────────┘
```

### Screen 2: Results Page

```
┌────────────────────────────────────────────┐
│  ← Back                         Print 🖨️   │
├────────────────────────────────────────────┤
│  Top 3 Suggestions for "Sichuan dinner"   │
│                                            │
│  ┌──────────────────────────────────────┐ │
│  │  [Recipe Photo]                      │ │
│  │                                      │ │
│  │  Kung Pao Chicken 宫保鸡丁            │ │
│  │  🌶️🌶️ Medium Spicy                  │ │
│  │  ⏱️ 35 min  👨‍🍳 Medium               │ │
│  │  Sichuan • Chicken • Peanuts         │ │
│  │                                      │ │
│  │         [Select This Recipe →]       │ │
│  └──────────────────────────────────────┘ │
│                                            │
│  ┌──────────────────────────────────────┐ │
│  │  [Recipe Photo]                      │ │
│  │  Mapo Tofu 麻婆豆腐                   │ │
│  │  🌶️🌶️🌶️ Hot & Numbing              │ │
│  │  ⏱️ 25 min  👨‍🍳 Easy                 │ │
│  └──────────────────────────────────────┘ │
│                                            │
│  [Show More Suggestions...]               │
│                                            │
│  📝 Shopping List (for selected)          │
│  ┌──────────────────────────────────────┐ │
│  │  • Chicken thigh: 500g               │ │
│  │  • Dried chilies: 8 pieces           │ │
│  │  • Peanuts: 100g                     │ │
│  │  ... (4 more items)                  │ │
│  │                                      │ │
│  │  [📱 Send to WhatsApp]               │ │
│  └──────────────────────────────────────┘ │
└────────────────────────────────────────────┘
```

### Screen 3: Recipe Detail

```
┌────────────────────────────────────────────┐
│  ← Back to Results                         │
├────────────────────────────────────────────┤
│  Kung Pao Chicken 宫保鸡丁                  │
│  Sichuan Cuisine • 35 min • Medium         │
│                                            │
│  ┌──────────────────────────────────────┐ │
│  │                                      │ │
│  │     [YouTube Video Player]           │ │
│  │                                      │ │
│  └──────────────────────────────────────┘ │
│                                            │
│  💡 To watch in Burmese/English:          │
│     Click ⚙️ → Subtitles → Auto-translate │
│                                            │
│  📝 Ingredients (4 servings):              │
│  ☐ Chicken thigh: 500g (cut in cubes)     │
│  ☐ Dried red chilies: 8 pieces            │
│  ☐ Sichuan peppercorns: 1 tsp             │
│  ☐ Peanuts: 100g                          │
│  ... (8 more)                             │
│                                            │
│  👨‍🍳 Instructions:                         │
│  1. Cut chicken into 1-inch cubes (3 min) │
│  2. Marinate with soy sauce (5 min)       │
│  3. Heat wok on high heat...              │
│  ... (8 more steps)                       │
│                                            │
│  [Generate Shopping List]                 │
│  [Add to Meal Plan]                       │
│  [⭐ Save to Favorites]                   │
└────────────────────────────────────────────┘
```

---

## 🧪 Testing Requirements

### Unit Tests
- Recipe search accuracy (precision/recall)
- Shopping list consolidation logic
- Dietary filter correctness

### Integration Tests
- RAG retrieval quality (relevant results)
- DeepSeek API response format
- End-to-end meal planning flow

### User Acceptance Testing
- 10 beta users test all flows
- Collect feedback on:
  - Recipe quality
  - UI clarity
  - Response speed
  - Shopping list usefulness

### Performance Tests
- API response time <3s (95th percentile)
- Support 50 concurrent users
- Handle 1000 queries/day

---

## 📈 Rollout Plan

### Week 1-2: Development
- Setup infrastructure
- Scrape and curate 200 recipes
- Build RAG system
- Develop API endpoints
- Create basic frontend

### Week 3: Internal Testing
- Team dogfooding
- Fix critical bugs
- Refine prompts
- Optimize recipe suggestions

### Week 4: Beta Launch
- Invite 20 friends/family
- Monitor usage
- Collect feedback
- Iterate on UX

### Week 5-6: Public Launch
- Social media announcement
- Product Hunt launch
- Expat Facebook groups
- Singapore parenting forums

---

## 💰 Budget & Costs

### MVP 1.0 (Month 1)
- Domain: $15/year
- Vercel hosting: $0 (free tier)
- Backend hosting: $10/month (Render/Railway)
- DeepSeek API: $20-50/month (depends on usage)
- Total: ~$30-70/month

### Scaling (100 users)
- Backend: $50/month
- Database: $0 (ChromaDB embedded)
- DeepSeek API: $100/month
- Total: ~$150/month

---

## 🚀 Success Criteria

### Must Achieve (MVP 1.0)
- ✅ 100+ sign-ups within 2 weeks
- ✅ 30+ daily active users by Week 4
- ✅ <3 second response time (P95)
- ✅ 80%+ recipe relevance (user feedback)
- ✅ 50%+ Week 1 retention

### Nice to Have
- 🎯 NPS score >40
- 🎯 70%+ weekly retention
- 🎯 3+ queries per active user
- 🎯 5+ organic social shares

### Signals to Pivot/Iterate
- ⚠️ <20% Week 1 retention → UX/product issues
- ⚠️ <50% recipe satisfaction → Improve dataset
- ⚠️ High bounce rate → Simplify onboarding
- ⚠️ Low query volume → Add more use cases

---

## 📝 Open Questions & Risks

### Questions
1. **Recipe ownership:** Legal to link YouTube videos?
   - Mitigation: Yes, linking is fair use. Add attribution.

2. **Language barrier:** Will helpers actually watch videos?
   - Mitigation: Keep instructions very simple, test with real helpers

3. **Pricing:** Free forever or freemium?
   - Decision: Free for MVP 1.0, decide after 100 users

### Risks
| Risk | Probability | Impact | Mitigation |
|------|------------|--------|------------|
| Low recipe quality | Medium | High | Manual curation, user ratings |
| API costs too high | Low | Medium | Monitor usage, set budget alerts |
| Poor AI suggestions | Medium | High | Human-in-loop review, feedback loop |
| Low user retention | Medium | High | Weekly check-ins, iterate on UX |
| Helper non-adoption | High | Medium | Test with 5 real helpers first |

---

## 📋 Handoff Checklist

For development teams (or AI assistants):

### Frontend Team
- [ ] Read UI mockups section
- [ ] Implement responsive design (mobile-first)
- [ ] Integrate with backend API endpoints
- [ ] Add loading states and error handling
- [ ] Test on iOS Safari, Chrome, Samsung Internet

### Backend Team
- [ ] Implement FastAPI endpoints per spec
- [ ] Setup ChromaDB with 200+ recipes
- [ ] Integrate DeepSeek API
- [ ] Add request logging
- [ ] Deploy to staging environment

### Data Team
- [ ] Scrape 200-500 Chinese recipes
- [ ] Format in specified JSON schema
- [ ] Generate embeddings
- [ ] Load into ChromaDB
- [ ] Validate search quality

### QA Team
- [ ] Test all user flows
- [ ] Verify API response times
- [ ] Check mobile responsiveness
- [ ] Test with real recipes/queries
- [ ] Report bugs in GitHub Issues

---

## 📞 Stakeholders & Contacts

**Product Owner:** [Your Name]  
**Target Users:** Singaporean households with domestic helpers  
**Launch Date:** 4-6 weeks from kickoff  
**Feedback Channel:** GitHub Issues / Google Form

---

## 🔄 Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-03-04 | Product Manager | Initial PRD for MVP 1.0 |

---

## Appendix A: Sample Recipes

### Example 1: Kung Pao Chicken
```json
{
  "recipe_id": "rec_001",
  "name_en": "Kung Pao Chicken",
  "name_zh": "宫保鸡丁",
  "cuisine": "Sichuan",
  "description": "A classic Sichuan dish with tender chicken, crunchy peanuts, and a perfect balance of spicy, sweet, and savory flavors.",
  "spice_level": 3,
  "difficulty": 2,
  "cooking_time_min": 35,
  "prep_time_min": 15,
  "servings": 4,
  "ingredients": [
    {"item": "Chicken thigh", "quantity": 500, "unit": "g", "notes": "Cut into 1-inch cubes"},
    {"item": "Dried red chilies", "quantity": 8, "unit": "pieces"},
    {"item": "Sichuan peppercorns", "quantity": 1, "unit": "tsp"},
    {"item": "Peanuts", "quantity": 100, "unit": "g", "notes": "Roasted"},
    {"item": "Soy sauce", "quantity": 2, "unit": "tbsp"},
    {"item": "Chinese black vinegar", "quantity": 1, "unit": "tbsp"},
    {"item": "Sugar", "quantity": 1, "unit": "tbsp"},
    {"item": "Cornstarch", "quantity": 1, "unit": "tsp"},
    {"item": "Garlic", "quantity": 4, "unit": "cloves", "notes": "Minced"},
    {"item": "Ginger", "quantity": 1, "unit": "inch", "notes": "Minced"},
    {"item": "Scallions", "quantity": 3, "unit": "stalks", "notes": "Cut into 1-inch pieces"},
    {"item": "Vegetable oil", "quantity": 3, "unit": "tbsp"}
  ],
  "instructions": [
    {"step": 1, "action": "Cut chicken thigh into 1-inch cubes", "time_min": 3},
    {"step": 2, "action": "Marinate chicken with 1 tbsp soy sauce and cornstarch for 10 minutes", "time_min": 10},
    {"step": 3, "action": "Mix sauce: 1 tbsp soy sauce, black vinegar, sugar, and 2 tbsp water", "time_min": 2},
    {"step": 4, "action": "Heat wok on high heat, add 2 tbsp oil", "time_min": 1},
    {"step": 5, "action": "Stir-fry chicken until cooked (5 min), set aside", "time_min": 5},
    {"step": 6, "action": "In same wok, add 1 tbsp oil, dried chilies, and Sichuan peppercorns. Fry for 30 seconds.", "time_min": 1},
    {"step": 7, "action": "Add garlic, ginger, and white parts of scallions. Stir-fry for 30 seconds.", "time_min": 1},
    {"step": 8, "action": "Return chicken to wok, add sauce and peanuts. Toss for 2 minutes.", "time_min": 2},
    {"step": 9, "action": "Add green parts of scallions, toss, and serve immediately.", "time_min": 1}
  ],
  "video_url": "https://www.youtube.com/watch?v=Qbypzy9jjGo",
  "video_channel": "Chinese Cooking Demystified",
  "tags": ["authentic", "spicy", "stir-fry", "chicken", "peanuts", "numbing"],
  "dietary_info": {
    "vegetarian": false,
    "vegan": false,
    "gluten_free": false,
    "contains_nuts": true,
    "halal_adaptable": true
  },
  "cost_estimate_sgd": 12.50
}
```

---

## Appendix B: API Contract Examples

### POST /api/plan-meal - Complete Example

**Request:**
```json
{
  "query": "Plan dinner for tonight, something with chicken, not too spicy, under 45 minutes",
  "preferences": {
    "cuisine": ["sichuan", "cantonese"],
    "spice_level": "medium",
    "cooking_time_max": 45,
    "difficulty_max": 2,
    "dietary_restrictions": ["no_seafood"],
    "servings": 4
  },
  "num_suggestions": 3
}
```

**Response:**
```json
{
  "success": true,
  "query": "Plan dinner for tonight, something with chicken, not too spicy, under 45 minutes",
  "suggestions": [
    {
      "recipe_id": "rec_012",
      "name_en": "Soy Sauce Chicken",
      "name_zh": "豉油鸡",
      "cuisine": "Cantonese",
      "description": "Tender chicken braised in soy sauce with aromatics. Simple and delicious.",
      "spice_level": 0,
      "difficulty": 1,
      "cooking_time_min": 40,
      "prep_time_min": 10,
      "servings": 4,
      "video_url": "https://youtube.com/watch?v=abc123",
      "video_channel": "Made With Lau",
      "ingredients_summary": "Chicken, soy sauce, ginger, scallions, sugar",
      "tags": ["easy", "mild", "braised", "cantonese"],
      "cost_estimate_sgd": 10.50,
      "why_suggested": "Matches your preference for mild chicken dishes under 45 minutes. Cantonese style is less spicy than Sichuan."
    },
    {
      "recipe_id": "rec_023",
      "name_en": "Hainanese Chicken Rice",
      "name_zh": "海南鸡饭",
      "cuisine": "Hainanese",
      "description": "Singapore's national dish. Poached chicken with fragrant rice.",
      "spice_level": 0,
      "difficulty": 2,
      "cooking_time_min": 45,
      "prep_time_min": 15,
      "servings": 4,
      "video_url": "https://youtube.com/watch?v=def456",
      "cost_estimate_sgd": 12.00,
      "why_suggested": "Popular in Singapore, familiar to your helper. Very mild and easy to cook."
    }
  ],
  "alternatives_count": 12,
  "processing_time_ms": 2340,
  "timestamp": "2026-03-04T15:30:45Z"
}
```

---

**END OF PRD**

---

## Next Steps for Implementation

This PRD is now ready to hand to:

1. **Figma AI / v0.dev / Vercel v0:**
   - Give them the UI mockups section
   - Ask for responsive React components
   - Reference the design tokens

2. **Claude / ChatGPT for Backend:**
   - Give them the API specifications
   - Ask to implement FastAPI endpoints
   - Include the data model schema

3. **Cursor / Bolt.new for Full Stack:**
   - Give them entire PRD
   - Ask to scaffold complete project
   - Generate both frontend and backend

4. **Data Scraping AI:**
   - Give them Appendix A format
   - Ask to scrape 200-500 recipes
   - Format as JSON

Would you like me to:
1. Create specific prompts for each AI tool?
2. Break this into smaller implementation tasks?
3. Add more technical details to any section?
