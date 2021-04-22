# Kubernetes
## Iniciando o estudo sobre gerenciamento de containers

____

* Uma máquina executa X containers, para suportar mais deles é necessario:
    * Escalabilidade vertical -> Adicionar mais poder computacional -> Comprar mais hardware para o computador atual
    * Escalabilidade horizontal -> É quando é adicionado mais máquinas (computadores) numa rede e há comunicação entre os computadores
        * Cluster

* Kubernetes é:
    * Capaz de criar um cluster de containers, sendo capaz de:
        * Subir, Destruir, "Ressucitar" nós conforme requisitado ou dá necessidade
    * Multiplataforma -> AWS, Google cloud, Azure e demais
    * Caracteristicas:
        * Pods -> Encapsula um container -> Menor parte
        * Faz load balance
        * Cria e gerencia as redes
    * Para gerenciar uma rede o kube cria dois tipos de máquinas
        * Master -> Gerencia o cluster, receber os comandos, manter os containers em funcionamento
            * Dentro do master temos:
                * API -> Um rest responsavel por receber e executar comandos
                * C-M -> Control manegment -> Manter o estado das coisas
                * Sched -> Gerencia os pods
                * ETCD -> Banco de dados chave valor que guarda todas as infos do kube
        * Node -> A menor parte e que realiza a execução do desejado
            * Dentro do node temos:
                * kubelet -> Controla e gerencia os pods dentro dos nodes
                * k-proxy -> Comunicação entre os nodes dentro do kube
    * Funcionamento
        * Control page é a gestão sobre os nodes 
        * O API é o nó central que comunica dos demais modulos do control plane aos nodes.
        * A comunicação é feita dessa forma:
            * Computador ordena pelo Kubectl
            * Kubectl faz a comunicação para a API
            * API gerencia dos nodes com base na ordem recebida

##### Para instalar no Windows

Para instalar o Kubernete no Windows é necessário instalar o Docker e depois disso habilitar a opção de Kubernetes no sistema, após isso ele será instalado,

##### Para instalar no Linux

O Kubernetes é separado do Docker, sendo assim é necessário baixar o pacote do kube e instalar separadamento, más necessita dele e do minikube que a criação dos cluster.

Os passos são:

* Instalar o kubernetes
* Instalar o minikube
* Instalar uma vm-driver -> Virtualbox
* Criar o cluster com o minikube, Ex:

```
minikube start --vm-driver=virtualbox
```

Este comando deverá ser executado toda a vez que foi desejado iniciar o processo kube no sistema

Comandos:
```
#### kubernetes
sudo apt-get install curl -y
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl

#### Agora para o minikube:
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v1.12.1/minikube-linux-amd64 \ && chmod +x minikube
sudo install minikube /usr/local/bin/
```

#### Kubectl

Interface que ordena-ra a API a realizar ações por métodos imperativos ou declarativos

#### PODS 

Ex:
Docker gerar containers
Kube gerar pods

* Pods são uma capsula, eles podem conter de um a mais containers
* Um pod consome somente 1 IP, mesmo possuindo mais de um container interno, nesta situação ocorre o mapeamento de portas
* Pods são efêmeros -> Isso é, sempre é um novo pod, não é a copia de um ou reiniciar o pod que já apresentou o problema
* Caso um pod só tenha um container e este container apresentar problemas, o pod é morto e é criado um novo, caso o pod tenha mais de 1 container, o pod só será destruido se todos os containers internos estiverem mortos
* Dentro de um pod os containers compartilham:
    * Namespaces
    * Volumes
    * Rede -> Ex: Um container de porta 80 faz localhost para 9000 que é outro container -> Isso funciona porque todos estão dentro do POD

##### Declaratico e interativo

* O método de editar e configurar na mão os pod são chamadas de interativas
* Método declarativo é quando se é usado formas de automatizadas

#### Alguns comandos

Mostrar as nós cluster do kubernetes no host:

```
C:\Users\guilhermerb>kubectl get nodes
NAME             STATUS   ROLES    AGE   VERSION
docker-desktop   Ready    master   63m   v1.19.7
```

* Name -> Nome do cluster
* Status -> O status atual do cluster
* Roles -> Papel do cluster
* Age -> Tempo que foi criado 
* Version -> Versão do kube usada para criar o cluster

##### Comandos os POD's

Criar um pod:
```
kubectl run <nome> --image=<imagem>
```
Listar os pods:
```
kubectl get pods 
```

Acompanhar em tempo real a criação de um pod:
```
kubectl get pods --watch
```

Listar detalhes dos pods:
```
C:\Users\guilhermerb\Desktop>kubectl get pods -o wide
NAME          READY   STATUS    RESTARTS   AGE     IP          NODE             NOMINATED NODE   READINESS GATES
teste-pod-1   1/1     Running   1          3h51m   10.1.0.15   docker-desktop   <none>           <none>
teste-pod-2   1/1     Running   0          2m33s   10.1.0.20   docker-desktop   <none>           <none>
teste-pod-3   1/1     Running   0          13m     10.1.0.19   docker-desktop   <none>           <none>
```

