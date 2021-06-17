## This repo tracks some of the concepts of Docker and Kubernetes

Kubernetes es la plataforma de orquestación con mayor éxito en el mercado.

- https://github.com/platzi/curso-kubernetes

## Notes

- K8s --> abbreviation with kubernetes

## Summary of containers and introduction to K8s

Los contenedores no son un first class citizen del kernel de Linux, un contenedor no es una entidad previamente definida.
Es un concepto abstracto conformado por diferentes tecnologías que se potencian las unas a las otras y trabajando en conjunto componen esta tecnología.

1. `Cgroups o Control Groups`: Son los que permiten que un contenedor o proceso tenga aislado los recursos como memoria, I/O, Disco y más. Limitan los recursos del SO
2. `Namespaces`: Permiten aislar el proceso para que viva en un sandbox y no pueda haber otros recursos de SO o contenedores.
   – `Mount Namespaces`: Permite que nuestro proceso tenga una visibilidad reducida de los directorios donde trabaja.
   – `Networking Namespaces`: Permite que cada contenedor tenga su stack de red, (direccion de IP, routers)
   – `PID ode proceso`. --> de procesos

3. `Chroot`: Cambia el root directory de un proceso.

<img src="./container-vrs-vm.png" alt="container-vrs-vm"/>

- Los contenedores comparten un mismo OS.
- `Hypervisor` nos permite tener varios OS.

## Pods a containers

Pods --> entidad atomica scheduling, sobre la cual kubernetes ejecuta sus contenedores

- Todos los contenedores del mismo pod comparten el mismo namespaces de red.
- Por tanto tienen la misma dirección IP y se ven unos a otros como procesos corriendo dentro del mismo sistema.

1. Docker & Kubernetes

- Docker se encarga principalmente de gestionar los contenedores. (ciclo de vida de los contenedores) --> docker correr contenedor, con tanta memoria y tanta cpu.
- Kubernetes es una evolución de proyectos de Google Borg & Omega. --> yo tengo esta cantidad de contenedores que necesito que se relacionen y ahi es donde entra Kubernetes, uno instala kubernetes en estos nodos. Espcifica donde y como necesita que corran esos pods.
- Kubernetes pertenece a la CNCF (Cloud Native Computing Foundation).
- Todos los cloud providers (GCP/AWS/Azure/DO) ofrecen servicios de managed k8s utilizando Docker como su container runtime
- Es la plataforma más extensiva para orquestación de servicios e infraestructura

2. Kubernetes en la práctica

- K8s permite correr varias réplicas y asegurarse que todas se encuentren funcionando.
- Provee un balanceador de carga interno o externo automáticamente para nuestros servicios (automatico).
- Definir diferentes mecanismos para hacer roll-outs de código. (para definir estrategias para deployar)
- Políticas de scaling automáticas.
- Jobs batch. (un proceso que tiene un tiempo de vida especifico)
- Correr servicios con datos stateful.
- y Muchas otras cosas (CRDs, service catalog, RBAC (roll back access control))

## Pod

<img src="./recursos-kubernetes.png" alt="recursos-kubernetes"/>

- vive en un nodo
- Un pod son uno o mas contenedores que viven juntos y `comparten un namespaces de red`.
- Pod, unidad de orchestracion.
- Todos los contenedores que viven dentro de un mismo Pod comparten el `mismo segmento de red`. osea todos tiene la misma direccion IP, y se ven unos a otros.

### Kubernetes architecture

<img src="./architecture.png" alt="architecture"/>

Arquitectura de K8s tiene 2 partes:

- Nodo master
- Nodos o Minions

<img src="./architecture2.png" alt="architecture2"/>

##### 1. Nodo Master

Nodos que controlan el estado, el cerebro de Kubernetes

1. `API Server`: A lo que todo se conecta, los agentes, el CLI, el dashboard etc. Cuando se cae un nodo master es lo que se pierde. Se usa el algoritmo de ruft para algoritmo de elección.
2. `Scheduler`: Cuando se deben crear un job, un pod en máquinas específicas, el scheduler se encarga de asignar las tareas y administrar los flujos de trabajos, revisando siempre las restricciones y los recursos disponibles.
3. `Controller Manager`: Es un proceso que está en un ciclo de reconciliación constante buscando llegar al estado deseado con base al modelo declarativo con el que se le dan instrucciones a K8s.
   ++Tipos de controller manager: ++

   - Replica manager
   - Deployment manager
   - Service manager
     …

