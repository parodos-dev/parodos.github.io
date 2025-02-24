---
title: "CRD Version v1alpha2"
date: 2025-02-12
---
         
The v1alpha2 version of [Orchestrator CRD](https://github.com/rhdhorchestrator/orchestrator-helm-operator/blob/main/config/crd/bases/rhdh.redhat.com_orchestrators.yaml) was introduced in Orchestrator 1.4 version and is currently supported. 


## New Fields
In OSL 1.35, these new features are introduced:
1. Support for [Workflow Monitoring](https://docs.redhat.com/en/documentation/red_hat_developer_hub/1.4/html/release_notes/technology-preview#technology-preview)
2. Support for [Knative Eventing](https://sonataflow.org/serverlessworkflow/main/cloud/operator/configuring-knative-eventing-resources.html)

Hence, the CRD schema extends to allow configuration for these features by the user. 

- orchestrator.sonataflowPlatform.monitoring.enabled
- orchestrator.sonataflowPlatform.eventing.broker.name
- orchestrator.sonataflowPlatform.eventing.broker.namespace

## Deleted Fields
In [RHDH 1.4](https://docs.redhat.com/en/documentation/red_hat_developer_hub/1.4/html/release_notes/technology-preview#technology-preview), the notifications and signals plugins are now part of RHDH image and no longer need to be configured by the user.

Hence, these plugin fields are now removed from the CRD schema.

- rhdhPlugins.notifications.package		
- rhdhPlugins.notifications.integrity		
- rhdhPlugins.notificationsBackend.package
- rhdhPlugins.notificationsBackend.integrity
- rhdhPlugins.signals.package		
- rhdhPlugins.signals.integrity		
- rhdhPlugins.signalsBackend.package		
- rhdhPlugins.signalsBackend.integrity
- rhdhPlugins.notificationsEmail.package
- rhdhPlugins.notificationsEmail.integrity

## Renamed Fields
For consistency in the subscription resource/configuration in the CRD, these fields are renamed.
- sonataFlowOperator.subscription.source
- serverlessOperator.subscription.source
  

The following Orchestrator CR is an sample of the api v1alpha2 version.


```yaml
apiVersion: rhdh.redhat.com/v1alpha2
kind: Orchestrator
metadata:
  name: orchestrator-sample
spec:
  sonataFlowOperator:
    isReleaseCandidate: false # Indicates RC builds should be used by the chart to install Sonataflow
    enabled: true # whether the operator should be deployed by the chart
    subscription:
      namespace: openshift-serverless-logic # namespace where the operator should be deployed
      channel: alpha # channel of an operator package to subscribe to
      installPlanApproval: Automatic # whether the update should be installed automatically
      name: logic-operator-rhel8 # name of the operator package
      source: redhat-operators # name of the catalog source
      startingCSV: logic-operator-rhel8.v1.35.0 # The initial version of the operator
  serverlessOperator:
    enabled: true # whether the operator should be deployed by the chart
    subscription:
      namespace: openshift-serverless # namespace where the operator should be deployed
      channel: stable # channel of an operator package to subscribe to
      installPlanApproval: Automatic # whether the update should be installed automatically
      name: serverless-operator # name of the operator package
      source: redhat-operators # name of the catalog source
      startingCSV: serverless-operator.v1.35.0 # The initial version of the operator
  rhdhOperator:
    isReleaseCandidate: false # Indicates RC builds should be used by the chart to install RHDH
    enabled: true # whether the operator should be deployed by the chart
    enableGuestProvider: true # whether to enable guest provider
    secretRef:
      name: backstage-backend-auth-secret # name of the secret that contains the credentials for the plugin to establish a communication channel with the Kubernetes API, ArgoCD, GitHub servers and SMTP mail server.
      backstage:
        backendSecret: BACKEND_SECRET # Key in the secret with name defined in the 'name' field that contains the value of the Backstage backend secret. Defaults to 'BACKEND_SECRET'. It's required.
      github: # GitHub specific configuration fields that are injected to the backstage instance to allow the plugin to communicate with GitHub.
        token: GITHUB_TOKEN # Key in the secret with name defined in the 'name' field that contains the value of the authentication token as expected by GitHub. Required for importing resource to the catalog, launching software templates and more. Defaults to 'GITHUB_TOKEN', empty for not available.
        clientId: GITHUB_CLIENT_ID # Key in the secret with name defined in the 'name' field that contains the value of the client ID that you generated on GitHub, for GitHub authentication (requires GitHub App). Defaults to 'GITHUB_CLIENT_ID', empty for not available.
        clientSecret: GITHUB_CLIENT_SECRET # Key in the secret with name defined in the 'name' field that contains the value of the client secret tied to the generated client ID. Defaults to 'GITHUB_CLIENT_SECRET', empty for not available.
      gitlab: # Gitlab specific configuration fields that are injected to the backstage instance to allow the plugin to communicate with Gitlab.
        host: GITLAB_HOST # Key in the secret with name defined in the 'name' field that contains the value of Gitlab Host's name. Defaults to 'GITHUB_HOST', empty for not available.
        token: GITLAB_TOKEN # Key in the secret with name defined in the 'name' field that contains the value of the authentication token as expected by Gitlab. Required for importing resource to the catalog, launching software templates and more. Defaults to 'GITLAB_TOKEN', empty for not available.
      k8s: # Kubernetes specific configuration fields that are injected to the backstage instance to allow the plugin to communicate with the Kubernetes API Server.
        clusterToken: K8S_CLUSTER_TOKEN # Key in the secret with name defined in the 'name' field that contains the value of the Kubernetes API bearer token used for authentication. Defaults to 'K8S_CLUSTER_TOKEN', empty for not available.
        clusterUrl: K8S_CLUSTER_URL # Key in the secret with name defined in the 'name' field that contains the value of the API URL of the kubernetes cluster. Defaults to 'K8S_CLUSTER_URL', empty for not available.
      argocd: # ArgoCD specific configuration fields that are injected to the backstage instance to allow the plugin to communicate with ArgoCD. Note that ArgoCD must be deployed beforehand and the argocd.enabled field must be set to true as well.
        url: ARGOCD_URL # Key in the secret with name defined in the 'name' field that contains the value of the URL of the ArgoCD API server. Defaults to 'ARGOCD_URL', empty for not available.
        username: ARGOCD_USERNAME # Key in the secret with name defined in the 'name' field that contains the value of the username to login to ArgoCD. Defaults to 'ARGOCD_USERNAME', empty for not available.
        password: ARGOCD_PASSWORD # Key in the secret with name  defined in the 'name' field that contains the value of the password to authenticate to ArgoCD. Defaults to 'ARGOCD_PASSWORD', empty for not available.
      notificationsEmail:
        hostname: NOTIFICATIONS_EMAIL_HOSTNAME # Key in the secret with name defined in the 'name' field that contains the value of the hostname of the SMTP server for the notifications plugin. Defaults to 'NOTIFICATIONS_EMAIL_HOSTNAME', empty for not available.
        username: NOTIFICATIONS_EMAIL_USERNAME # Key in the secret with name defined in the 'name' field that contains the value of the username of the SMTP server for the notifications plugin. Defaults to 'NOTIFICATIONS_EMAIL_USERNAME', empty for not available.
        password: NOTIFICATIONS_EMAIL_PASSWORD # Key in the secret with name defined in the 'name' field that contains the value of the password of the SMTP server for the notifications plugin. Defaults to 'NOTIFICATIONS_EMAIL_PASSWORD', empty for not available.
    subscription:
      namespace: rhdh-operator # namespace where the operator should be deployed
      channel: fast-1.4 # channel of an operator package to subscribe to
      installPlanApproval: Automatic # whether the update should be installed automatically
      name: rhdh # name of the operator package
      source: redhat-operators # name of the catalog source
      startingCSV: "" # The initial version of the operator
      targetNamespace: rhdh-operator # the target namespace for the backstage CR in which RHDH instance is created
  rhdhPlugins: # RHDH plugins required for the Orchestrator
    npmRegistry: "https://npm.registry.redhat.com" # NPM registry is defined already in the container, but sometimes the registry need to be modified to use different versions of the plugin, for example: staging(https://npm.stage.registry.redhat.com) or development repositories
    scope: "https://github.com/rhdhorchestrator/orchestrator-plugins-internal-release/releases/download/1.4.0-rc.7"
    orchestrator:
      package: "backstage-plugin-orchestrator-1.4.0-rc.7.tgz"
      integrity: sha512-Vclb+TIL8cEtf9G2nx0UJ+kMJnCGZuYG/Xcw0Otdo/fZGuynnoCaAZ6rHnt4PR6LerekHYWNUbzM3X+AVj5cwg==
    orchestratorBackend:
      package: "backstage-plugin-orchestrator-backend-dynamic-1.4.0-rc.7.tgz"
      integrity: sha512-bxD0Au2V9BeUMcZBfNYrPSQ161vmZyKwm6Yik5keZZ09tenkc8fNjipwJsWVFQCDcAOOxdBAE0ibgHtddl3NKw==
    notificationsEmail:
      enabled: false # whether to install the notifications email plugin. requires setting of hostname and credentials in backstage secret to enable. See value backstage-backend-auth-secret. See plugin configuration at https://github.com/backstage/backstage/blob/master/plugins/notifications-backend-module-email/config.d.ts
      port: 587 # SMTP server port
      sender: "" # the email sender address
      replyTo: "" # reply-to address
  postgres:
    serviceName: "sonataflow-psql-postgresql" # The name of the Postgres DB service to be used by platform services. Cannot be empty.
    serviceNamespace: "sonataflow-infra" # The namespace of the Postgres DB service to be used by platform services.
    authSecret:
      name: "sonataflow-psql-postgresql" # name of existing secret to use for PostgreSQL credentials.
      userKey: postgres-username # name of key in existing secret to use for PostgreSQL credentials.
      passwordKey: postgres-password # name of key in existing secret to use for PostgreSQL credentials.
    database: sonataflow # existing database instance used by data index and job service
  orchestrator:
    namespace: "sonataflow-infra" # Namespace where sonataflow's workflows run. The value is captured when running the setup.sh script and stored as a label in the selected namespace. User can override the value by populating this field. Defaults to `sonataflow-infra`.
    sonataflowPlatform:
      monitoring:
        enabled: true # whether to enable monitoring
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "1Gi"
          cpu: "500m"
      eventing:
        broker:
          name: "my-knative" # Name of existing Broker instance. Optional
          namespace: "knative" # Namespace of existing Broker instance. Optional      
  tekton:
    enabled: false # whether to create the Tekton pipeline resources
  argocd:
    enabled: false # whether to install the ArgoCD plugin and create the orchestrator AppProject
    namespace: "" # Defines the namespace where the orchestrator's instance of ArgoCD is deployed. The value is captured when running setup.sh script and stored as a label in the selected namespace. User can override the value by populating this field. Defaults to `orchestrator-gitops` in the setup.sh script.
  networkPolicy:
    rhdhNamespace: "rhdh-operator" # Namespace of existing RHDH instance
```