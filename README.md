<h1>arquitetura_aws_docker</h1>
Este repositório contém a segunda atividade avaliativa do programa de estágio da Compass.UOL (PB 20/03/2023). A execução da atividade proposta é descrita na documentação abaixo e envolveu conhecimentos em infraestrutura da Amazon Web Services (AWS), Linux e Docker.<br><br>

<h2>Tabela de conteúdos</h2>
<ul>
  <li><a href="#req_exercicio">Requisitos do exercício</a><br>
  <li><a href="#exe_exercicio">Executando os requisitos do exercício</a><br>
  <ul>
    <li><a href="#ec2_web">Criando uma instância EC2 pela console web da AWS</a><br>
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

<div id="ec2_web"><h4>Criando uma instância EC2 pela console web da AWS:</h4><div>
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
