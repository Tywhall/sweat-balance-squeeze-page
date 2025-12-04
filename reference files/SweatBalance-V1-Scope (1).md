# SWEAT BALANCE V1: CONCRETE SCOPE & FLOWS

## Overview

**Product:** A PWA (Progressive Web App) workout tracking and planning system for weekend warriors.

**User:** Active adults 25–55 who do outdoor recreation (skiing, MTB, running, climbing, paddling, etc.) on weekends and want to train smarter during the week to prevent injury and stay fresh.

**Goal:** In 4–6 weeks, launch a working MVP that:
- Captures user identity and weekend activity preferences
- Generates a simple, personalized weekly workout plan
- Allows users to log workouts in real-time (mobile, offline-first)
- Collects brief post-workout feedback
- Uses that feedback to tweak next week's plan

**Scope: V1 focuses on ONE sport** (e.g., Trail Running, Mountain Biking, or Skiing). Expand to others in V2.

---

## 1. CRITICAL SCREENS & FLOWS

### 1.1 Onboarding Flow (First-Time User)

**Duration:** 60–90 seconds | **Data Captured:** User profile, preferences, constraints

#### Screen 1: Welcome + Sport Selection
- **Title:** "What do you do on weekends?"
- **Visual:** Simple icons for each sport (bike, ski, trail running, climbing, paddling, etc.)
- **Action:** User taps ONE sport (v1 shows only one, expandable later)
  - Trail Running
  - Mountain Biking
  - Downhill Skiing
  - Trail Hiking
  - Paddling/Kayaking
  - Rock Climbing
  - *"Other"* (free text)

**Output:** `selectedSport: string`

---

#### Screen 2: Weekday Schedule
- **Title:** "How many days can you train during the week?"
- **Options:** 
  - 2 days
  - 3 days
  - 4 days
  - 5 days
- **Explanation text:** "We'll plan your workouts on these days. Rest days help you recover for weekends."

**Output:** `trainingDaysPerWeek: number`

---

#### Screen 3: Session Duration
- **Title:** "How long can each session be?"
- **Options:** 
  - 15 mins
  - 20 mins
  - 30 mins
  - 45 mins
  - 60 mins
- **Explainer:** "Shorter sessions = we focus on essentials. Longer = we add more volume."

**Output:** `sessionDurationMinutes: number`

---

#### Screen 4: Equipment Access
- **Title:** "Where do you train?"
- **Checkboxes (can select multiple):**
  - Home (bodyweight / dumbbells / bands)
  - Gym (full equipment access)
  - Outdoor (park, trails, bodyweight)
  - Mix of above

**Output:** `equipmentAccess: string[]`

---

#### Screen 5: Weak Links / Red Flags
- **Title:** "Any areas we should protect?"
- **Instruction:** "If you have a previous injury or area that feels fragile, let us know. We'll adapt your plan."
- **Body Map (simplified):** Large tappable regions
  - Knees
  - Shoulders
  - Low Back
  - Hips
  - Ankles/Achilles
  - Elbows
  - Other (text input)
- **Also show:** "No weak links" (default)

**Output:** `weakLinks: string[]`

---

#### Screen 6: Intensity Preference
- **Title:** "How fresh do you want to be for weekends?"
- **Slider (3 positions):**
  - ← **Fresh** (light weekday training, prioritize recovery)
  - **Balanced** (moderate load, good mix of fitness + recovery)
  - **Push** → (harder weekday training, more strength building)

**Output:** `intensityPreference: 'fresh' | 'balanced' | 'push'`

---

#### Screen 7: Personal Details (Optional but encouraged)
- **Title:** "Tell us a bit about you"
- **Fields:**
  - Age
  - M / F / Other (improves exercise selection accuracy)
  - Fitness level: Beginner / Intermediate / Advanced
- **Button:** "Continue without sharing" (skip optional fields)

