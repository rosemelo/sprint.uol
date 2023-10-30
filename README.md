# sprint.uol
## Desafio 1
### Instalando um servidor Oracle Linux 8.x em uma virtual Box 

Primeiro, faça o download do Virtual Box no site:<b>


https://www.virtualbox.org/wiki/Downloads <b>


clique em próximo até finalizar.

Baixe o Oracle Linux:

https://edelivery.oracle.com


Faça login ou crie uma nova conta<b>
Busque por Oracle Linux<b>
Escolha a versão 8.1<b> 

Em plataformas e idiomas clique seta para baixo e selecione a arquitetura da sua plataforma e clique em continuar
Revise e assine os termos de adesão e clique na caixa de seleção para aceitar o contrato de licença e clique no botão continuar

Assim que terminar o download, acesse a VM para continuar a instalação,<b>
escolha a opção padrão “Testar mídia e instalar Oracle Linux” e logo será exibida uma janela<b> 
de boas vindas, então selecione o idioma a ser usado e clique em continuar.<b>
Configure o layout do teclado <b>
O idioma<b>
O fuso horário que pode ser ajustado manualmente<b>
Sempre clicando em concluído (Done) para voltar a tela de resumo da instalação<b>
Escolha o software a ser usado <b>
Selecione o destino da instalação<b>
Caso deseje ter um recurso mais avançado do kernel em relação a imagem, selecione o kdump <b>
Caso deseje pode configurar os recursos de rede ou deixar em configuração automática<b>
Você pode adicionar uma política de segurança ou não de acordo com a necessidade do seu projeto<b>
Clique no botão iniciar instalação<b>
Enquanto a instalação é realizada, defina o seu usuário e senha root
Defina também se esse usuário terá privilégios de administrador no sistema usando o sudo<b>
Após completar a instalação o sistema inicializará a sua instalação do Oracle Linux e é só colocar o seu usuário e senha.<b>


Repita o processo para criar outra VM na opção server (Sem GUI)<b>



## Desafio 2
### Configurando o IP fixo nos dois servidores 
Se vc chegou nesse ponto sem usuário e senha, pode criar agora:

    ```bash
    sudo user add nome-do-usuario
    ```
    
    ```bash
    sudo password senha-de-usuario
    ```

repita a senha-de-usuario<b>

Vamos verificar o staus das interfaces de rede com o comando:

    ```bash
    nmcli device status
    ```

    
Podemos configurar o IP de dois modos, o modo temporário e o permanente, <b>
aqui vou lhe mostra como configurar de modo permanente editando o arquivo, dessa forma ele presiste diante de uma reinicialização.<b>
Você pode acessar o arquivo onde estão as informações referente ao IP da sua máquina com o comando:

    ```bash
    sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
    ```
    
Dentro desse arquivo iremos alterar alguns itens e x será um número escolhido por você para definir a sua rede:

    ```bash
    BOOTPROTO= none
    IPADDR= 192.168.X.XX
    PREFIXO= 24
    GATEWAY= 192.168.x.x
    DNS1= 8.8.8.8
    DNS2= 8.8.4.4
    ```



Eu usei o DNS público do Google, mas você pode utilizar outros, procure sempre DNS confiáveis<b>

Feito isso, irá salvar<b>
Ctrl + O<b>
e sair:<b>
Ctrl + X<b>


Reinicie o serviço de rede após editar o arquivo com o comando:

    ```bash
    systemctl restart NetworkManager
    ```
    
Você também pode reiniciar apenas a interface:

    ```bash
    ifdown enp0s3
    ```

    ```bash
    ifup enp0s3
    ```
    
Para testar a conexão, você pode ir no PowerShell e digitar o seguinte comando:

    ```bash
    ssh usuario_da_vm_destino@ip_vm_destino
    ```

Ex.:
ssh melocouto@192.168.0.30<b>

O sistema irá perguntar se deseja conectar.<b>
Responda sim, coloque sua senha de usuário e a conexão é estabelecida<b>
Faça o mesmo na outra VM<b>
Para finalizar essa etapa vamos verificar se as duas VMs conversam usando o comando ping e o IP da outra VM em cada uma. <b>



### Criando um NFS do servidor 1 para o servidor 2. 
Vamos estabelecer que uma vm será o cliente e a outra o servidor, então no servidor, instale os pacotes de utilitários do NFS com o comando:

    ```bash
    dnf install -y nfs-utils
    ```
    
Crie um diretório para os arquivos compartilhados:

    ```bash
    sudo mkdir / nfs-share
    ```
    
Crie também alguns arquivos de teste:

    ```bash
    sudo fallocate -l 10MB /nfs-share/file1
    ```
    
    ```
    bash
    sudo fallocate -l 10MB /nfs-share/file2
    ```
    
    ```bash
    echo "Estamos criando um arquivo de texto compartilhado." | sudo tee /nfs-share/shared-text.txt > /dev/null
    ```
    
Verifique os arquivos criados:

    ```bash
    ls -lh /nfs-share
    ```
    

Como é só um exercício, vamos alterar as opções de compartilhamnento, assim não precisaremos incluir várias opções na configuração ok?<b>
Então vamos dar permissão total:<b>

    ```bash
    sudo chmod -R 777 /nfs-share
    ```
    
Agora vomos definir com quem iremos compartilhar, que será a nossa vm cliente:

    ```bash
    echo "/nfs-share <ip_vm_cliente>(rw)" | sudo tee -a /etc/exports > /dev/null
    ```
    
Agora vamos definir o tráfego NFS:<b>
Vamos defini-lo como permanente e depois mostrar o tráfego permitido:

    ```bash
    sudo firewall-cmd --permanent --zone=public --add-service=nfs
    sudo firewall-cmd --reload
    sudo firewall-cmd --list-all
    ```

Ative o serciço NFS:

    ```bash
    sudo systemctl enable --now nfs-server
    ```
    
E mostre quais serviços estão disponíveis usando o serviço NFS:

    ```bash
    showmount -e
    ```
    

Eu tive problemas com o firewall, ele não estava habilitado e aparecia a informação "Firewalld is not running",  então eu segui os comandos:

    ```bash
    sudo yum install firewalld
    sudo systemctl start firewalld
    sudo systemctl enable firewalld
    ```
    
Você também pode conferir:

    ```bash
    sudo systemctl status firewalld
    ```
    
Vamos iniciar o NFS:

    ```bash
    sudo systemctl enable --now nfs-server
    ```
    
    ```bash
    showmount -e
    ```
    
## Desafio 3

### Instalando o Mariadb na VM01 




### Instalando o Wordpress na VM02




### Salvando os estáticos do wordpress







