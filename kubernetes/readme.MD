## This repo tracks some of the concepts of Docker and Kubernetes

Kubernetes es la plataforma de orquestación con mayor éxito en el mercado.

## Notes

- K8s --> abbreviation with kubernetes

## Summary of containers and introduction to K8s

Los contenedores no son un first class citizen del kernel de Linux, un contenedor no es una entidad previamente definida. Es un concepto abstracto conformado por diferentes tecnologías que se potencian las unas a las otras y trabajando en conjunto componen esta tecnología.

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

### Pod

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

  - `sudo apt-get update && sudo apt-get install -y apt-transport-https
  - curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
  - echo “deb https://apt.kubernetes.io/ kubernetes-xenial main” | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
  - sudo apt-get update
  - sudo apt-get install -y kubectl

- Para ejecutar el minikube seria:

  - minikube start --drive=virtualbox ( no viene por default, para hacer default es, minikube config set driver virtualbox, y podras ejecutar minikube start con virtualbox )
  - kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
  - kubectl expose deployment hello-minikube --port=8080 --type=NodePort
  - minikube service hello-minikube

  - Algunos comandos que te puede ayudar
    - kubectl get service (listar servicios)
    - kubectl get pod (lilstar pod)
    - kubectl get all (listar todo)

- minikube crea una maquina virtual
- internamente usa kubeadm

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

- `kubectl get pods --all-namespaces`
- `kubectl run pingpong --image alpine ping 1.1.1.1` --> deprecated but works, it creates a pod.
- `kubectl logs deploy/pingpong` del deployment pingpong quiero ver los logs.
- `kubectl logs deploy/pingpong --tail 20` to see the last 20 logs
- `kubectl logs deploy/pingpong --tail 20 -f` to follow actual logs generated.
- `sudo kubectl logs -l run=pingpong` -l es un selector
- `kubectl apply -f pingpong.yaml` para llamar el yaml de este proyecto.
- `kubectl describe pods pingpong` describe el pod

### Herarquia creada

`deployment` is what let us update this pod.
`replicaset`, el recurso que controla la cantidad de replicas.
`service`, en caso de que el recurso sea un servicio que se tiene que escuchar en algun puerto.
por ultimo el `pod`.

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

## Accessing containers

El tipo de servicio en Kubernetes pueden ser de cuatro formas diferentes:

- `Cluster IP`: Una IP virtual es asignada para el servicio
- `NodePort`: Un puerto es asignado para el servicio en todos los nodos
- `Load Balancer`: Un balanceador externo es provisionado para el servicio. Solo disponible cuando se usa un servicio con un balanceador
- `ExternalNam`e: Una entrada de DNS manejado por CoreDNS

#Curl al primer POD
curl http://$(kubectl get po -l app=httpenv -o jsonpath="{.items[0].status.podIP}"):8888 | jq ""

#Curl al service
curl http://$(kubectl get service/httpenv -o jsonpath='{.spec.clusterIP}'):8888 | jq ""

#Ciclo para revisar el Curl extrayendo cual sea la IP
for i in $(seq 10); do curl -s http://$(kubectl get service/httpenv -o jsonpath='{.spec.clusterIP}'):8888 | jq ".HOSTNAME"; done

"""
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
curl http://10.44.0.5:8888 |jq "" // formatea resultado hayq ye revisar ela version de linux
kubectl expose deployment httpenv --port=8888
kubectl get svc --> servicio
for i in $(seq 10); do curl -s http://10.110.57.28:8888 |jq .HOSTNAME; done
"""

## How to deploy services

Para los que quieren reproducir todo en local, hice lo siguiente:

Instalar Kind
Kind permite crear clusters en local con multiples nodos. Pueden seguir este video, donde al comienzo explican como instalarlo: https://www.youtube.com/watch?v=4p4DqdTDqkk

Crear el cluster
Guarden esta configuracion en un .yaml

## a cluster with 1 control-plane nodes and 5 workers

https://www.youtube.com/watch?v=4p4DqdTDqkk
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:

- role: control-plane
- role: worker
- role: worker
- role: worker
- role: worker
- role: worker

Creamos el cluster con kind

`kind create cluster --config kind-config.yaml`

Y seguimos los pasos de la clase de platzi
Exponer servicio
Posiblemente, al seguir la clase y querer acceder al WebUI colocando en el browser localhost:<<NodePort>>, se encuentren con un connnection refused. Esto, creo, sucede por que el puerto no mapea desde el localhost o 127.0.0.1, sino que Kind hace un bind con una direccion local de la maquina. Para saber cual es la direccion ip, arme este comando

`docker inspect -f "{{ .NetworkSettings.IPAddress }}" $(docker ps --filter "name=kind-control-plane" -q)`

Con el output, ya pueden ir al browser y acceder. EJ: http://172.17.0.7:31104

En mi caso, estoy usando Linux. Por lo que he leido, puede que en Mac o Windows no se exponga el puerto, por lo que pueden explorar crear los clusters usando extraPortMapping

"""
kubectl create deployment redis --image=redis --> crear deployment, service y demas en k8 usando redis image
kubectl get all
kubectl delete service httpenv
kubectl get all
kubectl get pods
kubectl delete service httpenv
kubectl get pods
kubectl create deployment hasher --image=dockercoins/hasher:v0.1
kubeclt get pods
kubectl get pods
kubectl create deployment rng --image=dockercoins/rng:v0.1
kubectl get pods
kubectl create deployment webui --image=dockercoins/webui:v0.1
kubectl get pods
kubectl create deployment worker --image=dockercoins/worker:v0.1
kubectl logs deploy/rng
kubectl logs deploy/worker
kubectl expose deployment redis --port 6379 --> para exponer deployments para que se puedan hablar entre si.
kubectl expose deployment rng --port 80
kubectl expose deployment hasher --port 80
kubectl expose deploy/webui --type=NodePort --port=80
"""

imagenes de docker-coins --> https://hub.docker.com/u/dockercoins

port externo 8080:80 interno

## Exponiendo servicios interna y externamente (kubectl-proxy)

- `kubectl-proxy` es un proxy que corre foreground y nos permite acceder a la API de kubernetes de manera autenticada.

- `kubectl port-forward` nos permite realizar lo mismo que kubectl-proxy, pero accediendo a cualquier puerto del servicio expuesto en nuestro cluster

"""
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
"""
