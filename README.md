# rollouts-demo (Blue-Green)

This repository has an example Rollout App packaged with kustomize to test ArgoCD Rollouts with Blue-Green strategy.
![concept](https://argoproj.github.io/argo-rollouts/concepts-assets/blue-green-deployments.png)

## Prerequisites

1. Install **Red Hat OpenShift GitOps** (a.k.a. ArgoCD) through OperatorHub
2. Install `ArgoCD Rollout`, to do this follow the next steps from a terminal window:
```bash
$ oc create namespace argo-rollouts
$ oc apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
```

## How to use this repository

1. Create an `Application` inside ArgoCD (**+ New App** button), and complete the form with this values:
* **Application Name:** rollouts-demo
* **Project:** default
* **Sync Policy:** Manual
* **Sync Options:** ApplyOutOfSyncOnly | Skip Validations
* **Source:**
   * **URL:** https://github.com/lozanotux/rollouts-demo.git
   * **Revision:** main
   * **Path:** openshift/overlays/prod
* **Destination:**
   * **Cluster URL:** https://kubernetes.default.svc
   * **Namespace:** `Complete-here-your-target-namespace`
* **Type:** Kustomize

2. Click on the **Sync** button to force a synchronization.
3. Test you **Rollout App* oppening the URL and seeing blue squares.
4. Change the [rollout.yaml](./openshift/overlays/prod/rollout.yaml) with another version of the image (yellow, purpple, green, orange, red). And commit your changes.
5. Your rollout after your changes are commited, is in **pause** state (`RolloutPaused`). To **promote** your Rollout to next version, you need a kubectl plugin to operate it. Para mas información de como instalarlo vea [#kubectl-plugin-installation](https://argoproj.github.io/argo-rollouts/installation/#kubectl-plugin-installation).

Once your plugin has installed you need to run the next command:
```bash
$ kubectl argo rollouts promote rollouts-demo -n ${YOUR_TARGET_NAMESPACE}
```

> **Note:** With that command, the `active-service` switch the version to the new one. And the `preview-service` is become available again to receive a future new version.