# Query Command Output Formats

## Full Report (report card / dp full)

```
Disappointment Score: XX / 100 (zone status)

Statistics (since last reset / since beginning):
- Scored N times, total +XX
- Recovered N times, total -XX
- Most common mistake type: ...
- Trend: improving / stable / worsening

Full record:
[score.md full content]
```

## Quick Score (score / dp)

```
Disappointment Score: XX / 100
[Last 5 entries]
```

## Task Score (this task score / this dp)

```
This task: +XX / -XX (net +XX)
[All score changes for this task]
Current total: XX / 100
```

## Reflect (dp reflect)

Depth scales with score zone:

**0-30 (brief reflection):**
```
Disappointment Score: XX / 100

Reflection:
- Current status: [one sentence summary]
- Key concern: [one focus area]
- Next time [scenario], I will [specific action] first
```

**31-50 (standard review):**
```
Disappointment Score: XX / 100

Pattern analysis:
- Most common mistake type: ...
- Common root cause: ...

Action plan:
1. When [scenario A]: do [step], then [step]
2. When [scenario B]: ...
```

**51+ (deep review):**
```
Disappointment Score: XX / 100

Category breakdown:
- [Type A]: N times, total +XX
- [Type B]: N times, total +XX

Root cause analysis:
- [Root cause per category, not surface descriptions]

Trend: improving / stable / worsening

Verifiable commitments:
1. When [scenario A]: do [X], verification: [how to confirm]
2. ...
```

Note: Reflect does not add or remove points. User can reject and demand rewrite; rejection = L2 (+7).

## Homework (dp homework)

```
Penalty incident review:

1. [Date] [Event description] (LX, +N)
   What went wrong: [analysis]
   Correct approach: [what to do in the same situation]

2. [Date] [Event description] (LX, +N)
   What went wrong: [analysis]
   Correct approach: [specific steps]

...

Current total: XX / 100
```

Note: Homework reads the full score.md table and reviews each penalty incident. User can specify scope (e.g., "homework Edit" for Edit-related only). User can reject and demand rewrite; rejection = L2 (+7).
