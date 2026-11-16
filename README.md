# NISAR-AI-STUDIO-AI-Text-to-Video-Editor
Turn scripts into videos. Automate storytelling, visual creation, voiceovers, captions, editing, and rendering.
NISAR AI STUDIO — AI Text-to-Video Editor

«Turn scripts into videos. Automate storytelling, visual creation, voiceovers, captions, editing, and rendering.»

NISAR AI STUDIO is an AI-powered video creation platform designed to transform text, scripts, stories, and creative prompts into edited video projects.

The platform combines AI storyboard generation, AI images, image animation, AI video clips, multilingual voiceovers, subtitles, automated editing, and FFmpeg rendering.

Project status: Development specification / implementation status to be verified. Production readiness and deployment are not claimed until the release gates in this README have been satisfied.

---

1. Product Overview

Users provide a script or topic. The platform processes it into a structured video project and produces an exportable MP4.

End-to-end workflow

1. Text or script submission.
2. AI script analysis.
3. Scene and storyboard generation.
4. Visual asset generation.
5. AI image animation or video clip generation.
6. Urdu or English voiceover.
7. Automatic subtitle generation and synchronization.
8. Music, transitions, and audio mixing.
9. FFmpeg video rendering.
10. Preview, export, and project storage.

2. Core Features

AI Script-to-Storyboard

- Convert scripts into structured scenes.
- Generate scene descriptions and visual prompts.
- Estimate scene duration.
- Allow users to edit generated scenes.
- Preserve the original user script.

AI Visual Generation

- Generate images for individual scenes.
- Animate still images with motion effects.
- Generate AI video clips through supported providers.
- Support provider adapters instead of hard-coding one vendor.
- Track asset source, generation status, and usage.

AI Voiceover

- Urdu and English narration.
- Configurable voice and speaking speed.
- Audio preview before rendering.
- Scene-level voiceover assignment.
- Audio duration validation.

Automatic Editing

- Scene ordering and timing.
- Transitions and motion effects.
- Subtitle placement and styling.
- Background music and volume controls.
- Audio/video synchronization.
- Manual timeline adjustments.

Video Export

Preset| Aspect ratio| Resolution
YouTube| 16:9| 1920 × 1080
Shorts / Reels| 9:16| 1080 × 1920
Instagram Square| 1:1| 1080 × 1080

Output format: MP4.

3. Platform Architecture

Layer| Technology| Responsibility
Frontend| Next.js + TypeScript| Dashboard and editor
UI| Tailwind CSS| Responsive interface
Backend| Node.js| API and business logic
Authentication| Firebase Auth| User identity
Database| Firestore| Projects and job metadata
Storage| Firebase Storage| Media assets
Processing| Google Cloud Run| Rendering workers
Queue| Cloud Tasks| Asynchronous jobs
Video engine| FFmpeg| Encoding and editing
AI provider| Gemini| Script and visual workflows
AI provider| Claude| Script analysis and planning
Monitoring| Cloud Logging| Operational visibility

Architecture principles

- Modular services.
- Provider-independent AI integration.
- Asynchronous rendering.
- Idempotent job processing.
- Secure asset access.
- Auditable project operations.
- Explicit error handling and retry policies.

4. Suggested Repository Structure

nisar-ai-text-to-video/
├── apps/
│   └── web/
│       ├── app/
│       ├── components/
│       ├── features/
│       ├── hooks/
│       └── lib/
├── services/
│   ├── api/
│   └── renderer/
├── packages/
│   ├── ai-providers/
│   ├── video-engine/
│   ├── shared/
│   └── validation/
├── firebase/
│   ├── firestore.rules
│   ├── storage.rules
│   └── firestore.indexes.json
├── infrastructure/
│   ├── cloud-run/
│   └── cloud-tasks/
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── security/
│   └── e2e/
├── scripts/
├── docs/
│   ├── ARCHITECTURE.md
│   ├── SECURITY.md
│   ├── DEPLOYMENT.md
│   └── ROLLBACK.md
├── .env.example
├── .gitignore
├── package.json
├── README.md
└── LICENSE

