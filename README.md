# GitOps Microservices

Loja online baseada em microserviços com deploy automatizado, utilizando práticas
de GitOps com Kubernetes e Argo CD.

> Este projeto utiliza o arquivo [kubernetes-manifests.yaml](https://raw.githubusercontent.com/GoogleCloudPlatform/microservices-demo/refs/heads/main/release/kubernetes-manifests.yaml) do repositório [microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo) da Google Cloud Platform.
---

## Pré-requisitos
- Cluster Kubernetes em execução (Rancher Desktop, Minikube, Kind, etc)
- `kubectl` instalado e configurado

## Como executar o projeto

### Instalação do Argo CD no cluster local

Execute os seguintes comandos para instalar o Argo CD:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Acessando Argo CD via interface web (UI)

1. Mapeie a porta do serviço Argo CD para acessar via navegador:
    ```bash
    kubectl port-forward service/argocd-server -n argocd 8080:443
    ```
    > Acesse a tela de login do Argo CD em [localhost:8080](http://localhost:8080)

2. Obtenha a senha inicial de usuário `admin`:
    ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```

### Criação da Aplicação no Argo CD

1. No menu lateral, clique em **Applications** e, em seguida, clique no botão **+ NEW APP**
2. Preencha os campos obrigatórios na aba **GENERAL**
    - **Application Name:** `online-boutique`
    - **Project Name:** `default`
3. Na seção **SOURCE**:
    - **Repository URL:** `https://github.com/algacyr-melo/gitops-microservices`
    - **Revision:** `HEAD`
    - **Path:** `k8s`
4. Na seção **DESTINATION**:
    - **Cluster URL:** `https://kubernetes.default.svc`
    - **Namespace:** `default`

> Clique em **CREATE** e aguarde o Argo CD sincronizar com o repositório Git
e aplicar as definições dos manifests no seu Cluster local

### Mapeamento de porta para acessar a loja virtual

Execute o seguinte comando para acessar a loja via navegador:

```bash
kubectl port-forward service/frontend-external 8081:80
```
> Acesse a loja virtual em [localhost:8081](http://localhost:8081)
