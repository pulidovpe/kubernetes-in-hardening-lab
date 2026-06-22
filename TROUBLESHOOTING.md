# TROUBLESHOOTING — Lab 6 completo

## DNS timeout en pods (connection timed out)
## Causa: Puerto 8472/udp cerrado en UFW en alguna VM
## Flannel VXLAN necesita 8472/udp en VM1 Y VM2

# Verificar: sudo ufw status | grep 8472
# Solución: sudo ufw allow 8472/udp en VM1 y VM2
# Síntoma característico: pod en VM2 no llega a pod en VM1
# kubectl exec <pod-vm2> -- ping <pod-ip-vm1> -> 100% loss

## NO instalar kube-router en K3s 1.35 ARM64
## Es incompatible y corrompe iptables bloqueando todo el tráfico
## Si se instaló por error, reinstalar K3s completamente:

# sudo /usr/local/bin/k3s-uninstall.sh # en VM1
# sudo /usr/local/bin/k3s-agent-uninstall.sh # en VM2

## kubectl auth can-i --list --as=dev-juan falla desde VM3
## Causa: --as requiere permisos de impersonación (solo admin)
## Desde VM3 con kubeconfig de dev-juan, usar SIN --as:

# kubectl auth can-i --list -n desarrollo

## Desde VM1 con kubeconfig admin, sí funciona con --as
## kubectl apply de ServiceAccount falla con Forbidden
## Causa: KUBECONFIG de dev-juan activo desde el paso anterior
## Solución: export KUBECONFIG=~/.kube/config
## K3s no arranca con encryption-config
## Ver: sudo journalctl -u k3s -n 30 | grep -i error
## La clave AES debe tener exactamente 44 chars (32 bytes base64)

# Verificar: echo -n $ENCRYPTION_KEY | wc -c

## Falco no detecta eventos de contenedores
## El módulo del kernel no cargó

# Verificar: lsmod | grep falco
# Solución: sudo falco-driver-loader --compile

## CSR de usuario queda en Pending

# Solución: kubectl certificate approve dev-juan
# Verificar: kubectl get csr (debe pasar a Approved,Issued)

## nslookup kubernetes.default falla con NXDOMAIN
## El nombre corto no resuelve — usar siempre el FQDN:

# kubectl exec <pod> -- \
# nslookup kubernetes.default.svc.cluster.local

## 📌 Error config file is missing 'target_mapping' section en Kube-Bench
## Síntoma: kube-bench aborta la ejecución al intentar auditar K3s.

## Causa Raíz: El binario global no puede asociar automáticamente las estructuras comprimidas de K3s con el estándar CIS clásico de Kubernetes.

## Solución: Clonar el repositorio oficial, mapear las configuraciones globalmente y forzar el flag de la versión del benchmark de la siguiente manera:

git clone [https://github.com/aquasecurity/kube-bench.git](https://github.com/aquasecurity/kube-bench.git)
sudo mkdir -p /etc/kube-bench
sudo cp -r kube-bench/cfg /etc/kube-bench/
sudo kube-bench run --config-dir /etc/kube-bench/cfg --version 1.35 > kube-bench-report-vm2.txt


