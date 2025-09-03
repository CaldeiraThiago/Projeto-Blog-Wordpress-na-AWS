# Blog Dinâmico de Três Camadas na AWS

## Visão Geral

Este projeto é um blog dinâmico, desenvolvido para demonstrar a criação e implantação de uma aplicação com arquitetura de três camadas na AWS. O foco foi em segurança, separação de responsabilidades e gerenciamento de recursos com Amazon VPC, Amazon EC2 e Amazon RDS. O projeto foi mantido dentro da Free Tier.

## Arquitetura
A aplicação é dividida em três camadas distintas, cada uma com um papel específico, para garantir maior segurança e escalabilidade.

**Camada de Apresentação e Lógica:** Uma instância Amazon EC2 hospeda a aplicação web (WordPress) e o servidor web (Apache). Ela está em uma sub-rede pública para ser acessível pela internet.

**Camada de Dados:** Um banco de dados Amazon RDS (MariaDB/MySQL) armazena todo o conteúdo do blog, como posts e usuários. Ele está em uma sub-rede privada e só pode ser acessado pelo EC2, garantindo que não fique exposto à internet.

**VPC (Virtual Private Cloud):** A VPC age como uma rede privada, isolando todos os recursos do projeto. Dentro dela, os Security Groups controlam o tráfego, permitindo a comunicação segura entre as camadas.

## Passo a Passo da Implementação
**1. Configuração da VPC e Segurança:** Detalhes sobre a criação da VPC com sub-redes e as regras dos Security Groups para a comunicação entre o EC2 e o RDS.

**2. Configuração do Banco de Dados:** Explicação da criação da instância Amazon RDS na sub-rede privada, selecionando o motor de banco de dados e configurando o usuário e senha.

**3. Configuração do Servidor Web:** Descrição do lançamento da instância t2.micro no Amazon EC2, instalação do software necessário (Apache/PHP) e a configuração do WordPress.

**4. Conectividade e Implantação:** Detalhes sobre como a aplicação foi conectada ao banco de dados e como o WordPress foi configurado.

Desafios e Soluções
Desafio: Como isolar o banco de dados do acesso público, mas ainda permitir que a aplicação web se conecte a ele?

Solução: Coloquei o Amazon RDS em uma sub-rede privada e criei um Security Group para ele, permitindo apenas conexões vindas do Security Group da instância do Amazon EC2.

Desafio: Como gerenciar o acesso SSH à instância EC2 de forma segura?

Solução: Utilizei um par de chaves SSH (.pem) para me conectar à instância, seguindo a melhor prática de não usar senhas para acesso administrativo.

Custos
Este projeto foi totalmente implementado utilizando os recursos da Free Tier da AWS, com custos estimados em zero dólares mensais.
