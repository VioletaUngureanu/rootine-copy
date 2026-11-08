# Sprint 1 backend patches (BE-10, BE-11)

These patches apply to the main project `dariusturcu22/rootine` (branch `main`
at the Demo 1 / PR #49 merge). They are generated as unified diffs.

- `BE-10.patch` — Handle AI response shapes and API errors.
  `crop: null` and `not_supported` / `crop_not_supported` / `invalid_image`
  become valid diagnoses (not 502); consistent RFC 9457 Problem Details with a
  `title`; invalid JWT stays 401. Includes integration tests and a
  `docs/api/backend.md` note that `crop` may be `null`.
- `BE-11.patch` — Move the AI call outside database transactions and add
  structured logging. Introduces `DiagnosisPersistence` (short transactional
  steps), makes `createDiagnosis` non-transactional orchestration, and adds a
  unit test proving the AI call runs with no transaction open.

## Apply order

BE-11 edits the same `createDiagnosis` method as BE-10, so apply BE-10 first.

```bash
# from the repo root of dariusturcu22/rootine, on an up-to-date main
git switch -c feat/BE-10-ai-response-shapes
git apply --3way patches/BE-10.patch
./mvnw spotless:apply
./mvnw verify            # Docker running, for the *IT tests
# commit, push, open PR

git switch main && git pull
git switch -c feat/BE-11-ai-outside-tx
git apply --3way patches/BE-11.patch   # or branch off BE-10 if it is not merged yet
./mvnw spotless:apply
./mvnw verify
```

If `git apply` reports context mismatch (the upstream moved since these were
generated), use `git apply --3way` so Git does a three-way merge, or apply the
changes by hand from the diff.
