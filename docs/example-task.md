# Example Task: AI Marketing SaaS Competitor Analysis

> A working example with web research and parallel execution. Run it to see multi-agent in action.

## Progress Tracker

| Task | Status | Completed | Output |
|------|--------|-----------|--------|
| Task 0: Market Research | ⬜ not_started | - | `docs/research/target-companies.json` |
| Task 1a: Competitor A Research | ⬜ not_started | - | `data/company-a.json` |
| Task 1b: Competitor B Research | ⬜ not_started | - | `data/company-b.json` |
| Task 1c: Competitor C Research | ⬜ not_started | - | `data/company-c.json` |
| Task 2: Comparative Analysis | ⬜ not_started | - | `data/analysis.json` |
| Task 3: Report Generation | ⬜ not_started | - | `output/report.html` |

**Parallel Execution:** Tasks 1a, 1b, 1c can run simultaneously

**Status Legend:**
- ⬜ `not_started` - Not yet started
- 🔄 `in_progress` - In progress
- ✅ `completed` - Completed and verified
- ⏸️ `blocked` - Waiting for dependencies

---

## Execution Order

```
Task 0: Market Research & Company Selection
    │
    ▼
    ┌─────────────────┬─────────────────┐
    │                 │                 │
    ▼                 ▼                 ▼
Task 1a:          Task 1b:          Task 1c:
Competitor A      Competitor B      Competitor C
Research          Research          Research
(parallel)        (parallel)        (parallel)
    │                 │                 │
    └─────────────────┼─────────────────┘
                      │
                      ▼
              Task 2: Comparative Analysis
                      │
                      ▼
              Task 3: Report Generation
```

---

## Task 0: Market Research & Company Selection

### Goal
Research the AI CRM/Marketing Automation SaaS market and select 3 competitors for in-depth analysis.

### Prerequisites
- None (first task)

### Context for Agent
```
You are conducting market research on AI-powered CRM and marketing automation SaaS solutions.

1. Use WebSearch to find major players in "AI CRM marketing automation SaaS"
2. Identify at least 5-7 companies in this space
3. Select 3 companies for detailed analysis based on:
   - Market presence (established vs emerging)
   - Geographic diversity (global vs regional)
   - Different positioning (enterprise vs SMB)
4. Create the output JSON file with your selections
5. Create docs/research/ directory if it doesn't exist
```

### Tasks to Perform
1. Search for "AI CRM marketing automation SaaS companies 2024"
2. Search for "best marketing automation platforms AI personalization"
3. Compile list of major players
4. Select 3 companies with rationale
5. Save structured output

### Success Criteria
- [ ] `docs/research/target-companies.json` exists
- [ ] Contains exactly 3 companies
- [ ] Each company has: name, website, selection_reason, market_position
- [ ] Companies represent diverse market positions

### Output
```json
// docs/research/target-companies.json
{
  "research_date": "YYYY-MM-DD",
  "market": "AI CRM Marketing Automation SaaS",
  "companies": [
    {
      "id": "company-a",
      "name": "Company Name",
      "website": "https://...",
      "selection_reason": "Why selected",
      "market_position": "enterprise|smb|mid-market"
    }
    // ... 2 more companies
  ]
}
```

### Dependencies
- None

---

## Task 1a: Competitor A Research

### Goal
Conduct deep research on the first selected competitor.

### Prerequisites
- Task 0 completed
- `docs/research/target-companies.json` exists

### Context for Agent
```
You are researching the FIRST company from docs/research/target-companies.json.

1. First, read docs/research/target-companies.json to get company details
2. Use WebSearch and WebFetch to gather detailed information
3. Focus on: features, pricing, target customers, AI capabilities
4. Create data/ directory if it doesn't exist
5. Save findings to data/company-a.json
6. Do NOT modify any other company files (company-b.json, company-c.json)
```