4. Etcd: (BD altamente disponible) Key value store que permite que el cluster este altamente disponible.
   Componentes muy importantes que viven en los nodos:

##### 2. Nodos

1. `Kubelet`: Agente de kubernetes, se conecta con el control play y le pregunta que recursos (pods, contenedores) debo correr al scheduler via API Server. Monitorea los pods constantemente para saber si están vivos, los recursos disponibles etc y le comunica constantemente al scheduler via API Server.
2. `Kube-proxy`: Se encarga de balancear el tráfico que corre en nuestros contenedores/servicios. Una vez llega una request se encarga de decidir a que pod y contenedor debe de ir.

Todos los nodos y masters están conectados a una red física para poder hablarsen entre sí.

## Sistemas imperativos y declarativos

- Un sistema es imperativo cuando ejecuta una seria de pasos, que deben seguir un orden especifico. Si algun paso se interrumpe, la secuencia inicia desde el paso 1.
  - Poco escalable,
  - alto riesgo de ineficiencias.
- Un sistema es declarativo cuando trata de converger a un estado deseado, a partir de un estado actual.

  - Altamente escalable,

  - apunta a generar eficiencias evolutivas

Declarativo parece sencillo (siempre y cuando uno sepa cómo hacerlo)
Todo en Kubernetes se crea desde un spec que describe cuál es el estado deseado del sistema
Kubernetes constantemente converge con esa especificación

## Modelo de red de Kubernetes

- Todo el cluster es una gran red del mismo segmento
- Todos los nodos deben conectarse entre si, sin NAT (Network Adress Translation)
- Todos los pods deben conectarse entre si, sin NAT
- kube-proxy es el componente para conectarnos a pods y contenedores (userland proxy/iptables)
- Los pods trabajan a capa 3 (transporte) y los servicios a capa 4 (protocolos)

<img src="./OSI.png" alt="osi"/>

## Useful links

- https://labs.play-with-docker.com/
- https://labs.play-with-k8s.com/
- https://www.cncf.io/the-childrens-illustrated-guide-to-kubernetes/
- https://docker-saigon.github.io/post/Docker-Internals/
- https://www.youtube.com/watch?v=EDXSvhbaTvM
- https://github.com/platzi/curso-kubernetes

rng -> generates random bytes
hasher --> generates a hash from the data that is sent
redis --> it is a cache service to store data
worker --> get random data, generates the hash and stores the data using redis.

## Minukube, Kubeadm

- `Minikube` es una herramienta para desplegar un cluster en tu máquina local.
- `kubeadm` es un boostrap, un utilitario que permite realizar todo lo mostrado en el repositorio de Kelsey.

- https://github.com/kelseyhightower/kubernetes-the-hard-way
- https://github.com/kubernetes/minikube

Para los que no tienen kubectl instalado, aqui les dejo como hacerlo

- Ubuntu/Debian o Hypriot OS

  - `sudo apt-get update && sudo apt-get install -y apt-transport-https`
  - `curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`
  - `echo “deb https://apt.kubernetes.io/ kubernetes-xenial main” | sudo tee -a /etc/apt/sources.list.d/kubernetes.list`
  - `sudo apt-get update`
  - `sudo apt-get install -y kubectl`

- Para ejecutar el minikube seria:

  - `minikube start --drive=virtualbox` ( no viene por default, para hacer default es, minikube config set driver virtualbox, y podras ejecutar minikube start con virtualbox )
  - `kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4`
  - `kubectl expose deployment hello-minikube --port=8080 --type=NodePort`
  - `minikube service hello-minikube`

  - Algunos comandos que te puede ayudar
    - `kubectl get service` (listar servicios)
    - `kubectl get pod` (lilstar pod)
    - `kubectl get all` (listar todo)

- minikube crea una maquina virtual
- internamente usa `kubeadm`

## EKS is Elastic Kubernetes Service

