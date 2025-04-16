# 🚀 FASE 2: Instalación del clúster Kubernetes con kubeadm

### 🔰 Introducción
En esta segunda fase del proyecto, se procederá a la instalación y configuración básica de un clúster Kubernetes sobre las máquinas virtuales previamente creadas en Proxmox. Se utilizará kubeadm, una herramienta oficial que facilita la inicialización del clúster de forma segura y reproducible.

Durante esta fase, se instalarán los componentes principales en el nodo maestro y se unirán los nodos trabajadores al clúster, estableciendo la base para el despliegue de aplicaciones en fases posteriores.

## 1. Requisitos previos en todas las máquinas.
Antes de instalar Kubernetes, se deben cumplir ciertos requisitos en todos los nodos (master y workers).

### 🧩 Desactivar swap:
```
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

### 🔧 Cargar módulos del kernel necesarios:
```
echo 'br_netfilter' | sudo tee /etc/modules-load.d/k8s.conf
```

### 🔧 Ajustes de red:
```
echo 'net.bridge.bridge-nf-call-ip6tables = 1' | sudo tee -a /etc/sysctl.d/k8s.conf
echo 'net.bridge.bridge-nf-call-iptables = 1' | sudo tee -a /etc/sysctl.d/k8s.conf
sudo sysctl --system
```

## 2. Instalación de Docker o containerd.
Kubernetes necesita un runtime de contenedores. En este caso utilizaremos containerd.

### 🐳 Instalar containerd:
```
sudo apt update && sudo apt install -y containerd
```

### 🔧 Configurar containerd:
```
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd
sudo systemctl enable containerd
```

## 3. Instalación de kubeadm, kubelet y kubectl.
### 🧪 Comandos de instalación:
```
sudo apt update && sudo apt install -y apt-transport-https ca-certificates curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## 4. Inicialización del nodo master.
Este paso se realiza solo en el nodo master para inicializar el clúster Kubernetes.

### ⚙️ Ejecutar:
```
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```
### 👤 Configurar el acceso como usuario normal:
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## 5. Instalación del complemento de red (CNI) desde el nodo master.
Kubernetes necesita un complemento de red para la comunicación entre pods. Este paso se realiza desde el nodo master, una vez configurado el acceso a kubectl.

### 🌐 Instalar Calico:
```
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

## 6. Unión de nodos workers al clúster.
Este paso se realiza en cada nodo worker para que se unan al clúster ya creado.

### 👇 Ejecutar el comando proporcionado tras la inicialización del master:
```
sudo kubeadm join 192.168.1.100:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

---

### ✅ Resultado final de la Fase 2.
Tras finalizar esta fase:
- Se ha creado un clúster Kubernetes funcional con un nodo maestro y uno o más nodos trabajadores.
- Se ha instalado un complemento de red (Calico) para la comunicación entre pods.
- Todos los nodos están correctamente conectados y sincronizados.
- El entorno está listo para desplegar aplicaciones y añadir funcionalidades como monitorización o CI/CD en las siguientes fases.

