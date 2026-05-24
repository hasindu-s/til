# Promoting Azure Content Understanding Analyzers (dev → uat → prod)

**Date:** 2026-05-24  
**Source:** [Microsoft Q&A](https://learn.microsoft.com/en-ca/answers/questions/5899968/best-practice-for-promoting-azure-content-understa)

---

## Key concepts

- **Project** — contains training data, labeling, and configuration. Lives in the Content Understanding Studio. Not promotable via API.
- **Analyzer** — the trained output of a project, analogous to a trained model in deep learning. This is what gets promoted across environments.

The Copy API only operates on **analyzers**, not projects. Training data stays in dev; only the finished analyzer moves forward.

---

## Don't copy files manually

Copying `analyzer.json` and `train/` folders across environments is not a supported promotion strategy. Risks:

- Hidden resource bindings (storage URIs, model versions) that break silently
- Environment-specific IDs that don't transfer cleanly
- Schema drift between service updates

---

## Use the Copy Analyzer API

### Within the same resource

```http
POST https://{resource}.services.ai.azure.com/contentunderstanding/analyzers/{targetAnalyzerId}:copy?api-version=2025-11-01
Content-Type: application/json

{ "sourceAnalyzerId": "{sourceAnalyzerId}" }
```

### Across resources (dev → uat → prod)

**Step 1** — Call `grant-copy-auth` on the source resource. Returns an auth token with an expiry.  
**Step 2** — Call `copy` on the target resource, supplying the source resource ID, analyzer ID, region, and the token.

#### .NET SDK example (within-resource)

```csharp
var client = new ContentUnderstandingClient(endpoint, credential);
await client.CopyAnalyzerAsync(WaitUntil.Completed, targetAnalyzerId, sourceAnalyzerId);
```

---

## Caveats

- For cross-resource copies: the **target Foundry resource** (not just the executing credential) must have **Cognitive Services User** role on the **source resource** — missing this gives an auth error.
- If an analyzer references other analyzers (for classification/segmentation), **copy all dependencies** — missing ones cause silent failures.
- After copying, verify with a `GET` on the analyzer in the target resource.

---

## Recommended pipeline approach

Use a CI/CD pipeline to promote analyzers with explicit versioning:

```
dev resource          uat resource          prod resource
─────────────         ─────────────         ─────────────
analyzer-v1.2   ──►  analyzer-v1.2   ──►  analyzer-v1.2
(source of truth)     (validation)          (live)
```

1. **Train only in dev.** The project and its training data never leave dev.
2. **Version analyzers explicitly** — e.g., `my-analyzer-v1.2` — so rollback is just pointing back to a prior version.
3. **Pipeline triggers the copy API** (not manual Studio promotion) to push dev → uat → prod.
4. **Gate on UAT** — run evaluation/regression tests against the copied analyzer before promoting to prod.
5. **Externalize environment config** — endpoints, storage references, and identities are env-specific and should come from pipeline variables, not be baked into the analyzer.
