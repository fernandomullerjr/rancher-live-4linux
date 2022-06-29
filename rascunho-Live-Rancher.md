
# Dia 28/06/2022

# GitOps at Scale com Fleet no Rancher
Dia 28 de junho às 19:00.

O Rancher é uma uma plataforma de código fonte aberto que permite o gerenciamento de uma infraestrutura de conteineres (seja usando Docker ou Kubernetes) com o objetivo de efetuar o deploy de aplicações em ambientes de rede local ou em serviços de nuvem (Azure, Digital Ocean, AWS, etc).

Roberto Farias de Souza
Atua como Analista de Infraestrutura Sênior em Software Livre na 4Linux – Open Software Specialists, formado em Gestão de Tecnologia da Informação pela Unisul – Universidade do Sul de SC.
Atua com foco em DevOps, possuindo 26 anos de experiência profissional em Tecnologia da Informação.
Acumula experiência em LDAP, Samba 3/4, Segurança em servidores Linux, Gerencia de configurações, Administração de bancos de dados PostgreSQL, JBoss, WildFly, Gerenciamento de Containers (Docker/Kubernetes/Rancher/OpenShift), Gerenciamento de CI/CD (Git/Jenkins/Rundeck), Monitoramento de servidores (Zabbix/Prometheus), Zimbra, OpenStack, ELK e Serviços em Cloud AWS/GCP/Azure.
Possui expertise como Instrutor e Conteudista m mais de 15 cursos nas modalidades presencial e a distância na 4Linux.


# O que você irá aprender

01

Conhecendo o Rancher.
02

Criar projetos na GCP.
03

Gerenciar permissão e chave de acesso.
04

Configurar Firewall e IP externos.
05

Instalação do Rancher.
06

Acessar painel e conhecer os menus.
07

Criar Cluster Kubernetes Google GKE.
08

Introdução a GitOps com Fleet.
09

Configurar Cluster Groups, cluster e Git Repos.
10

Realizar Deploy do WordPress Single Cluster com Fleet.



# AULA

- Conecta no GCP
- Gera as credenciais para o Rancher poder utilizar a API

- MENU - API e Serviços
<https://console.cloud.google.com/apis/library?hl=pt-br&project=mystical-glass-354722>

- Procurar por IAM
<https://console.cloud.google.com/apis/library/iam.googleapis.com?hl=pt-br&project=mystical-glass-354722>
NECESSÁRIO ATIVAR A API


- Clicar em "Biblioteca" e pesquisar por "Kubernetes"
- Ativar a API do Kubernetes.



- Acessar "IAM e administrador"
- Clicar em "IAM"
<https://console.cloud.google.com/iam-admin/iam?hl=pt-br&project=mystical-glass-354722>
Procurar pelo "123233014192-compute@developer.gserviceaccount.com 	Compute Engine default service account "
Ela vai ser usada pelo Rancher.

Clicar na caneta a direita, "editar principal"
- Clicar em "adicionar outro papel"
- Procurar por "Kubernetes"
- Selecionar a opção "Administardor do Kubernetes", com acesso total.
- Clicar em "Salvar"



- Ir em "Conta de Serviço"
- Clicar em "atualizar"
<https://console.cloud.google.com/iam-admin/serviceaccounts?hl=pt-br&project=mystical-glass-354722>
- Vai ter uma conta:
 123233014192-compute@developer.gserviceaccount.com 	
- A direita, clicar nos 3 pontinhos e "Gerenciar Chaves"
- Criar uma chave em JSON mesmo
Chave
6e24706eff86655d0a49fddc32274929af43cb0a 
Chave privada salva no seu computador
mystical-glass-354722-6e24706eff86.json permite acesso aos recursos da nuvem. Por isso, armazene-o de maneira segura.Conheça mais práticas recomendadas




# Configurar Firewall e IP externos.

- Acessar "Rede VPC"
<https://console.cloud.google.com/networking/networks/list?referrer=search&hl=pt-br&project=mystical-glass-354722>

- Ir em "Firewall"

- clicar em "criar regra de firewall"

nome:
nodeports

Registros:
desativado

prioridade:
1000

destinos:
Todas as instancias na rede

Filtro de origem:
Intervalos IPv4

