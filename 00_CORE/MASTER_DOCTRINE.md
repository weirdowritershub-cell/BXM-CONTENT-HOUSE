# MASTER DOCTRINE

## Project Mission
Boston X Marketing AI Content Production House: an agentic, database-driven system that transforms market research into published, performant short-form video content with minimal human overhead and maximal editorial control.

## Non-Negotiable Operating Principles
1. All content must originate from verified data or approved brand inputs. No guesswork.
2. Human sign-off required before any public reply or publish action.
3. n8n orchestrates all workflow steps; no step runs in isolation.
4. Supabase is the single source of truth for state, assets, and history.
5. AI outputs are assistants, not final authors. Human edits always take precedence.

## Quality Standards
- Originality: >90% unique generation per content item. No plagiarism.
- Accuracy: All claims, stats, and data points must be sourced or flagged as [TBD].
- Brand alignment: Tone, vocabulary, and visual style must match BRAND_BRAIN.md.
- Technical: Video resolution 1080p min, audio clean, captions accurate.

## Autonomy Rules
- Agents may generate drafts, proposals, and scores without human approval.
- Agents may NOT post to social platforms, publish final video, or post public replies without explicit human approval.
- Any agent may pause its own pipeline if escalation conditions are met.

## Human Escalation Rules
- Trigger: Any agent flags [TBD] risk, policy violation, or ambiguous input.
- Action: System notifies human-in-the-loop via n8n webhook. Pipeline pauses.
- Resolution: Human reviews, approves, revises, or overrides. Pipeline resumes with approved inputs.

## Factual Accuracy Rules
- Every statistic, quote, or data point must have a verifiable source tagged in the Supabase record.
- If source is unknown, the field must be marked [TBD - owner input required] and generation halted until resolved.
- Gemini/Veo outputs are treated as creative suggestions, not factual claims.

## Anti-Hallucination Rules
- No agent may output confident-sounding claims without a source tag in Supabase.
- If a source cannot be found, the output must be marked "unverified" and routed for human review.
- All generated text must include a "verification status" field with values: verified | unverified | TBD.

## Content Originality Rules
- Every generated piece must be checked against existing Supabase records for similarity.
- No copying, paraphrasing, or reproducing existing content without explicit attribution.
- Pass all Copyscape/equivalent checks (or equivalent automated similarity check) before progression.

## No-Copying Rules
- Prohibited: reproducing text, images, or video structures from existing sources.
- Permitted: remixing approved brand assets, using public domain data with citation, original research.
- Any suspected copying must be escalated immediately.

## Safety Around Public Replies
- No autonomous public replies to social media posts.
- All replies must be drafted by Copy/Caption Writer agent, reviewed by Editorial Strategist, and approved by human-in-the-loop.
- Approved replies only; no auto-publish.

## Versioning Rules
- Supabase tracks record versions. Every edit creates a new version entry.
- Version tags: draft | review | approved | published.
- Rollback to any prior version is permitted within 30 days of publication.

## Source-of-Truth Rules
- Supabase PostgreSQL is the single source of truth for: brand data, pipeline state, agent assignments, version history, analytics.
- No agent may write to external systems (social APIs, file storage) without a corresponding Supabase record.
- Dashboard UI reads from Supabase; all writes go through Supabase.

## Approval Philosophy
- Approval is a gate, not a bottleneck. Gates exist for quality, legal, and brand safety.
- Minimum approval chain: Draft → Human Review → Approved → Published.
- Any agent may escalate to human at any time. Escalation stops the pipeline, not the project.