# Kubernetes con K3s - Seguridad y Hardening

RBAC · Network Policies · Secrets · Trivy · Falco
Edición UTM + Apple Silicon (M4) — K3s sobre Ubuntu Server 22.04

Repository dedicated to documenting hardening practices, access controls, network security, and runtime auditing in a lightweight Kubernetes (K3s) cluster.

---

## 📅 Log de Laboratorios Diarios

| Día | Enfoque Tecnológico | Entidades Evaluadas / Entregables | Estado |
| :--- | :--- | :--- | :--- |
| **Día 1** | Reconocimiento inicial | `nodos.txt`, `versiones.txt` | 🟢 Completado |
| **Día 2** | Auditoría de cargas de trabajo | `pods-produccion.txt` | 🟢 Completado |
| **Día 3** | Gestión de Accesos (RBAC) | `rbac-estado.txt`, `csr-dev-juan.yaml` | 🟢 Completado |
| **Día 4** | Seguridad de Red (Network Policies) | `network-policies.txt`, `netpol-produccion.yaml` | 🟢 Completado |
| **Día 5** | Gestión de Secretos | `secrets-inventario.txt`, `pod-con-secret.yaml` | 🟢 Completado |
| **Día 6** | Seguridad en Tiempo de Ejecución (Runtime) | **Trivy Static Scanning, Falco Security DaemonSet (eBPF)** | 🟢 Completado |
| **Día 7** | *Por definir / Simulación de ataques y análisis forense* | *Próximamente* | 🟡 Planificado |
| **Día 8** | *Por definir / Documentación, GitHub y reflexión final* | *Próximamente* | 🟡 Planificado |

---

## 🚀 Detalle: Día 6 - Trivy + Falco Runtime Security

En esta sesión se implementó una estrategia defensiva de dos capas: **Análisis Estático (Shift Left)** y **Seguridad Activa en Caliente (Runtime Security)**.

### 1. Escaneo de Vulnerabilidades con Trivy
Se instaló `trivy` en el nodo de gestión (`vm3-atacante`) para auditar las imágenes de contenedor antes y durante su ciclo de vida en el clúster.
* **Evidencia:** Reportes almacenados en `k3s/dia6/`.

### 2. Monitoreo en Tiempo de Ejecución con Falco (DaemonSet)
Para garantizar visibilidad total sin importar el agendamiento de Pods (`vm1-objetivo`, `vm2`), se migró la arquitectura hacia un modelo nativo en Kubernetes utilizando **Helm**.

* **Motor Utilizado:** Sonda de eBPF Moderno (`modern_ebpf`), óptimo para entornos virtualizados ARM64 sobre UTM.
* **Despliegue:** `helm install falco falcosecurity/falco -f k3s/manifests/falco-values.yaml --namespace falco`

#### Reglas Personalizadas de Auditoría Implementadas:
* **Detección de Lectura de Archivos Sensibles:** Alerta crítica disparada mediante syscalls exitosas (`open_read`) enfocadas en los contenedores (`container.id != host`) apuntando a `/etc/shadow` y `/etc/passwd`.
* **Filtro de Falsos Positivos:** Exclusión exitosa de llamadas masivas originadas por demonios del sistema operativo base (como `auditd` del host).

---

## 🛠️ Próxima Fase: GitOps & Continuous Delivery con ArgoCD

Una vez concluido el ciclo de hardening de 8 días, el laboratorio evolucionará hacia un modelo de **GitOps** para automatizar y sincronizar de forma segura toda la infraestructura declarativa de este repositorio.



### Objetivos de la Fase GitOps:
1. **Despliegue Declarativo:** Automatizar la aplicación de todos los manifiestos de la carpeta `k3s/manifests/` (Network Policies, RBAC, Falco Values, etc.) mediante ArgoCD.
2. **Prevención de Drift (Desviación de Configuración):** Configurar ArgoCD en modo *Auto-Sync* y *Prune* para revertir automáticamente cualquier cambio manual no autorizado que ocurra directamente en el clúster con `kubectl`.
3. **Estrategia Segura de Repositorios:** Separar las credenciales sensibles y aplicar prácticas de mínimo privilegio a las ServiceAccounts que usará el controlador de ArgoCD.
