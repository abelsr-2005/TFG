![](/.imgs/documentation/ies.img)

# Implementación y gestión de contenedores con Kubernetes en entornos empresariales.
#### Abel Sánchez Ramos

## 1. Introducción.
En los últimos años, la tecnología ha evolucionado a grandes escalas, y con ella, la forma en que las empresas gestionan sus aplicaciones y servicios. En este contexto, Kubernetes ha emergido como una solución clave para orquestar contenedores, facilitando la automatización, la escalabilidad y la eficiencia en los despliegues de software. ¿Pero qué significa realmente esto en el día a día de una empresa?

Imaginemos una organización que necesita ejecutar múltiples aplicaciones en diferentes entornos, como servidores locales y la nube. Sin una herramienta adecuada, la gestión de estos sistemas se vuelve caótica y propensa a errores. Kubernetes llega para resolver estos problemas, permitiendo que las aplicaciones se desplieguen de forma automática, se adapten a la demanda y se mantengan en funcionamiento sin intervención manual constante.

Desde su creación por Google y su posterior donación a la Cloud Native Computing Foundation, Kubernetes ha ganado una gran adopción en el mundo empresarial. Su flexibilidad y potencia han hecho que compañías de todos los tamaños lo implementen para mejorar sus procesos. No obstante, su uso no está exento de desafíos: la curva de aprendizaje puede ser pronunciada y requiere una planificación adecuada para aprovechar todo su potencial.

Este trabajo explorará cómo implementar y gestionar Kubernetes en entornos empresariales, desglosando sus ventajas, los desafíos que presenta y las mejores prácticas para su administración. A lo largo del documento, abordaremos desde los conceptos básicos de su arquitectura hasta su integración con herramientas de monitorización y seguridad, con el objetivo de proporcionar una visión clara y práctica de su uso en el mundo real.

### 1.1. Objetivos del trabajo.
El propósito de este trabajo es proporcionar un conocimiento detallado y práctico sobre Kubernetes, centrándose en su implementación y gestión en entornos empresariales. Para ello, se han establecido los siguientes objetivos específicos:

 - Comprender el funcionamiento y la arquitectura de Kubernetes.
Se realizarán actividades como, analizar la estructura y sus componentes principales. Se estudiará como interactúan los nodos, pods y servicios dentro de un clúster. Pol último conoceremos los mecanismos internos de comunicación y gestión de cargas de trabajo.
 - Aprender a instalar y configurar un clúster Kubernetes.
En este punto trataremos actividades como explorar diferentes métodos de instalación como Minikube, Kubeadm y servicios en la nube. También configuraremos entornos de desarrollo y producción, asegurando su estabilidad y rendimiento. Por último, implementaremos estrategias para garantizar alta disponibilidad y tolerancia a fallos. 
 - Implementar buenas prácticas de seguridad y monitorización.
Aquí aplicaremos políticas de control de acceso mediante RBAC. Configuraremos herramientas como Prometheus y Grafana para la monitorización del clúster. Por último, gestionaremos secretos y configuraciones sensibles con ConfigMaps y Secrets.
 - Desplegar una aplicación en Kubernetes utilizando un pipeline de CI/CD.
Automatizaremos el despliegue de aplicaciones mediante herramientas como GitHub Actions y ArgoCD. Integraremos Kubernetes con entornos de integración y entrega continua. Por último, analizaremos estrategias para el versionado y rollback de aplicaciones en producción.
 - Evaluar casos de uso empresarial y su impacto en la administración de sistemas.
En este último punto trataremos ejemplos reales de empresas que han integrado Kubernetes. Analizaremos los beneficios y desafíos de la migración de aplicaciones monolíticas a contenedores. Por último, identificaremos tendencias futuras en la adopción y evolución de Kubernetes en la industria.

### 1.2. Metodología.
Para abordar este estudio, se adoptará un enfoque práctico. Se llevará a cabo la implementación de un clúster Kubernetes y se analizarán casos de uso reales dentro del sector empresarial. A través de pruebas y experimentos en un entorno controlado, se recopilarán métricas sobre el rendimiento, la escalabilidad y la seguridad de Kubernetes, permitiendo una evaluación detallada de sus capacidades y limitaciones.

## 2. Fundamentos de Kubernetes.
### 2.1. Historia y evolución.
Kubernetes tiene su origen en Google, donde nació como proyecto interno llamado Borg. Borg fue el sistema utilizado por Google para gestionar sus contenedores a gran escala, y con el tiempo, la empresa decidió compartir su experiencia con el mundo a través de un proyecto de código abierto llamado Kubernetes. En 2015, este proyecto fue donado a Cloud Native Computing Foundation, lo que permitió su rápido crecimiento y adopción en la industria tecnológica.

Desde su lanzamiento, ha revolucionado la forma en la que las empresas implementan y gestionan sus aplicaciones. Gracias a su naturaleza de código abierto y su fuerte respaldo por parte de la comunidad, se ha convertido en el estándar de facto para la orquestación de contenedores. Empresas de todos los tamaños han implementado Kubernetes, desde startups que buscan flexibilidad hasta grandes corporaciones que necesitan gestionar miles de aplicaciones en la nube.

