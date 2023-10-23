# sprint.uol
## Desafio 1
### Instalando um servidor Oracle Linux 8.x em uma virtual Box 

<b>Primeiro, faça o download do Virtual Box no site:
<b>https://www.virtualbox.org/wiki/Downloads <b><b>

<b><b>
<b>clique em próximo até finalizar.
<b><b>
<b>Baixe o Oracle Linux:<b><b>
https://edelivery.oracle.com<b><b>


Faça login ou crie uma nova conta
Busque por Oracle Linux
Escolha a versão 8.1<b><b>

Em plataformas e idiomas clique seta para baixo e selecione a arquitetura da sua plataforma e clique em continuar
Revise e assine os termos de adesão e clique na caixa de seleção para aceitar o contrato de licença e clique no botão continuar

Assim que terminar o download, acesse a VM para continuar a instalação, escolha a opção padrão “Testar mídia e instalar Oracle Linux” e logo será exibida uma janela de boas vindas, então selecione o idioma a ser usado e clique em continuar.
Configure o layout do teclado 
O idioma
O fuso horário que pode ser ajustado manualmente
Sempre clicando em concluído (Done) para voltar a tela de resumo da instalação
Escolha o software a ser usado 
Selecione o destino da instalação
Caso deseje ter um recurso mais avançado do kernel em relação a imagem, selecione o kdump 
Caso deseje pode configurar os recursos de rede ou deixar em configuração automática
Você pode adicionar uma política de segurança ou não de acordo com a necessidade do seu projeto
Clique no botão iniciar instalação
Enquanto a instalação é realizada, defina o seu usuário e senha root
Defina também se esse usuário terá privilégios de administrador no sistema usando o sudo
Após completar a instalação o sistema inicializará a sua instalação do Oracle Linux e é só colocar o seu usuário e senha.

Repita o processo para criar outra VM na opção server (Sem GUI)



## Desafio 2
### Configurando o IP fixo nos dois servidores 
Se vc chegou nesse ponto sem usuário e senha, pode criar agora:
    sudo user add nome-do-usuario
    sudo password senha-de-usuario
repita a senha-de-usuario

Vamos verificar o staus das interfaces de rede com o comando;
    nmcli device status
Podemos configurar o IP de dois modos, o modo temporário e o permanente, aqui vou lhe mostra como configurar de modo permanente editando o arquivo, dessa forma ele presiste diante de uma reinicialização.
Você pode acessar o arquivo onde estão as informações referente ao IP da sua máquina com o comando:
    sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
Dentro desse arquivo iremos alterar alguns itens e x será um número escolhido por você para definir a sua rede:

<b>BOOTPROTO= none
<b>IPADDR= 192.168.X.XX
<b>PREFIXO= 24
<b>GATEWAY= 192.168.x.x
<b>DNS1= 8.8.8.8
<b>DNS2= 8.8.4.4

Eu usei o DNS público do Google, mas você pode utilizar outros, procure sempre DNS confiáveis

Feito isso, irá salvar
Ctrl + O
e sair:
Ctrl + X


Reinicie o serviço de rede após editar o arquivo com o comando
    systemctl restart NetworkManager


Você também pode reiniciar apenas a interface
    ifdown enp0s3
    ifup enp0s3

Para testar a conexão, você pode ir no PowerShell e digitar o seguinte comando
    ssh <seu-nome-de-usuario-da-vm-destino>@<o-ip-da-vm-destino> Ex.:
    ssh melocouto@192.168.0.30

O sistema irá perguntar se deseja conectar.
Responda sim, coloque sua senha de usuário e a conexão é estabelecida
Faça o mesmo na outra VM
Para finalizar essa etapa vamos verificar se as duas VMs conversam usando o comando ping e o IP da outra VM em cada uma. 



### Criando um NFS do servidor 1 para o servidor 2. 
Vamos estabelecer que uma vm será o cliente e a outra o servidor, então no servidor, instale os pacotes de utilitários do NFS com o comando:
    dnf install -y nfs-utils
Com o pacote instalado, verá a seguinte msg:

<b><b>
Crie um diretório para os arquivos compartilhados:
    sudo mkdir / nfs-share
Crie também alguns arquivos de teste:
    sudo fallocate -l 10MB /nfs-share/file1
    sudo fallocate -l 10MB /nfs-share/file2
    echo "Estamos criando um arquivo de texto compartilhado." | sudo tee /nfs-share/shared-text.txt > /dev/null
Verifique os arquivos criados:
    ls -lh /nfs-share
    
    
<b><b>
Como é só um exercício, vamos alterar as opções de compartilhamnento, assim não precisaremos incluir várias opções na configuração ok?
Então vamos dar permissão total:
    sudo chmod -R 777 /nfs-share
Agora vomos definir com quem iremos compartilhar, que será a nossa vm cliente
    echo "/nfs-share <ip_vm_cliente>(rw)" | sudo tee -a /etc/exports > /dev/null
Agora vamos definir o tráfego NFS:
Vamos defini-lo como permanente e depois mostrar o tráfego permitido.

    sudo firewall-cmd --permanent --zone=public --add-service=nfs
    sudo firewall-cmd --reload
    sudo firewall-cmd --list-all

Ative o serciço NFS:
    sudo systemctl enable --now nfs-server
E mostre quais serviços estão disponíveis usando o serviço NFS
    showmount -e

Eu tive problemas com o firewall, ele não estava habilitado e aparecia a informação "Firewalld is not running",  então eu segui os comandos
<b>
    sudo yum install firewalld
    sudo systemctl start firewalld
    sudo systemctl enable firewalld
Você também pode conferir
    sudo systemctl status firewalld
Vamos iniciar o NFS
    sudo systemctl enable --now nfs-server
    showmount -e


## Desafio 3

### Instalando o Mariadb na VM01 


### Instalando o Wordpress na VM02


### Salvando os estáticos do wordpress