- Es un servicio que ofrece AWS.
- Check the following link --> https://www.youtube.com/watch?v=VeC85k6PAY4
- https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html
- https://logz.io/blog/amazon-eks-cluster/

## Concepts

- `Pod`: Contiene contenedores y son creados mediante los Replication Controllers
- `Replication Controller`: Mantiene la contidad de réplicas (pods) que se le indicaque (Y se asegura que así sea en el tiempo). Existe uno por cada elemento de la aplicación: redis-master (1 réplicas), redis-slave (2 réplicas) y guessbook (3 réplicas)
- `Service`: Es la puerta de entradas a los Pods, sirven como balanceador de carga interno al cluster. También representan una IP más estable que las de los Pods ya que éstos últimos podrían fallar, detenerse, eliminarse, etc. Existe uno por cada elemento de la aplicación pero para el guessbook es del tipo LoadBalancer ya que se expone su servicio fuera del cluster y los otros son del tipo ClusterIP

## Kubectl or kube control

- command line interface tool.
- tool to talk with kubernetes
- kubectl let us see all the resources of our cluster.

- `kubectl get nodes` to get all nodes
- `kubectl get nodes -o wide` output wide (more explicit)
- `kubectl get no -o yaml` to see output in a yaml
- `kubectl --config` to use specific user
- `kubectl --server --user` to be more specific and not use a config file.
- `kubectl describe nodes node1` describes the node1
- `kubectl explain node.spec` explains what is that object about
- `kubectl explain node --recursive` toda la lista de objectos que podemos tener
- https://kubernetes.io/docs/reference/kubectl/overview/

The kubectl command line tool lets you control Kubernetes clusters. For configuration, kubectl looks for a file named config in the $HOME/.kube directory. You can specify other kubeconfig files by setting the KUBECONFIG environment variable or by setting the –kubeconfig flag.

## Creation and managment of pods

- https://callistaenterprise.se/blogg/teknik/2017/12/20/kubernetes-on-docker-in-docker/

- `kubectl get pods --all-namespaces` todos los pods de todos los containers
- `kubectl run pingpong --image alpine ping 1.1.1.1` --> deprecated but works, it creates a pod.
- `kubectl logs deploy/pingpong` del deployment pingpong quiero ver los logs.
- `kubectl logs deploy/pingpong --tail 20` to see the last 20 logs
- `kubectl logs deploy/pingpong --tail 20 -f` to follow actual logs generated.
- `sudo kubectl logs -l run=pingpong` -l es un selector
- `kubectl apply -f pingpong.yaml` para llamar el yaml de este proyecto.
- `kubectl describe pods pingpong` describe el pod

### Herarquia creada

- `kubectl create deployment pingdnsgoogle --image=alpine -- ping 8.8.8.8`
- `kubectl scale deployments/pingdnsgoogle --replicas 8`
- `kubectl get pods -w`
- `kubectl delete pods pingdnsgoogle-55f9457fb9-t5t5`
- `kubectl describe pod pingdnsgoogle-55f9457fb9-6wjfg`
- `kubectl get pod pingdnsgoogle-55f9457fb9-6wjfg -o yaml`
- `kubectl run --dry-run -o yaml ping --image alpine ping 8.8.4.4`

`deployment` is what let us update this pod.
`replicaset`, el recurso que controla la cantidad de replicas.
`service`, en caso de que el recurso sea un servicio que se tiene que escuchar en algun puerto.
por ultimo el
`pod` lo que corre el container

## Deployments y replica sets

Para los que llegan hasta aquí usen play with K8s las instrucciones para hacer las replicas es crear los deployments de la siguiente manera:

- `kubectl create deployment <nombre> --image=<imagen> -- <comando>`
  Para este caso:

- `kubectl create deployment pingpong --image=alpine -- ping 1.1.1.1`
  Luego revisan que este haya sido creado de la manera correcta:

- `kubectl get pods`
  $ NAME READY STATUS RESTARTS AGE
  pingpong-<XXXXXXXXXXX> 1/1 Running 0 82s

una vez creado el deployment pueden realizar las replicas OJO: utilizar el nombre del servicio sin el código que le subsigue:

- ` kubectl scale deployments/pingpong --replicas <numero>`