### 2.2. Arquitectura básica.
Kubernetes trabaja en un modelo maestro-trabajador, donde un nodo maestro administra y coordina el funcionamiento del clúster, mientras que los nodos trabajadores ejecutan las cargas de trabajo en contenedores.

El nodo maestro lo componen: API Server, Scheduler, Controller Manager, etcd.
- API Server: Es el componente central que gestiona todas las solicitudes dentro del clúster y expone la API de Kubernetes.
- Scheduler: Asigna las cargas de trabajo a los nodos trabajadores en función de la disponibilidad de recursos.
- Controller Manager: Monitoriza el estado del clúster y se encarga de garantizar que las aplicaciones funcionen según lo definido en los manifiestos.
- Etcd: Es la base de datos distribuida que almacena la configuración y el estado global del clúster.

Los nodos trabajadores lo componen el Kubelet, Container Runtime y Kube Proxy.
- Kubelet: Es un proceso que se ejecuta en cada nodo trabajador y se encarga de garantizar que los contenedores estén en funcionamiento.
- Container Runtime: Es el software responsable de ejecutar los contenedores (Docker, containerd, CRI-O, etc).
- Kube Proxy: Mantiene la red y facilita la comunicación entre servicios.

Los pods:
- Un pod es la unidad mínima de despliegue en Kubernetes y puede contener uno o más contenedores.
- Comparte red y almacenamiento entre sus contenedores, lo que facilita la comunicación interna.

Los servicios actúan como un punto de acceso estable a los pods, permitiendo la comunicación entre diferentes partes de la aplicación. Se pueden configurar como ClusterIP (para la comunicación interna), NodePort (exposición en cada nodo) o LoadBalancer (integración con balanceadores de carga en la nube).
Esta arquitectura modular y flexible permite que Kubernetes gestione aplicaciones de manera eficiente, asegurando escalabilidad, alta disponibilidad y resiliencia ante fallos.

## 3. Instalación y configuración de Kubernetes.
### 3.1. Métodos de instalación.
Existen múltiples formas de instalar un clúster Kubernetes, dependiendo del entorno y las necesidades de la empresa:
- Minikube: Ideal para entornos de desarrollo y pruebas locales. Permite desplegar un clúster Kubernetes en un solo nodo, facilitando la experimentación y el aprendizaje.
- Kubeadm: Recomendado para instalaciones en servidores físicos o virtuales. Es una de las opciones más utilizadas para clústeres de producción por su flexibilidad y control sobre la configuración.
- Servicios gestionados: Proveedores como AWS EKS, Google GKE o Azure AKS ofrecen Kubernetes como servicio, eliminando la necesidad de gestionar la infraestructura subyacente y facilitando la administración a gran escala.

### 3.2. Configuración básica del clúster.
Una vez instalado kubernetes, es fundamental realizar una configuración adecuada del clúster para garantizar su rendimiento y seguridad:
1.  Definir el plan de red del clúster: Kubernetes requiere una red interna eficiente para la comunicación entre los nodos y los pods. Opciones como Calico, Flannel o Cilium son ampliamente utilizadas para este propósito.
2.  Configurar los nodos maestros y trabajadores: Se deben unir los nodos trabajadores al nodo maestro, asegurando que cada uno tenga los recursos adecuados.
3.  Instalar complementos esenciales:
     - CoreDNS: Sistema de resolución de nombres dentro del clúster.
     - Kubernetes Dashboard: Interfaz gráfica para gestionar el clúster.
     - Metric Server: Permite recopilar métricas del uso de laCPU y memoria.
     - Herramientas de almacenamiento: Aplicaciones como Persistent Volumens facilitan la gestión de almacenamiento persistente.
  
Con estos pasos, el clúster Kubernetes estará listo para desplegar y gestionar aplicaciones de manera eficiente, asegurando alta disponibilidad y escalabilidad.

## 4. Seguridad en Kubernetes.
La seguridad es algo esencial en cualquier aspecto de Kubernetes para garantizar la integridad, confidencialidad y disponibilidad de las aplicaciones desplegadas en un entorno empresarial. 
A diferencia de los entornos tradicionales, Kubernetes permite el despliegue dinámico de contenedores en múltiples nodos dentro de un clúster, lo que, aunque altamente beneficioso, también abre la puerta a posibles vulnerabilidades si no se implementan los mecanismos de protección adecuados.

A continuación, se presentan algunas soluciones clave organizadas por su propósito:
- Role-Based Access Control (RBAC): Kubernetes implementa RBAC para restringir y administrar los permisos dentro del clúster. Con RBAC, es posible definir roles y asignarlos a usuarios o grupos, limitando su capacidad de interactuar con los recursos. Esto previene accesos no autorizados y minimiza el impacto de posibles compromisos.
- Network Policies: Permiten definir reglas que restringen la comunicación entre Pods dentro del clúster. A través de estas políticas, los administradores pueden controlar qué servicios pueden comunicarse entre sí, reduciendo la superficie de ataque y limitando el movimiento lateral de amenazas.
- Escaneo de Vulnerabilidades: Herramientas como Trivy y Clair analizan las imágenes de contenedores en busca de vulnerabilidades conocidas, garantizando que solo se desplieguen versiones seguras en el clúster.
- Gestión de Secretos con HashiCorp Vault: Kubernetes almacena credenciales y secretos de configuración de forma nativa, pero herramientas como HashiCorp Vault proporcionan una gestión más segura, permitiendo el almacenamiento cifrado y el acceso controlado a información sensible.

