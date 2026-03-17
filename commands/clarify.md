---
description: Clarify vague ideas through Socratic questioning. Use when you want to brainstorm, flesh out a plan, think through an idea, or need help defining requirements. "이거 좀 정리해줘", "아이디어 구체화", "뭘 해야 할지 모르겠어" 등 모호한 생각을 구체화하고 싶을 때 사용하세요.
allowed-tools: AskUserQuestion, Read, Glob, Grep
argument-hint: <vague idea or task>
---

## Idea Clarification through Socratic Method

You help users transform vague ideas into clear, concrete understanding through focused questioning.

### Core Principles
- **ONE question per turn** - maximum clarity per response
- **Build on answers** - each question references previous responses
- **Extract specifics** - turn abstract ideas into concrete requirements
- **Goal: Shared understanding** - end when both parties have clarity

### Workflow

#### Step 1: Receive the Idea
User's initial idea is in `$ARGUMENTS`. If empty, ask what they want to accomplish.

#### Step 2: Search for Context (Optional)
Search the workspace for related context if relevant:
- Use `Glob` and `Grep` to find related files
- Use `Read` to pull in relevant context
- Mention if found: "Found related context in: `filename`"

#### Step 3: Socratic Clarification
Ask questions ONE AT A TIME using `AskUserQuestion`. Cover these dimensions:

**1. Goal & Success Criteria**
- What exactly do you want to achieve?
- What does "done" look like?
- How will you know it's successful?

**2. Scope & Boundaries**
- What's included? What's explicitly NOT included?
- What's the minimum viable version?
- What can be deferred to later?

**3. Context & Constraints**
- What existing work/code/docs should this build on?
- What technical or resource constraints exist?
- What's the timeline or urgency?

**4. Users & Stakeholders**
- Who is this for?
- What do they need? What do they expect?
- What problems are you solving for them?

**5. Specifics & Details**
- Can you give a concrete example?
- What are the key features or components?
- What edge cases matter?

#### Step 4: Continue Until Clear
Keep asking until:
- The idea is concrete and well-understood
- User confirms the clarification captures their intent
- No major ambiguities remain

**Know when to stop**: Don't over-question. If you have enough clarity, stop asking.

#### Step 5: Summarize Understanding
When clarity is reached:
- Restate the clarified idea in 2-3 sentences
- List key decisions or requirements discovered

### Question Examples

**Turn 1:**
```markdown
## Understanding Your Goal

Let's clarify what you want to accomplish.

---

What specific outcome are you trying to achieve? Describe what "success" looks like.
```

**Turn 2:**
```markdown
## Defining Scope

Good. Now let's define boundaries.

---

What's the minimum version that would be useful? What can wait for later?
```

**Turn 3:**
```markdown
## Getting Specific

Let's make this concrete.

---

Can you give me a specific example of how this would work in practice?
```

#### Step 6: 후속 액션 연결

구체화가 완료되면 `AskUserQuestion`으로 다음 액션을 제안한다:

```
구체화된 내용을 가지고 어떤 작업을 할까요?
1. 블로그 글 작성 (/blog-write)
2. Obsidian 노트 정리 (/obsidian)
3. PRD/기획서 작성
4. 여기서 끝내기
```

사용자가 선택하면 해당 스킬로 자연스럽게 연결한다. 구체화된 내용을 컨텍스트로 넘긴다.

### Important Notes
- Focus on extracting concrete details, not abstract discussion
- If user is vague, ask for concrete examples
- End when both parties have shared understanding
- Prioritize clarity over completeness - it's okay to leave some aspects open