This is a proposed structure; actual directories must match the implemented repository.

5. Video Processing Pipeline

USER SCRIPT
    |
    v
SCRIPT VALIDATION
    |
    v
AI STORYBOARD
    |
    v
SCENE GENERATION
    |
    +------ AI IMAGE GENERATION
    |
    +------ AI VIDEO GENERATION
    |
    +------ IMAGE ANIMATION
    |
    v
VOICEOVER GENERATION
    |
    v
SUBTITLE SYNCHRONIZATION
    |
    v
TIMELINE COMPOSITION
    |
    v
FFMPEG RENDERING
    |
    v
OUTPUT VALIDATION
    |
    v
SECURE MP4 STORAGE
    |
    v
USER PREVIEW AND EXPORT

Job states

QUEUED
  |
  v
PROCESSING
  |
  +----> RETRY_PENDING
  |
  +----> FAILED
  |
  v
VALIDATING
  |
  v
COMPLETED

A job must not be marked completed until the output file passes validation.

6. Firestore Data Model

Suggested collections:

users/
projects/
video_jobs/
scenes/
media_assets/
voiceovers/
render_outputs/
provider_usage/
audit_logs/

Example video job document

{
  "ownerUid": "USER_UID",
  "projectId": "PROJECT_ID",
  "status": "queued",
  "format": "vertical",
  "resolution": "1080x1920",
  "language": "ur",
  "provider": "gemini",
  "createdAt": "SERVER_TIMESTAMP",
  "updatedAt": "SERVER_TIMESTAMP"
}

Use server-generated timestamps in actual Firestore writes. The example is illustrative, not a complete schema.

7. Security Requirements

Security is a release requirement, not an optional enhancement.

- Firebase Authentication required for protected operations.
- Firestore rules enforce user ownership.
- Storage rules prevent unauthorized media access.
- Validate file type, file size, and media metadata.
- Validate all API inputs using schemas.
- Store AI provider keys in Google Secret Manager.
- Never expose provider keys in browser bundles.
- Apply user-level quotas and rate limits.
- Restrict Cloud Run service permissions.
- Use signed URLs with expiration where appropriate.
- Validate ownership before every project or asset operation.
- Maintain audit logs for sensitive operations.
- Prevent arbitrary FFmpeg command execution.
- Restrict media processing to controlled temporary directories.
- Enforce processing timeouts and resource limits.

8. Environment Configuration

Create ".env.example":

NODE_ENV=development
NEXT_PUBLIC_APP_NAME=NISAR AI STUDIO

FIREBASE_PROJECT_ID=
FIREBASE_CLIENT_EMAIL=
FIREBASE_PRIVATE_KEY=

GEMINI_API_KEY=
ANTHROPIC_API_KEY=

GOOGLE_CLOUD_PROJECT=
GOOGLE_CLOUD_REGION=
FIREBASE_STORAGE_BUCKET=

CLOUD_TASKS_QUEUE=
RENDER_SERVICE_URL=

Never commit ".env" files, private keys, service account credentials, or production secrets.

Use Secret Manager for production credentials. Public Firebase configuration is not a replacement for security rules.

9. Local Development

Prerequisites

- Node.js LTS
- npm
- FFmpeg
- Firebase project or Firebase Emulator Suite
- Google Cloud project for cloud processing

Install

git clone YOUR_REPOSITORY_URL
cd nisar-ai-text-to-video

npm install

Configure environment variables using ".env.example".

Development

npm run dev

Quality checks

npm run lint
npm run typecheck
npm run test
npm run build

These commands must be defined in "package.json". A successful command exit code and retained test output are required as evidence.

10. Testing Strategy

Unit tests

- Script validation.
- Storyboard parsing.
- Scene timing calculations.
- Subtitle formatting.
- Provider adapter behavior.
- FFmpeg argument construction.

Integration tests

- Authentication.
- Firestore access control.
- Storage upload and retrieval.
- Job queue lifecycle.
- AI provider error handling.
- Video rendering pipeline.