Estas herramientas conforman un ecosistema de seguridad robusto para Kubernetes, ayudando a prevenir, detectar y mitigar riesgos en entornos de contenedores. Implementar una estrategia de seguridad integral es esencial para garantizar la estabilidad y protección de las aplicaciones desplegadas en un clúster.

## 5. Despliegue de aplicaciones en Kubernetes.
El despliegue de aplicaciones en Kubernetes representa una de las funcionalidades más potentes y demandadas en entornos empresariales. Este proceso permite administrar el ciclo de vida completo de las aplicaciones, desde su creación hasta su actualización o eliminación, con altos niveles de automatización, control y escalabilidad.

Kubernetes ofrece un modelo declarativo, donde los desarrolladores y administradores describen el estado deseado del sistema (por ejemplo, cuántas réplicas debe tener una aplicación) y el clúster se encarga de alcanzarlo y mantenerlo. Esto permite reducir errores humanos, responder rápidamente ante caídas y gestionar picos de tráfico sin intervención manual.

### 5.1. Comcepto de despliegue en Kubernetes
Desplegar una aplicación en Kubernetes implica ejecutar contenedores dentro de un clúster, organizándolos mediante objetos como Pods, Deployments, y Services.

- La principal ventaja es que Kubernetes permite realizar despliegues:
- Automatizados, gracias al controlador de Deployment.
- Escalables, mediante la adición o eliminación de réplicas de pods.
- Resilientes, reprogramando pods fallidos automáticamente.

### 5.2. Componentes claves del Despliegue.
Los principales elementos involucrados en el despliegue de aplicaciones son:
- Pods: Unidad mínima de ejecución que encapsula uno o más contenedores. Comparten red y almacenamiento, facilitando la comunicación interna entre procesos relacionados.
- Deployments: Administran la creación y actualización de Pods de forma declarativa. Kubernetes garantiza que el estado real del sistema se acerque al estado deseado definido por el usuario.
- Services: Abstracción que expone una aplicación como un punto de red estable. Pueden ser:
  - ClusterIP: Solo accesible desde dentro del clúster.
  - NodePort: Expone el servicio en un puerto específico de cada nodo.
  - LoadBalancer: Utilizado en la nube para balancear la carga automáticamente.
- ConfigMaps y Secrets:
  - ConfigMaps: Almacenan configuraciones no sensibles (por ejemplo, variables de entorno).
  - Secrets: Manejan información sensible (por ejemplo, contraseñas, claves de API) de forma segura.

### 5.3. Estrategias de despliegue
Kubernetes soporta diversas estrategias de despliegue para minimizar interrupciones y facilitar la transición entre versiones:
- Rolling Update (por defecto): Actualiza gradualmente los pods de una aplicación sin tiempo de inactividad. Sustituye cada pod uno por uno hasta que todos estén actualizados.
- Blue-Green Deployment: Mantiene dos entornos (azul y verde), uno activo y otro pasivo. Se despliega la nueva versión en el entorno pasivo y se cambia el tráfico cuando todo esté listo. Permite volver fácilmente a la versión anterior.
- Canary Deployment: Despliega una nueva versión a un pequeño subconjunto de usuarios. Si todo funciona correctamente, se amplía la distribución al resto. Muy útil para detectar errores tempranos.

Estas estrategias son fundamentales para entornos de producción, donde cualquier fallo puede implicar pérdidas económicas o de servicio.

### 5.4. Integración con CI/CD.
La integración de Kubernetes con herramientas CI/CD permite automatizar todo el proceso de compilación, pruebas y despliegue de aplicaciones. Esto mejora la eficiencia del equipo y reduce los errores humanos.
Herramientas destacadas:
- GitHub Actions: Permite definir flujos de trabajo directamente desde el repositorio. Por ejemplo, cada vez que se haga un push a la rama principal, se puede construir la imagen y desplegar en Kubernetes.
- Argo CD: Solución declarativa basada en GitOps. Supervisa un repositorio Git y aplica automáticamente los cambios detectados al clúster. Garantiza que el estado del clúster siempre esté sincronizado con lo que define el repositorio.

Ejemplo de flujo CI/CD:
1. El desarrollador sube cambios a GitHub.
2. GitHub Actions construye una nueva imagen Docker y la publica en un registry (como Docker Hub).
3. Se actualiza un manifiesto YAML con la nueva versión.
4. Argo CD detecta el cambio y aplica el nuevo manifiesto en Kubernetes.

Gracias a este enfoque automatizado, los equipos pueden hacer despliegues rápidos, controlados y seguros en cualquier entorno, desde desarrollo hasta producción.

