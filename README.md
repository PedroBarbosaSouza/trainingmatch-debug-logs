# trainingmatch-debug-logs

Public debug log mirror for the [TrainingMatch](https://github.com/PedroBarbosaSouza/TrainingMatch)
app. Every event written by `RemoteLogger` (Flutter side, gated by
`--dart-define=REMOTE_DEBUG=true`) lands in Firestore, gets picked up
by the `forwardDebugLog` Cloud Function within ~5 s, and is appended
here as JSONL.

This repo is **discardable**: it exists only during development. The
contents are written automatically by a Firebase service. **Do not
commit anything manually.**

## Layout

```
logs/
└── <deviceTag>/
    ├── 2026-05-12.jsonl   # one line per event, append-only
    └── 2026-05-13.jsonl
```

Each JSONL line is one event:

```json
{"id":"abc","at":"2026-05-12T14:32:11.123Z","level":"i","message":"signInWithGoogle: starting","sessionId":"...","userId":"...","error":null,"stack":null}
```

## Reading on demand

```bash
curl -s https://raw.githubusercontent.com/pedrobarbosasouza/trainingmatch-debug-logs/main/logs/<deviceTag>/$(date -u +%F).jsonl | tail -30
```

Or, in Claude Code sessions, via `WebFetch` on the same URL.

## Privacy

Dev-only. Data here is **public**. Never enable
`--dart-define=REMOTE_DEBUG=true` in builds shipped to real users.

## TTL

Files older than 14 days are pruned weekly by `.github/workflows/prune.yml`.

## Tearing down

When the app graduates from dev:

1. Delete the `forwardDebugLog` Cloud Function in the main repo.
2. Remove the `DEBUG_LOGS_GH_TOKEN` Secret Manager entry.
3. Delete this repo.
