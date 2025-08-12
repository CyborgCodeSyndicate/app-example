# 🔍 Detect Microservice Test Tags

This composite GitHub Action detects which microservice-specific test tags should be executed based on the files changed in a commit or pull request.

### 🧠 Use Case

In microservice architectures, it's inefficient to run all regression tests when only one service changes. This action maps changed paths to test tags so that only relevant tests (e.g. tagged `@account-service`, `@payment-service`) are executed.

---

## 📥 Inputs

| Name             | Required | Description                                                                                      |
|------------------|----------|--------------------------------------------------------------------------------------------------|
| `before`         | ✅ Yes   | The git SHA before the change                                                                    |
| `after`          | ✅ Yes   | The git SHA after the change                                                                     |
| `service_tag_map`| ✅ Yes   | Newline-separated list of path → tag mappings (e.g. `src/main/java/account/ -> account-service`) |

---

## 📤 Outputs

| Name  | Description                                |
|--------|--------------------------------------------|
| `tags` | Comma-separated list of detected test tags |

---

## 🚀 Example Usage

```yaml
- uses: actions/checkout@v4

- name: Detect changed test tags
  id: detect
  uses: CyborgCodeSyndicate/actions/.github/actions/detect-tags@v1
  with:
    before: ${{ github.event.before }}
    after: ${{ github.sha }}
    service_tag_map: |
      src/account/ -> account-service
      src/payment/ -> payment-service

- name: Run relevant tests
  uses: CyborgCodeSyndicate/temp-project/.github/workflows/ui-tests.yml@main
  with:
    tagsInclude: ${{ steps.detect.outputs.tags || 'Regression' }}
    tagsExclude: Flaky
    # ... other inputs ...