### Research Areas
| Area | What to Find |
|------|--------------|
| Product | Core features, AI/ML capabilities, integrations |
| Pricing | Plans, pricing model, enterprise options |
| Customers | Target segment, notable clients, use cases |
| Positioning | Key differentiators, marketing message |
| Technology | Personalization approach, data requirements |

### Success Criteria
- [ ] `data/company-a.json` exists
- [ ] Contains all research areas
- [ ] Sources are documented
- [ ] Data is structured for comparison

### Output
```json
// data/company-a.json
{
  "company_name": "...",
  "research_date": "YYYY-MM-DD",
  "website": "...",
  "product": {
    "core_features": [],
    "ai_capabilities": [],
    "integrations": []
  },
  "pricing": {
    "model": "subscription|usage|hybrid",
    "plans": [],
    "enterprise_available": true|false
  },
  "customers": {
    "target_segment": "enterprise|smb|mid-market",
    "notable_clients": [],
    "use_cases": []
  },
  "positioning": {
    "tagline": "...",
    "key_differentiators": [],
    "strengths": [],
    "weaknesses": []
  },
  "sources": []
}
```

### Dependencies
- Task 0

---

## Task 1b: Competitor B Research

### Goal
Conduct deep research on the second selected competitor.

### Prerequisites
- Task 0 completed
- `docs/research/target-companies.json` exists

### Context for Agent
```
You are researching the SECOND company from docs/research/target-companies.json.

1. First, read docs/research/target-companies.json to get company details
2. Use WebSearch and WebFetch to gather detailed information
3. Focus on: features, pricing, target customers, AI capabilities
4. Create data/ directory if it doesn't exist
5. Save findings to data/company-b.json
6. Do NOT modify any other company files (company-a.json, company-c.json)
```

### Research Areas
| Area | What to Find |
|------|--------------|
| Product | Core features, AI/ML capabilities, integrations |
| Pricing | Plans, pricing model, enterprise options |
| Customers | Target segment, notable clients, use cases |
| Positioning | Key differentiators, marketing message |
| Technology | Personalization approach, data requirements |

### Success Criteria
- [ ] `data/company-b.json` exists
- [ ] Contains all research areas
- [ ] Sources are documented
- [ ] Data is structured for comparison

### Output
```json
// data/company-b.json
{
  "company_name": "...",
  "research_date": "YYYY-MM-DD",
  // ... same structure as company-a.json
}
```

### Dependencies
- Task 0

---

## Task 1c: Competitor C Research

### Goal
Conduct deep research on the third selected competitor.

### Prerequisites
- Task 0 completed
- `docs/research/target-companies.json` exists

### Context for Agent
```
You are researching the THIRD company from docs/research/target-companies.json.

1. First, read docs/research/target-companies.json to get company details
2. Use WebSearch and WebFetch to gather detailed information
3. Focus on: features, pricing, target customers, AI capabilities
4. Create data/ directory if it doesn't exist
5. Save findings to data/company-c.json
6. Do NOT modify any other company files (company-a.json, company-b.json)
```

### Research Areas
| Area | What to Find |
|------|--------------|
| Product | Core features, AI/ML capabilities, integrations |
| Pricing | Plans, pricing model, enterprise options |
| Customers | Target segment, notable clients, use cases |
| Positioning | Key differentiators, marketing message |
| Technology | Personalization approach, data requirements |

### Success Criteria
- [ ] `data/company-c.json` exists
- [ ] Contains all research areas
- [ ] Sources are documented
- [ ] Data is structured for comparison

### Output
```json
// data/company-c.json
{
  "company_name": "...",
  "research_date": "YYYY-MM-DD",
  // ... same structure as company-a.json
}
```

### Dependencies
- Task 0

---

## Task 2: Comparative Analysis

### Goal
Analyze and compare all three competitors, generating actionable insights.

### Prerequisites
- Task 1a completed: `data/company-a.json` exists
- Task 1b completed: `data/company-b.json` exists
- Task 1c completed: `data/company-c.json` exists

