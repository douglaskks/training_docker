### Treinando Docker

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
<a href="https://cdn-icons-png.flaticon.com/512/5610/5610989.png" target="_blank"><img height="20" width="20" src="https://cdn-icons-png.flaticon.com/512/5610/5610989.png" target="_blank"></a>  Observação: A configuração de rede está com 0.0.0.0/0 para facilitar o acesso mas<br>
não é uma boa prática utilizar essa cong. de rede pois pode deixar a instância vulnerável<br>
estou utilizando porquê é um ambiente apenas para testes, nada de confidêncial está em risco<br>
a instância foi criada apenas para fins práticos.
</div><br>

Após isso ainda tem um script para automatizar algumas instalações da instância<br>
para automatizar alguns passos e ir adiantando:

<h1><a href="https://cdn-icons-png.flaticon.com/512/8870/8870481.png" target="_blank"><img height="22" width="22" src="https://cdn-icons-png.flaticon.com/512/8870/8870481.png" target="_blank"></a>  user_data.sh</h1>

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
    


<h1> Referências </h1>

<a align="center" href="https://docs.docker.com/engine/install/rhel/"> Documentação Docker </a><a align="center" href="https://cdn-icons-png.flaticon.com/512/5969/5969120.png" target="_blank"><img height="22" width="22" src="https://cdn-icons-png.flaticon.com/512/5969/5969120.png" target="_blank"></a>
