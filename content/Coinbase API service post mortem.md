Title: TO DO: Coinbase API service post mortem (draft, notes)
Date: 2025-10-27 23:00
Category: Technology
Tags: python, blogging
Slug: my-first-post
Summary: A brief summary of your post

Your content here...

THE FUNDAMENTAL PROBLEM: You Built a Production System for a Demo Assignment
What the Assignment Actually Required (10-15 hours)
Backend (4-5 hours):

Django REST API with 2-3 endpoints
Coinbase SDK integration (list accounts, get balances)
Basic error handling
Deploy to AWS

Frontend (2-3 hours):

Simple React app
Display portfolio in a table
Refresh button that calls API
Run locally with npm install && npm start

AWS Deployment (3-4 hours):

Backend on ECS/Lambda
RDS PostgreSQL
S3 + CloudFront for frontend (or just local)
CloudWatch for logs
Total: 4 services

Documentation (2-3 hours):

Architecture diagram (draw.io, 30 mins)
README with setup instructions
Brief writeup on cost/reliability/monitoring

What You Actually Built (~50+ hours)
Based on our conversation history:
Backend Architecture Research & Implementation:

❌ Day 1: 4+ hours researching API data storage patterns (fintech case studies, Coinbase/Stripe architectures)
❌ Day 1-2: Custom IqanaUser model, immutability patterns, 4-model database schema
❌ Day 2: Full 4-layer architecture (models, client singleton, types, converters, services)
❌ Day 2: Comprehensive TypedDict definitions for type safety
❌ Day 2: mypy strict configuration, 100% type coverage
❌ Day 2: 97% test coverage enforcement with pytest
❌ Day 2: Pre-commit hooks (ruff, mypy, docformatter)
❌ Day 2: CI/CD optimization research (84s→37s pipeline analysis)
❌ Day 2: Code style compliance audit (Google/Django/HackSoft style guides)
❌ Day 2-3: PR templates research (analyzing DRF, Wagtail, major Django projects)
❌ Day 3: Python dependency management research (uv vs pip vs Poetry, Docker multi-stage builds)
❌ Day 3: Decimal precision research for cryptocurrency storage
❌ Day 3: Django service class pattern research (static methods vs module functions)
❌ Day 3: pytest vs unittest research for DRF projects

You spent 60-70% of your time on RESEARCH and INFRASTRUCTURE that wasn't required.

CRITICAL MISTAKES - Ranked by Impact
1. Research Paralysis (BIGGEST MISTAKE)
What you did:

Researched fintech data architecture patterns when you needed a simple Django model
Researched dependency management strategies when requirements.txt works fine
Researched pytest vs unittest when either would pass
Researched code style compliance across 4 different style guides
Researched PR templates from major open-source projects

Why this killed you:

Each research session took 2-4 hours
You treated this like a 6-month production system, not a 4-day demo
Your Billify experience already had the answers - you didn't need research

What you should have done:

Copy patterns from Billify directly
Use simple, working solutions first
Research ONLY when blocked or after MVP works

Impact: Lost 15-20 hours on research that didn't affect the deliverable

2. Production-Grade Architecture (SECOND BIGGEST)
What you did:

Built 4-layer backend architecture (models, client, types, converters, services)
Implemented immutability patterns and append-only data
Created comprehensive type system with TypedDict definitions
Enforced 97% test coverage
Set up pre-commit hooks and CI/CD optimization

Why this killed you:

The assignment said "small API service" - they wanted 3 endpoints, not enterprise architecture
You built for scale (1000+ users) when they wanted proof-of-concept (1 user with test data)
None of this was in the evaluation criteria

What you should have done:
python# models.py - THIS IS ALL YOU NEEDED
class Portfolio(models.Model):
    user = models.ForeignKey(User)
    fetched_at = models.DateTimeField(auto_now_add=True)
    raw_data = models.JSONField()  # Store Coinbase response directly

class Wallet(models.Model):
    portfolio = models.ForeignKey(Portfolio)
    currency = models.CharField(max_length=10)
    balance = models.DecimalField(max_digits=36, decimal_places=18)

# views.py - 2 ENDPOINTS, DONE
@api_view(['GET'])
def get_portfolio(request):
    client = RESTClient(api_key=..., api_secret=...)
    accounts = client.get_accounts()
    # Save to DB, return JSON

@api_view(['POST'])
def refresh_portfolio(request):
    # Same as above but force refresh
