# Terraform-Aborgagem-Pratica

📚Conhecendo Terraform
Uma Breve explicação
O Terraform é uma ferramenta de Infraestrutura como Código (IaC), ou seja, através de código é possível provisionar e gerenciar infraestrutura em nuvem e local de forma segura e eficiente.
Segue uma estrutura de Provider e Resource, sendo o Provider as configurações necessárias para se conectar ao provedor e o Resourve as configurações feitas para provisionar a infraestrtura na nuvem escolhida

Comandos Terraform
Principais Comandos
Terraform init - Inicia o Terraform
Terraform validate - Faz a validação do código para determinar se está tudo correto
Terraform plan - Apresenta as alterações realizadas no código antes de aplicar
Terraform apply - Aplica as alterações realizadas no código Terraform
Terraform refresh - Faz com que o sistema entenda modificações realizadas no código
Terraform destroy - Deleta toda a infraestrutura cloud criada via código
Terraform Output - Exibe na tela informações definidas nos arquivos output
Terraform Show - Mostra detalhes do arquivo Tfstate(arquivo de estado atual do código terraform), que é o código principal da infraestrutura em nuvem
Arquivos Terraform
O Terraform tem também sua estrutura de arquivos. A utilização desses arquivos pode sim variar conforme a escolha do profissional, no entanto, os arquivos comunmente utilizados são:

main.tf - Como o prórpio nome diz, é o arquivo principal. É nele que são configurados os recursos que mais tarde construirão a infraestrtura cloud.
terraform.tfstate - Esse arquivo é um espelho exato do main.tf após executado. Ele presenta o arquivo de estado atual da infraestrutura cloud criada.
terraform.tfstate.backup - É o arquivo de backup do tfstate. Ou seja, caso haja uma alteração errônea no terraform.tfstate ou se perca o arquivo, basta utilizar o tfstate.backup para reestabelecer a infraestrutura.
Estes arquivos devem ser manipulados sempre com bastante cuidado, pois representam diretamente o código da infraestrtutura executada no provedor cloud. Perder esses arquivos compromete a operação da infraestrutura em caso de qualquer incidente gerada no ambiente de TI.

🕹Preparando o ambiente Terraform
Para começo de uso do terraform, vamos realizar a instalação do Visual Studio no computador. Recomendo instalar no seu sitema operacinal de escolha. Como estou usando o Ubuntu, sua instalação é muito simples.

1º - Instalando VS code
Comandos com permissão root:
apt update
apt install gpg -y
apt install wget -y 
apt install software-properties-common apt-transport-https wget -y
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | tee /etc/apt/trusted.gpg.d/microsoft.gpg >
echo "deb [arch=amd64] https://packages.microsoft.com/repos/code stable main" | tee /etc/apt/sources.list.d/vscode.list
apt update
apt install code -y
Comando no linux para rodar vs code como root
code --no-sandbox --user-data-dir "dirtorio que o vs ta"
Comandos sem permissão root:
sudo apt update
sudo apt install gpg -y
sudo apt install wget -y 
sudo apt install software-properties-common apt-transport-https wget -y
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/microsoft.gpg >
echo "deb [arch=amd64] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list
sudo apt update
sudo apt install code -y
💻Mão na Massa com Terraform
1º - Criando pasta para armazenamento de arquivos
No meu sistema operacional, a fim de manter a melhor organização, irei criar uma pasta para armazenar todos os arquivos de terraform em um só lugar. Para isso, criarei a pasta Projetos. Meu Print

2º - Indo para o VS-Code
No terminal, dentro da pasta 'projeto', abriremos o Vs code com o comando code . 

VS Code já aberto ta pasta 'projetos' 

3º - Criando o arquivo main
Para criarmos o arquivo main, basta gerarmos um novo arquivo no Vs code com o nome main.tf. No entanto, antes eu irei criar uma pasta chamada terraform dedicada para esse nosso primeiro projeto.

3.1 - Pasta Criada 

3.2 - Abrindo terminal e elevando user para root Meu Print Meu Print

3.3 - Arquivo criado a partir do comando touch main.tf Meu Print
Recomendo fazer desta forma, pois assim você conseguirá executar todos os comando do terraform sem a necessidade de a cada comando digitar sudo no início de cada comando.


4º - Difinindo as configurações principais do main.tf
Antes de começarmos a criar o recursos (resources) em si, primeiro precisamos criar as definições do provedor, de versão do terraform e estabelecer a comunicação com a nossa conta aws.

4.1 - Definição de provider e versao
Para definir o provedor e a versão, estruturamos da seguinte forma

