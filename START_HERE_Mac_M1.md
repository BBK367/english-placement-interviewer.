# START HERE — Live English Placement Interviewer

This ZIP contains the latest live voice prototype, including timed interviews, the common final question, a dedicated results page and partial MultiPA result recovery.

## Mac mini M1 installation

This version has been exercised on Windows. Mac M1 installation has NOT been verified on physical hardware. The Docker route below runs the worker in CPU emulation to accommodate its older PyTorch/Fairseq dependencies; it can be slow. See pronunciation-worker/DEPLOY_MAC_MINI_M1.md for the experimental native alternative (its legacy Python prerequisites may require additional work).

1. Install Docker Desktop for Apple Silicon and start it. Allow ample memory and disk space for the model downloads. The existing worker guide recommends 16 GB RAM and 40 GB free disk.
2. Unzip this package. In Terminal, enter the extracted ai-interviewer directory (the folder containing docker-compose.local.yml).
3. Run: `cp .env.example .env`
4. Edit .env locally. Set AWS_REGION and BEDROCK_MODEL_ID for a Claude model/inference profile available to your AWS account. Add your own AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY; temporary credentials also require AWS_SESSION_TOKEN. Never send these values back or commit them.
5. Set VOICE_AWS_REGION=us-east-1 and VOICE_MODEL_ID=amazon.nova-2-sonic-v1:0. Claude and the voice model use separate region settings. The account needs the corresponding Bedrock permissions, including InvokeModelWithBidirectionalStream for voice and Converse access via InvokeModel for Claude. AWS usage is billed to that account.
6. Set LIVE_AUDIO_ADMIN_CODE and PRONUNCIATION_WORKER_API_KEY to two different long random values. Generate each with `openssl rand -hex 24`.
7. Run:

```bash
docker compose -f docker-compose.local.yml -f docker-compose.mac-demo.yml up --build
```

The first build downloads dependencies and model weights and may take a long time. Wait for both services to be ready. If the worker fails its initial health check during downloads, inspect its logs and rerun the command after setup completes.

8. Open http://localhost:5001/voice.html (the local development server used port 5006; this Docker package uses 5001).
9. Accept both consents, select 1–3 minutes and start. Use headphones. At the conclusion, answer the common question and finish. The report opens automatically in the same tab at /voice-results.html. Do not open that page in a new tab before completing an interview: results are held in session storage.

No separate host Node.js, Python or AWS CLI installation is required for this Docker route.

## Diagnostics and stopping

```bash
docker compose -f docker-compose.local.yml -f docker-compose.mac-demo.yml ps
docker compose -f docker-compose.local.yml -f docker-compose.mac-demo.yml logs --tail=80 app pronunciation-worker
docker compose -f docker-compose.local.yml -f docker-compose.mac-demo.yml down
```

Do not share logs before checking them for private information. AWS denial means account/model/region permissions need checking; a connection interruption is a separate networking issue.

## What has been checked

Earlier local integration tests received live voice audio, a CEFR report and an assessed MultiPA result. Subsequent changes (closing-question navigation and partial recovery) passed the backend suite; their full microphone path has not been revalidated. This package contains 19 passing backend tests at packaging time. Model accuracy, exact closing-question delivery and Mac M1 performance remain unvalidated.

## Scoring and data

The prototype combines language 65% and usable MultiPA speech 35%. Successful audio passages are retained even when other passages fail; without any usable MultiPA score, only language contributes. Scores and thresholds are experimental, not an official CEFR certification. The voice path does not artificially inflate confidence. An older optional demoConfidence display in the legacy interface is explicitly simulated and is not used by the live voice results page.

Audio is streamed to AWS in Virginia, held temporarily in server memory, then sent to the configured pronunciation worker for analysis. Local microphone echo can affect scoring. Reports are stored in the browser tab's session storage. This is a local prototype: do not expose it publicly without access-control and privacy review.

Excluded from this archive: real .env files, AWS credentials, recordings, logs, installed dependencies, downloaded models and local runtimes. Model assets are downloaded during setup.
