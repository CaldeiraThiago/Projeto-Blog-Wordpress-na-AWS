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

**Criação da VPC**: Feito a criação da vpc-projeto-blog para isolar o projeto dentro da assinatura da AWS:

<img width="1646" height="352" alt="image" src="https://github.com/user-attachments/assets/6ae99015-22cf-4182-b7b3-b798dd2cdea3" /> <br>

**Criação sub-rede publica**:

<img width="1637" height="526" alt="image" src="https://github.com/user-attachments/assets/7f64e2f7-62d1-4412-aae8-b9224a87e435" /> <br>

**Criação sub-rede privada**:

<img width="1649" height="519" alt="image" src="https://github.com/user-attachments/assets/516b0ff4-39b3-4054-bfc9-f49c68ba2acf" /> <br>

**Criação do Internet Gateway**: Criei a ProjetoBlog-IG e anexei a vpc-projeto-blog.

<img width="1649" height="335" alt="image" src="https://github.com/user-attachments/assets/cac9adcb-f760-402f-a712-5d06c3c4d350" />

**Criação do Grupo de Segurança Público:** Criado o Grupo de Seguranca ServidorWeb-SG que será voltado para Servidores Web. 
- **Regra de Entrada**: Criado a regra de entrada SSH que libera apenas IPs da minha rede. Criado uma regra HTTP para a origem 0.0.0.0/0.

<img width="1646" height="502" alt="image" src="https://github.com/user-attachments/assets/4258f538-f0df-4aeb-836b-ece8a4d3f8a8" /> <br>

**Criação do Grupo de Segurança Privado:** Criado o Grupo de Seguranca Database-SG voltado para instancias de banco de dados.
- **Regra de Entrada**: Criado a regra de entrada do tipo MYSQL/Aurora, para que seja acessado apenas por instâncias que estão no Grupo de Seguranca ServidorWeb-SG.

<img width="1644" height="500" alt="image" src="https://github.com/user-attachments/assets/8bfe1bf1-4c6a-4db4-a375-f45f263195be" /> <br><br>


**2. Configuração do Banco de Dados:** Explicação da criação da instância Mysql na sub-rede privada, selecionando o motor de banco de dados e configurando o usuário e senha.

<img width="1602" height="758" alt="image" src="https://github.com/user-attachments/assets/54cc852c-ede8-413a-99e2-a7728ea8bfb0" /><br>

- **Classe da instancia:** db.t4g.micro
- **Imagem:** MySQL Community
- **Região:** us-east-1a
- **VPC:** vpc-projeto-blog (vpc-0ad9a90be615abfd4)
- **Grupo de sub-redes:** default-vpc-0ad9a90be615abfd4
- **Grupos de segurança da VPC:** Database-SG (sg-0d7f1b8321707b5cb)
- **Porta:** 3306 <br><br>

**3. Configuração do Servidor Web:** Descrição do lançamento da instância t2.micro no Amazon EC2, instalação do software necessário (Apache/PHP) e a configuração do WordPress.

**ServerWeb-Blog:** Criado o servidor web para execução da aplicação Wordpress:
- **VPC:** vpc-projeto-blog
- **Grupos de segurança:** ServidorWeb-SG
- **Sub-rede:** ProjetoBlog-SubredePublica
- **Instancia:** amazon/al2023-ami-2023.8.20250818.0-kernel-6.1-x86_64

<img width="1658" height="760" alt="image" src="https://github.com/user-attachments/assets/414aab90-0c6a-4ae8-888f-4ffc0c1dcba2" /> <br>

## Instalação do Software

- **Conectando a instãncia:** Primeiro me conectei a instância via protocolo SSH com o certificado PEM gerado para o ServerWeb:

<img width="859" height="332" alt="image" src="https://github.com/user-attachments/assets/d92af13b-608d-42b5-892c-52c88644bf1c" /> <br>

- **Instalando o Servidor Web (Apache) e o PHP:**

<img width="840" height="179" alt="image" src="https://github.com/user-attachments/assets/12145fe0-91e2-40b3-8c12-348a5394a90f" /><br>

<img width="787" height="476" alt="image" src="https://github.com/user-attachments/assets/31ea9cee-24d2-4484-bb46-09507372b369" /> <br>

## Baixar e Configurar o WordPress

- Navegue até o diretório /var/www/html/;
- wget https://wordpress.org/latest.tar.gz
- tar -xzf latest.tar.gz
- sudo mv wordpress/* .
- sudo chown -R apache:apache /var/www/html/

## Conexão com o Banco de Dados




**4. Conectividade e Implantação:** Detalhes sobre como a aplicação foi conectada ao banco de dados e como o WordPress foi configurado.

Desafios e Soluções
Desafio: Como isolar o banco de dados do acesso público, mas ainda permitir que a aplicação web se conecte a ele?

Solução: Coloquei o Amazon RDS em uma sub-rede privada e criei um Security Group para ele, permitindo apenas conexões vindas do Security Group da instância do Amazon EC2.

Desafio: Como gerenciar o acesso SSH à instância EC2 de forma segura?

Solução: Utilizei um par de chaves SSH (.pem) para me conectar à instância, seguindo a melhor prática de não usar senhas para acesso administrativo.

Custos
Este projeto foi totalmente implementado utilizando os recursos da Free Tier da AWS, com custos estimados em zero dólares mensais.
