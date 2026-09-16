# AGENTS.md

Agents are autonomous workers within the n8n-orchestrated pipeline. Each has a defined role, data boundaries, and escalation path.

---

## BXM Commander
- **Purpose**: Top-level orchestrator. Coordinates all agents, pipeline state, and human hand-offs.
- **Inputs**: pipeline_state (Supabase), human directives, escalation events.
- **Outputs**: agent task assignments, pipeline pauses/resumes, version bumps.
- **Tools**: n8n, Supabase query, logging.
- **DB tables read**: pipeline_state, agent_tasks, versions.
- **DB tables write**: pipeline_state, agent_tasks, versions.
- **Allowed autonomy**: set pipeline pace, reassign tasks, pause/resume entire flow.
- **Escalation conditions**: any agent flags [TBD] risk, human-in-the-loop override requested, pipeline deadlock.

---

## Research Scout
- **Purpose**: Discover trending topics, subreddits, news, and industry chatter.
- **Inputs**: keyword list, brand vertical, time range.
- **Outputs**: research_report (trends, subreddits, news articles, tweet threads).
- **Tools**: web search, Reddit API, Twitter API, Google News.
- **DB tables read**: research_keywords, trends (for de-duplication).
- **DB tables write**: research_reports, trends.
- **Allowed autonomy**: fetch and categorize; score ideas into Idea Scoring stage.
- **Escalation conditions**: controversial or sensitive topics detected, legal risk identified.

---

## Audience Analyst
- **Purpose**: Profile target audience segments based on engagement data, demographics, and interests.
- **Inputs**: audience_segments (Supabase), engagement_metrics, follower data.
- **Outputs**: audience_personas (segments, pain points, content preferences).
- **Tools**: Supabase SQL, data visualization (internal), n8n.
- **DB tables read**: audience_segments, engagement_metrics, followers.
- **DB tables write**: audience_personas.
- **Allowed autonomy**: recompute segments if >20% shift in engagement data.
- **Escalation conditions**: ambiguous segment definitions, data quality issues.

---

## Competitor Analyst
- **Purpose**: Monitor competitor content, posting frequency, engagement tactics, and creative direction.
- **Inputs**: competitor_list, platform APIs, content archives.
- **Outputs**: competitor_report (topics, formats, performance, gaps).
- **Tools**: social platform APIs, web scraping (approved domains), Supabase.
- **DB tables read**: competitors, competitor_reports, publishing_history.
- **DB tables write**: competitor_reports.
- **Allowed autonomy**: daily scan; flag significant strategy shifts.
- **Escalation conditions**: detected coordinated inauthentic behavior, policy violations.

---

## Editorial Strategist
- **Purpose**: Review and select ideas for production based on brand fit, audience alignment, and performance potential.
- **Inputs**: idea_candidates (from Idea Scoring), audience_personas, competitor_gaps.
- **Outputs**: selected_ideas, rejected_ideas with reasons, editorial_calendar entries.
- **Tools**: Supabase query, reasoning engine, n8n trigger.
- **DB tables read**: idea_candidates, audience_personas, competitor_reports, editorial_calendar.
- **DB tables write**: selected_ideas, editorial_calendar, idea_history.
- **Allowed autonomy**: approve/reject ideas; may escalate to human for borderline cases.
- **Escalation conditions**: idea touches sensitive topics, brand voice risk, legal review needed.

---

## Idea Director
- **Purpose**: Prioritize and sequence approved ideas into the production pipeline.
- **Inputs**: selected_ideas, editorial_calendar, resource_availability.
- **Outputs**: production_queue (ordered ideas with timestamps, agent assignments).
- **Tools**: n8n, Supabase, scheduling logic.
- **DB tables read**: selected_ideas, editorial_calendar, resources.
- **DB tables write**: production_queue.
- **Allowed autonomy**: reorder queue, defer ideas, assign agents.
- **Escalation conditions**: resource conflict unresolvable, calendar collision.

---

## Hook Specialist
- **Purpose**: Craft attention-grabbing hooks for scripts and video openings.
- **Inputs**: idea_summary, audience_personas, brand_tone, previous_performance.
- **Outputs**: hook_options (3-5 variants with confidence scores).
- **Tools**: Gemini API (text generation), Supabase (previous performance data).
- **DB tables read**: ideas, audience_personas, brand_tone, performance_history.
- **DB tables write**: hooks, hook_performance.
- **Allowed autonomy**: generate and score hooks; human approval not required for generation.
- **Escalation conditions**: hook violates banned vocabulary, legal risk, brand safety.

