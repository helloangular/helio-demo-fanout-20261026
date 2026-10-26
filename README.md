# helio-demo-fanout-20261026

Internal Helio demo (26 Oct 2026, https://cicd.dagly.io). Shows one release going through a multi-stage pipeline with
fan-out, fan-in and parallel bank environments, gated by Helio. Synthetic: every job prints log lines and sleeps;
nothing is deployed anywhere. The repository holds no secrets.

```
build ─┬─ unit-tests ─┐
       ├─ sast ───────┼─ publish ─ qa: qa_east ─ preprod: preprod_boa ∥ preprod_chase ∥ preprod_citi ─ smoke ─ production: prod_boa ∥ prod_chase ─ verify
       └─ sca ────────┘
```

- `.github/workflows/demo-fanout-release.yml`: the caller Helio dispatches. It calls the Environment Catalog template
  `helloangular/helio-client-capacity-20260916/.github/workflows/demo-fanout-deploy.yml@demo-fanout-20261026`.
- The build makes one small, deterministic `helio-demo-app.war` and its sha256. Every deploy job downloads the same
  artifact, checks it and prints the same sha256 ("same build everywhere").
- `.github/workflows/demo-probe.yml`: one-off readiness probe for Helio's gate profile.
- Every commit Helio may dispatch from is tagged `helio/workflow/<sha>` in the same step.