**Output:** `age: string, gender: string, fitnessLevel: string`

---

#### Screen 8: Confirmation & First Plan Generation
- **Title:** "Generating your first week…"
- **Loading state:** Brief animation (2–3 sec)
- **Then:** "Here's your first week!"
- **Shows:** 
  - Weekly grid with all workout days marked
  - Weekend activity marked in a different color
  - Simple CTA: "View Today's Workout" or "View Full Week"

**Output:** Triggers first plan generation (backend call to LLM or template engine)

---

### 1.2 Weekly Plan View (Post-Onboarding)

**URL:** `/week` or `/dashboard`  
**User can see:** Current week and next week (preview)

#### Layout:
```
[Sweat Balance Logo]  [Profile Icon]

"Your Week - Dec 2-8, 2025"

Monday          Tuesday        Wednesday
Strength        Mobility       Upper + Shoulders
Lower + Knees   Core           12 min
23 min          18 min         

Saturday        ← Highlighted in different color
Trail Run
"Your big day"

[< Previous]  [Next >]

[Logged] [Upcoming] [Completed] ← Tabs
```

**Key Features:**
- Large, thumb-friendly workout tiles
- Color-coding: Gray (future), Blue (today), Green (completed), Red (missed)
- Tap to expand and see full workout details
- Swipe left/right to change weeks
- Small summary at bottom:
  - "3 workouts logged this week"
  - "Ready for Saturday!"

---

### 1.3 Today's Workout Screen (Core Interaction)

**URL:** `/today` or `/workout/start`  
**When user lands here:** They tapped "Start Today's Workout" or opened app on a training day

#### Pre-Workout (Not Yet Started)
```
[Back]                    [Menu]

Trail Running Strength
Lower Body + Knee Protection

Est. Duration: 24 min
Last done: Nov 28 (you did 5 squats instead of 6)

[Big Green Button: START WORKOUT]

Or scroll to see:
- Full exercise list
- YouTube links to form videos
- Alt exercises if needed
```

**Key Features:**
- Large CTA button (minimum 48x48 px touch target)
- Shows *last performance* (if repeat workout)
- Shows estimated duration clearly
- cache exercisedb from github to show form videos. Include this database in the app, https://github.com/Tywhall/exercisedb-SB

---

#### During Workout (Main Logging Interface)

**Flow for each exercise:**

```
[Back]  [Exit Workout]

Exercise 1 of 5
Goblet Squat (2/3 sets remaining)

[Video thumb] [← Scroll for form cue]

Weight (lbs):  [150] +/- buttons
Reps Done:     [12]  +/- buttons
RPE / How Hard: [7] (1-10 slider)

[✓ Log This Set]

Or:
[Skip Exercise]
[Swap to Alt Exercise]
[Rest Timer: 60 sec] (auto-starts)
```

**Key UX Points:**
- One exercise per screen (mobile-first)
- Large +/– buttons for weight/reps (not keyboard input)
- Default weight/reps from *last time* (pre-filled)
- Red/yellow warnings if drastically different from history
  - "You did 10 reps last time, logging 2—sure?"
- RPE slider (1–10) for intensity feedback
- Simple "How did that feel?" options:
  - Easy
  - Just Right
  - Hard
  - Pain (avoid this)

---

#### Post-Workout Feedback

```
[Back]

Workout Complete! 🎉
Trail Run Strength - 26 min

[Summary]
- 5 exercises completed
- Highest lift: Goblet Squat 160 lbs
- Took 26 min (target: 24 min)

Quick Feedback:
"How was this workout overall?"
[ ] Too easy
[ ] Just right
[ ] Too hard
[ ] Pain/discomfort (where?)

"Will you make your weekend activity as planned?"
[ ] Yes, going for it
[ ] Maybe
[ ] No (reason?)

[SUBMIT FEEDBACK]

Then:
"Thanks! We'll adjust next week's plan based on this."
[View This Week]  [Back to Home]
```

