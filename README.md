# Instalando Arch Linux e aprendendo sobre Linux no Processo
==========

![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/ArchLinux.png)

Requisitos
==========
 * Conhecimento Básico em Shell
 * Particionamento de Disco
 * Gravação de ISO em CD ou Pen drive
 * Conexão com a Internet

1 - Obtendo a ISO de instalação do Arch Linux
==========
Você pode obter a ISO de instalação do Arch na [pagina de download] (https://www.archlinux.org/download/). Como trata-se de uma distribuição Rolling Release, é gerada uma ISO atualizada mensalmente. Após obter a instalação, grave a ISO em um CD ou Pen Drive e inicie a instalação.

![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/1.1.png)

Após iniciar a instalação, verifique se você tem conexão com a internet (utilizarei conexão cabeada durante o procedimento). Teste com o seguinte comando:
>
	ping 8.8.8.8 -c3

Caso você ainda não tenha conexão com a internet ative o dhcpcd com o comando:
>
	systemctl start dhcpcd

Caso deseje conectar-se via Wireless utilize o comando `wifi-menu` para configurar a sua conexão.
>
	wifi-menu
 
2 - Configurando as partições
==========
Existem várias formas e ferramentas para se particionar o disco. Iremos utilizar a ferramenta `Cfdisk` para criar as partições, e irei abordar a forma de particionamento para os dois tipos de tabelas de partições (GPT e MBR).

**Tenha cuidado nessa parte!**

***Em seguida particione o seu disco de acordo com o tipo que você utilizar, GPT ou MBR (estão separadas por partes, 2.1 e 2.2).***


Inicie o Cfdisk:
>
	cfdisk
A interface de particionamento é essa:
![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/2.2.png)


### 2.1 MBR - BIOS

Será criada a seguinte partição:
>
	/dev/sda1	como /boot		sera bootavel, tamanho 512M
	/dev/sda2	como /			com o tamanho que desejar	

No `cfdisk` deverá ficar da seguinte forma:
![ArchLinux2](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/2.3.png)

Se tudo estiver correto grave as alterações no disco.

Agora iremos formatar e montar a partição criada.

Execute os seguintes comandos:
>
	mkfs.ext4 /dev/sda1  (A partição informada será formatada em ext4)
	mkfs.ext4 /dev/sda2  (A partição informada será formatada em ext4)	
	mount /dev/sda2 /mnt (Será montada a partição em /mnt)
	mkdir /mnt/boot (Será criada a pasta boot dentro de /mnt)
	mkdir /mnt/home (Será criada a home dentro de /mnt)
	mount /dev/sda1 /mnt/boot (Monta a partição de boot)

### 2.2 GPT - UEFI

Serão criadas as seguintes partições:
>
	/dev/sda1	como /boot/efi		com o tamanho de 512M
	/dev/sda2	como /			com o tamanho que desejar

No `cfdisk` devera ficar da seguinte forma:
![ArchLinux2](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/2.4.png)

Se tudo estiver correto grave as alterações no disco.

Agora iremos formatar e montar as partições criadas.

Execute os seguintes comandos:
>
	mkfs.vfat -F32 /dev/sda1  (Formata e prepara a partição de boot)
	mkfs.ext4 /dev/sda2 (A partição informada será formatada em ext4)
	mount /dev/sda2 /mnt (Será montada a partição sda2 em /mnt)
	mkdir -p /mnt/boot/efi (Será criada a pasta boot/efi dentro de /mnt)
	mkdir /mnt/home (Será criada a pasta home dentro de /mnt)
	mount /dev/sda1 /mnt/boot/efi (Monta a partição de boot)

3 - Instalando o Sistema Base do Arch Linux
==========
Iremos agora instalar a base do sistema. Caso sua internet for lenta sugiro um café! =D

Execute o seguinte comando:
>
	pacstrap /mnt base base-devel

O Download ira começar...

![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/3.1.png)

Aguarde o processo ser finalizado...

![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/3.2.png)

Em seguida devemos gerar o nosso fstab que significa File System Table, que fica localizado em /etc/fstab. Ele define como as partições do disco e outros dispositivos serão montados. 

Iremos utilizar uma ferramenta do sistema para gerar o fstab automaticamente.

Para gerá-lo execute:
>
	genfstab /mnt > /mnt/etc/fstab

Caso desejar você pode visualizar o arquivo ou editá-lo com:
>
	cat /mnt/etc/fstab
	nano /mnt/etc/fstab

Arquivo gerado pelo genfstab:
![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/3.3.png)

4 - Configurando o Sistema
==========
Após termos instalado o sistema e gerado o fstab, agora devemos realizar algumas configurações básicas.
Para isso devemos acessar o sistema, com o seguinte comando:
>
	arch-chroot /mnt

Após executar o comando deverá ficar assim:
![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/4.1.png)

Agora devemos configurar o locales, para definir o nosso idioma e layout do teclado.
Abra o arquivo /etc/locale.gen e remova o # da frente de pt_BR.UTF-8 UTF-8, ou referente ao idioma do seu teclado.
>
	nano /etc/locale.gen (Remova # do inicio da linha referente ao idioma desejado)
	locale-gen

Após realizar as modificações salve o arquivo.
![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/4.2.png)

Em seguida iremos configurar o nosso idioma, criando o arquivo /etc/locale.conf (caso deseje utilizar um idioma diferente adapte o comando de acordo com o desejado).

Execute o comando, para definir o idioma:
>
	echo LANG=pt_BR.UTF-8 > /etc/locale.conf
	export LANG=pt_BR.UTF-8

Foi criado o arquivo com os parâmetros passados no comando:
![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/4.3.png)

Agora vamos configurar o fuso horário.
Podem ser vistas as regiões do Brazil com o seguinte comando:
>
	ls /usr/share/zoneinfo/Brazil

No meu caso irei utilizar East, execute o comando para criar um link simbólico:
>
	ln -s /usr/share/zoneinfo/Brazil/East /etc/localtime

Vamos definir um hostname para a maquina:
>
	echo Arch > /etc/hostname

Criando um usuário (o comando abaixo cria e adiciona o usuário em varios grupos, verifique qual você deseja):
>
	useradd -m -G wheel -s /bin/bash nome_usuario

E definir uma senha de root:
>
	passwd

5 - Instalando o Bootloader
==========
Iremos utilizar o grub, mas caso deseje utilizar outro bootloader podem ser obtidas mais informações na [pagina oficial] (https://wiki.archlinux.org/index.php/Boot_loaders).

Da mesma maneira que no particionamento de disco, que foi dividido em duas partes GPT e MBR, a instalação do grub tambem será divida nessas duas partes. Prossiga de acordo com seu tipo de tabela de partição:

### 5.1 MBR - BIOS
Será instalado o Grub e os-prober que permite que sejam reconhecidos outros sistemas operacionais instalados na maquina. E em seguida geradas as configurações para o grub.

Execute os seguintes comandos:
>
	pacman -Sy
	pacman -S grub os-prober
	grub-install /dev/sda
	mkinitcpio -p linux
	grub-mkconfig -o /boot/grub/grub.cfg

### 5.2 GPT - UEFI
Será instalado o Grub e os-prober que permite que sejam reconhecidos outros sistemas operacionais instalados na maquina. E em seguida geradas as configurações para o grub.

Execute os seguintes comandos:
>
	pacman -Sy
	pacman -S grub efibootmgr os-prober
	grub-install /dev/sda
	grub-mkconfig -o /boot/grub/grub.cfg
	mkinitcpio -p linux

### 5.3 Alternativa ao Grub
Caso não queira utilizar o grub, existe uma alternativa boa para computadores que possuem suporte a UEFI. Pode ser utilizado o Refind, é um bootloader eficiente, bonito e que detecta automaticamente os sistemas operacionais instalados na maquina.

Para funcionar corretamente é necessário que o ponto de montagem da partição de boot seja /boot/efi.
Para instalar o Refind execute:
>
	pacman -Sy
	pacman -S refind-efi efibootmgr
	refind-install (Instala Refind)

![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/refind.png)

6 - Configurando o Gerenciador de pacotes Pacman
==========
Iremos definir algumas configurações adicionais para seu gerenciador de pacotes.

Vamos habilitar o repositório multilib, o qual permite ao usuário baixar e executar aplicações 32 e 64 bits.

Abra o arquivo pacman.conf que fica localizado em /etc.
>
	nano /etc/pacman.conf

E adicione ou descomente as linhas:
>
	[multilib]
	Include = /etc/pacman.d/mirrorlist

Deverá ficar assim:

![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/6.1.png)

Vamos tambem alterar algumas configurações do pacman, isso é opcional. Vamos deixar as cores das fontes no terminal coloridas, e utilizar um pacman na barra de progresso.

Exemplo:

![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/6.3.png)

Abra o arquivo `pacman.conf` e descomente a linha Color e adicione ILoveCandy:
Ficando assim:
>
	nano /etc/pacman.conf
	
	Color
	ILoveCandy

Dessa forma:

![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/6.2.png)

7 - Finalizando a Instalação
==========
Pronto!

Agora seu sistema já está instalado. Podemos sair da instalação e reiniciar.

Execute os comandos:
>
	exit
	umount -a
	reboot

8 - Configurando o Desktop
==========
Agora que o sistema já está instalado, iremos instalar o servidor grafico, drivers e softwares para que possamos instalar e configurar o nosso ambiente de trabalho. Irei instalar o GNOME por preferência. Você pode obter mais informações sobre outros ambientes de trabalho na [pagina oficial] (https://wiki.archlinux.org/index.php/desktop_environment).

Caso você não tiver conexão com a internet, execute o seguinte comando:
>
	systemctl start dhcpcd
	systemctl enable dhcpcd

Execute os comandos abaixo para instalar o servidor grafico, suporte a 3D e audio.
>
	pacman -Sy (Atualiza os repositórios)
	pacman -S alsa-utils
	pacman -S xorg-server xorg-xinit xorg-server-utils
	pacman -S xf86-video-vesa
	pacman -S mesa
	pacman -S xorg-twm xorg-xclock xterm
	pacman -S ttf-dejavu

Vamos instalar o suporte a Wireless:
>
	pacman -S networkmanager network-manager-applet gnome-keyring
	systemctl enable NetworkManager.service

Instalar suporte a outros tipos de sistemas de arquivos e discos:
>
	pacman -S dosfstools ntfs-3g nilfs-utils mtools libmtp gpart

Vamos instalar o driver de video da Intel e suporte a Touchpad se for o seu caso:
>
	pacman -S xf86-video-intel
	pacman -S xf86-input-synaptics

Agora vamos instalar o GNOME:
>
	pacman -S gnome

E habilitar a inicialização do GDM (Gnome Display Manager) com o sistema:
>
	systemctl enable gdm

Vamos reiniciar:
>
	reboot


Agora seu sistema está pronto para você configurar ao seu gosto!
![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/print.png)

Caso tenha alguma duvida, podem ser obtidas mais informações no [guia para iniciantes] (https://wiki.archlinux.org/index.php/Beginners%27_guide).

Estamos desenvolvendo o [ALPI] (https://github.com/tiagorlampert/alpi) que permite facilitar a configuração do sistema após a instalação. Caso queira testar para automatizar a configuração do sistema, você pode obtê-lo executando os seguintes comandos:
>
	pacman -Sy
	pacman -S git wget
	git clone https://github.com/tiagorlampert/alpi.git
	cd alpi
	./alpi.sh

9 - Gerenciando o Sistema
==========

Agora serão abordados alguns tópicos importantes e básicos para o gerenciamento de pacotes no sistema.

### 9.1 Pacman
![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/pacman.png)

Pacman é o nome do gerenciador de pacotes, o objetivo dele é tornar mais facil o gerenciamento de pacotes do sistema, ele utiliza pacotes binários `.pkg.tar.xz` com o uso de um sistema de compilação chamado ABS (que será abordado mais tarde).

Alguns comandos que podem ser utilizados:
>
	pacman -Sy = atualiza os repositórios;
	pacman -Su = atualiza a distribuição;
	pacman -S pacote = instala um pacote;
	pacman -R pacote = remove um pacote;
	pacman -Rs pacote = remove o pacote junto com as dependências não usadas por outros pacotes;
	pacman -Ss pacote = procura por um pacote;
	pacman -Sw pacote = apenas baixa o pacote e não o instala;
	pacman -Si pacote = mostra informações de um pacote não instalado;
	pacman -Qi pacote = mostra informações do pacote já instalado;
	pacman -Ql pacote = mostra todos os arquivos pertencentes ao pacote;
	pacman -Qu = mostra os pacotes que serão atualizados;
	pacman -Q = lista todos os pacotes instalados;
	pacman -Qo arquivo = mostra a qual pacote aquele arquivo pertence;
	pacman -Sc = deleta do cache todos os pacotes antigos ;
	pacman -Scc = limpa o cache, removendo todos os pacotes existentes no /var/cache/pacman/pkg/;

### 9.2 AUR (Arch User Repository)

O AUR é um repositório mantido pela comunidade do Arch, e foi criado para organizar e criar novos pacotes. Ele contem PKGBUILDs que permitem ao usuário compilar pacotes utilizando o makepkg e depois instalar utilizando o pacman. Pode ser acessado no site https://aur.archlinux.org/.

### 9.3 ABS (Arch Build System)

O ABS é um sistema para compilação de pacotes a partir do código fonte. Enquanto o pacman utiliza binários, o ABS utiliza uma coleção de ferramentas para compilar fontes e gerar pacotes `.pkg.tar.xz`, instalaveis pelo pacman.

Vamos ver como funciona o `makepkg` que é uma ferramenta que permite baixar e compilar fontes para criar pacotes `.pkg.tar.*`.

Agora iremos obter o PKGBUILD de um programa chamado Yaourt direto do AUR, para então compilarmos e instalarmos ele. 

Vamos baixar o PKGBUILD do Yaourt e de outra dependencia dele:
>
	wget https://aur.archlinux.org/cgit/aur.git/snapshot/yaourt.tar.gz
	wget https://aur.archlinux.org/cgit/aur.git/snapshot/package-query.tar.gz

Após baixarmos, vamos extrair:
>
	tar -xvf *.tar.gz

Entre dentro da pasta da dependência do Yaourt, chamada package-query:
>
	cd package-query

**Detalhe importante, o makepkg não pode ser executado como root!**

E execute o makepkg para compilar o programa e instalar:
>
	makepkg -sic

***Após ter feito isso repita o procedimento com o Yaourt.***

Volte para o diretório anterior:
>
	cd ..

Entre dentro da pasta do Yaourt:
>
	cd yaourt

E execute o makepkg para compilar o programa e instalar:
>
	makepkg -sic

Isso é o básico para se saber como baixar e compilar um pacote manualmente do AUR.


### 9.4 Yaourt (Yet AnOther User Repository Tool)

![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/yaourt.png)

Como as vezes o processo de compilação e instalação de um programa do AUR pode ser muito trabalhoso, devido a sua grande quantidade de dependências. Existem algumas ferramentas que fazem todo o trabalho "pesado" para nós. E uma delas se chama Yaourt.

Essa ferramenta automatiza o acesso ao AUR para a busca e instalação de programas. Ele contem funções que o pacman por si só não possui.

Para utilizar o yaourt, ao invés de usar `"pacman"`, substitua o comando por `"yaourt"`.

Exemplo, para atualizar o sistema:
>
	pacman -Syu
	yaourt -Syua


10 - Finalizando
==========

![ArchLinux](https://github.com/tiagorlampert/InstalandoArchLinux/blob/master/src/arch.png)

Após instalar e configurar o básico no sistema ainda resta muita coisa para ser ajustada, é muito importante dar uma olhada em [guia para iniciantes] (https://wiki.archlinux.org/index.php/Beginners%27_guide) e [recomendações gerais] (https://wiki.archlinux.org/index.php/General_recommendations). E veja algo sobre o [pacman] (https://wiki.archlinux.org/index.php/pacman) que é o gerenciador de pacotes do sistema. É claro que a maioria das coisas você só irá aprender com a utilização do sistema, mas é importante ler a documentação da distribuição para se ter uma base teórica de como funciona o Arch.

Arch Linux é um sistema altamente personalizavel, rapido e estavel. Como exige muito da parte do usuário, proporciona um alto nivel de aprendizagem. Permite ao usuário saber como o sistema foi montado, e ver realmente oque acontece no sistema. Você decide oque vai no sistema, não terá coisas desnecessárias que nunca vai chegar a utilizar rodando em seu sistema, sendo assim terá um sistema rápido e flexivel. Para mais informações veja [O Jeito Arch] (https://wiki.archlinux.org/index.php/The_Arch_Way_%28Portugu%C3%AAs%29).
