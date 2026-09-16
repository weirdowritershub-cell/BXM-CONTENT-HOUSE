# SYSTEM ARCHITECTURE

## Pipeline
Research
  → Audience Intelligence
  → Idea Generation
  → Idea Scoring
  → Editorial Selection
  → Script
  → Creative Direction
  → Static or Reel Production
  → Gemini Keyframes
  → Flow/Veo Clips
  → Video QA
  → FFmpeg Assembly
  → Final QA
  → Publishing
  → Comment Monitoring
  → Engagement Analytics
  → Performance Learning
  → feedback into Research/Ideas

## Responsibilities

### OpenCode
- CLI tooling and automation scaffold generation.
- Reads: none (system orchestrator).
- Writes: project structure files, task lists.
- Tools: command-line, file system.
- DB tables read: none (initial setup only).
- DB tables write: none (initial setup only).
- Autonomy: low (setup only).
- Escalation: N/A (initialization phase).

### Codex
- Code completion and scaffolding support within IDE.
- Reads: codebase context, existing patterns.
- Writes: boilerplate code, function implementations.
- Tools: IDE integration, language model.
- DB tables read: none (development-time only).
- DB tables write: none (development-time only).
- Autonomy: medium (suggests, does not commit).
- Escalation: human review before merge.

### ChatGPT
- General ideation, copy refinement, brainstorming partner.
- Reads: conversation context, project briefs.
- Writes: draft copy, ideas, refinements.
- Tools: natural language interface.
- DB tables read: copy_drafts, ideas (for context).
- DB tables write: copy_drafts (draft stage only).
- Autonomy: medium (draft only).
- Escalation: required before any copy goes live.

### Gemini
- Multimodal LLM: text, image, keyframe generation.
- Reads: script, creative direction, brand brain.
- Writes: keyframe prompts, image suggestions, text overlays.
- Tools: Gemini API, image generation.
- DB tables read: keyframe_prompts, scripts, creative_direction.
- DB tables write: keyframe_prompts, generated_assets.
- Autonomy: medium (generates draft keyframes).
- Escalation: required before FFmpeg assembly.

### Flow / Veo
- Video clip generation from prompts and keyframes.
- Reads: Gemini keyframes, creative direction, video QA feedback.
- Writes: video clips, assembled segments.
- Tools: Flow API, Veo API.
- DB tables read: video_clips, flow_veo_prompts.
- DB tables write: video_clips, assembly_log.
- Autonomy: medium (generates clips).
- Escalation: required before FFmpeg assembly and final QA.

### n8n
- Workflow orchestration engine. Connects all agents, triggers, and state transitions.
- Reads: all Supabase tables via workflow definitions.
- Writes: pipeline state, agent task assignments, version records.
- Tools: n8n nodes, webhooks, triggers.
- DB tables read: all (state, tasks, versions).
- DB tables write: pipeline_state, agent_tasks, versions.
- Autonomy: high (orchestrates entire pipeline).
- Escalation: any node can trigger human-in-the-loop webhook.

### Supabase
- PostgreSQL database. Single source of truth for all data.
- Reads: all tables as needed by agents and pipeline.
- Writes: all tables via agent actions and n8n transitions.
- Tools: PostgreSQL, REST APIs, realtime subscriptions.
- DB tables read/write: all (see individual agent tables).
- Autonomy: high (data persistence layer).
- Escalation: N/A (data layer only).

### FFmpeg
- Video assembly and post-processing.
- Reads: video_clips, assembly_log, final_QA_status.
- Writes: final_video file, assembly metadata.
- Tools: FFmpeg CLI/ library.
- DB tables read: video_clips, assembly_log.
- DB tables write: final_video_meta, assembly_log.
- Autonomy: medium (executes assembled pipeline).
- Escalation: required if assembly fails quality checks.

### Next.js Dashboard
- React frontend for human oversight, approval, and monitoring.
- Reads: all Supabase tables (real-time).
- Writes: approval records, human feedback, version updates.
- Tools: Next.js, React, Supabase client.
- DB tables read: all (for UI rendering).
- DB tables write: approvals, human_feedback, version status.
- Autonomy: low (human-supervised).
- Escalation: human actions trigger pipeline changes.

### Social Platform APIs
- Twitter/X, LinkedIn, Instagram, TikTok posting and engagement.
- Reads: publishing_queue, social_credentials, comment_monitoring.
- Writes: posts, comments, engagement metrics.
- Tools: platform SDKs, OAuth, API clients.
- DB tables read: publishing_queue, social_credentials.
- DB tables write: posts, comments, engagement_metrics.
- Autonomy: low (human-approved posts only).
- Escalation: any post flagged by Comment Intelligence Agent routes to human.