**Key UX Points:**
- Celebrate completion (emoji, visual feedback)
- 4–5 quick questions only (no friction)
- Body-part selector for pain reports
- Feedback used for:
  - Weekly plan tweaks
  - Load management
  - Injury prevention alerts

---

### 1.4 Workout History / Progress View

**URL:** `/history` or `/workouts`

```
[Back]

Past Workouts

November 2025
─────────────
Mon Nov 25   Strength - Lower
Goblet Squat: 150 x 12, 150 x 10
Leg Press: 250 x 8
Feedback: "Just right"

Wed Nov 27   Mobility - Core
Cat-Cow: 3 min
Dead Bug: 2 x 10
Feedback: "Easy, felt good"

[Scroll for more]
```

**Key Features:**
- Reverse chronological order
- Grouped by month
- Tap to expand and see full details
- Show feedback badge (easy/just right/hard/pain)
- Simple volume trends (e.g., "Squat weight trending +5 lbs over 4 weeks")

---

## 2. BACKEND / LOGIC FLOWS

### 2.1 Plan Generation (Week-to-Week)

**Trigger:** Every Sunday at 6 PM (user timezone)

**Input Data:**
- User preferences (from onboarding)
- Previous week's feedback (logged workouts, ratings)
- Weak links and injury flags
- Current week's planned weekday activity

**Process:**

1. **Load Base Template**  
   Fetch sport-specific base template (e.g., "Trail Running Strength Week 1–4")  
   Template includes:
   - 3–4 workout structures (Lower, Upper, Mobility)
   - Exercise lists per category
   - Rep ranges, volumes, rest times
   
2. **Apply Constraints**  
   - Filter exercises: Remove those targeting weak links unless safe alternative exists
   - Adjust duration: If user has 20 min, remove exercises to fit
   - Equipment: Filter exercises to available equipment

3. **Personalization (LLM or Rule-Based)**  
   - If first week: Send to LLM with user data to generate bespoke plan
   - If repeat week: Apply small tweaks based on feedback:
     - Last week "hard" → reduce volume 10% this week
     - Last week "easy" → increase weight/volume 5%
     - User reported knee pain → sub safer quad exercises
   - Output: Detailed weekly plan JSON

4. **Store & Serve**  
   - Save plan to DB (link to user + week number)
   - Cache on client (IndexedDB) for offline access
   - Show plan in `/week` view

---

### 2.2 Feedback Loop Integration

**After each workout, user submits:**
```json
{
  "workoutId": "UUID",
  "date": "2025-12-02",
  "exercise": "Goblet Squat",
  "setsCompleted": 3,
  "repsPerSet": [12, 12, 10],
  "weight": 150,
  "rpe": 7,
  "overallFeeling": "just_right",
  "pain": null,
  "weekendActivityPrepared": "yes",
  "notes": "felt strong"
}
```

**What We Do With It:**
- Store in DB (linked to user + week)
- Aggregate into weekly summary:
  - Avg RPE
  - Avg difficulty rating
  - Any pain flags
  - Success prep for weekend activity
- Feed into next week's LLM prompt if personalizing:
  - "User found last week too hard; adjust volumes down"
  - "User had knee discomfort Wed; avoid deep squats"

---

### 2.3 Offline Persistence (PWA)

**On App Launch:**
1. Check if online
2. If offline:
   - Serve cached weekly plan (IndexedDB)
   - Serve cached exercise library
   - Allow full logging experience
3. When online:
   - Queue any pending workout logs to DB
   - Sync latest plan if user refreshed
   - Show sync status badge

**Service Worker Caching:**
- Cache: All HTML, CSS, JS, exercise images/videos (selectively)
- Network first for: Updated user data, real-time feedback
- Cache first for: Exercise library, workout templates

---

## 3. DATA MODEL (Simplified)

