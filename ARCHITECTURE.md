# Home Lab Architecture

This document outlines the target architecture for this Kubernetes home lab project. The entire system is designed around a GitOps workflow, where the GitHub repository is the single source of truth.

## Architecture Diagram

The diagram below illustrates the flow of configuration from the developer to the live applications running in the cluster.

```mermaid
graph TD
    subgraph "Local Development"
        A[Developer]
    end

    subgraph "Source of Truth"
        B[GitHub Repo: minikube-homelab]
    end

    subgraph "Kubernetes Cluster (Minikube/AKS)"
        C[Argo CD]

        subgraph "Platform Services"
            D[Observability: Prometheus, Grafana, Loki]
            E[Networking: Istio, Traefik, Cert-Manager]
            F[Storage: Longhorn]
            G[Security: Kyverno, External Secrets]
        end

        subgraph "Deployed Applications"
            H[App 1]
            I[App 2]
            J[...]
        end
    end

    A -- "1. git push (YAML manifests)" --> B
    C -- "2. Watches Repo for Changes" --> B
    C -- "3. Applies Manifests to Cluster" --> D
    C -- "3. Applies Manifests to Cluster" --> E
    C -- "3. Applies Manifests to Cluster" --> F
    C -- "3. Applies Manifests to Cluster" --> G
    C -- "3. Applies Manifests to Cluster" --> H
    C -- "3. Applies Manifests to Cluster" --> I
    C -- "3. Applies Manifests to Cluster" --> J

    style B fill:#bbf,stroke:#333,stroke-width:2px
    style C fill:#f9f,stroke:#333,stroke-width:2px
```

## Workflow

1.  **Commit & Push:** The platform engineer (or developer) makes all configuration changes—from deploying a new application to updating a security policy—by committing YAML manifests to the `minikube-homelab` Git repository.
2.  **Argo CD Sync:** Argo CD, running inside the Kubernetes cluster, continuously monitors the repository. When it detects a change, it automatically pulls the new configuration.
3.  **Deploy:** Argo CD compares the desired state from the Git repository with the actual state of the cluster and applies the necessary changes to bring the cluster into alignment. This ensures that the Git repository is always the single source of truth.
