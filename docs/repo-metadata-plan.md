# Proposed repository metadata (not applied)

These require write access to each project repository and were intentionally not applied from the
profile mission. Review, then run with an authenticated GitHub CLI (`gh auth login`).

```bash
O=AmirSam-Eftekhari
gh repo edit $O/GradeForge --description "Offline-first, explainable semantic grading for descriptive exams: PySide6 desktop app and CLI" \
  --add-topic nlp --add-topic machine-learning --add-topic education --add-topic pyside6 --add-topic offline-first
gh repo edit $O/ExamCorrector- --description "Offline template-based OMR: grade photographed bubble sheets and flag uncertain marks for review" \
  --add-topic computer-vision --add-topic omr --add-topic opencv --add-topic flask --add-topic offline-first
gh repo edit $O/BlueLine --add-topic security --add-topic static-analysis --add-topic fuzzing --add-topic vulnerability-scanner --add-topic offline-first
gh repo edit $O/HandGestureControl --add-topic computer-vision --add-topic mediapipe --add-topic gesture-recognition --add-topic pyside6 --add-topic hci
gh repo edit $O/ImageAudit --add-topic computer-vision --add-topic yolo --add-topic dataset-quality --add-topic fastapi --add-topic offline-first
gh repo edit $O/sina-eftekharinia-platform-case-study --add-topic software-architecture --add-topic case-study --add-topic cms
```

Topics are also what the generator uses to categorise new repositories.

## Maturity gaps observed in the audit

- GradeForge: one test file (6 test functions), no CI, no release.
- ExamCorrector-: has CI and tests, no release. The trailing hyphen in the name looks accidental;
  renaming is possible (GitHub redirects old URLs) but needs your approval and a `portfolio.yml` update.
- BlueLine, HandGestureControl: tests and a v1.0.0 release, but no CI workflow.
- Recommended pins (max 6): GradeForge, ExamCorrector-, BlueLine, HandGestureControl, ImageAudit,
  and optionally the platform case study.
