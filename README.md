<h1>arquitetura_aws_docker</h1>
Este repositório contém a segunda atividade avaliativa do programa de estágio da Compass.UOL (PB 20/03/2023). A execução da atividade proposta é descrita na documentação abaixo e envolveu conhecimentos em infraestrutura da Amazon Web Services (AWS), Linux e Docker.<br><br>

<h2>Tabela de conteúdos</h2>
<ul>
  <li><a href="#req_exercicio">Requisitos do exercício</a><br>
  <li><a href="#exe_exercicio">Executando os requisitos do exercício</a><br>
  <ul>
    <li><a href="#ec2_web">Configurando uma instância EC2</a><br>
    <li><a href="#portas">Configurando o grupo de segurança da instância EC2</a><br>
    <li><a href="#nfs">Configurando um Network File System (NFS)</a><br>
    <li><a href="#rds">Configurando um Relational Database Service (RDS)</a><br>
    <li><a href="#ami">Configurando a Amazon Image (AMI)</a><br>
  </ul>
</ul><br>

<div id="req_exercicio"><h2>Requisitos do exercício</h2><div>
<ul>
  <li>Instalar e configurar o Docker ou Containerd no host EC2 - Ponto adicional para o trabalho utilizar a instalação via script de start instance (user_data.sh);<br>
  <li>Efetuar deploy de uma aplicação Wordpress com container de aplicação RDS database MySQL;<br>
  <li>Configurar a utilização do serviço EFS AWS para estáticos do container de aplicação Wordpress;<br>
  <li>Configurar o serviço de Load Balancer AWS para a aplicação Wordpress.<br> 
</ul>

<div id="exe_exercicio"><h2>Executando os requisitos do exercício</h2><div>

<div id="ec2_web"><h4>Configurando uma instância EC2:</h4><div>
Este processo refere-se à criação de uma instância EC2 com o sistema operacional Amazon Linux 2 (família t3.small e 16 GB de armazenamento SSD).<br>
<ol>
  <li>No console web da AWS, através do menu 'Serviços', no canto superior esquerdo, acesse o serviço de 'EC2'. O termo 'EC2' também pode ser buscado através da barra de pesquisa, no topo da página.<br>
  <li>Na coluna esquerda, na sessão 'Instâncias', clique em 'Instâncias'.<br>
  <li>Para criar uma nova instância, clique no botão 'Executar instâncias' no topo da página.<br>
  <li>Em 'Nome e tags', adicione um nome para a instância e tags (opcional).<br>
  <li>Na seleção das imagens da aplicação, selecione o grupo 'Amazon Linux', especificamente 'Amazon Linux 2 Kernel 5.10 AMI 2.0.20230504.1 x86_64 HVM gp2'. O ID dessa AMI (Amazon Image) deve ser: ami-06a0cd9728546d178.<br>
  <li>No 'Tipo de instância', selecione 't3.small'.<br>
  <li>Na sessão 'Par de chaves (login)', gere um novo par de chaves para acessar essa instância, ou selecione uma chave existente.<br>
  <lil>Para gerar um novo par de chaves, clique no botão 'Criar novo par de chaves'. Atribua um nome, selecione o tipo, o formato do arquivo (.pem para OpenSSH e .ppk para uso com Putty) e uma tag (opcional). Após finalizar a operação, salve o arquivo baixado em local seguro.<br>
  <li>Na seção 'Configurações de rede', marque a opção 'Criar grupo de segurança', ou selecione um grupo já existente. Configurações específicas de rede para esse grupo serão feitas no requisito '<a href="#portas">Configurando o grupo de segurança da instância EC2</a>'.<br>
  <li>Na sessão 'Configurar armazenamento', selecione 16GB, tipo gp2 de volume raiz.<br>
  <li>Em ‘Detalhes avançados’, na sessão ‘Dados do usuário’, preencha o campo com o conteúdo do script (user_data.sh) contido nesse repositório.<br>
  <li>Em 'Resumo', verifique as configurações selecionadas, certificando-se que o 'Número de instâncias' seja '1'. Clique no botão 'Executar instância'.<br>
</ol>

<div id="portas"><h4>Configurando o grupo de segurança da instância EC2:</h4><div>
Este processo refere-se à configuração de regras de entrada no grupo de segurança associado à instância, neste caso, realizado através do console web.<br>
<ol>
  <li>No console web da AWS, através do menu 'Serviços', no canto superior esquerdo, acesse o serviço de 'EC2'. O termo 'EC2' também pode ser buscado através da barra de pesquisa, no topo da página.<br>
  <li>Na coluna esquerda, na seção 'Rede e segurança', clique em 'Security groups'.<br>
  <li>Selecione o grupo de segurança criado anteriormente, clique no botão 'Ações' e 'Editar regras de entrada'.<br>
  <li>Clique em 'Adicionar regra' ao inserir os dados de cada linha da tabela abaixo.<br><br>
  <table>
    <thead>
      <tr>
        <th>Tipo</th>
        <th>Intervalo de portas</th>
        <th>Tipo de origem</th>
    </thead>
    <tbody>
      <tr>
        <td>SSH</td>
        <td>22</td>
        <td>Qualquer local-IPv4</td>
      </tr>
      <tr>
        <td>MYSQL/Aurora</td>
        <td>3306</td>
        <td>Qualquer local-IPv4</td>
      </tr>
      <tr>
        <td>NFS</td>
        <td>2049</td>
        <td>Qualquer local-IPv4</td>
      </tr>
      <tr>
        <td>HTTP</td>
        <td>80</td>
        <td>Qualquer local-IPv4</td>
      </tr>
      <tr>
        <td>HTTPS</td>
        <td>443</td>
        <td>Qualquer local-IPv4</td>
      </tr>
    </tbody>
    </table>
  <li>Após inseridas as regras de entrada, clique em 'Salvar regras'.<br>
