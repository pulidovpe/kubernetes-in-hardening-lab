# Kubernetes con K3s - Seguridad y Hardening

RBAC · Network Policies · Secrets · Trivy · Falco
Edición UTM + Apple Silicon (M4) — K3s sobre Ubuntu Server 22.04

Repository dedicated to documenting hardening practices, access controls, network security, and runtime auditing in a lightweight Kubernetes (K3s) cluster.

---

## 📅 Log de Laboratorios Diarios

| Día | Enfoque Tecnológico | Entidades Evaluadas / Entregables | Estado |
| :--- | :--- | :--- | :--- |
| **Día 1** | Instalación K3s: server en VM1 + agent en VM2  | `nodos.txt`, `versiones.txt` | 🟢 Completado |
| **Día 2** | Workloads seguros: Pods, Deployments y namespaces | `pods-produccion.txt` | 🟢 Completado |
| **Día 3** | RBAC: usuarios, roles y permisos mínimos | `rbac-estado.txt`, `csr-dev-juan.yaml` | 🟢 Completado |
| **Día 4** | Network Policies: segmentación de tráfico entre pods | `network-policies.txt`, `netpol-produccion.yaml` | 🟢 Completado |
| **Día 5** | Secrets: gestión segura de credenciales en K8s | `secrets-inventario.txt`, `pod-con-secret.yaml` | 🟢 Completado |
| **Día 6** | Trivy + Falco: auditoría de imágenes y runtime | **Trivy Static Scanning, Falco Security DaemonSet (eBPF)** | 🟢 Completado |
| **Día 7** | *Simulación de ataques y análisis forense* | `evidencia/` | 🟢 Completado |
| **Día 8** | *Documentación, GitHub* | `kube-bench-report-final.txt` | 🟢 Completado |

---
## 🚀 Detalles de las Últimas Sesiones

### Día 7 - Simulación de Ataques + Evidencia Forense
Se ejecutaron simulaciones controladas de compromisos de contenedores desde la `vm3-atacante`.
* **Acciones:** Inyección de payloads de lectura sobre `/etc/shadow` y ejecución de binarios sospechosos en caliente.
* **Resultado:** Validación de las reglas de telemetría de Falco y captura exitosa de logs de auditoría forense que confirman la contención del ataque.

### Día 8 - Kube-Bench CIS Compliance + Cierre
Auditoría formal de las configuraciones de seguridad del Control Plane y los Nodos Trabajadores frente a los estándares de la industria.
* **Estrategia Utilizada:** Implementación del set de configuraciones clonadas externamente ejecutando:
  ```bash
  sudo kube-bench run --config-dir /etc/kube-bench/cfg --version 1.31 > kube-bench-report-vm2.txt
  ```

* **Entregables:** Reportes de conformidad almacenados en el repositorio listos para su remediación en futuras iteraciones.
---

## 🛠️ Próxima Fase: GitOps & Continuous Delivery con ArgoCD

Una vez concluido el ciclo de hardening de 8 días, el laboratorio evolucionará hacia un modelo de **GitOps** para automatizar y sincronizar de forma segura toda la infraestructura declarativa de este repositorio.



### Objetivos de la Fase GitOps:
1. **Despliegue Declarativo:** Automatizar la aplicación de todos los manifiestos de la carpeta `k3s/manifests/` (Network Policies, RBAC, Falco Values, etc.) mediante ArgoCD.
2. **Prevención de Drift (Desviación de Configuración):** Configurar ArgoCD en modo *Auto-Sync* y *Prune* para revertir automáticamente cualquier cambio manual no autorizado que ocurra directamente en el clúster con `kubectl`.
3. **Estrategia Segura de Repositorios:** Separar las credenciales sensibles y aplicar prácticas de mínimo privilegio a las ServiceAccounts que usará el controlador de ArgoCD.