terraform {
required_providers {
 aws = {
  source = "hashicorp/aws"
  version = "~>5.0"
  }
}
required_version = ">= 1.12.2"
}
Imagem exemplo
Meu Print

Explicando

terraform {} - Este é o bloco terraform e ele serve justamente para configurar o próprio Terraform antes de qualquer recurso ser declarado. Esse bloco define configurações globais que afetam todo o comportamento do Terraform
required_providers - É dentro dessa estrutura que eu informo ao meu terraform que provider eu vou utilizar (Aws, Azure, Gcp etc), sua origem (caminho de onde vai buscar seus pacotes e dependências, e a última versão lançada do pelo provedor, que contempla as últimas atualizações.
required_version - É o local onde defino qual a versão do terraform eu utilizarei. É recomendável sempre utilizar a última lançada para pegar todas as atualizações
Veja, todas essa definições ficam dentro do bloco de terraform porque são informações necessárias para que o terraform funcione adequadamente.

4.2 - Definição de usuário
Para que o terraform possa conversar com a console AWS, é necessário que seja criado um usuário (podendo ser uma Role ou um par de chaves) para que a infraestrutura seja provisionada via código. Sendo assim, você primeiro irá configurar um usuário no IAM da sua console e após ele estar criado, setar ele no terraform.

Usuario criado no IAM
Meu Print

Para que as ações realizadas no terraform sejam executadas com sucesso, é necessário criar uma policy manualmente no user IAM e adicionar nela todas as informações necessárias. Meu Print

{
   "Version": "2012-10-17",
   "Statement": [
   	{
   		"Effect": "Allow",
   		"Action": [
   			"iam:GetRole",
   			"iam:ListRolePolicies",
   			"iam:GetRolePolicy",
   			"iam:ListAttachedRolePolicies",
   			"iam:GetPolicy",
   			"iam:GetPolicyVersion",
   			"iam:GetInstanceProfile",
   			"iam:PassRole",
   			"iam:RemoveRoleFromInstanceProfile",
   			"iam:DeleteInstanceProfile",
   			"iam:ListInstanceProfilesForRole",
   			"iam:DetachRolePolicy",
   			"iam:DeleteRole"
   		],
   		"Resource": [
   			"arn:aws:iam::'Id da sua conta':role/Role-ssm",
   			"arn:aws:iam::'Id da sua conta':instance-profile/Role-ssm",
   			"arn:aws:iam::'Id da sua conta':role/Role-ssm"
   		]
   	}
   ]
}
Definição de usário no código terraform
Meu Print

provider "aws" {
profile = "jm-tf"
region = "us-east-1"
}
Observação: A região é a mesma em que seu ambiente está executando. Eu coloquei us-east-1, por ser a região em que minha infra esta.

Após gerado o par de chaves Key e configurado o usuário no bloco terraform, é necessário adicionar essas informações no arquivo de configuração do AWS cli.

4.3 - Instalando o AWS cli
Usar o comando:

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
4.3.1 - Adicionando credenciais no arquivo de configuração da AWS
Antes de efetivamente adicionar as credenciais no arquivo de configuração, é necessário ver se já nao há chaves configuradas. para isso, utilizamos esses dois comandos:

nano ~/.aws/configure

nano ~/.aws/credentials

4.3.2 - Limpeza de valores definidos
Para realizar a limpeza de valores ja definidos nesses arquivos de configuração, utilizamos os comandos:

rm -f  ~/.aws/config 

rm -f ~/.aws/credentials

4.3.3 - Definindo os valores nos arquivos de configuração
Para definir os valores gerados pelo user do IAM nos arquivos de configuração, utilizamos o seguinte comando:

aws configure --profile "seu usuario IAM" Meu Print

🚀Lançando Recursos na AWS
1º - Lançando a EC2
Os resources - que são os recursos da AWS - é o que provisiona a infraestrtura da console aws. Para utilizarmos, usamos a seguinte estrtutura resource "tipo-de-recurso" "nome-recurso" {}

Como exemplo, EC2 lançada. Após o script pronto, iniciado o terraform (terraform init) e aplicado o código (terraform apply) Meu Print

EC2 lançada na console AWS
Meu Print

resource "aws_instance" "Minha-EC2" {
intance_type = "t3.micro"
ami = "ami-052064a798f08f0d3"
subnet_id = "subnet-0c1f2ca01ca4b66d7" #Subnet da VPC
security_groups = ["sg-0268d6a0a7c8ebe9e"] #Security Group atribuído a EC2
}
Uma observação importante é que, exceto ao utilizar uma VPC Default (criada pela própria AWS), ao lançar uma EC2 é necessário definir uma Subnet_id e um Security_Group para que a EC2 seja lançada com sucesso. E aqui é utilizado apenas o Subnet_ID, não sendo necessária a declaraçãode uma VPC_ID.

2º - Criando Security Group
O security group é bem simples de lançar, bastando definir apenas duas configurações, a description e a name.
. Meu Print

resource "aws_security_group" "SG-custom-2" {
 description = "Meu SG customizado"
 name = "SG-custom-2"
}
observação: Quando você não define uma VPC, esse security group é automaticamente associado a uma VPC default

3º - Adicionando a uma VPC
Veja que neste exemplo eu adicionei uma VPC manualmente (uma VPC customizada criada por mim) e o terraform, ao executar um terraform apply, irá realizar um replaced. Ou seja, irá derrubar o que está rodando para aplicar a nova alteração. Essa condição é algo que você tem que ter ciência quando estiver atuando em um ambiente produtivo.
Meu Print

VPC alterada
Meu Print

resource "aws_security_group" "SG-custom-2" {
 description = "Meu SG customizado"
 name = "SG-custom-2"
 vpc_id = "vpc-0efcc7cbfc8c0040c"
}
4 º - Criando regras de entrada (inbound) e saida (outbound)
É justamente em Inbound e Outbound que estabelecemos a comunicação entre os variados tipos de serviços fornecidos pela AWS. No terraform, o inboud é referenciado como ingress e o outbound por egress. e dentro do bloco de cada um, adicionamos as configurações.
Meu Print

 resource "aws_security_group" "SG-custom-2" {
  description = "Meu SG customizado"
  name = "SG-custom-2"
  vpc_id = "vpc-0efcc7cbfc8c0040c"

  ingress {
  from_port = "80"
  to_port = "80"
  protocol = "tcp"
  cid_blocks = ["0.0.0.0/0"]
  description = "entrada porta 80" 
    }

  egress {
  from_port = "0" # De todas as portas
  to_port = "0" # Para todas as portas
  protocol = "-1" # Determina a saída para toda as portas
  cidr_blocks = ["0.0.0.0/0"]
  description = "saida para o mundo"
  
   }
 }
para atribuir um nome ao security group, utilizamos o bloco tags.

Definindo Tag no código
Meu Print

Tag Name
Meu Print

5º Criando a VPC, Subnet e Internet Gateway
Para criar uma rede e suas depedências (subnet, route table, Internet Gateway etc), é necessária a criação de diversos blocos de resources. Abaixo, segue contrução total da rede.

5.1 - Criação VPC
Para criar a VPC, utilizamos o resouce "aws_vpc" "Identificador_name" { cidr_block = "ip_rede"}
Meu Print Meu Print

resource "aws_vpc" "teste" {
 cidr_block = "10.0.0.0/24"
}
O Cidr_block é um identificador de endereço de rede e também determina a quantidade de endereços deponíveis de hosts. Numa rede /24, temos um total de 256 endereços disponíveis para hosts.

5.2 - Criação subnet
Vou criar aqui apenas uma subnet e dini-la para rodar na avaiability_zone a (us-east-1a).
Meu Print Meu Print

resource "aws_subnet" "Sub_a" {
 vpc_id = aws_vpc.teste.id
cidr_block = "10.0.0.64/26"
avaibility_zone = "us-east-1a"

tags = {
 name = "Sub-a"
 }
}

5.3 - Criação Internet Gateway
O internet Gateway (ig) é o recurso que permite a rede interna ter acesso a internet (rede externa).
Meu Print

Meu Print

resource "aws_internet_gateway" "my-ig" {
 vpc_id = aws_vpc.teste.id

  tags = {
  name = "my-ig"
}
}
5.4 - Criação Route Table
A Route Table determina para onde o tráfego de rede é direcionado, atuando como um controlador de tráfego da VPC. É nela que setamos o internet Gateway.

Meu Print

Meu Print

resource "aws_route_table" "my-rt" {
 vpc_id = aws_vpc.teste.id

route {
 cidr_block = "0.0.0.0/0"
 gateway_id = aws_internet_gateway.my-ig.id
  }
 }
5.5 - Associando Route Table a Subnet
Mesmo criando os resources (subnet, internet gateway e route table, é necessário associá-los através do bloco aws_route_table_association Meu Print Meu Print

resource "aws_route_table_association" "associate" {
 subnet_id = aws_subnet.Sub_a.id
 route_table_id = aws_route_table.my-rt.id
}
