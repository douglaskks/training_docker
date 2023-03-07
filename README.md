Este repositório foi criado para adicionar meus conhcimentos pôsto em prática<br>
com o Docker e alguns dos princiapais componentes da AWS (Amazon Web Services).<br>
<div style:"display= inline_block">
<a href="https://cdn-icons-png.flaticon.com/512/25/25657.png" target="_blank"><img height="200" width="200" src="https://cdn-icons-png.flaticon.com/512/25/25657.png" target="_blank"></a> <a href="https://img.icons8.com/color/256/amazon-web-services.png" target="_blank"><img height="200" width="200" src="https://img.icons8.com/color/256/amazon-web-services.png" target="_blank"></a>
</div><br>
Aqui irei mostrar todo o processo e o passo a passo para criar instância, adicionar<br>
contêineres e gerenciá-los.


### Detalhes da instância AWS (Amazon Web Services)


Tipo de Instância | Tipo de chave | Cong. Rede | Armazenamento
---|---|---|---
T3 Small (Family) | .pem | 0.0.0.0/0 | 8GiB (gp2)
<div>

### Portas da instância (entradas e saídas)

TCP | UDP | NFS | HTTP | SSH | HTTPS
---|---|---|---|---|---
8080 | 2049 | 2049 | 80 | 22 | 443
111 | 111 | 
443 | 
80 |


<a href="https://cdn-icons-png.flaticon.com/512/5610/5610989.png" target="_blank"><img height="20" width="20" src="https://cdn-icons-png.flaticon.com/512/5610/5610989.png" target="_blank"></a>  Observação: A configuração de rede está com 0.0.0.0/0 para facilitar o acesso mas<br>
não é uma boa prática utilizar essa cong. de rede pois pode deixar a instância vulnerável<br>
estou utilizando porquê é um ambiente apenas para testes, nada de confidêncial está em risco<br>
a instância foi criada apenas para fins práticos.
</div><br>

### Passo a passo para a execução do projeto

Script para automatizar algumas instalações e configurações da instância<br>


<h1><a href="https://cdn-icons-png.flaticon.com/512/8870/8870481.png" target="_blank"><img height="22" width="22" src="https://cdn-icons-png.flaticon.com/512/8870/8870481.png" target="_blank"></a>user_data.sh</h1>
    
    #!/bin/bash
    yum update -y
    yum install -y httpd
    yum install yum remove docker \
                      docker-client \
                      docker-client-latest \
                      docker-common \
                      docker-latest \
                      docker-latest-logrotate \
                      docker-logrotate \
                      docker-engine \
                      podman \
                      runc
    yum install -y yum-utils
    yum-config-manager \
        --add-repo \
        https://download.docker.com/linux/rhel/docker-ce.repo
    yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    yum install -y git

    curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o docker-compose
    mv docker-compose /usr/local/bin && sudo chmod +x /usr/local/bin/docker-compose
    ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

O passo a passo da configuração do Linux está no outro projeto feito<br>
para criar o NFS que foi feito anteriormente <a href="https://github.com/douglaskks/NFS-Linux---Verificador-Online">clique aqui</a> aqui iremos <br>
tratar apenas o passo a passo da AWS.

### 1° Passo ( EFS )

Inicialmente foi configurado o efs (elastic file system):<br>
    - Criar pasta do efs com o <i> mkdir efs-utils </i><br>
    - Montar o EFS no diretório criado acima: <i> sudo mount IP_OU_DNS_DO_NFS:/ /mnt/nfs </i><br>
    - Verificar de o efs está funcionando: <i> df -h </i><br>
    - Automatizar o inicio do efs no boot (opcional): <i> vim /etc/fstab </i><br>
    - Adicionar uma linha abaixo do que já estiver escrito: <i> IP_OU_DNS_DO_NFS:/ /mnt/nfs nfs defaults 0 0 </i><br>
    - Salvar arquivo: <i> ESC, :wq, ENTER </i><br>

