
# 🔐 FASE 4: Seguridad en Kubernetes

## 🧭 Introducción

La seguridad en un clúster Kubernetes no es un añadido, sino una necesidad esencial. En esta fase se implementarán las primeras medidas para proteger el acceso al clúster, controlar los privilegios de los usuarios, gestionar secretos de forma segura y limitar las comunicaciones entre pods. Estas prácticas representan los pilares básicos de la seguridad en Kubernetes.

Nos centraremos en:
- Control de acceso basado en roles (RBAC)
- Gestión de secretos con `Secrets`
- Políticas de red (`NetworkPolicies`) para aislar pods

---

## 1. Control de acceso con RBAC (Role-Based Access Control)

Kubernetes incorpora RBAC para definir **qué puede hacer cada usuario** dentro del clúster. Esto es esencial para evitar que cualquier usuario tenga permisos de administración completa.

### 👤 Crear un usuario con acceso limitado

1. Crear el ServiceAccount:
```bash
kubectl create serviceaccount operador
```

2. Crear una `Role` con permisos básicos:

```yaml
# archivo: role-lectura.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: lector-pods
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

3. Asociar la Role al usuario:

```yaml
# archivo: rolebinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: acceso-lector
  namespace: default
subjects:
- kind: ServiceAccount
  name: operador
  namespace: default
roleRef:
  kind: Role
  name: lector-pods
  apiGroup: rbac.authorization.k8s.io
```

Aplicar ambos manifiestos:
```bash
kubectl apply -f role-lectura.yaml
kubectl apply -f rolebinding.yaml
```

✅ Ahora, el usuario `operador` solo puede listar y ver pods en el namespace `default`.

---

## 2. Gestión de secretos con Kubernetes Secrets

Los secretos (`Secrets`) permiten almacenar información sensible, como contraseñas o tokens, de forma cifrada en el clúster.

### 🔐 Crear un secreto sencillo:
```bash
kubectl create secret generic db-password --from-literal=password=MiContraseñaSuperSegura123
```

Puedes ver su contenido:
```bash
kubectl get secret db-password -o yaml
```

**Nunca debes codificar contraseñas directamente en los manifiestos de despliegue.** Usa `envFrom` o referencias indirectas.

### 📦 Usar secretos en un pod:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ejemplo-secreto
spec:
  containers:
  - name: app
    image: nginx
    env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-password
          key: password
```

---

## 3. Políticas de red (NetworkPolicies)

Por defecto, todos los pods pueden comunicarse entre sí. Esto es peligroso en entornos reales. Las `NetworkPolicies` permiten limitar qué pods pueden hablar con cuáles.

### 🔒 Ejemplo: permitir acceso solo desde un pod concreto
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: acceso-desde-backend
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: base-datos
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: backend
```

Este ejemplo solo permite que los pods con la etiqueta `app=backend` accedan a los pods con etiqueta `app=base-datos`.

📌 Es necesario tener un CNI que soporte políticas de red, como **Calico**.

---

## ✅ Resultado final de la Fase 4

- Se ha limitado el acceso mediante cuentas de servicio y RBAC.
- Se han gestionado credenciales sensibles usando `Secrets`.
- Se ha comenzado a aplicar un modelo de red **zero-trust**, donde se define explícitamente quién puede comunicarse con quién.

Con estos cimientos, el clúster se vuelve más robusto, confiable y preparado para entornos reales con múltiples usuarios o aplicaciones.