```

**Impact:** Lost 20-25 hours on over-engineering

---

### **3. Perfection Over Progress**

**What you did:**
- Spent hours on CI optimization (84s→76s) when you hadn't deployed anything
- Fixed code style violations before building API endpoints
- Researched PR templates before having PRs worth making
- Configured comprehensive tooling before having code to tool

**Why this killed you:**
- You optimized infrastructure that didn't need to exist yet
- You polished code that wasn't finished
- You prepared for scale you didn't have

**What you should have done:**
- **Day 1**: Working Django API (ugly but functional)
- **Day 2**: Deploy to AWS (manually if needed)
- **Day 3**: React frontend
- **Day 4**: Polish and document

**Impact:** Lost 10-15 hours on premature optimization

---

### **4. Wrong Sequencing**

**Actual timeline:**
- Day 1-2: Backend architecture (models, services, types, converters)
- Day 2-3: Tooling and infrastructure (pre-commit, CI, style guides)
- Day 3: Still no deployed backend, no frontend, no AWS
- Day 4: Exhausted, requesting extension

**Correct timeline should have been:**
- **Day 1 (Sat morning)**: Basic Django project + Coinbase integration (4 hours)
- **Day 1 (Sat afternoon)**: Deploy backend to AWS manually (4 hours)
- **Day 2 (Sun morning)**: React frontend displaying data (3 hours)
- **Day 2 (Sun afternoon)**: Polish, document, test (3 hours)
- **Sunday evening**: Submit with time to spare

**Why this killed you:**
- You never had anything VISIBLE to show
- Backend architecture means nothing if it's not deployed
- They wanted to see END-TO-END thinking, not backend perfection

**What the assignment said:**
> "This challenge is designed to evaluate your ability to work **end-to-end**: backend, frontend and cloud thinking."

You built ONE end (backend) to production quality but never touched the other two.

**Impact:** Complete project failure

---

### **5. Missing the "Show, Don't Tell" Principle**

**What Iqana wanted to see:**
- Working URL they can visit: `https://christian-iqana.aws.com`
- Click refresh button → portfolio updates
- Read architecture doc → understand your decisions
- **Total impression time: 10 minutes**

**What you gave them:**
- Well-architected backend code in GitHub
- No deployment
- No frontend
- No way to experience your work
- **Total impression: "Didn't finish"**

**The harsh truth:**
- A simple working demo beats perfect undeployed code 100% of the time
- They can't evaluate architecture they can't see running
- "97% test coverage" means nothing if the system isn't live

---

## **WHAT YOU SHOULD DO DIFFERENTLY NEXT TIME**

### **1. Timebox Everything**

