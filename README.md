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

    curl -L "https://github.com/docker/compose/releases/download/latest/docker-compose-$(uname -s)-$(uname -m)" -o docker-compose
    mv docker-compose /usr/local/bin && sudo chmod +x /usr/local/bin/docker-compose
    ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

O passo a passo da configuração do Linux está no outro projeto feito<br>
para criar o NFS que foi feito anteriormente <a href="https://github.com/douglaskks/NFS-Linux---Verificador-Online">clique aqui</a> aqui iremos <br>
tratar apena so passo a passo da AWS.

### 1° Passo

Inicialmente foi configurado o efs (elastic file system):
    - Criar pasta do efs com o <i> mkdir efs-utils </i>
    - Montar o EFS no diretório criado acima: <i> sudo mount IP_OU_DNS_DO_NFS:/ /mnt/nfs </i>
    - Verificar de o efs está funcionando: <i> df -h </i>
    - Automatizar o inicio do efs no boot (opcional): <i> vim /etc/fstab </i>
    - Adicionar uma linha abaixo do que já estiver escrito: <i> IP_OU_DNS_DO_NFS:/ /mnt/nfs nfs defaults 0 0 </i>
    - Salvar arquivo: <i> ESC, :wq, ENTER </i>

<h1> Referências </h1>

<a align="center" href="https://docs.docker.com/engine/install/rhel/"> Documentação Docker </a><a align="center" href="https://cdn-icons-png.flaticon.com/512/5969/5969120.png" target="_blank"><img height="22" width="22" src="https://cdn-icons-png.flaticon.com/512/5969/5969120.png" target="_blank"></a>

<a align="center" href="https://help.hcltechsw.com/bigfix/10.0/mcm/MCM/Config/install_docker_ce_docker_compose_on_rhel_8.html"> HCLSoftware </a><a align="center" href="https://help.hcltechsw.com/bigfix/10.0/mcm/MCM/Config/install_docker_ce_docker_compose_on_rhel_8.html" target="_blank"><img height="22" width="22" src="https://cdn-icons-png.flaticon.com/512/5969/5969120.png" target="_blank"></a>
