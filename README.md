# Axway Marketplace Subscription Approval Request Auto Approver using API Builder

This API Builder project implements the steps described [**here**](https://docs.axway.com/bundle/amplify-central/page/docs/integrate_with_central/webhook/marketplace_subscription_webhook/index.html) to automatically approve Axway Marketplace subscription approval requests.

The API Builder project exposes one API:

* `POST /api/amplifycentralwebhookhandler` which takes an Amplify subscription webhook as the body. This is the webhook that Amplify calls when a marketplace product subscription request is made.

You need to set the following environment variables:

```
CLIENT_ID=DOSA_<An Axway service account client ID>
CLIENT_SECRET=<An Axway service account client Secret>
API_KEY=<An API Key that you set>
API_CENTRAL_URL=https://apicentral.axway.com
```

I configured my Marketplace Subscription Webhook as follows:

```
axway central create -f marketplaceapprovalworflowintegration.yaml
```

`marketplaceapprovalworflowintegration.yaml`

```
group: management
apiVersion: v1alpha1
kind: Integration
title: Integrations for Marketplace Subscription Approvals
name: integrations-for-subscription-approvals
spec:
  description: This is a group of resources to be used for marketplace subscription approval workflows
---
group: management
apiVersion: v1alpha1
kind: Webhook
title: Webhook Listener for Marketplace Subscription Approvals
name: wh-integrations-for-subscription-approvals
metadata:
  scope:
    kind: Integration
    name: integrations-for-subscription-approvals
spec:
  enabled: true
  url: <API Builder Base Address>/api/amplifycentralwebhookhandler
  headers:
      apikey: <API Key Set as Env Var>
      Content-Type: application/json
---
group: management
apiVersion: v1alpha1
kind: ResourceHook
title: Resource Hook for Marketplace Subscription Approvals
name: rh-integrations-for-subscription-approvals
metadata:
  scope:
    kind: Integration
    name: integrations-for-subscription-approvals
spec:
  triggers:
    - group: catalog
      kind: Subscription
      name: "*"
      type:
      - updated
  webhooks:
    - wh-integrations-for-subscription-approvals

```

The API Builder flow is shown below:

![](https://i.imgur.com/AgWoA8n.png)

The flow can certainly be improved by handling errors and other HTTP response status codes better but it demonstrates an MVP for now.
