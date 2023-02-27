### Treinando Docker

Este repositório foi criado para adicionar meus conhcimentos pôsto em prática<br>
com o Docker e alguns dos princiapais componentes da AWS (Amazon Web Services).

Aqui irei mostrar todo o processo e o passo a passo para criar instância, adicionar<br>
contêineres e gerenciá-los.

### Detalhes da instância AWS (Amazon Web Services)

Tipo de Instância | Tipo de chave | Cong. Rede | Armazenamento
---|---|---|---
T3 Small (Family) | .pem | 0.0.0.0/0 | 8GiB (gp2)

<p>Observação: A configuração de rede está com 0.0.0.0/0 para facilitar o acesso mas<br>
não é uma boa prática utilizar essa cong. de rede pois pode deixar a instância vulnerável<br>
estou utilizando porquê é um ambiente apenas para testes, nada de confidêncial está em risco<br>
a instância foi criada apenas para fins práticos.</p>

Após isso ainda tem um script para automatizar algumas instalações da instância<br>
para automatizar alguns passos e ir adiantando:

    #!/bin/bash
    # Use this for your user data (script from top to bottom)
    # install httpd (Linux 2 version)
    yum update -y
    yum install -y httpd
    systemctl start httpd
    systemctl enable httpd
    echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
    
