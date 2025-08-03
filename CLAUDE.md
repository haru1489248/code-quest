# CLAUDE.md - RPG Learning Platform for Engineers

## 🎯 Project Overview

A gamified learning platform for engineers that combines RPG elements with skill development. Users progress through job-specific roadmaps, level up their skills, and advance their engineering careers through an RPG-inspired interface.

## 🛠️ Tech Stack

- **Frontend**: Next.js with TipTap editor
- **Authentication**: NextAuth.js (Google OAuth integration)
- **Database**: Vercel Postgres (initial) → PostgreSQL (AWS production)
- **ORM**: Prisma
- **Styling**: Tailwind CSS (admin panel)
- **Deployment**: Vercel (ISR, Preview Deployment)
- **Integration**: GitHub API for skill assessment

## 🎮 Core Features

### Job Class System (Initial Selection)

**Main Classes**
-  Frontend Engineer
-  Backend Engineer
-  DevOps Engineer
-  Security Engineer
-  Data Scientist/AI Engineer
-  Mobile App Engineer

**Job Change System**
- Job transfer available at Level 30
- Partial skill inheritance from previous job
-  class (Full-stack) unlocked after experiencing all classes

### ⭐ Skill Tree Design

**Frontend Engineer Example**
```
Lv1: HTML Basics → CSS Basics → JavaScript Basics
Lv10: React Basics → State Management
Lv20: TypeScript → Next.js
Lv30: Performance Optimization → PWA
Lv40: Micro Frontend → [Hidden Skills]
```

**Hidden Skills**
- "Bug Hunter": Discover specific complex bugs
- "Code Artist": High ratings for beautiful code
- "Speed Coder": Clear time-limited challenges

### 📊 GitHub Integration & Level Assessment

**Automatic Skill Detection**
- Commit frequency & history analysis
- Programming languages & frameworks used
- Repository stars & forks
- Pull request quality
- Code review participation
- Issue resolution count

**Initial Level Setting**
- Appropriate starting point based on GitHub analysis
- "Skip Test" option for known areas
- Start slightly below actual skill level for motivation

### 🗺️ User-Proposed Roadmap System

**Proposal Features**
- Add skills to existing roadmaps
- Create entirely new roadmaps
- Community voting for adoption

**Quality Control**
- Review system by advanced users
- Feedback from users who attempted the roadmap
- AI-powered content validation

## 🎮 RPG Elements

### Player Status Screen
```
[Player Name] Lv.25 Frontend Engineer

HP: 1250/1250 (Learning Stamina)
MP: 850/1000 (Concentration)
EXP: 15,420/20,000

【Base Stats】
- Coding Power: 78
- Debugging Power: 65
- Design Power: 45
- Communication Power: 52

【Equipped Skills】
- React (Lv.15)
- TypeScript (Lv.12)
- Git (Lv.20)
```

### Level Up Effects
- Full-screen light animation
- Special animation for new skill unlocks
- Classic RPG-style level up sound
- Status point allocation

### 🏆 Badge & Title System

**Learning Badges**
- "Daily Learning Demon": 30 consecutive login days
- "Night Owl Studier": 100 hours of late-night learning
- "Morning Activity Master": 50 hours of early morning learning

**Technical Badges**
- "Bug Exterminator": Clear 100 debugging problems
- "Performance Maniac": Complete all optimization challenges
- "Security Guard": Obtain security-related certifications

### 🎯 Quest System

**Daily Quests**
- "Today's Learning": Study for 30 minutes
- "Code Review": Comment on others' code
- "Q&A Helper": Answer one community question

**Main Quests**
- Story-driven learning for each job class
- Final boss (large-scale project challenge)

**Side Quests**
- User-proposed learning challenges
- Limited-time events

## 🤝 Social Features

### Guild/Team System
- Team formation with learners sharing the same goals
- Progress sharing within teams
- Collaborative challenges and hackathon events

### Mentor System
- Advanced users support beginners
- Points earned for mentoring
- Review functionality

## 🔧 Database Design

```sql
-- User Profile
users (id, github_id, level, job_class, total_exp, hp, mp)

-- Skill Progress
user_skills (user_id, skill_id, level, exp)

-- Roadmaps
roadmaps (id, job_class, created_by_user, is_official)
roadmap_skills (roadmap_id, skill_id, order, required_level)

-- User Proposals
proposed_skills (user_id, skill_name, description, votes, status)

-- Badges & Achievements
user_badges (user_id, badge_id, earned_at)
badges (id, name, description, icon, rarity)

-- Social Features
guilds (id, name, description, leader_id)
guild_members (guild_id, user_id, role, joined_at)
```

## 🎨 UI/UX Requirements

### Design Themes
- Retro RPG-inspired interface
- Retro pixel art aesthetics
- Smooth animations and transitions
- Mobile-responsive design

### Key Screens
- Character selection (job class)
- Skill tree visualization
- Progress dashboard
- Quest log
- Guild management
- Leaderboards

## 🚀 MVP Features (Phase 1)

1. User authentication with GitHub
2. Job class selection
3. Basic skill tree (3 main classes)
4. GitHub skill assessment
5. Simple quest system
6. Level up mechanics
7. Basic badge system

## 🔮 Future Enhancements (Phase 2+)

1. Advanced social features
2. Mentor system
3. User-generated content
4. Mobile app (React Native)
5. AI-powered personalized learning
6. Corporate training plans
7. Certification partnerships

## 🎯 Success Metrics

- User engagement (daily active users)
- Learning completion rates
- Skill progression speed
- Community contribution (proposals, reviews)
- GitHub integration effectiveness
- User retention rates

## 🔐 Security & Privacy

- Secure GitHub token handling
- User data encryption
- GDPR compliance
- Rate limiting for API calls
- Content moderation system

---

**Note**: This platform aims to make learning engineering skills as engaging as playing an RPG game, while providing real value for career development.

## 📚 Additional Documentation

- **Coding Guidelines**: See [`./docs/CODING_GUIDELINES.md`](./docs/CODING_GUIDELINES.md)

## 🔍 Code Review Standards

### Security Review Criteria
- **Authentication & Authorization**: All user data must be properly secured
- **Input Validation**: Sanitize all user inputs to prevent XSS/SQL injection
- **API Security**: Rate limiting, proper CORS configuration
- **Token Management**: Secure storage and rotation of GitHub/OAuth tokens
- **Data Privacy**: GDPR compliance, proper data encryption

### Performance Standards
- **Page Load Time**: < 3 seconds for initial load
- **Animation Performance**: 60 FPS for all game animations
- **Database Queries**: Optimize N+1 queries, use proper indexing
- **Bundle Size**: Keep JavaScript bundles under 200KB (gzipped)
- **Image Optimization**: Use Next.js Image component, lazy loading

### Code Quality Requirements
- **TypeScript**: Strict mode enabled, no `any` types without justification
- **React Best Practices**: Proper hooks usage, memoization where needed
- **Testing**: Minimum 80% code coverage for critical features
- **Documentation**: JSDoc comments for all public APIs
- **Error Handling**: Comprehensive error boundaries and logging

### Review Focus Areas
1. **Game Mechanics Implementation**
   - Skill progression calculations
   - Experience and level algorithms
   - Quest completion logic
   - Badge awarding system

2. **User Experience**
   - Smooth transitions and animations
   - Mobile responsiveness
   - Accessibility (WCAG 2.1 AA compliance)
   - Loading states and error messages

3. **Integration Points**
   - GitHub API usage optimization
   - Database transaction handling
   - Real-time updates (WebSockets/SSE)
   - Third-party service integration