Existe una relacion entre los deployments y los replicasets.

El `DEPLOYMENT` es un construct, una estructura, del mas alto nivel que va a permitir escalar nuestros pods, hacer rolling upgrades y hacer rollbacks.

Multiples deployments pueden entrar en juego para generar un canary deployment (ej: dos versiones de una aplicacion con la 1 corriendo con 5 pods, promovemos un pod a la version 2, enviamos un poco de trafico revisando las metricas para ver que este todo bien, y mandamos otro pod para ver que todo funcione bien y asi hasta que esten todos. Si algo falla, se hace rollback)

El deployment permite hacer este workflow pero delega al replicaset la creacion y el scaling de los pods.

El `REPLICASET` es un construct del mas low level que se asegura de que haya una cantidad de pods definida corriendo en un determinado momento. Es raro que lo modifiquemos directamente, sino a traves del deployment.

Para borrar un pod uno puede hacer

- `kubectl delete pod pingpong....`

- `kubectl run --dry-run -o yaml pingpong --image alpine ping 1.1.1.1`

## Accessing containers

- `minikube ssh -p cluster_name` para obtener el puerto en minikube.
- `curl http://pod_id:pod_port`

El tipo de servicio en Kubernetes pueden ser de cuatro formas diferentes:

- `Cluster IP`: Una IP virtual es asignada para el servicio
- `NodePort`: Un puerto es asignado para el servicio en todos los nodos
- `Load Balancer`: Un balanceador externo es provisionado para el servicio. Solo disponible cuando se usa un servicio con un balanceador
- `ExternalName`: Una entrada de DNS manejado por CoreDNS

- `curl http://$(kubectl get po -l app=httpenv -o jsonpath="{.items[0].status.podIP}"):8888 | jq ""` Curl al primer POD

`curl http://$(kubectl get service/httpenv -o jsonpath='{.spec.clusterIP}'):8888 | jq ""` Curl al service

```shell
# Ciclo para revisar el Curl extrayendo cual sea la IP
for i in $(seq 10); do curl -s http://$(kubectl get service/httpenv -o jsonpath='{.spec.clusterIP}'):8888 | jq ".HOSTNAME"; done
```

```shell
kubectl get pods
kubectl describe pod idpod
kubectl get all
kubectl delete deploy/httpenv
kubectl create deployment httpenv --image jpetazzo/httpenv
kubectl get pods
kubectl get all
kubectl scale deployment httpenv --replicas=5
kubectl get pods
kubectl get pods -o wide
curl http://10.44.0.5:8888
curl http://10.44.0.5:8888 |jq "" # formatea resultado hayq ye revisar ela version de linux
kubectl expose deployment httpenv --port=8888  # crea servicio
kubectl get svc --> servicio
for i in $(seq 10); do curl -s http://10.110.57.28:8888 |jq .HOSTNAME; done
```

## How to deploy services

Para los que quieren reproducir todo en local, hice lo siguiente:

- Instalar Kind
- Kind permite crear clusters en local con multiples nodos. Pueden seguir este video, donde al comienzo explican como instalarlo: https://www.youtube.com/watch?v=4p4DqdTDqkk

- Crear el cluster
- Guarden esta configuracion en un .yaml

```yaml
## a cluster with 1 control-plane nodes and 5 workers
apiVersion: kind.x-k8s.io/v1alpha4
kind: Cluster
nodes:
  - role: control-plane
  - role: worker
  - role: worker
  - role: worker
  - role: worker
  - role: worker
```

- `kind create cluster --config kind-config.yaml` (Creamos el cluster con kind)

Y seguimos los pasos de la clase de platzi
Exponer servicio
Posiblemente, al seguir la clase y querer acceder al WebUI colocando en el browser localhost:<<NodePort>>, se encuentren con un connnection refused. Esto, creo, sucede por que el puerto no mapea desde el localhost o 127.0.0.1, sino que Kind hace un bind con una direccion local de la maquina. Para saber cual es la direccion ip, arme este comando

`docker inspect -f "{{ .NetworkSettings.IPAddress }}" $(docker ps --filter "name=kind-control-plane" -q)`