Intervalos de IPv4 de origem:
0.0.0.0/0

Portas e protocolos especificados:
TCP
30000-32767

Clicar em "criar"



- No menu esquerdo, clicar em "Endereços ip"
<https://console.cloud.google.com/networking/addresses/list?hl=pt-br&project=mystical-glass-354722>

- Clicar em "Reservar um endereço estático "

nome:
rancher

npivel:
premium

Clicar em "Reservar"


ip
34.136.135.215 




# Instalação do Rancher.
- Subir uma EC2 para instalar o Rancher nela.
- Acessar "Compute Engine"
- Acessar "Instancias de VM"

- Clicar em "criar"
- Região
selecionar a mesma região que o endereço ip reservado:
iowa

- Tipo de máquina:
e2-standard-2
2 CPU, 8GB RAM

- Em "Disco de inicializaçção"
clicar em "mudar"
Escolher Ubuntu
Ubuntu 20
Disco permanentemente equilibrado
30 GB


- Em "Firewall"
marcar as caixas:
"Permitir tráfego HTTP"
"Permitir tráfego HTTPS"


- Clicar em "REDE, DISCOS, SEGURANÇA, ADMINISTRAÇÃO, LOCATÁRIO UNICO"
Clicar em "REDE"
"Interface De rede"
"default"
"Endereço IPv4 Externo", selecionar o ip público que foi gerado mais cedo


Clicar em "Criar".


- Clicar na seta perto de "Conectar"
ir em "Abrir na janela do navegador"



fernandomj90@rancher:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        29G  1.8G   28G   7% /
devtmpfs        3.9G     0  3.9G   0% /dev
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           795M  924K  794M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/loop0       56M   56M     0 100% /snap/core18/2409
/dev/loop1       62M   62M     0 100% /snap/core20/1518
/dev/loop2      299M  299M     0 100% /snap/google-cloud-cli/44
/dev/loop3       68M   68M     0 100% /snap/lxd/22753
/dev/loop4       47M   47M     0 100% /snap/snapd/16010
/dev/sda15      105M  5.2M  100M   5% /boot/efi
tmpfs           795M     0  795M   0% /run/user/1001
fernandomj90@rancher:~$ 



# Etapas
<http://dontfile.com/liverancher4linux>


### Instalação do Docker

curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo docker version


### Instalação do Rancher

sudo docker container run --privileged -d --name rancher-manager --restart=unless-stopped -p 80:80 -p 443:443 rancher/rancher:v2.6.4

sudo docker container ls

sudo docker logs rancher-manager
sudo docker logs rancher-manager -f


### Exibir a senha do admin:

sudo docker logs rancher-manager 2>&1 | grep "Bootstrap Password:"


### Vagrant Quick Start:
https://rancher.com/docs/rancher/v2.5/en/quick-start-guide/deployment/quickstart-vagrant/





~~~~bash
root@rancher:/home/fernandomj90# sudo docker container ls
CONTAINER ID   IMAGE                    COMMAND           CREATED         STATUS         PORTS                                                                      NAMES
7c4f67147d04   rancher/rancher:v2.6.4   "entrypoint.sh"   6 seconds ago   Up 2 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   rancher-manager
root@rancher:/home/fernandomj90# 
~~~~



~~~~bash
root@rancher:/home/fernandomj90# sudo docker logs rancher-manager | head
2022/06/28 23:23:46 [INFO] Rancher version v2.6.4 (4b4e29678) is starting
2022/06/28 23:23:46 [INFO] Rancher arguments {ACMEDomains:[] AddLocal:true Embedded:false BindHost: HTTPListenPort:80 HTTPSListenPort:443 K8sMode:auto Debug:false Trace:false NoCACerts:false AuditLogPath:/var/log/auditlog/rancher-api-audit.log AuditLogMaxage:10 AuditLogMaxsize:100 AuditLogMaxbackup:10 AuditLevel:0 Features: ClusterRegistry:}
2022/06/28 23:23:46 [INFO] Listening on /tmp/log.sock
2022/06/28 23:23:46 [INFO] Waiting for k3s to start
2022/06/28 23:23:47 [INFO] Waiting for server to become available: an error on the server ("apiserver not ready") has prevented the request from succeeding
2022/06/28 23:23:49 [INFO] Waiting for server to become available: an error on the server ("apiserver not ready") has prevented the request from succeeding
2022/06/28 23:24:01 [INFO] Running in single server mode, will not peer connections
2022/06/28 23:24:01 [INFO] Applying CRD features.management.cattle.io
2022/06/28 23:24:01 [INFO] Waiting for CRD features.management.cattle.io to become available
2022/06/28 23:24:01 [INFO] Done waiting for CRD features.management.cattle.io to become available
root@rancher:/home/fernandomj90#
~~~~




