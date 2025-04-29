
# ⚙️ FASE 3: Configuración del clúster Kubernetes

## 🔰 Introducción

Una vez desplegado el clúster Kubernetes, es necesario realizar una serie de configuraciones adicionales para garantizar su correcto funcionamiento, accesibilidad y gestión. En esta fase se configurará la red de pods, se desplegará una interfaz gráfica de administración (Kubernetes Dashboard) y se habilitará el soporte para volúmenes persistentes.

Estas configuraciones permitirán administrar el clúster de forma visual, almacenar datos de forma persistente en los pods, y facilitar el despliegue de aplicaciones reales.

---

## 1. Verificar estado del clúster (desde el nodo master)

```bash
kubectl get nodes
```

Este comando debe mostrar todos los nodos en estado `Ready`. Si alguno aparece como `NotReady`, revisar la instalación anterior.

---

## 2. Revisión del complemento de red (Calico)

Comprobar que Calico esté corriendo correctamente:

```bash
kubectl get pods -n kube-system
```

Debes ver pods tipo `calico-node-xxxx` con estado `Running`.

---

## 3. Desplegar Kubernetes Dashboard

Kubernetes ofrece una interfaz gráfica web para la administración del clúster.

### 🔧 Desplegar Dashboard

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

### 🌐 Crear un usuario administrador

Crear el archivo `admin-user.yaml`:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

Aplicar el archivo:

```bash
kubectl apply -f admin-user.yaml
```

### 🔑 Obtener token de acceso

```bash
kubectl -n kubernetes-dashboard create token admin-user
```

### 🌍 Acceder al Dashboard

Iniciar un proxy:

```bash
kubectl proxy
```

Abrir en el navegador:

```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

Seleccionar **Token** e introducir el token copiado.

---

## 4. Activar volúmenes persistentes con hostPath

Para pruebas locales se puede usar el tipo de almacenamiento `hostPath`:

```yaml
volumes:
  - name: datos-app
    hostPath:
      path: /mnt/data
      type: DirectoryOrCreate
```

Esto crea una carpeta persistente en el nodo donde se ejecuta el pod.

---

## ✅ Resultado final de la Fase 3

- El clúster es accesible mediante `kubectl` y desde el Dashboard.
- Los nodos están funcionando correctamente con red Calico.
- Se han habilitado volúmenes persistentes para uso posterior.

Con esta configuración, el entorno está listo para desplegar servicios reales de forma cómoda y profesional.