Con el output, ya pueden ir al browser y acceder. EJ: http://172.17.0.7:31104

En mi caso, estoy usando Linux. Por lo que he leido, puede que en Mac o Windows no se exponga el puerto, por lo que pueden explorar crear los clusters usando extraPortMapping

```shell
kubectl create deployment redis --image=redis --> crear deployment, service y demas en k8 usando redis image
kubectl get all
kubectl delete service httpenv
kubectl get all
kubectl get pods
kubectl delete service httpenv
kubectl get pods
kubectl create deployment hasher --image=dockercoins/hasher:v0.1
kubectl get pods
kubectl create deployment rng --image=dockercoins/rng:v0.1
kubectl get pods
kubectl create deployment webui --image=dockercoins/webui:v0.1
kubectl get pods
kubectl create deployment worker --image=dockercoins/worker:v0.1
kubectl logs deploy/rng
kubectl logs deploy/worker
kubectl expose deployment redis --port 6379 # para exponer deployments para que se puedan hablar entre si.
kubectl expose deployment rng --port 80
kubectl expose deployment hasher --port 80
kubectl expose deploy/webui --type=NodePort --port=80
```

- imagenes de docker-coins --> https://hub.docker.com/u/dockercoins

port externo 8080:80 interno

## Exponiendo servicios interna y externamente (kubectl-proxy)

- `kubectl-proxy` es un proxy que corre foreground y nos permite acceder a la API de kubernetes de manera autenticada.

- `kubectl port-forward` nos permite realizar lo mismo que kubectl-proxy, pero accediendo a cualquier puerto del servicio expuesto en nuestro cluster

```shell
  kubectl get services kubernetes
  curl https://10.96.0.1
  curl -k https://10.96.0.1 //no revisar certificados
  kubectl proxy &
  curl http://localhost:8001
  curl http://localhost:8001/version
  fg
  kubectl proxy --help
  kubectl get service
  kubectl port-forward svc/redis 10000:6379 &
  telnet localhost 10000
  info
  ^]
```

## K8s dashboard

- Important links https://blog.heptio.com/on-securing-the-kubernetes-dashboard-16b09b1b7aca

Con minikube
`minikube addons enable dashboard`
`minikube addons list`
`minikube dashboard` y listo!

## Balanceo de carga y Daemon sets

Un DaemonSet se asegura que todos, o algunos nodos corra una copia de un Pod. No se crean en el CLI (kubectl), sino a través de los manifest files.

¿Cómo sabe k8s como enviar trafico a que pods?
Lo hace a tráves de los selectores, este permite el balanceo de carga. Cada service tiene los endpoints de los pods que estan corriendo para que otros
servicios puedan acceder. Al eliminar uno, k8s crea otro para cumplir con el estado deseado y lo integra automaticamente para ser destinatario de trafico.

```shell
kubectl get deploy/rng -o yaml --export > rng.yml
kind DaemonSet cambiar el tipo a kind: DaemonSet
kubectl apply -f rng.yml
kubectl apply -f rng.yml --validate=false #no validar el yml
kubectl describe service rng
kubectl get pods --selector=app=rng
kubectl label pod podname app- #borrar label
```

## Despliegues controlados (rolling upgrades)

Primero instala jq, es un procesador JSON de línea de comandos ligero y flexible.

- `sudo apt-get install jq -y`

Como usar la estrategia de rolling upgrades para desplegar nuevas versiones, minimizando el impacto a nuestros usuarios:
`kubectl get deploy –o json`: el output de todos los deployments que tenemos en nuestro sitio
`kubectl get deploy -o json | jq ".items[] | {name:.metadata.name} + .spec.strategy.rollingUpdate"`
`Kubectl get deploy –o json | jq “{name:.metadata.name} + .spec.strategy.rollingUpdate"`: itera sobre los ítems y trae de la metadata la estrategia del rollingupdate default.
Esto va a mostrar los diferentes servicios.

- `MAX SURGE`: cuantos pods se crean a partir de los que tengo, o sea que si hay 100 hasta 25 pods pueden estar creándose al mismo momento por default
- `MAX UNAVAILABLE`: a lo sumo puede haber, en un determinado momento del deploy, un 25% de mis pods no disponibles por default.

