# ACM Governance with GitOps (Argo CD) — Starter Repo

This repo is a minimal, production-ready skeleton for managing **Red Hat Advanced Cluster Management (RHACM) policies** as code via **OpenShift GitOps (Argo CD)**.

## What you get
- Reusable policy components (via **PolicyGenerator** + Kustomize)
- Environment targeting with **Placement**, **PolicySet**, and **PlacementBinding**
- Separate **stage** and **prod** overlays
- Argo CD Applications to auto-sync policies and placements
- Example **PolicyException** pattern
- Namespaces and ClusterSetBindings manifests

## Folder layout
```
acm-gitops/
├─ namespaces/                  # core namespaces on the hub
├─ policies/                    # policy sources (kustomize + PolicyGenerator)
│  ├─ security/disallow-privileged/
│  └─ compliance/require-namespace-label/
├─ placements/
│  ├─ stage/                    # Placement + PolicySet + Binding (+ ClusterSetBinding)
│  └─ prod/
└─ apps/                        # Argo CD Applications for each env
```

> Note: This repo assumes your hub already has RHACM and OpenShift GitOps installed.

## Quick start
1. **Create the `policies` namespace on the hub cluster** and bind your ClusterSets:
   ```sh
   oc apply -f namespaces/policies-namespace.yaml
   oc apply -f placements/stage/clustersetbinding.yaml
   oc apply -f placements/prod/clustersetbinding.yaml
   ```

2. **Point Argo CD at this repo** and apply the Applications:
   ```sh
   # Stage
   oc apply -n openshift-gitops -f apps/acm-policies-stage.yaml
   oc apply -n openshift-gitops -f apps/acm-placements-stage.yaml

   # Prod
   oc apply -n openshift-gitops -f apps/acm-policies-prod.yaml
   oc apply -n openshift-gitops -f apps/acm-placements-prod.yaml
   ```

3. Watch compliance in the ACM **Governance** UI.

## CI validation (optional)
A minimal GitHub Action is included to ensure Kustomize builds:
```
.github/workflows/validate.yaml
```

## Customize
- Put **new policies** under `policies/<domain>/<name>/` with a `policygenerator.yaml` and add the folder to `policies/kustomization.yaml`.
- Target clusters by editing `placements/<env>/placement.yaml`. You can filter by **ClusterSet** and/or labels.
- Group policies per environment in `PolicySet` manifests.
- Add **PolicyExceptions** as needed for targeted waivers.

---

© You. MIT or Apache-2.0 as you prefer.