Security tests

- Unauthorized project access.
- Cross-user asset access.
- Invalid file uploads.
- Oversized media.
- Secret exposure.
- API abuse and rate limiting.
- Malicious media and path traversal attempts.

End-to-end tests

- Submit a script.
- Generate a storyboard.
- Create visual assets.
- Generate voiceover.
- Render a video.
- Validate the MP4.
- Download the completed output.

Release evidence

Record:

- Test command.
- Execution date.
- Environment.
- Pass/fail counts.
- Failed test details.
- Build output.
- Security scan results.
- Deployment revision, if deployed.

No test is considered passed merely because it was written.

11. Deployment Strategy

Target infrastructure:

- Next.js frontend on Vercel or an approved hosting environment.
- Node.js API and rendering worker on Google Cloud Run.
- Firebase Auth and Firestore.
- Firebase Storage.
- Cloud Tasks for asynchronous rendering.

Deployment gates

1. Install dependencies from the lockfile.
2. Run lint and type checks.
3. Execute unit and integration tests.
4. Execute security checks.
5. Build the frontend and backend.
6. Deploy to staging.
7. Run staging smoke tests.
8. Verify authentication and authorization.
9. Render and validate a sample MP4.
10. Approve production deployment.
11. Verify production health and retain deployment evidence.

Deployment is not successful until the deployed revision and health checks are verified.

12. Rollback Strategy

Every release must have a recoverable previous version.

- Keep the previous frontend deployment available.
- Retain the previous Cloud Run revision.
- Deploy the previous verified application revision if the new version fails.
- Use backward-compatible Firestore schema changes.
- Back up critical metadata before migrations.
- Do not delete existing media during rollback.
- Keep secrets versioned in Secret Manager.
- Verify service health after rollback.

Detailed procedures belong in "docs/ROLLBACK.md".

13. Cost and Resource Controls

AI generation and video rendering can create significant costs.

Required controls:

- Per-user generation quotas.
- Maximum script and scene limits.
- Maximum upload size and duration.
- Rendering timeout.
- Queue concurrency limits.
- Provider usage tracking.
- Automatic cleanup of temporary files.
- User-visible job status and failure reasons.
- Explicit approval before expensive operations when configured.

Do not promise unlimited video generation without a funded capacity plan.

14. Copyright and Content Governance

- Use original scripts or content the user is authorized to use.
- Respect AI provider terms and media licensing conditions.
- Track the source of imported and generated assets.
- Do not use copyrighted music or visuals without permission.
- Provide a clear user content and data deletion process.

15. Release Policy

NISAR AI STUDIO follows this mandatory policy:

1. No false production-ready claims.
2. No deployment success claims without evidence.
3. Tests must actually execute and pass.
4. Security and validation checks are mandatory.
5. Failed tests must be documented.
6. Every code change must include rollback instructions.
7. No evidence → No DONE.

Release status

Requirement| Status
Product specification| Documented
Architecture proposal| Documented
Implementation| Not verified
Automated tests| Not executed
Security audit| Not executed
Deployment| Not verified
Production approval| Not granted

16. Roadmap

- [ ] Project scaffold and authentication.
- [ ] Script input and validation.
- [ ] AI storyboard engine.
- [ ] Image generation integration.
- [ ] Image animation integration.
- [ ] AI video provider integration.
- [ ] Urdu and English voiceovers.
- [ ] Automatic captions.
- [ ] Timeline editor.
- [ ] FFmpeg rendering worker.
- [ ] MP4 export and storage.
- [ ] User dashboard and job history.
- [ ] Admin monitoring and usage analytics.
- [ ] Security and integration tests.
- [ ] Staging deployment.
- [ ] Production release approval.

17. License

Choose and add an appropriate license before public distribution. Do not claim a license has been granted until the LICENSE file has been added and reviewed.

---

NISAR AI STUDIO

Build Smarter. Create Faster. Grow Better.

AI Automation • SaaS Development • Digital Systems