### 2° Passo ( Docker Compose )


     version: '3'
     services:
        db:
           image: mysql:8.0.19
           volumes:
              - db_data:/var/lib/mysql
           restart: always
           environment:
              MYSQL_ROOT_PASSWORD: wordpress
              MYSQL_USER: wordpress
              MYSQL_PASSWORD: wordpress
              MYSQL_DATABASE: wordpress
        wordpress:
           image: wordpress:latest
           volumes:
              - /efs-utils/montagem/wordpress:/var/www/html
           environment:
              WORDPRESS_DB_HOST: db
              WORDPRESS_DB_NAME: wordpress
              WORDPRESS_DB_USER: wordpress
              WORDPRESS_DB_PASSWORD: wordpress
           ports:
              - "8080:80"
           restart: always
           depends_on:
              - db
     volumes:
       db_data:

Com o <b>docker-compose.yml</b> criado seguiremos com os comandos.<br>
    - Iniciar o docker antes de tudo: <i> systemctl start docker.service </i><br>
    - Verificar se o docker está ativo: <i> systemctl status docker.service </i><br>
    - Com o docker ativo, iremos iniciar o docker compose para criar as instâncias<br>
       do Wordpress e o mysql: <i> docker-compose up -d </i><br>
    - Verificar se os contêineres estão funcionando corretamente: <i> docker container ps </i><br>
    
    
### 3° Passo ALB (Application Load Balancer)

Inicialmente na página inicial da AWS (Amazon Web Service) vá no serviço de EC2 e escolha<br>
a opção de <b> target Group </b> ou <b> Grupos de Destino </b> pois iremos configurar primeiro<br>
o grupo de destino pra depois criarmos o <b> Load Balancer </b>

Dentro do serviço de <b>Target Group</b> siga os seguintes passos:<br>
      - <b>Create Target Group</b> ou <b>Criar Grupo de Destino</b><br>
      - Escolha a configuração básica: <i> Instance </i> ou <i> Instância </i><br>
      - Crie um nome do Target Group de sua preferência<br>
      - Escolha o protocolo e adicione a porta em que seu contêiner está<br>
        rodando os serviços (Wordpress e Mysql).<br>
      - Escolha a VPC criada para que o target group saiba em qual Availability Zone<br>
        irá trabalhar.<br>
      - Health Checks: <i> HTTP </i><br>
      - Pode dar um <b> Next</b> ou <b>Próximo</b><br>
      <b>- Após isso irá aparecr a tela de conclusão</b>
      - Coloque a porta configurada para o site ficar disponível que você<br>
      configurou no Docker Compose, no meu caso foi 8080 e clique em <b> Include as pending below </b><br>
      - Revise as configurações que seu Target group ficou.<br>
      - Caso estiver tudo certo pode clicar em <b>Criar Grupo de Destino</b> ou <b> Create Target Group</b><br>
      
ALB Agora:<br>
      - Após concluir as configurações do Target group vamos para a opção<br>
      Load Balancer no Serviço de EC2<br>
      - Escolha a opção de <b> Application Load Balancer </b> e clique em <b> Create </b><br>
      - Escolha um nome para seu Load balance.<br>
      - No Scheme você deixará a opção <b> internal-facing</b><br>
      - O tipo de endereço de IP será o <b> IPv4 </b><br>
      - No Networking Map você irá selecionar sua VPC que terá 2 subredes configuradas<br>
      para que as AZ possam mudar se algum der errado.<br>
      - Após isso escolha seu <b> Security groups </b> ou <b> Grupo de segurança </b><br>
      - E por último na opção de Listeners você irá escolher seu Target Group criado anteriormente.<br>
      - Revise suas configurações.<br>
      - E clique em <b>Create Load Balancer</b> ou <b> Criar Load Balancer </b><br>

<h1> Referências </h1>

<a align="center" href="https://docs.docker.com/engine/install/rhel/"> Documentação Docker </a><a align="center" href="https://cdn-icons-png.flaticon.com/512/5969/5969120.png" target="_blank"><img height="22" width="22" src="https://cdn-icons-png.flaticon.com/512/5969/5969120.png" target="_blank"></a>

<a align="center" href="https://help.hcltechsw.com/bigfix/10.0/mcm/MCM/Config/install_docker_ce_docker_compose_on_rhel_8.html"> HCLSoftware </a><a align="center" href="https://help.hcltechsw.com/bigfix/10.0/mcm/MCM/Config/install_docker_ce_docker_compose_on_rhel_8.html" target="_blank"><img height="22" width="22" src="https://cdn-icons-png.flaticon.com/512/5969/5969120.png" target="_blank"></a>
