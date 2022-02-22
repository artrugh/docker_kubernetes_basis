## Kubernetes

The trend from Monolith to MicroService architecture, has increased the usage of containers.

Kubernetes is a proper way of managing those hundreds of containers.

Kubernetes is:

- an open source container orchestration tool.
- developed by Google.
- a tech-tool that helps to manage containerized applications in different deployment environments (physical machines, virtual machines, cloud environment).

Benefits of using a container orchestration technology:

- high availability or no downtime (always accesable by the users).
- scalability or high performance (it loads fast, high response from the application).
- disaster recovery - backup and restore.

### Kubernetes basic architecture

- Master node
    The master has 4 processes running on it, which are absoluty neccesary to run and manage properly the  cluster.:
   1. **API Server:** Entry point to the Kubernetes cluster. All the processes connect with this API (Processes: UI, API, CLI). It acts as a gatekeeper for authentication, it basically validates requests.
    2. **Controller manager:** It keeps track of what is happening in the cluster, detecting crashings and trying to recover the state as soon as possible.
    3. **Scheduler:** It decides on which worker node the next container should be schedule on based on the available resources.
    4. **Etcs:** It is a key values storage of the cluster state. It holds Kubernetes backing store, configuration and kluster state.

- Virtual networks
    turn all the nodes into one powerful unifed machine.

- Worker nodes
    - each worker has multiple Pods with docker containers deployed and running on it. 
    - each work has 3 processes running on it:
        1. **Kubelet process**: It interacts with the container and the node. It takes care of start the Pod with the container inside.
        2. **Container runtime**
        3. **Kube proxy**. It is responsable for serving request from services to Pods. It takes care of the performance of the cluster.

### Kubernetes Components

#### Pod

- A Pod is the smallest unit.
- A Pod is an abstraction layer over a container.
- A Pod creates a running environment layer on top of the container.

On other words, it is a wrapper over a container or more containers.
Usually there is one Pod per application container.
Kubernetes offers virtual network.
Each Pod has an **internal IP address**.

Pods are **efimiral**, they die when a container crash. When they die, a new Pod is created in its place.

Each Pod can have a replicated Pod. This replicas are defined in a **Blueprint**, which is called **deployment**.

#### Services

Services are attached to Pods.
Services substitute the **Pod IP address** with **permanent IP address**.
If a Pod dies, the Service stays in place.
Services are **load balancers**, they catch a request and send it to the Pod which is less busy.

#### Ingress

When the app wants to communicate with an external source through an API, the Ingress manages the request, before it reaches the service. It basically routes traffic into the cluster.

Ingress Controller is the entrypoint to the cluster and evaluates processes **Ingress Rules** and manages **redirections**.

There are third-party implementations.

It can be a proxy server (software or hardware solution). It is a separate server, with a public IP address and open ports. It is the only entrypoint to the cluster.

Configuration:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dashboard-ingress
  namespace: kubernetes-dashboard
spec:
  rules:
  - host: dashboard.com
    http:
      paths:
      - backend:
          serviceName: kubernetes-dashboard
          servicePort: 80

```

Example with TLS Certificate - https//

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secret-tls
  namespace: default
type: kubernetes.io/tls
data:
    tls.crt: base64 encoded cert
    tls.key: base64 encoded key
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-example-ingress
spec:
    tls:
    - hosts:
        - myapp.com
        secretName: myapp-secret-tls
  rules:
    - host: myapp.com
        http:
            paths:
            - backend:
                serviceName: myapp-internal-service
                servicePort: 80

```

### Namespaces

They are virtual cluster inside the cluster.

You can have multiple namespaces in a cluster. They organise and groupe resources (ex database, monitoring, loggin) 

They are 4 namespaces by default:

1. **kube-system:** information of the system process.
2. **kube-public:** public accesible data.
3. **kubeonode-lease:** information about the availability of the nodes.
4. **default:** place where created resources are located.

Namespaces can be created by kubectl

```sh
kubectl create namespace namespace-name
```

or by config file

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: mysql-configmap
    namespace: my-namespace
data:
  database_url: mysql-service.database
```

Benefits:
- Resources grouped in Namespaces.
- Conflicts: Many teams, same application.
- Resource sharing: Production and Development.
- Resource sharing: Blue/Green Deployment.
- Access and resources limits on Namespaces.

Each namespace must define it owns ConfigMap and Secret.
Some components, like volumes and nodes, can not be created in a namespace.

### External Configurations

- Config Map

You connect Config Map with the Pod.

- Secret

It is used to store secret data (credentials).
It stores it in a base64 encoded format.
The Pods will have access to the secrets.

### Data storage

Volumes attach a physical storage to the database, in a local machine or remote, out of the cluster (clouds).

### Blueprints

- Deployment

It a layer of abstraction on top of Pods.
It takes care of replicating Pods for stateless applications through the ReplicaSet.

- StatefulSet

If database Pods are replicated by deployment, because they will cause **data inconsistences**.

Instead, StatefulSet is used for **stateful apps or Databases**, which cares to read and write data **syncronize**, avoiding data inconsistences.

Each replica Pod has it own persistent identity **Blueprint** (fixed ordered name), created from the same specification, and they are not interchangeable.

They also have fixed **individual domain name system**. When the Pod restarts the IP address changes but the name and endpoint stays the same.

Only one Pod is allowed to write data, avoiding data inconsistences. It is called **master**, the others are called **slaves**. Pods are created syncronously. The master Pod  is the one which is created first.

Despite each Pod uses different physical storage, each Pod must have the same data at the same time. It means that when the master update data, all the Pods are synchronous updated. To ensure it, a process runs which cares of the **continuosly synchronizing** of the data. 

As well, if a **Pod Replica** is deleted, it will follow the LIFO structure: last in, first out.

Persistent Volume lifecycle isn't tied to other component's lifecycle. Even if all the Pods die, Persistent Volumes survive.

You have to configurate a Persistent Volume to the StatefulSet.

Each Pod has a Persistent Volume which stores the **synchronous data state** of the Pod. When the Pod dies, the new Pod accesses the last state Pod data from the Persistent Volume. 

However, it is a common practice to host Databases outside of the Kubernetes Cluster.

### Minikube

Minikube is a one node Kubernetes Cluster where the master's processes and the worker's processes run. It runs through a Virtual Box.

### Kudeectl

Kudeectl is a command line for Kubernetes Cluster, to communicate with the Api Server.

### Configuration

Pods and Services are created through configuration.

All the configurations go through the **master node**, with a process called **API Server**.

The configuration can be created by Kubernetes clients (UI, API, CLI), which communicate with the Master node to API Server with a configuration request.

This request configuration is a **Template** or **Blueprint** in yaml or json file.

The configurations are indicative.

Each configuration files has 3 parts:

- header

```yaml
apiVersion: apps/v1
kind: Deployment 
```

- metadata

```yaml
metadata:
    name: my-app
    labels:
        app: my-app
