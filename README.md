# Argo CD with Traefik Ingress – Install via Helm

This repository provides a Helm-based setup to install **Argo CD** into a Kubernetes cluster with **Traefik Ingress** for external access.
It handles bootstrapping Argo CD, retrieving the initial admin password, and securing the installation.

---

## Prerequisites

* A running Kubernetes cluster and `kubectl` configured
* Helm v3.9+
* Permissions to create namespaces and cluster resources

---

## 1) Clone the repository

## 2) Create and edit `values.yaml`

```bash
cp values.sample.yaml values.yaml && vim values.yaml
```

Example `values.yaml`:

```yaml
host: test.example.com
```

Update `host` to your own domain.
Traefik will expose Argo CD on this hostname.

---

## 3) Install / upgrade Argo CD with Helm

```bash
helm dependency build chart
helm upgrade --install argocd chart --create-namespace -n argocd --values values.yaml
```

This deploys Argo CD with Traefik ingress in the `argocd` namespace.

---

## 4) Retrieve the initial admin password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d && echo
```

Copy and store the password securely.

---

## 5) Delete the initial admin secret (cleanup / hardening)

⚠️ Do this **only after** logging in with the initial password and changing the `admin` password (or disabling the `admin` account).

```bash
kubectl -n argocd delete secret argocd-initial-admin-secret
```

---

## Access Argo CD

Once Traefik ingress and DNS are set up, open:

```
https://test.example.com
```

(or whatever `host` you set in `values.yaml`)

Login with:

* **Username:** `admin`
* **Password:** (from step 4)

---

## CLI Login & Change Password

### Login via CLI

```bash
argocd login test.example.com --username admin
```

Replace `<password>` with the one from step 4.

### Update Password

```bash
argocd account update-password
```

This will prompt for the old and new password.