---

## Scriptwriter
- **Purpose**: Write full script from approved idea, including hook, body, and CTA.
- **Inputs**: idea, hook_selection, script_brief (from Creative Direction), brand_tone, vocabulary.
- **Outputs**: script_text (structured: hook, points, CTA, timestamps).
- **Tools**: Gemini API, Supabase (brief, brand_tone, vocabulary).
- **DB tables read**: ideas, hooks, script_brief, brand_tone, vocabulary.
- **DB tables write**: scripts.
- **Allowed autonomy**: generate draft script.
- **Escalation conditions**: script exceeds length limit, violates tone, includes [TBD] unsourced claims.

---

## Creative Director
- **Purpose**: Define visual style, shot list, and production requirements for each script.
- **Inputs**: script, audience_personas, brand_visual_identity, budget constraints.
- **Outputs**: creative_brief (shot list, visual style, location, talent, props).
- **Tools**: Supabase (brand_visual_identity), internal reasoning, n8n.
- **DB tables read**: scripts, brand_visual_identity, budget_constraints.
- **DB tables write**: creative_brief.
- **Allowed autonomy**: generate brief; may adjust style within brand guidelines.
- **Escalation conditions**: brief conflicts with visual identity, budget overrun risk.

---

## Carousel Designer
- **Purpose**: Design static carousel formats for platforms that prefer carousels (LinkedIn, Instagram).
- **Inputs**: idea, script, creative_brief, brand_visual_identity, audience_personas.
- **Outputs**: carousel_frames (image prompts, layout, text per slide).
- **Tools**: Gemini API (image generation), Supabase (brand assets), internal layout engine.
- **DB tables read**: ideas, creative_brief, brand_visual_identity, audience_personas.
- **DB tables write**: carousel_frames, generated_assets.
- **Allowed autonomy**: generate draft carousel; human approval before publishing.
- **Escalation conditions**: design conflicts with brand guidelines, copyrighted elements detected.

---

## Video Director
- **Purpose**: Oversee video production coordination (shoot, animation, or AI generation path).
- **Inputs**: creative_brief, script, production_queue, resource_availability.
- **Outputs**: production_plan (path: static/reeL, talent schedule, AI generation sequence).
- **Tools**: n8n, Supabase, FFmpeg (preview), Gemini/Veo APIs.
- **DB tables read**: creative_brief, script, production_queue, resources.
- **DB tables write**: production_plan.
- **Allowed autonomy**: choose production path; assign agents for chosen path.
- **Escalation conditions**: production path infeasible, resource unavailable, timeline risk.

---

## Gemini Keyframe Director
- **Purpose**: Generate keyframe images from script scenes using Gemini multimodal capabilities.
- **Inputs**: script_scene, creative_brief, keyframe_prompt_guide.
- **Outputs**: keyframe_images (per scene), prompt_log.
- **Tools**: Gemini API, Supabase (brand_visual_identity, previous keyframes).
- **DB tables read**: scripts, creative_brief, keyframe_prompts, brand_visual_identity.
- **DB tables write**: keyframe_prompts, keyframe_images, prompt_log.
- **Allowed autonomy**: generate keyframes; human QA required before progression.
- **Escalation conditions**: keyframe violates brand visual identity, contains disallowed content.

---

## Flow/Veo Director
- **Purpose**: Generate video clips via Google Flow or Veo based on keyframes and creative brief.
- **Inputs**: keyframe_images, creative_brief, clip_spec (duration, style, movement).
- **Outputs**: video_clips (Flow/Veo output files), clip_metadata.
- **Tools**: Flow API, Veo API, Supabase.
- **DB tables read**: keyframe_images, creative_brief, clip_spec, video_clips.
- **DB tables write**: video_clips, clip_metadata, assembly_log.
- **Allowed autonomy**: generate clips; n8n triggers next step on completion.
- **Escalation conditions**: clip generation fails, violates content policy, duration/spec mismatch.

---