### Context for Agent
```
You are analyzing the competitive landscape based on research from Tasks 1a, 1b, 1c.

1. Read all three company JSON files from data/
2. Create comparison matrices for features, pricing, positioning
3. Identify market gaps and opportunities
4. Generate strategic insights
5. Save analysis to data/analysis.json
```

### Analysis Framework
| Analysis Type | Description |
|---------------|-------------|
| Feature Matrix | Which features each competitor offers |
| Pricing Comparison | Price points, value proposition |
| Positioning Map | How each positions themselves |
| SWOT Summary | Strengths/weaknesses of each |
| Market Gaps | Underserved needs or segments |

### Success Criteria
- [ ] `data/analysis.json` exists
- [ ] Feature comparison matrix complete
- [ ] Pricing comparison complete
- [ ] At least 3 market insights identified
- [ ] Strategic recommendations included

### Output
```json
// data/analysis.json
{
  "analysis_date": "YYYY-MM-DD",
  "companies_analyzed": ["A", "B", "C"],
  "feature_matrix": {
    "feature_name": {
      "company-a": true|false|"partial",
      "company-b": true|false|"partial",
      "company-c": true|false|"partial"
    }
  },
  "pricing_comparison": {
    "company-a": { "entry_price": "", "enterprise_price": "" },
    "company-b": { "entry_price": "", "enterprise_price": "" },
    "company-c": { "entry_price": "", "enterprise_price": "" }
  },
  "positioning_analysis": {
    "company-a": { "primary_focus": "", "target_customer": "" },
    "company-b": { "primary_focus": "", "target_customer": "" },
    "company-c": { "primary_focus": "", "target_customer": "" }
  },
  "market_insights": [
    { "insight": "...", "evidence": "...", "implication": "..." }
  ],
  "recommendations": [
    { "recommendation": "...", "rationale": "...", "priority": "high|medium|low" }
  ]
}
```

### Dependencies
- Task 1a
- Task 1b
- Task 1c

---

## Task 3: Report Generation

### Goal
Generate a professional, visually polished HTML report from the analysis data using the frontend-design skill.

### Prerequisites
- Task 2 completed: `data/analysis.json` exists

### Context for Agent
```
You are generating a high-quality visual HTML report from the competitive analysis.

1. Read data/analysis.json and all company JSON files from data/
2. Create output/ directory if it doesn't exist
3. Use the frontend-design skill (/frontend-design) to generate the report
4. The report should be a single HTML file with embedded CSS
5. Focus on professional design with modern aesthetics, not generic AI-generated look

When invoking frontend-design, provide this context:
- Data source: data/analysis.json, data/company-a.json, data/company-b.json, data/company-c.json
- Output: output/report.html
- Style: Professional competitive analysis dashboard
- Features: Responsive design, comparison tables, visual charts (CSS-based), card layouts
```

### Report Sections
1. **Executive Summary** - Key findings at a glance with metric cards
2. **Companies Overview** - Card-based intro to each competitor with logos/icons
3. **Feature Comparison** - Interactive visual matrix/table with checkmarks
4. **Pricing Analysis** - Comparison table with tier highlights
5. **Market Positioning** - Visual positioning map or diagram
6. **Strategic Insights** - Insight cards with icons
7. **Recommendations** - Priority-based action items with visual indicators

### Design Requirements
- Modern, clean design (avoid generic corporate look)
- Consistent color palette with accent colors for each company
- Responsive layout (works on desktop and tablet)
- Visual hierarchy with clear section separation
- Interactive hover effects on cards and tables
- CSS-only charts/graphs where applicable (no external dependencies)

### Success Criteria
- [ ] `output/report.html` exists
- [ ] Opens correctly in browser
- [ ] All 7 sections present
- [ ] Design is distinctive and professional (not generic)
- [ ] Data accurately reflects analysis.json
- [ ] Responsive on different screen sizes

### Test Method
```bash
# Open report in browser
open output/report.html  # macOS
# or
xdg-open output/report.html  # Linux
```

### Output
- `output/report.html` - Complete competitive analysis report with polished design

### Dependencies
- Task 2