~~~~bash
erros:
2022/06/28 23:25:20 [ERROR] error syncing 'cattle-fleet-system/helm-operation-dkxtl': handler helm-operation: an error on the server ("container not found (\"proxy\")") has prevented the request from succeeding (get pods helm-operation-dkxtl), requeuing

depois normalizou:
2022/06/28 23:25:28 [INFO] namespaceHandler: addProjectIDLabelToNamespace: adding label field.cattle.io/projectId=p-dn7rv to namespace=cattle-fleet-local-system
~~~~





~~~~bash
root@rancher:/home/fernandomj90# sudo docker logs rancher-manager 2>&1 | grep "Bootstrap Password:"
2022/06/28 23:24:27 [INFO] Bootstrap Password: 7gtqk9xbktwnxhbqq276ndg69hwkwt5bgr67xtsc2rsdcb85bl6f47
root@rancher:/home/fernandomj90# 
~~~~



- Acessar o Rancher via ip publico
34.136.135.215

Senha criada:



https://artifacthub.io/





#  07 - Criar Cluster Kubernetes Google GKE.

- Em "Cluster Management"
<https://34.136.135.215/dashboard/c/local/manager/provisioning.cattle.io.cluster>

- Clicar em "create"
Escolher o "Google GKE"

- Em "Cluster Name":
liverancher

- Em "Member" e "Labels" não vamos alterar

- Em "Google Account Access"
clicar em "Read from a file"
clicar em "Read from a file"
clicar em "Read from a file"
- Selecionar a chave JSON que foi criada mais cedo.
- Clicar em "Create".
- Clicar em "Configure cluster".
- Navegar até a parte "Node Pools".
- Em "Image Type", mudar para "Ubuntu with Docker".
- Em "Machine type", escolher a familia "n2-highmem-2", com 2 CPU e 16 GB RAM.

EM GROUP DETAILS
- Name, colocar:
    nodes
- Em "Initial Node Count" ele vem com 3.

- Clicar em "Create" e aguardar.


- Cluster Kubernetes já está disponível no painel do GCP.







# 08 - Introdução a GitOps com Fleet.

- Repositório que será usado:
<https://github.com/4linux/liveGitOpsRancher>

- Efetuado fork:
<https://github.com/fernandomullerjr/liveGitOpsRancher>


- O Fleet transforma os códigos em recursos.

- No Painel do Rancher, clicar em "Continuous Delivery".
<https://34.136.135.215/dashboard/c/c-wr58t/fleet>
- Clicar em "Git Repos".
- No Github, copiar o endereço HTTPS do repositório:
<https://github.com/fernandomullerjr/liveGitOpsRancher.git>


- Clicar em "Git Repos".
Clicar em "Add repository"

name:
liverancher

Repository url:
https://github.com/fernandomullerjr/liveGitOpsRancher.git

branch name:
main

Paths
The root of the repo is used by default. To use one or more different directories, add them here. 
[OBSERVAÇÃO]
Colocar o nome da pasta que tem o projeto com os manifestos YAML.
No caso vai ser:
single-cluster

Deploy to:
selecionar o cluster "liverancher"

# 10 - Realizar Deploy do WordPress Single Cluster com Fleet.

- Clicar em "create"
- Acompanhar o deploy:
<https://34.136.135.215/dashboard/c/c-wr58t/fleet/fleet.cattle.io.gitrepo>



- Para obter o endereço ip do Cluster e poder acessar o Wordpress:
clicar no Cluster "liverancher"
http://35.226.187.216:32299/wp-admin/install.php



- Página acessível:
<http://35.226.187.216:32299/>
