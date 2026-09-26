# K8s-rbac-audit-escape
Deployed and audited a vulnerable Kubernetes environment to simulate a container-to-host breakout. Evaluated threat vectors involving privileged execution contexts, host namespace sharing, and loose ClusterRoleBindings, followed by implementing runtime remediation strategies via Pod Security Standards.
# ☸️ Kubernetes RBAC Audit & Privileged Pod Escape Defense

A hands-on engineering analysis focused on production cluster security auditing, threat detection, and container escape containment.

## 🔍 Vulnerability Profile: payment-debug-daemon.yaml
A security audit of active cluster workloads exposed a critical misconfiguration within the `payment-gateway` namespace. The deployment allowed an aggressive container-to-host breakout vector.

### 🔴 Core Risk Indicators Identified
* **Host Namespace Leakage:** The manifest explicitly uses `hostPID: true` and `hostNetwork: true`, exposing the underlying node infrastructure directly to the container runtime.
* **Privileged Execution Context:** The container runs with `privileged: true`, granting root-level capability extensions over the host kernel.
* **Dangerous Volume Mounts:** A `hostPath` volume mounts the host's root file system (`/`) directly into `/host` inside the container, allowing complete host filesystem takeover.

---

## 🛠️ Telemetry & Technical Evidence
The following telemetry artifacts document the exact misconfigurations discovered during the live runtime cluster audit:

| Audit Context | Configuration Evidence |
| :--- | :--- |
| **RBAC ClusterRoleBinding Discovery** | ![Cluster Role Auditing](./evidence/rbac-binding.png) |
| **Privileged Pod Security Context** | ![Privileged Security Context](./evidence/privileged-pod-manifest.png) |

---

## 🛡️ Remediation Strategy
1. **Admission Control Validation:** Enforce Pod Security Standards (PSS) at a `restricted` level to block privileged pods natively.
2. **Context Hardening:** Force `allowPrivilegeEscalation: false` within all active container security contexts.
3. **Storage Isolation:** Deprecate the use of local `hostPath` volumes in favor of managed, isolated Persistent Volume Claims (PVC).
