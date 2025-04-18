![Banner La Marisma](/.imgs/documentation/ies.png)

# FASE 1: Preparación del entorno de trabajo en Proxmox

### 🔰 Introducción.
Antes de poder desplegar un clúster Kubernetes, es fundamental contar con un entorno de trabajo preparado, estable y replicable. En esta primera fase del proyecto, se utilizará el servidor Proxmox del instituto para virtualizar los nodos que formarán parte del clúster. Esta etapa es crucial ya que sentará las bases para el resto de las fases: se crearán y configurarán las máquinas virtuales necesarias, se instalará el sistema operativo Ubuntu Server y se establecerán los ajustes iniciales que garantizarán un correcto funcionamiento y conectividad entre los nodos.

El objetivo principal es dejar listo el entorno virtualizado para la posterior instalación de Kubernetes, asegurando que cada nodo disponga de los recursos y servicios mínimos indispensables para operar como parte de un clúster empresarial.

## 1. Acceso al entorno Proxmox
El entorno de virtualización se gestiona mediante la interfaz web de Proxmox. Para acceder, se utiliza el navegador accediendo a la siguiente dirección:

``https://DIRECCION_IP:8006``

Se inicia sesión con las credenciales proporcionadas por el centro educativo, ya sea como alumno o con permisos de administración según la organización establecida.

---

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

### 🛠️ Pasos para crear una VM en Proxmox:

1. Pulsar en "Crear VM" en el panel izquierdo.
2. Asignar un ID y nombre identificativo (por ejemplo, k8s-master).
3. Seleccionar la ISO de Ubuntu Server 22.04 previamente subida al almacenamiento.
4. Establecer:
    - Tipo de disco: VirtIO (recomendado por rendimiento).
    - Cantidad de CPU y RAM según necesidades.
    - Tipo de red: bridge (vmbr0).
5. Confirmar y crear la máquina.

Este proceso se repite para la segunda VM (k8s-worker1) utilizando la misma configuración.

---

## 3. Instalación de Ubuntu Server
Al iniciar las VMs creadas, se lanza el instalador de Ubuntu Server 22.04. El proceso es sencillo y guiado.

### 📋 Configuración recomendada:
- Idioma: Español
- Nombre del host: master o worker1
- Nombre de usuario: abel (personalizable)
- Contraseña segura
- Red: DHCP o configuración IP estática si se requiere
- Activar la opción "Instalar servidor OpenSSH"

Una vez concluida la instalación, se reinicia el sistema y se accede con las credenciales creadas.

---

4. Configuración inicial del sistema
Con Ubuntu ya operativo, se realiza una configuración básica común a todas las VMs para garantizar su correcto funcionamiento.

### 🔄 Actualizar el sistema y establecer la zona horaria
```
sudo apt update && sudo apt upgrade -y
sudo timedatectl set-timezone Europe/Madrid
```

### 🌐 Verificar conectividad de red
```
ip a
ping google.com -c 4
```
### 🔐 Activar y verificar el servicio SSH
```
sudo systemctl enable ssh
sudo systemctl start ssh
sudo systemctl status ssh
```
Esta configuración permite acceder a la máquina de forma remota, facilitando la administración del clúster desde una terminal o sistema externo.

--- 

### ✅ Resultado final de la Fase 1

Al completar esta fase, se dispone de:

- Dos máquinas virtuales en Proxmox con Ubuntu Server 22.04 LTS completamente operativas.

- Configuración básica aplicada: sistema actualizado, zona horaria correcta, conectividad y SSH habilitado.

Estas VMs están completamente preparadas para dar paso a la Fase 2: Instalación del clúster Kubernetes, donde se iniciará el proceso de despliegue del entorno de contenedores.

---

<div align="center">

### ✍️ Trabajo realizado por

# **Abel Sánchez Ramos**

_2º Administración de Sistemas Informáticos en Red (ASIR)_

📅 Marzo de 2025  
📍 IES La Marisma; Avda Santa Marta, Huelva

---

> **“No se trata solo de proteger sistemas, sino de entender que cada línea de código, cada puerto abierto y cada contraseña débil es una posible historia de ataque aún no contada.”**

</div>
