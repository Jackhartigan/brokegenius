# V2 Premium Features Integration Guide

## Features Implemented:
1. ✅ Challenge Zone (New Tab)
2. ✅ AI Insights Coach (Modal Component)
3. ✅ Streak System (Tracking Service + Display Component)
4. ✅ Supabase Integration (for future sync)

---

## Integration Steps:

### 1. Add AI Insights Button to Dashboard (DashboardScreen.tsx)

**Location**: Add after the Cash Score card (around line 700-800)

**Import at top of file**:
```typescript
import AIInsightsModal from "../../components/AIInsightsModal";
import { streakService, StreakData } from "../../services/streakService";
import StreakDisplay from "../../components/StreakDisplay";
```

**Add state in component** (around line 420):
```typescript
const [showAIInsights, setShowAIInsights] = useState(false);
const [streakData, setStreakData] = useState<StreakData | null>(null);
```

**Load streaks on mount** (add to useEffect around line 455):
```typescript
// Load streaks
streakService.getCurrentStreaks().then(setStreakData);

// Update daily log streak when expenses change
const todayExpenses = getTodaysExpenses();
if (todayExpenses.length > 0) {
  streakService.updateDailyLogStreak(true).then(setStreakData);
}
```

**Add button in JSX** (after Cash Score section):
```typescript
{/* AI Insights Button */}
<PremiumCard style={{ marginHorizontal: 24, marginBottom: 16 }} delay={400}>
  <Pressable
    onPress={() => setShowAIInsights(true)}
    style={{
      backgroundColor: '#FFFFFF',
      borderRadius: 16,
      padding: 20,
      flexDirection: 'row',
      alignItems: 'center',
      justifyContent: 'space-between',
    }}
  >
    <View style={{ flexDirection: 'row', alignItems: 'center', flex: 1 }}>
      <View
        style={{
          width: 48,
          height: 48,
          borderRadius: 24,
          backgroundColor: '#FF8C3215',
          alignItems: 'center',
          justifyContent: 'center',
          marginRight: 16,
        }}
      >
        <Ionicons name="sparkles" size={24} color="#FF8C32" />
      </View>
      <View style={{ flex: 1 }}>
        <Text style={{ fontSize: 16, fontWeight: 'bold', color: '#000000', marginBottom: 4 }}>
          Ask Brotinius
        </Text>
        <Text style={{ fontSize: 13, color: '#6B7280' }}>
          Why am I overspending?
        </Text>
      </View>
    </View>
    <Ionicons name="chevron-forward" size={20} color="#9CA3AF" />
  </Pressable>
</PremiumCard>

{/* Streak Display */}
{streakData && (
  <View style={{ paddingHorizontal: 24, marginBottom: 16 }}>
    <Text style={{ fontSize: 18, fontWeight: 'bold', color: '#000000', marginBottom: 12 }}>
      Your Streaks 🔥
    </Text>
    <View style={{ flexDirection: 'row', gap: 12 }}>
      <View style={{ flex: 1 }}>
        <StreakDisplay
          type="daily"
          count={streakData.dailyLogStreak}
          label="Day Streak"
        />
      </View>
      <View style={{ flex: 1 }}>
        <StreakDisplay
          type="budget"
          count={streakData.underBudgetStreak}
          label="Budget Streak"
        />
      </View>
    </View>
  </View>
)}

{/* AI Insights Modal */}
<AIInsightsModal
  visible={showAIInsights}
  onClose={() => setShowAIInsights(false)}
  weeklyBudget={monthlyBudget || (user?.income || 0)}
  roastStyle={user?.roastStyle || 'sarcastic'}
/>
```

---

### 2. Add AI Insights Button to Weekly Recap (WeeklyRecapScreen.tsx)

**Import at top**:
```typescript
import AIInsightsModal from "../../components/AIInsightsModal";
```

**Add state**:
```typescript
const [showAIInsights, setShowAIInsights] = useState(false);
```

**Add button** (after the header, before the recap content):
```typescript
{/* AI Insights Button */}
<TouchableOpacity
  onPress={() => setShowAIInsights(true)}
  style={{
    backgroundColor: '#FF8C32',
    marginHorizontal: 24,
    marginBottom: 20,
    paddingVertical: 16,
    paddingHorizontal: 20,
    borderRadius: 12,
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'center',
    shadowColor: '#FF8C32',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.3,
    shadowRadius: 8,
    elevation: 6,
  }}
  activeOpacity={0.8}
>
  <Ionicons name="sparkles" size={20} color="#FFFFFF" style={{ marginRight: 8 }} />
  <Text style={{ color: '#FFFFFF', fontSize: 16, fontWeight: '600' }}>
    Ask Brotinius About This Week
  </Text>
</TouchableOpacity>

{/* AI Insights Modal */}
<AIInsightsModal
  visible={showAIInsights}
  onClose={() => setShowAIInsights(false)}
  weeklyBudget={monthlyBudget || (user?.income || 0)}
  roastStyle={user?.roastStyle || 'sarcastic'}
/>
```

---

### 3. Add Streaks to Badges Screen (BadgesScreen.tsx)

**Import at top**:
```typescript
import { streakService, StreakData } from "../../services/streakService";
import StreakDisplay from "../../components/StreakDisplay";
```

**Add state**:
```typescript
const [streakData, setStreakData] = useState<StreakData | null>(null);
```

**Load on mount**:
```typescript
React.useEffect(() => {
  streakService.getCurrentStreaks().then(setStreakData);
}, []);
```