Descrição de um pod:
```
kubctl describe pod <nome>
```

Deletar um pod
```
kubectl delete pod <nome>
````

Deletar todos os pods
```
kubectl delete pods --all
```

Ex:
```
C:\Users\guilhermerb>kubectl run teste --image=alpine:latest
pod/teste created

C:\Users\guilhermerb>kubectl get pods
NAME    READY   STATUS              RESTARTS   AGE
teste   0/1     ContainerCreating   0          4s

C:\Users\guilhermerb>kubectl get pods --watch
NAME    READY   STATUS              RESTARTS   AGE
teste   0/1     ContainerCreating   0          7s
teste   0/1     Completed           0          12s
teste   0/1     Completed           1          15s
teste   0/1     CrashLoopBackOff    1          16s

C:\Users\guilhermerb>kubectl get pods
NAME    READY   STATUS             RESTARTS   AGE
teste   0/1     CrashLoopBackOff   1          30s

C:\Users\guilhermerb>kubectl get pods
NAME    READY   STATUS             RESTARTS   AGE
teste   0/1     CrashLoopBackOff   3          92s

C:\Users\guilhermerb>kubectl delete pod teste
pod "teste" deleted

C:\Users\guilhermerb>kubectl get pods
No resources found in default namespace.

```

Da para editar um pod, Ex:
```
kubectl edit pod <nome pod>
```

Um exemplo, o pod original roda a versão latest de um S.O, depois de alterar com esse comando, o pod passaram a construir imagens com a nova config

#### Criar um pod de forma declarativa

Para criar um pod de forma declarativa é necessário utilizar de um arquivo de extensão YAML ou JSON 

* apiVersion -> Versão da kubectl sendo estas  
    * Estavel (seguro para uso)
    * Alpha (Acabou de iniciar os testes)
    * Beta (Segunda fase de testes)


Exemplo de um pod.yaml:
```
#Versão da kubectl sendo estas -> Estavel (seguro para uso), alpha (Acabou de iniciar os testes) e beta (Segunda fase de testes)
apiVersion: v1 #Estavel
kind: Pod #Tipo
#Config do pod
## info do pod
metadata:
  name: teste-pod1
## Config do pod
spec:
  #Todos os containers que estaram dentro do pod
  containers:
    - name: teste-nginx
      image: nginx:latest 
```

Para subir este pod faça:

```
kubectl apply -f <caminho do arquivo>/<nome do arquivo>.yaml
```

Se o arquivo de origem for alterado e for aplicado um apply nele, os pod sofrera as mudanças

Necessidades para criar um pod:
* apiVersion
* kink
* metadata
* spec

Site para validar a estrutura do Kube YAML
Link: https://kubeyaml.com/

#### Deletar um pod criado por meios declarativo

```
kubectl delete -f <arquivo>
```

Ex:
```
C:\Users\guilhermerb>kubectl get pods
NAME         READY   STATUS    RESTARTS   AGE
teste-pod1   1/1     Running   0          3h

C:\Users\guilhermerb>cd Desktop

C:\Users\guilhermerb\Desktop>kubectl delete -f teste_pod.yaml
pod "teste-pod1" deleted

C:\Users\guilhermerb\Desktop>kubectl get pods
No resources found in default namespace.
```

#### Entrar dentro de um pod com um container

Acessar um pod de forma interrativa
```
C:\Users\guilhermerb\Desktop>kubectl exec -it teste-pod1 -- bash
root@teste-pod1:/# ls
bin   dev                  docker-entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint.d  etc                   lib   media  opt  root  sbin  sys  usr
root@teste-pod1:/#
```

Diferente do docker, quando digitado exit, ele não mata o container, ele fecha a interação

#### Comunicação entre pods

Pods dentro de um cluster podem se comunicar normalmente por motivos que estão dentro de uma mesma rede

SVC do Kube:
* Abstração para expor aplicações executando em ou mais pods
* Proveem IP's Fixos para comunicação
* Proveem um DNS para um ou mais pods
* São capazes de fazer balanceamento de carga

De forma resumida ele traz o: ClusterIP, NodePort e LoadBalancer

Os pods se comunicam via o SVC como intermediario


#### ClusterIP
* Funciona apenas dentro do cluster
* Service não é de mão dupla
    * Todos conseguem ver o service, porém o SVC não consegue se comunicar com todos
* Service é um arquivo a parte
* Para a identificações é necessario a criação de labels
    * Label deve ficar no podcliente, enquanto o svc faz referencia a ele
    * As lebels podem ter qualquer nomeação chave valor


Ex: Serviços e pods
```
C:\Users\guilhermerb\Desktop>kubectl get services
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   11h
svc-pod2     ClusterIP   10.99.49.103   <none>        80/TCP    15s

