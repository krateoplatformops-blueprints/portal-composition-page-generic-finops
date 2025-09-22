# Add Blueprint to *Portal Composition Page FinOps*

This repository contains a **Blueprint** designed for use with [Krateo PlatformOps](https://krateo.io), specifically targeting the **Composable Portal** feature.

## Purpose

The Blueprint enables users to **add a *Composition* page** of the Krateo Composable Portal, showcasing all informations that are auto-consistent and don't depend on the Composition itself, with the addition of **composition-specific** information targeting the [VM Azure Blueprint](https://github.com/krateoplatformops-blueprints/azure-vm-finops).

## What It Does

- Registers a new Composition in the Krateo *Compositions* page
- Supports namespace scoping and user permissions via RBAC (if configured)
- Showcases generic informations about the Composition
- Showcases FinOps data about the Azure Virtual Machine

## Usage

### Install the Helm Chart

Download Helm Chart values:

```sh
helm repo add marketplace https://marketplace.krateo.io
helm repo update marketplace
helm inspect values marketplace/portal-composition-page-generic-finops --version 1.0.0 > ~/portal-composition-page-generic-finops-values.yaml
```

Modify the *portal-composition-page-generic-finops-values.yaml* file as the following example:

```yaml
global:
  compositionApiVersion: composition.krateo.io/v0-1-2
  compositionGroup: composition.krateo.io
  compositionId: 9a40181e-d8ee-416f-92c5-f1d85c39f4d5
  compositionInstalledVersion: v0-1-2
  compositionKind: VmAzure
  compositionName: test
  compositionNamespace: demo-system
  compositionResource: vmazures
  krateoNamespace: krateo-system
```

Install the Blueprint using, as a release name, the *Blueprint* name (the Helm Chart name of the blueprint):

```sh
helm install test portal-composition-page-generic-finops \
  --repo https://marketplace.krateo.io \
  --namespace demo-system \
  --create-namespace \
  -f ~/portal-composition-page-generic-values.yaml
  --version 1.0.0 \
  --wait
```

### Install using Krateo Composable Operation

Install the CompositionDefinition for the *Blueprint*:

```sh
cat <<EOF | kubectl apply -f -
apiVersion: core.krateo.io/v1alpha1
kind: CompositionDefinition
metadata:
  name: portal-composition-page-generic-finops
  namespace: krateo-system
spec:
  chart:
    repo: portal-composition-page-generic-finops
    url: https://marketplace.krateo.io
    version: 1.0.0
EOF
```

Install the Blueprint using, as metadata.name, the *Blueprint* name (the Helm Chart name of the blueprint):

```sh
cat <<EOF | kubectl apply -f -
apiVersion: composition.krateo.io/v1-0-0
kind: PortalCompositionPageGeneric
metadata:
  name: test
  namespace: demo-system
spec:
  global:
    compositionApiVersion: composition.krateo.io/v0-1-2
    compositionGroup: composition.krateo.io
    compositionId: 9a40181e-d8ee-416f-92c5-f1d85c39f4d5
    compositionInstalledVersion: v0-1-2
    compositionKind: VmAzure
    compositionName: test
    compositionNamespace: demo-system
    compositionResource: vmazures
    krateoNamespace: krateo-system
EOF
```