**Add section** (before badges grid):
```typescript
{/* Streaks Section */}
{streakData && (
  <View style={{ paddingHorizontal: 24, marginBottom: 24 }}>
    <Text style={{ fontSize: 20, fontWeight: 'bold', color: '#000000', marginBottom: 16 }}>
      Your Streaks 🔥
    </Text>
    <View style={{ flexDirection: 'row', gap: 12, marginBottom: 12 }}>
      <View style={{ flex: 1 }}>
        <StreakDisplay
          type="daily"
          count={streakData.dailyLogStreak}
          label="Daily Logging"
        />
      </View>
      <View style={{ flex: 1 }}>
        <StreakDisplay
          type="budget"
          count={streakData.underBudgetStreak}
          label="Under Budget"
        />
      </View>
    </View>
    <View style={{ flexDirection: 'row', gap: 12 }}>
      <View style={{ flex: 1 }}>
        <StreakDisplay
          type="challenge"
          count={streakData.challengeCompleteStreak}
          label="Challenges"
        />
      </View>
      <View style={{ flex: 1 }}>
        <View style={{ padding: 16, backgroundColor: '#F9FAFB', borderRadius: 16, alignItems: 'center' }}>
          <Text style={{ fontSize: 12, fontWeight: '600', color: '#6B7280', marginBottom: 4 }}>
            Longest
          </Text>
          <Text style={{ fontSize: 24, fontWeight: 'bold', color: '#FF8C32' }}>
            {Math.max(
              streakData.longestDailyLogStreak,
              streakData.longestUnderBudgetStreak,
              streakData.longestChallengeStreak
            )}
          </Text>
          <Text style={{ fontSize: 11, color: '#9CA3AF' }}>days</Text>
        </View>
      </View>
    </View>
  </View>
)}
```

---

### 4. Environment Variables

Add to `.env`:
```
EXPO_PUBLIC_SUPABASE_URL=your_supabase_url_here
EXPO_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key_here
```

---

### 5. Supabase Database Schema (SQL)

Run this in your Supabase SQL editor:

```sql
-- Challenges Progress Table
CREATE TABLE challenges_progress (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id TEXT NOT NULL,
  challenge_id TEXT NOT NULL,
  challenge_title TEXT NOT NULL,
  challenge_description TEXT NOT NULL,
  target_metric TEXT NOT NULL,
  target_value NUMERIC NOT NULL,
  current_value NUMERIC DEFAULT 0,
  progress_percentage NUMERIC DEFAULT 0,
  is_joined BOOLEAN DEFAULT FALSE,
  is_completed BOOLEAN DEFAULT FALSE,
  started_at TIMESTAMPTZ,
  completed_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Insights History Table
CREATE TABLE insights_history (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id TEXT NOT NULL,
  insight_type TEXT NOT NULL, -- 'roast', 'tip', 'analysis'
  content TEXT NOT NULL,
  total_spent NUMERIC NOT NULL,
  top_category TEXT NOT NULL,
  overspend_amount NUMERIC DEFAULT 0,
  change_vs_previous NUMERIC DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Streaks Table
CREATE TABLE streaks (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id TEXT NOT NULL,
  streak_type TEXT NOT NULL, -- 'daily_log', 'under_budget', 'challenge_complete'
  current_streak INTEGER DEFAULT 0,
  longest_streak INTEGER DEFAULT 0,
  last_streak_date DATE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, streak_type)
);

-- Create indexes
CREATE INDEX idx_challenges_user_id ON challenges_progress(user_id);
CREATE INDEX idx_insights_user_id ON insights_history(user_id);
CREATE INDEX idx_streaks_user_id ON streaks(user_id);
```

---

## Testing Checklist:

### Challenge Zone:
- [ ] Navigate to "Challenges" tab
- [ ] See 5 auto-generated challenges
- [ ] Progress bars update correctly
- [ ] "Join Challenge" button works
- [ ] "Mark Complete" appears when progress = 100%
- [ ] Completed count updates in summary card

### AI Insights:
- [ ] Tap "Ask Brotinius" on Dashboard
- [ ] Modal opens with loading state
- [ ] See 2-3 roasts, tip, and summary
- [ ] Tap "Ask Brotinius" on Weekly Recap
- [ ] Insights are contextual to current week

### Streaks:
- [ ] Streak displays on Dashboard
- [ ] Streak displays on Badges page
- [ ] Daily log streak increments when expense added
- [ ] Under budget streak works correctly
- [ ] Longest streak shows maximum value

---

## Files Created:

1. `/src/services/supabaseClient.ts` - Supabase client and helpers
2. `/src/services/aiInsightsService.ts` - AI insights generation
3. `/src/services/streakService.ts` - Streak tracking logic
4. `/src/components/AIInsightsModal.tsx` - AI insights modal component
5. `/src/components/StreakDisplay.tsx` - Streak display component
6. `/src/screens/main/ChallengeZoneScreen.tsx` - Challenge Zone page

## Files Modified:

1. `/src/navigation/MainNavigator.tsx` - Added Challenge Zone tab
2. `/src/navigation/types.ts` - Added ChallengeZone to MainTabParamList

## Dependencies Added:

- `@supabase/supabase-js` - For backend sync (installed via bun)

---

## Notes:

- All features work locally without Supabase (using AsyncStorage)
- Supabase integration is ready for future multi-device sync
- AI Insights uses OpenAI GPT-4o (requires API key in .env)
- Fallback insights provided if AI fails
- Streaks auto-update and reset at midnight
- All UI follows Apple × Notion × Porsche design aesthetic

**Status: ✅ All V2 features implemented and ready for integration**