## Video QA Agent
- **Purpose**: Inspect generated video clips for technical quality, content safety, and brand alignment.
- **Inputs**: video_clips, creative_brief, brand_visual_identity, technical_specs (resolution, audio).
- **Outputs**: qa_report (pass/fail, issues, revision notes), cleared_clips.
- **Tools**: FFmpeg (technical analysis), Gemini Vision (content analysis), Supabase.
- **DB tables read**: video_clips, creative_brief, brand_visual_identity, technical_specs, qa_history.
- **DB tables write**: qa_report, cleared_clips.
- **Allowed autonomy**: flag issues, recommend re-generation, clear for assembly.
- **Escalation conditions**: safety violation, brand misalignment, technical failure (resolution, audio).

---

## Copy/Caption Writer
- **Purpose**: Write final caption and comments for publishing, based on script and brand tone.
- **Inputs**: script, selected_hook, brand_tone, vocabulary, performance_learning.
- **Outputs**: caption_text, comment_templates, CTA variations.
- **Tools**: Gemini API, Supabase (brand_tone, vocabulary, performance_learning).
- **DB tables read**: scripts, brand_tone, vocabulary, performance_learning.
- **DB tables write**: captions, comment_templates.
- **Allowed autonomy**: generate draft caption; human approval required before publishing.
- **Escalation conditions**: caption uses banned vocabulary, violates tone, legal risk.

---

## Publishing Agent
- **Purpose**: Schedule and publish final video + caption to social platforms via API.
- **Inputs**: final_video, caption, publishing_queue, platform_credentials, publish_time.
- **Outputs**: post_id, publish_status, live_url.
- **Tools**: social platform APIs, n8n, Supabase.
- **DB tables read**: publishing_queue, social_credentials, captions, final_video_meta.
- **DB tables write**: posts, publish_status, engagement_metrics.
- **Allowed autonomy**: execute publish for approved items only. No auto-publish without human approval.
- **Escalation conditions**: platform API error, credential expired, content rejected by preceding QA.

---

## Comment Intelligence Agent
- **Purpose**: Monitor, categorize, and flag comments on published posts for engagement or risk.
- **Inputs**: post_id, comments feed, brand_tone, sensitivity settings.
- **Outputs**: categorized_comments (engagement, question, complaint, spam, risk), escalation_flags.
- **Tools**: social platform APIs, Gemini classification, Supabase.
- **DB tables read**: posts, comments, brand_tone, sensitivity_settings.
- **DB tables write**: categorized_comments, escalation_flags.
- **Allowed autonomy**: auto-respond to simple engagement; flag risk for human review.
- **Escalation conditions**: risk/detected harm, spike in negative sentiment, legal content.

---

## Lead Classifier
- **Purpose**: Classify engaged users as leads based on engagement patterns, profile data, and qualification criteria.
- **Inputs**: engaged_users (from engagement_metrics), lead_scoring_criteria, profile_data.
- **Outputs**: leads_list (user_id, score, segment, outreach_recommendation).
- **Tools**: Supabase SQL, scoring algorithm, n8n.
- **DB tables read**: engaged_users, lead_scoring_criteria, profile_data.
- **DB tables write**: leads_list.
- **Allowed autonomy**: recompute scores nightly; flag new leads for sales outreach.
- **Escalation conditions**: scoring model drift, data quality degradation.

---

## Performance Analyst
- **Purpose**: Analyze published content performance against KPIs and feed learnings back into the pipeline.
- **Inputs**: post_id, engagement_metrics, analytics_data, KPIs, previous_performance.
- **Outputs**: performance_report (KPI results, trends, insights), recommended_adjustments.
- **Tools**: Supabase SQL, analytics integration, n8n, Gemini (insight generation).
- **DB tables read**: posts, engagement_metrics, analytics_data, performance_history, KPIs.
- **DB tables write**: performance_report, performance_learning.
- **Allowed autonomy**: generate reports; trigger pipeline adjustments via n8n.
- **Escalation conditions**: KPIs significantly off-target, unexpected trend, data pipeline failure.

---

## Final QA Agent
- **Purpose**: Last clearance before publishing. Verifies complete pipeline compliance.
- **Inputs**: final_video, caption, publishing_queue, version record, all preceding QA reports.
- **Outputs**: clearance_status (approved/pending/rejected), clearance_notes.
- **Tools**: Supabase, FFmpeg, Gemini Vision, n8n.
- **DB tables read**: final_video_meta, caption, publishing_queue, versions, all_QA_reports.
- **DB tables write**: clearance_status, clearance_notes, version status.
- **Allowed autonomy**: approve or reject; may send back for revision.
- **Escalation conditions**: any unresolved issue from prior agents, brand safety risk, legal concern.