Hacemos el rolling upgrade de la aplicación:
`kubectl set image deploy worker worker=dockercoins/worker:v0.2`
Cambiamos la imagen del deploy y le decimos que worker tenga la imagen dockercoins/worker:0.2

Si le mandamos una imagen que no existe o está rota, va a mantener un minimo de 25% de los nodos trabajando como antes y asi no interrumpir el funcionamiento del servicio.
Con `kubectl rollout undo deploy worker` hacemos un rollback a la versión anterior.

Para cambiar la estrategia de deployment: `Kubectl edit deploy worker`
Si pongo maxsurge en 1, solo se va a crear como maximo un pod nuevo por cada nuevo deployment. Si pongo maxunavailable en 0, no van a haber pods unavailables a la hora de hacer deployments. Esta estrategia es bastante conservadora: quiero crear de a uno y hasta que ese no este disponible, no cambiar nada.

## Healthchecks

Healthchecks es un organismo que tiene kubernetes para saber si nuestra aplicación está funcionando de una manera correcta para saber si debe removerla o reiniciarla al no estar en un estado deseable.

The kubelet uses `liveness` probes to know when to restart a container.
The kubelet uses `readiness` probes to know when a container is ready to start accepting traffic. A Pod is considered ready when all of its containers are ready. When a Pod is not ready, it is removed from Service load balancers.

The kubelet uses startup probes to know when a container application has started. This can be used to adopt liveness checks on slow starting containers, avoiding them getting killed by the kubelet before they are up and running.

**Liveness **- the kubelet uses these probes as an indicator to restart a container. A liveness probe is used to detect when an application is running and is unable to make progress. When a container gets in this state, the pod’s kubelet can restart the container via its restart policy.

**Readiness **- This type of probe is used to detect if a container is ready to accept traffic. You can use this probe to manage which pods are used as backends for load balancing services. If a pod is not ready, it can then be removed from the list of load balancers.

Types Health Checks:

- `ExecAction` - executes a command inside the container.
- `++TCPSocketAction` +± performs a TCP check against the container’s IP address on a specified port.
- `HTTPGetAction` - performs an HTTP GET request on the container’s IP.

## Gestionar stacks con Helm

<h1>¿Qué es Helm?</h1>
Es una herramienta que nos sirve como gestor de paquetes de Kubernetes.

Conceptos:

- `helm`: es el cliente de Helm.
- `tiller`: es el componente del servidor. Nos sirve para recibir los comandos que le enviamos desde helm. Ya que tiller se comunica directamente con el API de Kubernetes.
- `chart`: son los paquetes manejados por helm.

https://helm.sh/docs/intro/quickstart/

```shell
// Instalar helm
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash

// Verificar si tenemos helm instalado
helm

// Configurar helm
helm init

// Verificar si Tiller está instalado
kubectl get pods -n kube-system

// Dar permisos a helm
kubectl create clusterrolebinding add-on-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:default

// Buscar paquetes
helm search

// Ejemplo de cómo instalar un paquete
helm search prometheus
helm inspect stable/prometheus | less
helm install stable/prometheus --set server.service.type=NodePort --set server.persistentVolume.enabled=false

// Obtener servicios
kubectl get svc

// Crear helm chart
helm create dockercoins
cd dockercoins
mv templates/ templates-old
mkdir templates
cd ..
kubectl get -o yaml --export deployment worker
```

```shell
# export script
while read kind name; do
kubectl get -o yaml --export $kind $name > dockercoins/templates/$name-$kind.yaml
done <<EOF
deployment worker
deployment hasher
daemonset rng
deployment webui
deployment redis
service hasher
service rng
service webui
service redis
EOF
```

# Gestionando la configuración aplicativas utilizando Config Maps

Un configmap es un objeto de la API utilizado para almacenar datos no confidenciales en el formato clave-valor.
Un ConfigMap te permite desacoplar la configuración de un entorno específico de una imagen de contenedor, así las aplicaciones son fácilmente portables.

Tenemos diferentes formas de configurar nuestras aplicaciones:

- Argumentos por línea de comandos
- Variables de entorno (env map en el spec)
- Archivos de configuración (config maps)
  - Guardan tanto archivo como valores clave/valor