```

- specification
    It depends on the kind of component which is created.

```yaml
spec:
    replicas: 2
    selector:
        matchLabels:
```

### Status and Templates

- Status
    the status is automatically generated and added by Kubernetes, comparing the desired state and the actual state. Kubernetes will update the status permanently.

- Template
    the template key applies the Pods configuration Blueprint.

Example:

Set Kubernetes to create two Replicas Pods with a container running inside, based on my-app image.

Configure environment variables and ports.

The Controller manager, which controls the state, will make sure to recover the desire state if there is any issue.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: my-app
    labels:
        app: my-app
spec:
    replicas: 2
    selector:
        matchLabels:
            app: my-app
    template:
        metadata:
            labels:
                app: my-app
        spec:
            containers:
                - name: my-app
                  image: my-image
                  env:
                    - name: SOME_ENV
                      value: SOME_ENV
                  port:
                    - containerPort: 8080

```

### How to connect the Service to Deployment?

The Service is connected through the matadata labels from the deployment, which should match the selector key from the Service.

The selector app name of the Service matches the metadata labels name from the deployment or its Pod. Because Services should know which Pods belog to that service.

Services have **Port configuration**.

The Service should know to which Port it should foward the request. This is the **target port**. The target Port should match the Pot Port.

### Kubectx

```sh
brew install Kubectx
```

```sh
kubens
kubens my-namespace
```

### Helm

Helm Charts

- Bundle of YAML Files (reusable configuration).
- Create your own Helm Charts with Helm.
- Push them to Helm Repository.
- Download and use existing ones.

```sh
helm search <search-name>
```

#### Templating Engine

You can define a commond Blueprint for microservices and the values are replaced by placeholders.

The values are defined either via yaml file or with --set flag

values.yaml
```yaml
name: my-app
container: 
    name: my-app-container
    image: my-app-image
    port: 9001
```

template YAML config
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: {{ .Value.name}}
spec:
    containers:
        - name: {{ .Values.container.name }}
          image: {{ .Values.container.image }}
          port: {{ Values.container.port }}

```

They are useful to deploy the same application across different environments (development-staging-production)

Tree structure:

Matadata about the Chart.
mychart/Chart.yaml

Values for templates
mychart/Values.yaml

Chart dependecies inside
mychart/charts/...
mychart/templates/...


### Volumes

Docker Volumes are used for persistence data.

It is a cluster resource created via YAML file

You have to configure, create and manage what type of storage the application needs.

- Local
- Remote

Stores are external plugins to the cluster. Depending on the storage type, the spec attribute is different.

Roles:

- Admins
    set up and maintains the cluster.
    create Persistent Volumes.
- Users
    deploy the application in cluster.
    claim Persistent Volumes creting PVC.

**Persistent Volume Claim (PVC)** define the storage resources, like the name, the volume mode, the access mode, and the storage size. 
  
You must use the PVC in the Pod  configuration.

Claim
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata: 
    name: pvc-name
spec: 
    storageClassName: manual
    volumeMode: Filesystem
    accessModes:
        - ReadWriteOnce
    resources:
        requests:
            storage: 10Gi
```

Pod
```yaml
spec:
    containers:
        - name: myapp
        image: nginx
        volumeMounts:
        - mountPath: "/var/www/html"
        name: mypd
    volumes:
        - name: mypd
        persistentVolumeClaim:
            claimName: pvc-name
```

##### Storage Class

Storage Class provisions Persistent Volumes dynamically when a **Persistent Volume Claim** is generated.

It is an abstraction level that:

- abstracts underlying storage provider.
- parameters for the storage.

StorageBackend is defined in the Storage Class component.

- via "provisioner" attribute.
- each storage backend has own provisioner.
- internal provisioner - kubernetes.io
- external provisioner.
- configure parameters for the storage we want to request for persitent volumes

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
apiVersion: v1
metadata: 
    name: storage-class-name
provisioner: kubernetes.io/aws-ebs
parameters:
    type: io1
    iopsPerGB: "10"
    fsType: ext4
```

The Persistent Volum Claim Configuration reference the Storage Class in the parameters.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata: 
    name: pvc-name
spec: 
    accessModes:
        - ReadWriteOnce
    resources:
        requests:
            storage: 100Gi
        storageClassName: storage-class-name
```

When the Pod claims storage through the Persistent Volum Claim, the Persistent Volum Claim requests the storage from the Storage Class, and the Storage Class creates a Persistent Volume that meets the needs of the Claim.