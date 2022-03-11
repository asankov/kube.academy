# 01. Packaging

For a typical two-tier (frontend and backend) application we need more or less the following Kubernetes resources:

- `Deployment` for the frontend services
- `Deployment` for the backend services
- `Service` to expose to frontend services to the world
- `Service` to expose to backend services to the frontend services
- `ConfigMap` to configure the frontend and backend
- `Secret` to configure the communication between frontend and backend
- `Secret` to configure the communication between backend and database
- `Volume` and `PersistentVolumeClaim` for the database volume
- etc.

If we package these resource into a unified bundle that will help with a lot if we want to have multiple deployments of the application with different parameters.

So we need a way to package our Kubernetes resource into this bundle:

- Helm
- Kubernetes operators
- CNAB Application bundle
- ksonnet (deprecated)

## Helm

Developed by Deis (now part of Microsoft).
Bitnami is a main contributor.

Packaging format: Chart

Chart catalog available. Knows as `apt-get` of Kubernetes.