```shell
#create configmaps
kubectl create configmap haproxy --from-file=haproxy.cfg
#see configmaps
kubectl get configmap haproxy -o yaml | less
kubectl apply -f haproxy.yaml
kubectl create configmap registry --from-literal=http.addr=0.0.0.0:80
vi registry.yaml
kubectl apply -f  registry.yaml

```

## Volumenes

Un volumen nos va a permitir compartir archivos entre diferentes pods o en nuestro host. Estos se usan para que los archivos vivan a lo largo del tiempo y el pod pueda seguir haciendo uso de estos archivos de logs, archivos de configuración o cualquier otro.

Docker:

- Permiten compartir información entre contenedores del mismo host
- Permiten acceder a mecanismo de storage
- Docker config y docker secrets

Kubernetes:

- Permiten compartir información entre contenedores del mismo pod
- Permite acceder también a mecanismo de storage
- Se utilizan para el manejo de secrets y configuraciones

Ciclo de Vida

- El volumen se crea cuando el pod se crea.
  - Esto aplica principalmente para los volúmenes emptyDir.
  - Para otro tipo se conectan en vez de crearse.
- Un volumen se mantiene aún cuando se reinicie el contenedor.
- Un volumen se destruye cuando el pod se elimina.

## Introduction to Namespaces

Namespace provides an additional qualification to a resource name. This is helpful when multiple teams are using the same cluster and there is a potential of name collision. It can be as a virtual wall between multiple clusters.

Following are some of the important functionalities of a Namespace in Kubernetes:

- Namespaces help pod-to-pod communication using the same namespace.

- Namespaces are virtual clusters that can sit on top of the same physical cluster.

- They provide logical separation between the teams and their environments.

- `kubectl create namespace blue` Crear un namespace llamado blue

- `kubectl config set-context --current --namespace=blue` Cambiar al nuevo namespace

- `kubectl config get-contexts` Validar que se cambio de namespace

Kubernetes starts with four initial namespaces:

- `default` The default namespace for objects with no other namespace
- `kube-system` The namespace for objects created by the Kubernetes system
- `kube-public` This namespace is created automatically and is readable by all users (including those not authenticated). This namespace is mostly reserved for cluster usage, in case that some resources should be visible and readable publicly throughout the whole cluster. The public aspect of this namespace is only a convention, not a requirement.
- `kube-node-lease` This namespace for the lease objects associated with each node which improves the performance of the node heartbeats as the cluster scales.

check this --> https://github.com/jonmosco/kube-ps1

Que define uneinocamente uno recurso

- tipo de recurso
- nombre del recurso
- namespace al que pertenece

## Despliegue múltiples instancias de la misma aplicación en un solo cluster.

- Los namespaces no proveen aislación de recursos
- Un pod en un namespace A puede comunicarse con otro namespace B
- Un pod en el namespace default puede comunicarse con otro en el kube-system

## Authentication

- Autenticación es el método por el cual Kubernetes deja ingresar a un usuario.
- Autorización es el mecanismo para que un usuario tenga una serie determinada de permisos para realizar ciertas acciones sobre el cluster.

- Cuando el API server recibe un request intenta autorizarlo con uno o más de uno de los siguientes métodos:
  - Certificados TLS,
  - Bearer Tokens,
  - Basic Auth
  - o Proxy de autenticación.
- Si cualquier método rechaza la solicitud, se devuelve un 401.
- Si el request no es aceptado o rechazado, el usuario es anónimo.
- Por defecto el usuario anónimo no puede hacer ninguna operación en el cluster.

`kubectl config view --raw -o json | jq -r .users[0].user[“client-certificate-data”] | base64 -d | openssl x509 -text | grep Subject`

## Service account tokens

Service Account Tokens es un mecanismo de autenticación de kubernetes y vive dentro de la plataforma, nos permite dar permisos a diferentes tipos de usuarios.

- Existen en la API de kubernetes. kubectl get serviceaccount
- Pueden crearse, eliminarse y actualizarse
- Un service account está asociado a secretos kubectl get secrets
- Son utilizados para otorgar permisos a aplicaciones y servicios

## RBAC