C:\Users\guilhermerb\Desktop>kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   11h
svc-pod2     ClusterIP   10.99.49.103   <none>        80/TCP    118s

C:\Users\guilhermerb\Desktop>kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
teste-pod-1   1/1     Running   1          3h39m
teste-pod-2   1/1     Running   1          3h39m
teste-pod-3   1/1     Running   0          72s
```

Ex de redirecionamento de saida via service com pods e svc
```
C:\Users\guilhermerb\Desktop>kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   11h
svc-pod2     ClusterIP   10.99.49.103   <none>        80/TCP    118s

C:\Users\guilhermerb\Desktop>kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
teste-pod-1   1/1     Running   1          3h41m
teste-pod-2   1/1     Running   1          3h41m
teste-pod-3   1/1     Running   0          3m48s

C:\Users\guilhermerb\Desktop>kubectl exec -ti teste-pod-1 -- bash
root@teste-pod-1:/# curl 10.99.49.103:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

Lembrete, mesmo se matar o cluster com o label determinado, é só criar algo que resposta pelo label e por setado

Alterando algumas configs de porta no service, o resultado:
```
C:\Users\guilhermerb\Desktop>kubectl apply -f svc_pod2.yaml
service/svc-pod2 configured

C:\Users\guilhermerb\Desktop>kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
teste-pod-1   1/1     Running   1          3h48m
teste-pod-2   1/1     Running   0          27s
teste-pod-3   1/1     Running   0          10m

C:\Users\guilhermerb\Desktop>kubectl get services
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP    11h
svc-pod2     ClusterIP   10.99.49.103   <none>        9000/TCP   10m

C:\Users\guilhermerb\Desktop>kubectl exec -ti teste-pod-1 -- bash
root@teste-pod-1:/# curl 10.99.49.103:80
^C
root@teste-pod-1:/# curl 10.99.49.103:9000
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

P.S: Quando criar um port no service e só setar o port, ele assume que o port e o targetPort são os mesmo;

#### NodePort

ClusterIP serve para redirecionamento interno dos pods do cluster 
NodePort serve para realizar comunicação externa com o cluster, forá que nodePort tem funções de clusterIp

Arquivo de exemplo de nodePort
```
apiVersion: v1
kind: Service
metadata:
  name: svc-pod-1    
spec:
  type: NodePort
  ports:
    - port: 80
      nodePort: 30000
  selector:
    app: teste-pod-1
```

Services criados:
```
C:\Users\guilhermerb\Desktop>kubectl get services
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        22h
svc-pod-1    NodePort    10.100.185.86   <none>        80:30000/TCP   14m
svc-pod2     ClusterIP   10.99.49.103    <none>        9000/TCP       11h
```

P.S: No windows tem uma jogada, quando configurado o nodeport da forma simples apresentada, para acessar o serviço, você tem quefazer localhost mais a porta que o nodeport está entregando.

Ex:
```
C:\Users\guilhermerb\Desktop>kubectl get nodes -o wide
NAME             STATUS   ROLES    AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE         KERNEL-VERSION
          CONTAINER-RUNTIME
docker-desktop   Ready    master   22h   v1.19.7   192.168.65.4   <none>        Docker Desktop   5.4.72-microsoft-standard-WSL2   docker://20.10.5

C:\Users\guilhermerb\Desktop>kubectl get services
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        22h
svc-pod-1    NodePort    10.100.185.86   <none>        80:30000/TCP   9m49s
svc-pod2     ClusterIP   10.99.49.103    <none>        9000/TCP       11h

C:\Users\guilhermerb\Desktop>curl localhost:30000
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

O port de saída padrão é váriavel de 30000 a 32000, porém é possível fixar este, o nodePort citado no arquivo de NodePort fixa a porta de saída.

P.S: No windows e no linux tem uma diferença para acessar os pods, no Windows ele faz bind automatico entre o localhost e as portas externas, já no linux que usa minikube para rodar o kubectl é necessário usar o internal-IP

#### LoadBalancer

Cuida da distribuição de carga entre os pods relacionados, Ex:

```
apiVersion: v1
kind: Service
metadata:
  name: svc-pod-1-loadbalancer
spes:
  type: loadBalancer
  ports:
    - port: 80
      nodePort: 30001
  selector:
    app: teste-pod-1
```

#### Deletando services

```
kubectl delete services --all
```

#### Config Map

* Armazena configs para ser reutilizado em mais pods e pode ser desconectado dos mesmos
* Pode haver mais de um CM (Config map)

Referencia de somente um valor de várial
```
      env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            configMapKeyRef:
              name: db-configmap
              key: MYSQL_ROOT_PASSWORD
```

Referencia a todos os valores do configmap
```
      envFrom:
        - configMapRef:
            name: db_configmap
```