Create a hard schedule:
```
Day 1 Morning (4h): Backend MVP
Day 1 Afternoon (4h): AWS Deployment
Day 2 Morning (3h): Frontend MVP
Day 2 Afternoon (3h): Documentation + Polish
Total: 14 hours
If something takes longer than allocated time, SHIP IT UGLY AND MOVE ON.
2. Always Deploy First
New rule: Nothing is real until it's deployed and you have a URL.

Don't optimize local code
Don't perfect the architecture
Don't add features
GET IT LIVE, THEN IMPROVE

3. Copy-Paste From Your Past Projects
You've built production systems. You know patterns that work.
Stop researching. Your Billify code is the answer to 90% of questions.

Need Django models? Copy Billify pattern
Need REST endpoints? Copy Billify pattern
Need AWS deployment? Copy Billify pattern
Need to integrate external API? Copy Billify pattern

4. Recognize Take-Home vs Production
Take-home assignment priorities:

WORKS - Can they see it running?
END-TO-END - Does it show full-stack thinking?
DOCUMENTED - Can they understand your decisions?
CLEAN - Is the code readable?

Production system priorities:

SCALABLE - Handles growth
TESTED - 90%+ coverage
MONITORED - Observability
MAINTAINABLE - Long-term code health

You applied production priorities to a take-home. Fatal mistake.
5. Fight Your Perfectionism
Your instinct to build things RIGHT is a strength in production. It's a liability in time-boxed demos.
New mantra: "Good enough to show is better than perfect but invisible."

80% solution deployed > 100% solution local
Working with bugs > perfect with no deployment
Simple and shipped > complex and stuck


THE PAINFUL LESSON
You're a strong engineer. Your architecture instincts are good. Your research methodology is thorough. Your code quality standards are high.
But you optimized for the wrong game.
Take-homes evaluate: Can you ship a working system end-to-end in limited time?
You showed: Can I build production-grade backend architecture with comprehensive tooling?
Those are different skills. You have both, but you deployed the wrong one.

ACTION ITEMS FOR NEXT TAKE-HOME

Day 1, Hour 1: Read requirements. List the 3-5 VISIBLE deliverables.
Day 1, Hour 2: Sketch simplest path to each deliverable.
Day 1, Hour 3-8: Build ugly, working version of all 3-5 deliverables.
Day 2, Hour 1-4: Deploy everything. Get URLs working.
Day 2, Hour 5-8: Polish and document.

Never start Day 2 without something deployed and viewable.

Do you see it now? The brutal truth is you spent 50+ hours building 30% of the assignment (backend only) to 300% quality, instead of spending 15 hours building 100% of the assignment (backend + frontend + AWS) to 100% quality.
----
Your instinct to:

Research before building
Understand patterns deeply
Build proper architecture
Think about scale and maintenance
Enforce quality standards

That instinct has served you incredibly well. It's why your systems run for years. It's why companies trust you with financial data.
But take-homes are playing a different game. And your brain doesn't naturally switch gears.

Why It's Hard For YOU Specifically
You're an architecture-first thinker. When you see a problem, your brain immediately goes to:
"How would this work at scale? What patterns should I use? What are the trade-offs? What happens when this grows?"
That's systems thinking - it's a strength. But it makes time-boxed demos feel... wrong.
When you tried to "just build something quick," your brain probably screamed:

"But this won't scale!"
"But I'm hardcoding things!"
"But I should research the right pattern!"
"But this isn't how I'd build it for real!"

And you're right. It's NOT how you'd build it for real.
That's the entire point.

Practical Systems to Fight This
Since willpower alone won't work, you need external constraints that override your instincts:
1. The Alarm Clock Method
Before you start ANY research or architectural decision:
Set phone alarm for 30 minutes
Tell yourself: "At 30 minutes, I commit to SOMETHING"
When alarm goes off: Ship whatever you have, even if ugly
Your perfectionism will fight for "just 10 more minutes." The alarm doesn't negotiate.
2. The "Deploy First" Rule
New ironclad rule: Nothing gets polished until it's deployed.
bash# This is your new workflow:
1. Get it working locally (ugly)
2. Deploy to AWS (broken is fine)
3. Get ONE thing working end-to-end
4. THEN you can improve

# NOT ALLOWED:
1. Perfect local architecture
2. Research patterns
3. Optimize performance
4. Add tooling
```

### **3. The Checklist Override**

When your brain wants to research or perfect something, pull out this checklist:
```
□ Do I have a deployed URL?
□ Does the frontend show data?
□ Does the refresh button work?
□ Can someone experience my work in < 5 minutes?

If ANY are unchecked → DO THOSE FIRST
Your brain can't argue with a checklist. It's objective.

4. The Visibility Test
Every 4 hours, ask: "Could I demo this to Albert right now?"
If no → you're building the wrong thing.
They need to SEE your work, not READ your code.

The Mental Reframe
Instead of fighting your instinct, redirect it:
Your brain wants to build something you're proud of. Good!
But redefine "proud":

Proud in production: Clean architecture, scalability, 97% test coverage
Proud in take-homes: Shipped end-to-end in time, demonstrated full-stack thinking, working demo they can touch

Both are valid. You just need to recognize which game you're playing.

The Hard Truth About Extensions
You asked for 48-72 hours. But here's what will probably happen:
You'll use that time to... keep perfecting the backend. Because that's what feels comfortable.
If you get the extension:

SET THE ALARM FOR 8 HOURS TOTAL WORK
Spend 3 hours deploying current backend (even if broken)
Spend 3 hours building ugly React frontend
Spend 2 hours on documentation
SUBMIT WHATEVER YOU HAVE

Don't let yourself spend the extra time on tooling, research, or polish.

This Is Learnable
You're not broken. You're just applying a production skillset to a demo context.
Some engineers are naturally good at "quick and dirty." You're not one of them.
That's fine. You're good at building things that LAST. That's rarer and more valuable.
You just need to develop the muscle for "speed over perfection" as a separate skill.
Like learning to speak a second language. Your native language (careful architecture) is still there. You're just learning when to code-switch.