```
User
├── id (UUID)
├── email
├── selectedSport
├── trainingDaysPerWeek
├── sessionDurationMinutes
├── equipmentAccess (array)
├── weakLinks (array)
├── intensityPreference ('fresh'|'balanced'|'push')
├── age, gender, fitnessLevel
└── createdAt

WorkoutPlan (Weekly)
├── id (UUID)
├── userId
├── weekOf (date)
├── workouts (array of Workout objects)
├── generatedAt
└── version (if regenerated)

Workout (Single Session)
├── id (UUID)
├── planId
├── date
├── day (Mon-Fri, Sat, etc)
├── name ("Lower Strength + Knees")
├── estimatedDurationMin
├── exercises (array of Exercise objects)
└── notes

Exercise
├── id (UUID)
├── name
├── description
├── targetMuscles (array)
├── equipment (array)
├── category ('strength'|'mobility'|'cardio')
├── videoUrl
├── altExercises (array, in case of substitution)
└── notes

CompletedWorkoutLog
├── id (UUID)
├── userId
├── workoutId
├── date
├── exercises (array of logged set data)
├── overallRPE
├── overallFeeling ('easy'|'just_right'|'hard'|'pain')
├── painLocations (array)
├── weekendActivityPrepared ('yes'|'maybe'|'no')
├── durationMinutes (actual)
├── feedback (user notes)
└── createdAt
```

---

## 4. MINIMAL FEATURE SET (V1)

