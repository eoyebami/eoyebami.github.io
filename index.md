---
# EZ DevSecOps Blog
---

## Table of Contents
- [Languages](#languages)
  - [Python](#python)
    - [Python: Basics](#python-basics)
    - [Python: Fast Api](#python-fast-api)
- [Linux](#linux)
  - [Linux: General Knowledge](#linux-general-knowledge)
  - [Linux: Command Operations](#linux-command-operations)
  - [Linux: Networking](#linux-networking)
  - [Linux: Apis and HTTP Methods](#linux-apis-and-http-methods)
  - [Linux: Yamls and Json Parsing](#linux-yamls-and-json-parsing)
  - [Linux: TLS](#linux-tls)
  - [Linux: Partitions, File Systems, and LVMs](#linux-partitions-file-systems-and-lvms)
- [Virtual Machines](#virtual-machines)
  - [Virtual Box](#virtual-box)
  - [Vagrant](#vagrant)
- [Lightweight Directory Access Protocol](#ldap)
  - [LDAP: General Knowledge](#ldap-general-knowledge)
- [Logging & Monitoring](#logging-and-monitoring)
  - [ELK Stack ElasticSearch](#elk-stack-elasticsearch)
  - [ELK Stack: Logstash](#elk-stack-logstash)
  - [ELK Stack: Kibana](#elk-stack-kibana)
- [Container Orchestration](#container-orchestration)
  - [Docker](#docker)
  - [Kubernetes](#kubernetes)
    - [Kubernetes: Introduction](#kubernetes-introduction)
    - [Kubernetes: Scheduling](#kubernetes-scheduling)
    - [Kubernetes: Application Lifecycle Management](#kubernetes-application-lifecycle-management)
    - [Kubernetes: Security](#kubernetes-security)
    - [Kubernetes: Storage](#kubernetes-storage)
    - [Kubernetes: Networking](#kubernetes-networking)
    - [Kubernetes: Cluster Maintenance](#kubernetes-cluster-maintenance)
    - [Kubernetes: KubeAdm](#kubernetes-kubeadm)
  - [Helm](#helm)
    - [Helm: Introduction](#helm-introduction)
    - [Helm: Templatization Techniques](#helm-templatization-techniques)
  - [Kustomize](#kustomize)
    - [Kustomize: Introduction](#kustomize-introduction)
    - [Kustomize: Customization Techniques](#kustomize-customization-techniques)
  - [Kubernetes: Cluster Add-Ons](#kubernetes-cluster-add-ons)
    - [Metrics](#metrics)
    - [Istio](#istio)
      - [Istio: Introduction](#istio-introduction)
      - [Istio: Traffic Management](#istio-traffic-management)
      - [Istio: Security](#istio-security)
    - [Cert-Manager](#cert-manager)
- [Jenkins](#jenkins)
- [Infrastructure as Code](#infrastructure-as-code)
  - [Ansible](#ansible)
    - [Ansible: Inventory](#ansible-inventory)
    - [Ansible: Variables](#ansible-variables)
    - [Ansible: Playbooks](#ansible-playbooks)
- [Amazon Web Services](#amazon-web-services)
  - [Identity and Access Management](#identity-and-access-management)
  - [EKS](#eks)
- [Google Cloud Platform](#google-cloud-platform)
  - [VPC Networks](#vpc-networks)

## Languages

- ### Python

  - #### Python: Basics

    * [2024-08-07-python](languages/python/2024-08-07-python.md)
    * [2024-08-07-python-prints-and-inputs](languages/python/2024-08-07-python-prints-and-inputs.md)
    * [2024-08-08-python-basic-data-types](languages/python/2024-08-08-python-basic-data-types.md)
    * [2024-08-08-python-operators-and-expressions](languages/python/2024-08-08-python-operators-and-expressions.md)
    * [2024-08-08-python-conditionals](languages/python/2024-08-08-python-conditionals.md)
    * [2024-08-09-python-randomization](languages/python/2024-08-09-python-randomization.md)
    * [2024-08-10-python-lists](languages/python/2024-08-10-python-lists.md)
    * [2024-08-10-python-try-and-except-statements](languages/python/2024-08-10-python-try-and-except-statements.md)
    * [2024-08-10-python-functions](languages/python/2024-08-10-python-functions.md)
    * [2025-05-22-python-tuples](languages/python/2025-05-22-python-tuples.md)
    * [2025-05-29-python-dictionaries](languages/python/2025-05-29-python-dictionaries.md)
    * [2025-05-30-python-built-in-functions](languages/python/2025-05-30-python-built-in-functions.md)
    * [2025-06-22-python-classes-and-objects](languages/python/2025-06-22-python-classes-and-objects.md)
    * [2025-06-23-python-virtual-environments](languages/python/2025-06-23-python-virtual-environments.md)
    * [2025-06-25-python-asynchronous-programming](languages/python/2025-06-25-python-asynchronous-programming.md)
    * [2025-06-25-python-pydantic-models](languages/python/2025-06-25-python-pydantic-models.md)
    * [2025-06-26-python-typing](languages/python/2025-06-26-python-typing.md)

  - #### Python: Fast Api

    * [2025-06-23-python-fast-api-introduction](languages/python/fastapi/2025-06-23-python-fast-api-introduction.md)
    * [2025-06-25-python-fast-api-pydantic](languages/python/fastapi/2025-06-25-python-fast-api-pydantic.md)

## Linux

- ### Linux: General Knowledge

  * [2023-04-15-ssh-client-and-server](linux/2023-04-15-ssh-client-and-server.md)
  * [2023-04-15-yum-repo-configuration](linux/2023-04-15-yum-repo-configuration.md)
  * [2023-04-20-ntp-client-and-server](linux/2023-04-20-ntp-client-and-server.md) 

- ### Linux: Command Operations

  * [2023-04-15-compression](linux/2023-04-15-compression.md)
  * [2023-04-15-filtration](linux/2023-04-15-filtration.md)
  * [2023-04-15-manual](linux/2023-04-15-manual.md)
  * [2023-04-27-systemd](linux/2023-04-27-systemd.md)
  * [2023-05-10-misc-cmd](linux/2023-05-10-misc-cmd.md)
  * [2024-08-06-regex](linux/2024-08-06-regex.md)

- ### Linux: Networking

  * [2024-02-19-networking-in-linux](linux/2024-02-19-networking-in-linux.md)
  * [2024-02-19-dns](linux/2024-02-19-dns.md)
  * [2024-02-19-network-namespaces](linux/2024-02-19-network-namespaces.md)

- ### Linux: APIs and HTTP Methods

  * [2023-05-01-curl-and-rest-apis](http/2023-05-01-curl-and-rest-apis.md)
  * [2023-05-01-http-requests](http/2023-05-01-http-requests.md)

- ### Linux: YAMLS and JSON parsing

  * [2023-05-06-jq-json](linux/2023-05-06-jq-json.md)
  * [2024-03-07-yamls-and-jsons](linux/2024-03-07-yamls-and-jsons.md)
  * [2024-03-07-jsonpath](linux/2024-03-07-jsonpath.md)

- ### Linux: TLS

  * [2024-01-16-tls](linux/2024-01-16-tls.md)

- ### Linux: Partitions, File Systems, and LVMs

  * [2023-05-17-fdisk](linux/2023-05-17-fdisk.md)
  * [2023-05-22-parted](linux/2023-05-22-parted.md)
  * [2023-05-23-lvm-logical-volumes](linux/2023-05-23-lvm-logical-volumes.md)
  * [2023-05-26-swap-space](linux/2023-05-26-swap-space.md)

## Virtual Machines

- ### Virtual Box

  * [2023-04-29-vbox-configuration](vms/vbox/2023-04-29-vbox-configuration.md)
  * [2023-04-29-vbox-networking](vms/vbox/2023-04-29-vbox-networking.md)

- ### Vagrant

  * [2024-03-07-intro-to-vagrant](vms/vagrant/2024-03-07-intro-to-vagrant.md)
  * [2024-03-07-vagrantfile-config](vms/vagrant/2024-03-07-vagrantfile-config.md)
  * [2024-03-07-vm-backups-and-restore](vms/vagrant/2024-03-07-vm-backups-and-restore.md)

## LDAP

- ### LDAP: General Knowledge

  * [2024-03-29-intro-to-ldap](ldap/2024-03-29-intro-to-ldap.md)
  * [2024-03-31-configure-ldap](ldap/2024-03-31-configure-ldap.md)
  * [2024-03-31-ldap-account-manager](ldap/2024-03-31-ldap-account-manager.md)
  * [2024-04-06-ssl-tls-and-ldap](ldap/2024-04-06-ssl-tls-and-ldap.md)

## Logging and Monitoring

- ### ELK Stack: ElasticSearch

  * [2023-05-27-intro-to-elk](elk/2023-05-27-intro-to-elk.md)
  * [2023-05-28-elasticsearch.yml-config](elk/2023-05-28-elasticsearch.yml-config.md)

- ### ELK Stack: Logstash

  * [2023-05-29-logstash.yml-config](elk/2023-05-29-logstash.yml-config.md)
  * [2023-05-29-logstash-pipeline-input](elk/2023-05-29-logstash-pipeline-input.md)
  * [2023-05-29-logstash-pipeline-filter](elk/2023-05-29-logstash-pipeline-filter.md)

- ### ELK Stack: Kibana

  * [2023-05-28-kibana.yml-config](elk/2023-05-28-kibana.yml-config.md) 
  * [2023-06-04-kibana-index-patterns](elk/2023-06-04-kibana-index-patterns.md)

## Container Orchestration

- ### Docker

  * [2023-07-31-docker-run-complexities](k8s/docker/2023-07-31-docker-run-complexities.md)
  * [2023-08-05-dockerfile-cmd-and-entrypoint](k8s/docker/2023-08-05-dockerfile-cmd-and-entrypoint.md)
  * [2023-08-06-docker-compose](k8s/docker/2023-08-06-docker-compose.md)
  * [2023-08-06-docker-run-link](k8s/docker/2023-08-06-docker-run-link.md)
  * [2023-10-02-docker-storage](k8s/docker/2023-10-02-docker-storage.md)
  * [2024-01-31-docker-container-capabilities](k8s/docker/2024-01-31-docker-container-capabilities.md)
  * [2024-02-19-docker-networking](k8s/docker/2024-02-19-docker-networking.md)

- ### Kubernetes

  - #### Kubernetes: Introduction
 
    * [2023-10-03-k8-pods](k8s/2023-10-03-k8-pods.md)
    * [2023-10-03-k8-replicaset](k8s/2023-10-03-k8-replicaset.md)
    * [2023-10-03-k8-deployments](k8s/2023-10-03-k8-deployments.md)
    * [2023-10-17-services](k8s/2023-10-17-services.md)
    * [2023-12-13-example-voting-app](k8s/2023-12-13-example-voting-app.md)
    * [2023-12-18-k8-architecture](k8s/2023-12-18-k8-architecture.md)
    * [2023-12-19-k8-command-tricks](k8s/2023-12-19-k8-command-tricks.md)
    * [2023-12-19-namespaces](k8s/2023-12-19-namespaces.md)
    * [2023-12-29-daemonsets](k8s/2023-12-29-daemonsets.md)
    * [2024-02-18-environment-vars](k8s/2024-02-18-environment-vars.md.md)
    * [2024-03-07-k8-jsonpath](k8s/2024-03-07-k8-jsonpath.md)
    * [2024-04-23-k8-jobs](k8s/2024-04-23-k8-jobs.md)
 
  - #### Kubernetes: Scheduling
 
    * [2023-12-24-manual-scheduling](k8s/2023-12-24-manual-scheduling.md)
    * [2023-12-24-taints-and-tolerations](k8s/2023-12-24-taints-and-tolerations)
    * [2023-12-28-node-selectors](k8s/2023-12-28-node-selectors.md)
    * [2023-12-28-node-affinity](k8s/2023-12-28-node-affinity.md)
    * [2023-12-28-taints-tolerations-and-node-affinity](k8s/2023-12-28-taints-tolerations-and-node-affinity.md)
    * [2023-12-29-resource-allocation](k8s/2023-12-29-resource-allocation.md)
    * [2023-12-30-static-pods](k8s/2023-12-30-static-pods.md)
    * [2023-12-31-kube-scheduler-config](k8s/2023-12-31-kube-scheduler-config.md)
    * [2023-12-31-priority-classes](k8s/2023-12-31-priority-classes.md)
    * [2024-03-25-pod-affinity](k8s/2024-03-25-pod-affinity.md)
    * [2024-03-25-pod-disruption-budget](k8s/2024-03-25-pod-disruption-budget.md)
  
  - #### Kubernetes: Application Lifecycle Management
    
    * [2024-01-14-commands-and-args](k8s/2024-01-14-commands-and-args.md)
    * [2024-01-14-configmaps](k8s/2024-01-14-configmaps.md)
    * [2024-01-15-secrets](k8s/2024-01-15-secrets.md)
    * [2024-01-16-initContainers](k8s/2024-01-16-initContainers.md)
    * [2024-01-16-liveness-startup-readiness](k8s/2024-01-16-liveness-startup-readiness.md)
    * [2024-05-24-horizontal-pod-autoscaler](k8s/2024-05-24-horizontal-pod-autoscaler.md)

  - #### Kubernetes: Security
 
    * [2024-01-16-kube-api-authentication](k8s/2024-01-16-kube-api-authentication.md)
    * [2024-01-17-kubernetes-tls](k8s/2024-01-17-kubernetes-tls.md)
    * [2024-01-22-certificate-signing-request](k8s/2024-01-22-certificate-signing-request.md)
    * [2024-01-22-kubeconfig](k8s/2024-01-22-kubeconfig.md)
    * [2024-01-26-k8-apigroups](k8s/2024-01-26-k8-apigroups.md)
    * [2024-01-27-role-based-access-control](k8s/2024-01-27-role-based-access-control.md)
    * [2024-01-29-service-accounts](k8s/2024-01-29-service-accounts.md)
    * [2024-01-31-image-security](k8s/2024-01-31-image-security.md)
    * [2024-01-31-security-contexts](k8s/2024-01-31-security-contexts.md)
    * [2024-02-05-network-policies](k8s/2024-02-05-network-policies.md)

  - #### Kubernetes: Storage
 
    * [2024-02-12-volumes](k8s/2024-02-12-volumes.md)
    * [2024-02-12-persistent-volumes](k8s/2024-02-12-persistent-volumes.md)
    * [2024-02-13-storageclass](k8s/2024-02-13-storageclass.md)
    * [2024-02-13-projected-volumes](k8s/2024-02-13-projected-volumes.md)

  - #### Kubernetes: Networking
 
    * [2024-02-19-coredns](k8s/2024-02-19-coredns.md)
    * [2024-02-19-cni](k8s/2024-02-19-cni.md)
    * [2024-02-19-pod-networking](k8s/2024-02-19-pod-networking.md)
    * [2024-03-04-service-networking](k8s/2024-03-04-service-networking.md)
    * [2024-03-04-ingress](k8s/2024-03-04-ingress.md)

  - #### Kubernetes: Cluster Maintenance
 
    * [2024-03-05-cluster-maintenance](k8s/2024-03-05-cluster-maintenance.md)
    * [2024-03-06-backup-and-restore](k8s/2024-03-06-backup-and-restore.md)

  - #### Kubernetes: KubeAdm
 
    * [2024-03-06-intro-to-cluster-diy](k8s/2024-03-06-intro-to-cluster-diy.md)
    * [2024-03-07-kubeadm-k8-installation](k8s/2024-03-07-kubeadm-k8-installation.md)
    * [2024-03-09-kubeadmn-k8-vagrant](k8s/2024-03-09-kubeadmn-k8-vagrant.md)

- ### Helm

  - #### Helm: Introduction
 
    * [2024-04-12-intro-to-helm](k8s/helm/2024-04-12-intro-to-helm.md)
    * [2024-04-12-helm-install-and-configuration](k8s/helm/2024-04-12-helm-install-and-configuration.md)
    * [2024-04-14-helm-charts](k8s/helm/2024-04-14-helm-charts.md)
    * [2024-04-14-helm-command-tricks](k8s/helm/2024-04-14-helm-command-tricks.md)
    * [2024-04-15-helm-templatization](k8s/helm/2024-04-15-helm-templatization.md)

  - #### Helm: Templatization Techniques
 
    * [2024-04-15-helm-functions](k8s/helm/2024-04-15-helm-functions.md)
    * [2024-04-15-helm-date-formats](k8s/helm/2024-04-15-helm-date-formats.md)
    * [2024-04-19-helm-conditionals](k8s/helm/2024-04-19-helm-conditionals.md)
    * [2024-04-22-helm-named-templates](k8s/helm/2024-04-22-helm-named-templates.md)
    * [2024-04-22-helm-chart-hooks](k8s/helm/2024-04-22-helm-chart-hooks.md)
    * [2024-04-22-helm-packaging-signing-uploading-charts](k8s/helm/2024-04-22-helm-packaging-signing-uploading-charts.md)

- ### Kustomize

  - #### Kustomize: Introduction
 
    * [2024-05-26-intro-to-kustomize](k8s/kustomize/2024-05-26-intro-to-kustomize.md)

  - #### Kustomize: Customization Techniques
 
    * [2024-05-26-kustomize-transformers](k8s/kustomize/2024-05-26-kustomize-transformers.md)
    * [2024-05-27-kustomize-patches](k8s/kustomize/2024-05-27-kustomize-patches.md)
    * [2024-05-27-kustomize-overlays](k8s/kustomize/2024-05-27-kustomize-overlays.md)
    * [2024-05-31-kustomize-components](k8s/kustomize/2024-05-31-kustomize-components.md)
    * [2024-05-31-secret-configmap-generator](k8s/kustomize/2024-05-31-secret-configmap-generator.md)
    * [2024-06-01-kustomize-imperative-commands](k8s/kustomize/2024-06-01-kustomize-imperative-commands.md)

- ### Kubernetes: Cluster Add-Ons

- #### Metrics
 
    * [2024-01-01-metrics-server](k8s/2024-01-01-metrics-server.md)

- #### Istio
 
  - ##### Istio: Introduction
 
    * [2024-04-24-intro-to-istio](k8s/istio/2024-04-24-intro-to-istio.md)
    * [2024-04-24-istio-installation](k8s/istio/2024-04-24-istio-installation.md)
    * [2024-04-24-istioctl-commands](k8s/istio/2024-04-24-istioctl-commands.md)
    * [2024-05-24-istioOperator](k8s/istio/2024-05-24-istioOperator.md)

  - ##### Istio: Traffic Management
 
    * [2024-04-26-istio-gateways](k8s/istio/2024-04-26-istio-gateways.md)
    * [2024-04-26-istio-virtual-services](k8s/istio/2024-04-26-istio-virtual-services.md)
    * [2024-04-26-istio-destination-rules](k8s/istio/2024-04-26-istio-destination-rules.md)
 
  - ##### Istio: Security
 
    * [2024-04-30-istio-security-authentication](k8s/istio/2024-04-30-istio-security-authentication.md)
    * [2024-04-30-istio-security-authorization](k8s/istio/2024-04-30-istio-security-authorization.md)

- ### Cert-Manager
 
    * [2024-05-01-intro-to-cert-manager](k8s/cert-manager/2024-05-01-intro-to-cert-manager.md)
    * [2024-05-01-cert-manager-issuers](k8s/cert-manager/2024-05-01-cert-manager-issuers.md)
    * [2024-05-01-cert-manager-certificates](k8s/cert-manager/2024-05-01-cert-manager-certificates.md)
  
## Jenkins

  * [2024-01-26-jenkins-cred-manipulation-groovys](jenkins/2024-01-26-jenkins-cred-manipulation-groovys.md)
  * [2024-04-18-neat-jenkinsfile-strategies](jenkins/2024-04-18-neat-jenkinsfile-strategies.md)

## Infrastructure as Code

- ### Ansible

  - #### Ansible: Inventory
 
    * [2024-06-02-introduction-to-ansible](iac/ansible/2024-06-02-introduction-to-ansible.md)
    * [2024-06-02-ansible-inventory](iac/ansible/2024-06-02-ansible-inventory.md)
    * [2024-06-04-ansible-inventory-techniques](iac/ansible/2024-06-04-ansible-inventory-techniques.md)
    * [2024-07-05-ansible-commands](iac/ansible/2024-07-05-ansible-commands.md)

  - #### Ansible: Variables
 
    * [2024-06-04-ansible-variables](iac/ansible/2024-06-04-ansible-variables.md)
    * [2024-06-05-ansible-facts](iac/ansible/2024-06-05-ansible-facts.md)

  - #### Ansible: Playbooks
 
    * [2024-06-05-ansible-playbooks](iac/ansible/2024-06-05-ansible-playbooks.md)
    * [2024-06-06-ansible-playbook-conditionals](iac/ansible/2024-06-06-ansible-playbook-conditionals.md)
    * [2024-06-07-ansible-playbooks-loops](iac/ansible/2024-06-07-ansible-playbooks-loops.md)
    * [2024-06-07-ansible-modules](iac/ansible/2024-06-07-ansible-modules.md)
    * [2024-06-08-ansible-plugins](iac/ansible/2024-06-08-ansible-plugins.md)
    * [2024-06-08-ansible-handlers](iac/ansible/2024-06-08-ansible-handlers.md)
    * [2024-06-21-ansible-blocks](iac/ansible/2024-06-21-ansible-blocks.md)
    * [2024-06-21-ansible-roles](iac/ansible/2024-06-21-ansible-roles.md)
    * [2024-06-22-ansible-collections](iac/ansible/2024-06-25-ansible-collections.md)
    * [2024-06-26-jinja2-templates](iac/ansible/2024-06-26-jinja2-templates.md)
    * [2024-06-26-ansible-builtin-jinja2-templating](iac/ansible/2024-06-26-ansible-builtin-jinja2-templating.md)
    * [2024-07-05-ansible-configurations](iac/ansible/2024-07-05-ansible-configurations.md)
    * [2024-07-27-top-level-ansible-fields](iac/ansible/2024-07-27-top-level-ansible-fields.md)
    * [2024-07-27-ansible-file-separation](iac/ansible/2024-07-27-ansible-file-separation.md)
    * [2024-08-05-ansible-vault](iac/ansible/2024-08-05-ansible-vault.md)
       
## Amazon Web Services

- ### Identity and Access Management

  * [2023-04-22-cross-account-assume-role](aws/2023-04-22-cross-account-assume-role.md)

- ### EKS

  * [2024-01-27-eks-rbac](aws/2024-01-27-eks-rbac.md)
  * [2024-01-29-iam-roles-and-serviceaccounts](aws/2024-01-29-iam-roles-and-serviceaccounts.md)
  * [2024-01-31-aws-image-security](aws/2024-01-31-aws-image-security.md)

## Google Cloud Platform

- ### VPC Networks

  * [2023-05-30-gcp-networking](gcp/2023-05-30-gcp-networking.md)
  * [2023-05-30-gcp-ssh-tunneling](gcp/2023-05-30-gcp-ssh-tunneling.md)
