# FASE 1: Preparación del entorno de trabajo en Proxmox

## 🔰 Introducción.
Antes de poder desplegar un clúster Kubernetes, es fundamental contar con un entorno de trabajo preparado, estable y replicable. En esta primera fase del proyecto, se utilizará el servidor Proxmox del instituto para virtualizar los nodos que formarán parte del clúster. Esta etapa es crucial ya que sentará las bases para el resto de las fases: se crearán y configurarán las máquinas virtuales necesarias, se instalará el sistema operativo Ubuntu Server y se establecerán los ajustes iniciales que garantizarán un correcto funcionamiento y conectividad entre los nodos.

El objetivo principal es dejar listo el entorno virtualizado para la posterior instalación de Kubernetes, asegurando que cada nodo disponga de los recursos y servicios mínimos indispensables para operar como parte de un clúster empresarial.

## 1. Acceso al entorno Proxmox
El entorno de virtualización se gestiona mediante la interfaz web de Proxmox. Para acceder, se utiliza el navegador accediendo a la siguiente dirección:

``https://DIRECCION_IP:8006``

Se inicia sesión con las credenciales proporcionadas por el centro educativo, ya sea como alumno o con permisos de administración según la organización establecida.

## 2. Creación de máquinas virtuales (VMs).
Para la creación del clúster, se requieren al menos dos máquinas virtuales:
- k8s-master: Nodo maestro que actuará como controlador del clúster.
- k8s-worker1: Nodo trabajador que ejecutará los pods del clúster.

### 💡 Recursos recomendados por máquina virtual:

| Recurso | Valor recomendado            |
|---------|------------------------------|
| CPU     | 2 vCPU                       |
| RAM     | 2-4 GB                       |
| Disco   | 20 GB (almacenamiento local) |
| SO      | Ubuntu Server 22.04 LTS      |
| Red     | Bridge (vmbr0 o equivalente) |