### Must Have (MVP):
✓ Onboarding flow (8 screens)  
✓ Weekly plan view (shows next week, current week)  
✓ "Today" workout screen  
✓ Workout logging (sets, reps, weight, RPE)  
✓ Post-workout quick feedback (4–5 questions)  
✓ Offline persistence (PWA install-able)  
✓ Basic plan generation (template + simple tweaks)  
✓ Workout history view  
✓ Mobile-responsive design (thumb-friendly buttons, readable on 5" screens)  

### Nice to Have (V1.5, Post-Launch):
◐ Exercise video library with cached demos  
◐ Form cues and common mistakes  
◐ Body-part pain tracker  
◐ Weekly summary dashboard  
◐ "Protected weeks" counter  
◐ Export workout to PDF  

### Don't Build Yet (V2+):
✗ Social features, leaderboards  
✗ Calorie/nutrition tracking  
✗ Wearable integration (Fitbit, Apple Watch)  
✗ Video upload for form check  
✗ Advanced periodization (peaking, tapering)  
✗ Multi-sport profiles  

---

## 5. TECHNICAL STACK RECOMMENDATIONS

**Frontend:**
- React or Next.js (with App Router for PWA-friendly routing) - Use Next.js
- TailwindCSS for styling
- PWA libraries: Workbox (Service Workers), IndexedDB for cache
- Responsive design mobile-first
- Use Hero icons for icons. Be consistent with the icons used in the app.

**Backend:**
- Node.js / Express or Next.js API routes - use Node.js / Express
- PostgreSQL or Supabase for DB - Use Supabase
- LLM integration: OpenAI API (GPT-4o-mini for cost) or Claude - Use OpenAI API

**Deployment:**
- Vercel (full-stack, PWA-friendly, fast) - use this one
- Or: AWS Amplify + RDS

**Offline-First Architecture:**
- Client-side: React Query + IndexedDB
- Sync: Simple polling or real-time Supabase subscriptions when online

---

## 6. V1 DEVELOPMENT PHASES (Realistic Timeline)

**Phase 1: Core (Weeks 1–2)**
- Onboarding screens
- Weekly plan display
- Basic workouts logging UI

**Phase 2: Backend & Data (Weeks 2–3)**
- User model + auth (simple email + magic link)
- Workout template system
- DB schema
- Plan generation endpoint

**Phase 3: Offline & Polish (Weeks 3–4)**
- Service Worker setup
- IndexedDB caching
- PWA install prompt
- Mobile testing & responsive fixes

**Phase 4: LLM Integration (Weeks 4–5)**
- Plan generation endpoint (call LLM with user context)
- Feedback loop integration

**Phase 5: Testing & Launch (Week 5–6)**
- Bug fixes, UX polish
- Beta testing with 20–30 weekend warriors
- Iterate based on feedback
- Soft launch (Canada + close community)

---

## 7. KEY SUCCESS METRICS (Day 1)

**Track These From Launch:**
1. **Day 30 Retention:** Target 15%+ (industry avg 8–12%)
2. **Signup → First Workout:** % completing first log (target 50%+)
3. **Weekly Retention:** % who log 2+ workouts/week (target 60%+)
4. **Feedback Quality:** % submitting post-workout feedback (target 70%+)
5. **Churn:** MRR retention (target 95%+, i.e., 5% monthly churn)

**If Any Metric Falters:**
- Day 30 retention < 10%: Product gaps (simplify, improve UX)
- Churn > 8%/mo: Retention issue (improve plan personalization, add streaks/gamification)
- Low feedback rate: Feedback flow too complex (simplify)

---

## 8. POST-LAUNCH ROADMAP (Months 2–6)

**Month 2 (Feature Enhancements):**
- Add ski-specific workout templates
- Introduce "Protected Weeks" counter
- Simple progress chart (weight trends)

**Month 3 (Growth):**
- Gym + MTB profiles
- Blog content for SEO ("Prevent ACL injuries for skiers")
- Email nurture campaign (weekly digest)

**Month 4 (Retention):**
- Community feature (anon workout shares)
- Challenges (e.g., "Log 3x this week, get streak")
- A/B test pricing ($9.99 vs $14.99 vs $19.99)

**Month 5 (Monetization):**
- Launch paid tier ($14.99/mo)
- Free tier limits to 1 plan/week (or 7-day trial)
- Annual discount option (save 20%)

**Month 6 (Scale):**
- Paid ads test (Facebook/TikTok)
- Partner outreach (PT clinics, gyms, outdoor clubs)
- Consider native app wrapper (React Native)

---

## 9. SUCCESS CRITERIA FOR V1

✓ **MVP Shipped:** Fully usable PWA, installable on home screen  
✓ **User Adoption:** 100–200 signups in first month (organic, community)  
✓ **Retention Proof:** 15%+ of users log 2+ workouts/week by week 4  
✓ **Feedback Loop Working:** Plan tweaks based on logged feedback are obvious to users  
✓ **Zero Data Loss:** No user workouts lost, offline sync works flawlessly  
✓ **NPS ≥ 40:** Early users would recommend to a friend  

---

## 10. COMMON PITFALLS TO AVOID

❌ **Overbuilding:** Don't add social, nutrition, wearables in V1  
❌ **LLM Over-Reliance:** Use templates + rules first; LLM for personalization edge cases  
❌ **Complex Onboarding:** Keep it to 60 sec; extras can be optional  
❌ **Forgetting Offline:** People *will* train in gyms without WiFi  
❌ **Ignoring Retention:** Your unit economics depend on 25%+ annual retention; nail it early  
❌ **Slow Time-to-Log:** If workout logging takes >2 min, users won't do it  
❌ **No Feedback Loop:** If you're not collecting weekly feedback, you can't improve plans  

---

## SUMMARY

**Sweat Balance V1** is a focused, weekend-warrior-specific PWA that:

1. **Captures** who they are and what they do
2. **Generates** a simple, personalized weekly plan in <60 sec
3. **Allows** dead-simple workout logging (offline-first)
4. **Collects** brief feedback to adapt next week
5. **Tracks** progress and keeps them injury-free

Build it mobile-first, keep the UI minimal, obsess over retention, and iterate based on real user feedback. The niche is real, the unit economics work, and early users will tell you what to build next.

**Ship by Week 6. Learn from users by Month 2. Scale by Month 6.**