Role based access control(RBAC) es un mecanismo de kubernetes para gestionar roles y la asociación de estos a los usuarios para delimitar las acciones que pueden realizar dentro de la plataforma.

- Un rol es un objeto que contiene una lista de rules
- Un rolebiding asocia un rol a un usuario
- Pueden existir usuarios, roles y rolebidings con el mismo nombre
- Una buena práctica es tener un 1-1-1 bidings
- Los Cluster-scope permissions permiten definir permisos a nivel de cluster y no solo namespace
- Un pod puede estar asociado a un service-account

```shell
--Crear el service account
kubectl create sa viewer

--Crear el RoleBinding
kubectl create rolebinding viewercanview --clusterrole=view --serviceaccount=default:viewer

--Correr el pod
kubectl run eyepod --rm -ti --restart=Never --serviceaccount=viewer --image alpine

--Dentro del pod, obtener la última versión estable de kubernetes
curl https://storage.googleapis.com/kubernetes-release/release/stable.txt

--Descargar la versión de kubectl
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.20.1/bin/linux/amd64/kubectl

--Hacer a kubectl ejecutable

chmod +x ./kubectl

--Obtener todo

./kubectl get all

--Preguntas de authorization

./kubectl auth can-i list nodes

./kubectl auth can-i create pods

./kubectl auth can-i get pods

--Obtener RoleBindings

kubectl get clusterrolebindings -o yaml | grep -e kubernetes-admin -e system:masters

kubectl describe clusterrolebinding cluster-admin
```

Role and ClusterRole
An RBAC Role or ClusterRole contains rules that represent a set of permissions. Permissions are purely additive (there are no “deny” rules). A Role always sets permissions within a particular namespace; when you create a Role, you have to specify the namespace it belongs in. ClusterRole, by contrast, is a non-namespaced resource.

`

RoleBinding and ClusterRoleBinding
A role binding grants the permissions defined in a role to a user or set of users. It holds a list of subjects (users, groups, or service accounts), and a reference to the role being granted. A RoleBinding grants permissions within a specific namespace whereas a ClusterRoleBinding grants that access cluster-wide.

## Recomendaciones

Próximos pasos

- Establece una cultura de containers en la organización

  - Escribir Dockerfiles para una aplicación
  - Escribir compose files para describir servicios
  - Mostrar las ventajas de correr aplicaciones en contenedores
  - Configurar builds automáticos de imágenes
  - Automatizar el CI/CD (staging) pipeline

- Developer Experience: Si tienes una persona nueva, debe sentirse acompañada en este proceso de por qué usamos kubernetes y quieres mantener la armonía de ese proceso.

- Elección de un cluster de producción: Hay alternativas como Cloud, Managed o Self-managed, también puedes usar un cluster grande o múltiples pequeños.

- Recordar el uso de namespaces: Puedes desplegar varias versiones de tu aplicación en diferentes namespaces.

- Servicios con estados (stateful)

  - Intenta evitarlos al principio
  - Técnicas para exponerlos a los pods (ExternalName, ClusterIP, Ambassador)
  - Storage provider, Persistent volumens, Stateful set

- Gestión del tráfico Http

  - Ingress controllers (virtual host routing)

- Configuración de la aplicación

  - Secretos y config maps

- Stacks deployments
  - GitOps (infraestructure as code)
  - Heml, Spinnaker o Brigade

## GitOps

GitOps es una práctica que gestiona toda la configuración de nuestra infraestructura y nuestras aplicaciones en producción a través de Git. Git es la fuente de verdad. Esto implica que todo proceso de infraestructura conlleva code reviews, comentarios en los archivos de configuración y enlaces a issues y PR.

- Infraestructura como código
- Mecanismo de convergencia
- Uso de CI como fuente de verdad
- Pull vs Push
- Developers y operaciones(git)
- Actualizaciones atómicas

GitOps tomo tanta popularidad en la comunidad de DevOps por el impacto que genera.

- Poder desplegar features nuevos rápidos
- Reducir el tiempo para arreglar bugs
- Generar el sentimiento de control y empoderamiento. Confidencia y control
- 20 deploys por día
- 80% en ahorro del tiempo para arreglar errores en producción