</ol>

<div id="nfs"><h4>Configurando um Network File System (NFS):</h4><div>
Este processo refere-se à criação de um NFS na instância EC2.<br>
<ol>
  <li>No console web da AWS, através do menu 'Serviços', no canto superior esquerdo, acesse o serviço de 'EFS' (Elastic File System). O termo 'EFS' também pode ser buscado através da barra de pesquisa, no topo da página.<br>
  <li>Na coluna esquerda, clique em 'Sistemas de arquivos'. Depois, em ‘Criar sistema de arquivos’, no topo da página.<br>
  <li>Atribua um nome (opcional), selecione a Virtual Private Cloud (VPC) e clique em ‘Criar’.<br>
  <li>Nas informações do EFS criado, clique na aba ‘Rede’, logo abaixo da seção ‘Geral’. Clique em ‘Gerenciar’.<br>
  <li>Na seção ‘Destinos de montagem’, troque o grupo de segurança para o mesmo utilizado pela instância, para todas as zonas de disponibilidade listadas. Clique em ‘Salvar’.<br> 
  <li>Ainda na página de informações do NFS criado, clique em ‘Anexar’.<br>
  <li>Com a opção ‘Montar via DNS’ selecionada, copie o comando que aparece na seção ‘Usando o cliente do NFS:’. Um exemplo desse comando é: <code>sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0cf1d041dea5dc917.efs.us-east-1.amazonaws.com:/ efs</code><br>
  <li>No terminal da instância, crie um diretório que vai ser montado para receber os arquivos compartilhados. Para isso, use o comando <code>mkdir /mnt/efs</code> e depois <code>mkdir /mnt/efs/wordpress</code>. Neste exemplo, o diretório a ser montado é <code>/wordpress</code> no caminho <code>/mnt/efs/</code><br>
  <li>Execute o comando <code>sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0cf1d041dea5dc917.efs.us-east-1.amazonaws.com:/ /mnt/efs/wordpress</code>.<br>
  <li>Executando o comando <code>df -h</code>, deve-se ver o diretório montado corretamente. Aqui está um exemplo:<br><br>
<table>
  <thead>
    <tr>
      <th>Filesystem</th>
      <th>Size</th>
      <th>Used</th>
      <th>Avail</th>
      <th>Use%</th>
      <th>Mounted on</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>fs-0cf1d041dea5dc917.efs.us-east-1.amazonaws.com:/</td>
      <td>8.0E</td>
      <td>0</td>
      <td>8.0E</td>
      <td>0%</td>
      <td>/mnt/efs/wordpress</td>
    </tr>
  </tbody>
</table>
  <li>Para configurar a montagem automática do diretório quando a máquina é iniciada, execute o comando <code>sudo vim /etc/fstab</code> e adicione a linha <code>fs-0cf1d041dea5dc917.efs.us-east-1.amazonaws.com:/ /mnt/efs/wordpress nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport 0 0</code>.<br>
</ol>

<div id="rds"><h4>Configurando um Relational Database Service (RDS):</h4><div>
Este processo refere-se à criação de um RDS na console web da AWS.<br>
<ol>
  <li>No console web da AWS, através do menu 'Serviços', no canto superior esquerdo, acesse o serviço de 'RDS' (Relational Database Service). O termo 'RDS' também pode ser buscado através da barra de pesquisa, no topo da página.<br>
  <li>Na coluna esquerda, clique em 'Bancos de dados'. Depois, em ‘Criar banco de dados’, no topo da página.<br>
  <li>No método de ‘Criação padrão’, selecione MySQL em ‘Opções do mecanismo’. Em ‘Modelos’, selecione o nível gratuito.<br>
  <li>Na sessão ‘Configurações’, em ‘Configurações de credenciais’, adicione nome de usuário e senha.<br>
  <li>Ao final, clique em ‘Criar banco de dados’.<br>
</ol>

<div id="ami"><h4>Configurando a Amazon Image (AMI):</h4><div>
Este processo refere-se à criação de uma imagem a partir da instância executada anteriormente.
<ol>
  <li>No console web da AWS, através do menu 'Serviços', no canto superior esquerdo, acesse o serviço de 'EC2'. O termo 'EC2' também pode ser buscado através da barra de pesquisa, no topo da página.<br>
  <li>Na coluna esquerda, na sessão 'Instâncias', clique em 'Instâncias'.<br>
  <li>Selecione a instância que deseja criar a imagem, clique no menu ‘Ações’, ‘Imagem e modelos’ e ‘Criar imagem’.<br>
  <li>Insira um nome e clique em ‘Criar imagem’.<br>
</ol>
