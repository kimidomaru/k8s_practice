# Projeto Kubernetes Guess Game

## Sumário
- [Descrição](#descricao)
- [Requisitos](#requisitos)
- [Instruções](#instruções)
  - [1. Clonar o Repositório](#1-clonar-o-repositório)
  - [2. Implantação no Kubernetes](#2-implantação-no-kubernetes)
    - [2.1 Ingress Nginx](#21-ingress-nginx)
    - [2.2 Endereço do Ingress](#22-endereço-do-ingress)
    - [2.3 Implantação da Aplicação](#23-implantação-da-aplicação)
  - [3. Acessando a Aplicação](#3-acessando-a-aplicação)
  
## Descricao
Este projeto é uma prática de orquestração em K8s do projeto [GuessGame] (https://github/com/fams/guess_game), onde é gerado uma palavra e os jogadores tentam adivinhar. A aplicação consiste de um frontend desenvolvido em React e um backend em Node.JS responsável pela lógica do jogo e persistência dos dados em um banco de dados PostgreSQL. A proposta desse exercício é subir o ecossistema em uma arquitetura de microserviços no Kubernetes, adotando boas práticas no processo.

## Requisitos
Docker
Docker Compose
Kubernetes (Minikube, Kind, ou um cluster Kubernetes real)

## Instruções

### 1. Clonar o Repositório
Clone o repositório para sua máquina local:
```
git clone https://github.com/seu-usuario/guess-game.git
cd guess-game
```

### 2. Implantação no Kubernetes
Essa prática foi feita levando em conta um Cluster provisionado com Kind. Para implantar a aplicação no Kubernetes, execute os seguintes comandos:

### 2.1 Ingress Nginx
Implante o Ingress Ngix no seu cluster Kubernetes através do seguinte comando:

```
kubectl apply -f k8s/ingress-nginx.yaml
```

### 2.2 Endereço do Ingress
Para acessar a aplicação, você precisa adicionar o DNS do Ingress no arquivo de Hosts, caso esteja executando em uma máquina local. No Kind o endereço padrão é `127.0.0.1`. Adicione a seguinte entrada no arquivo hosts:

```
127.0.0.1 k8spuc.local
```

### 2.3 Implantação da Aplicação
Para implantar a aplicação no Kubernetes, execute os seguintes comando:

```
kubectl apply -f k8s/
```

O esperado é que todos os recursos sejam criados com sucesso. Para verificar se os recursos foram criados, execute o seguinte comando:

```
kubectl get all -n guess-game
```

O output esperado é algo similar a isso:

```

NAME                            READY   STATUS    RESTARTS        AGE
pod/backend-76d5878877-hcqrj    1/1     Running   3 (3h46m ago)   5h34m
pod/frontend-55d5d6557d-v5g6p   1/1     Running   0               3h23m
pod/postgres-5d5dcfcfd8-8pddn   1/1     Running   1 (3h47m ago)   5h34m

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/backend    ClusterIP   10.106.251.9    <none>        5000/TCP       5h34m
service/db         ClusterIP   10.102.21.64    <none>        5432/TCP       5h34m
service/frontend   NodePort    10.105.48.239   <none>        80:30005/TCP   3h23m

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/backend    1/1     1            1           5h34m
deployment.apps/frontend   1/1     1            1           3h23m
deployment.apps/postgres   1/1     1            1           5h34m

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/backend-76d5878877    1         1         1       5h34m
replicaset.apps/frontend-55d5d6557d   1         1         1       3h23m
replicaset.apps/postgres-5d5dcfcfd8   1         1         1       5h34m

NAME                                          REFERENCE            TARGETS                                     MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/backend   Deployment/backend   cpu: <unknown>/50%, memory: <unknown>/50%   1         5         1          5h34m

```

Também é preciso verificar os objetos do Ingress:
```
kubectl get all -n ingress-nginx
```
O Output esperado é algo como:
```bash
NAME                                            READY   STATUS      RESTARTS        AGE
pod/ingress-nginx-admission-create-q2gwj        0/1     Completed   0               5h35m
pod/ingress-nginx-admission-patch-flkqj         0/1     Completed   0               5h35m
pod/ingress-nginx-controller-6568cc55cd-tv7nw   1/1     Running     1 (3h48m ago)   5h35m

NAME                                         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/ingress-nginx-controller             NodePort    10.102.70.199   <none>        80:30080/TCP   5h35m
service/ingress-nginx-controller-admission   ClusterIP   10.103.141.99   <none>        443/TCP        5h35m

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           5h35m

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-6568cc55cd   1         1         1       5h35m

NAME                                       STATUS     COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   Complete   1/1           14s        5h35m
job.batch/ingress-nginx-admission-patch    Complete   1/1           14s        5h35m
```

### 3. Acessando a Aplicação
Para acessar a aplicação, abra o navegador e acesse o endereço `http://k8spuc.local:30080`. Você será redirecionado para a página inicial do jogo. A partir daí, você pode jogar e se divertir.