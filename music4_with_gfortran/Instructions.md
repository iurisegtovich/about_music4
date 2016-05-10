#Instructions

#Guia para utilização do MuSiC 
Guia versão 1 - Simulação GCMC
Iuri Soter Viana Segtovich
O programa foi utilizado na EQ/UFRJ em um computador com OS Ubuntu 12.10 64 bits

Os arquivos código-fonte do programa MuSiC (multipurpose simulation code) devem ser obtidos na página do programa, no site do grupo de pesquisa do Snurr.
Link para a página:
!----------------------------------------
	http://zeolites.cqe.northwestern.edu/Music/
!----------------------------------------
Nesse site há um link para download, esse link requer nome de usuário e senha, os quais são fornecidos via e-mail após contato com o autor, mediante fornecimento de algumas informações:
email do autor:
!----------------------------------------
snurr@northwestern.edu
!----------------------------------------
cópia da mensagem:
!----------------------------------------
For the Music code, please read the web page 
http://zeolites.cqe.northwestern.edu/Music/music.html 
Then send me an e-mail, fax, or letter with the following information.  We are collecting this information so that we can tell the agencies funding our research how many groups are using our code, how it is helping them, etc.  Continued funding will help us further develop the code.  The code is provided without support, but documentation is available on the web site. 
1.  Name 
2.  e-mail address 
3.  Address 
4.  Institution 
5.  What is your research area and how will you use Music?  (This does not limit how you may use it, but it is very useful to us to know this.) 
6.  Do you agree to cite the following paper in any publications resulting from use of Music? 
"Object-oriented programming paradigms for molecular modeling" A. Gupta, S. Chempath, M.J. Sanborn, L.A. Clark, R.Q. Snurr, Molecular Simulation 29, 29-46 (2003). 
7.  Do you agree to the terms of the GNU General Public License? 
Music -- Multipurpose Simulation Code:  Object-oriented molecular simulation software 
Copyright (C) 2002  Amit Gupta, Marty Sanborn, Louis Clark, Shaji Chempath, Lev Sarkisov, Randy Snurr 
This program is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 2 of the License, or (at your option) any later version. 
This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details: http://www.gnu.org/licenses/gpl-2.0.html
After I've received this information, I'll send you the username and password, so that you can download the code from the web site. 
Randy
!----------------------------------------
Após download dos códigos, devem ser compilados programas para diferentes operações.
A página do Snurr recomenda o tutorial de Düren, disponível na página da pesquisadora, no site da School of Engineering - University of Edinburgh.
Link para o tutorial:
!----------------------------------------
http://www.see.ed.ac.uk/~tduren/research/music/Tutorial/
!----------------------------------------
O programa foi utilizado em Ubuntu 12.10, 64bits, não foi possível compilar os códigos utilizando o compilador gfortran, foi necessária então a utilização do compilador ifort (intel fortran) para ubuntu, a instalação foi feita como descrito abaixo:
!----------------------------------------
[download]
http://software.intel.com/en-us/non-commercial-software-development
[descompactar o arquivo]
l_fcompxe_2013.5.192.tgz
[iniciar instalação]
sudo ./instal.sh
[Criar link:]
ln -s /opt/intel/composer_xe_2013.5.192/bin/intel64/ifort /usr/local/bin/ifort
!----------------------------------------
Caso sejam encontrados problemas, leitura do tópico abaixo é recomendada, pois o mesmo foi útil nessa instalação
!----------------------------------------
http://ubuntuforum-br.org/index.php?topic=58292.0
!----------------------------------------
Com esses arquivo foram compilados os programas musicmapmaker (para geração do arquivo de mapa de potencial *.pmap), musicmapdisplay (para geração do arquivo silimap.xyz contendo a visualização do mapa de potencial gerado),musicgcmc (para execução da simulação de monte carlo em ensembe grande canônico), e musicpostproc (para pós processamento dos arquivos de saída da simulação)
Durante a execução do programa segundo o tutorial de Düren, houve problema na execução dos programas (Segmentation fault (core dumped)).
Analisando os arquivos .F90 foi possível determinar a origem do erro como sendo uma operação de (=) entre propriedades de classes derivadas, do tipo ponteiros com uma dimensão em bmap.F90 e com 3 dimensões em pmap.F90 três dimensões. Foram feitas então as alterações a seguir,
No entanto, ao repetir o procedimento desde a etapa de compilação dos arquivos originais usando outro exemplo com 10 vezes menos pontos na geração do mapa de potencial, esse erro não foi mais observados.
!----------------------------------------
Em bmap.F90 (para o musicmapmaker.exe)
Feita declaração de novo contador (iuri) junto às variáveis integer da subroutine ‘bmap_init’
    Integer                        :: i, mapno, iuri
Alterados seguintes trechos
[era]
	bmaps(mapno)%rb(1:nfsize) = bmaps(mapno)%rb_temp(1:nfsize)
[é]
do iuri = 1,nfsize
	bmaps(mapno)%rb(iuri) = bmaps(mapno)%rb_temp(iuri)
end do

Em pmap.F90 (para o musicmapdisplay.exe)
Feitas declaração de três novos contadores (iuri1, iuri2, iuri3) junto às variáveis integer da subroutine ‘pmap_compact ‘
	Integer         :: i, j, k, ii, jj, kk, indx, jndx, kndx, iuri1, iuri2, iuri3
Alterados seguintes trechos
[era]
	lilMap%core%ptr=lilOneTempPtr
[é]
do iuri = 1,size(lilMap%core%ptr,1)
do iuri2 = 1,size(lilMap%core%ptr,2)
do iuri3 = 1,size(lilMap%core%ptr,3)
	lilMap%core%ptr(iuri,iuri2,iuri3)=lilOneTempPtr(iuri,iuri2,iuri3)
end do
end do
end do
!----------------------------------------
No arquivo ff.F90, necessário ao programa musicgcmc, foi modificado uma condicional, pois o ponteiro "actual_nrgs" se encontrava desalocado em chamadas posteriores a função, apesar do flag "first_time", responsável pela alocação do ponteiro estar como "false".
!----------------------------------------
Em ff.F90 (para o musicrungcmc.exe)
Na function ‘ff_chknrgs’
[era]
If (first_time) Then
[é]
If (.not.associated(actual_nrgs)) Then
!----------------------------------------
Compilação do programa:
Descompactar o arquivo  music-4.0.tar.gz
!----------------------------------------
	tar -xvzf music-4.0.tar.gz
!----------------------------------------
Nesse guia a pasta contendo os códigos será colocada na pasta home do usuário
!----------------------------------------
	mv music-4.0 ~/music4
!----------------------------------------
Os arquivos para compilação se encontram dentro da pasta src
!----------------------------------------
	cd music4/src
!----------------------------------------
Copiar driver do programa a ser compilado da pasta drivers para a pasta src, que contém os arquivos código fonte, renomeando-o para music.F90
!----------------------------------------
	cp ../drivers/ music_mapmaker.F90 music.F90
!----------------------------------------
O ‘F’ maiúsculo é essencial para o funcionamento do arquivo Makefile que será gerado
executar o script makemake para criar o arquivo Makefile
!----------------------------------------
  perl makemake
!----------------------------------------
Editar o Makefile, mudando a linha que define o seu compilador.
!----------------------------------------
	[era]
	# FORTRAN_COMPILER= yourcompilername
	[é]
	FORTRAN_COMPILER= gfortran
	[ou]
	FORTRAN_COMPILER= ifort
!----------------------------------------
Nesse arquivo pode-se também alterar as flags utilizadas pelo programa.
Agora executar o make para gerar o executável.
!----------------------------------------
	make
!----------------------------------------
É gerado na pasta music4 o executável com o nome post.exe, em seguida deve-se renomear ele para não ser sobrescrito durante a compilação do próximo programa.
!--------------------
	mv ../post.exe ../musicmapmaker.exe
!--------------------
Criar uma pasta com os dados para a simulação, por exemplo:
!----------------------------------------
Criar a pasta	~/music4/sim1
Contendo quatro pastas:
	~/music4/sim1/atoms
	~/music4/sim1/molecules
	~/music4/sim1/pmaps
	~/music4/sim1/run
Onde deverão constar os arquivos necessários para a simulação:
pasta atoms:
	*****.atm	um arquivo para cada átomo ou pseudoátomo
pasta molecules:
	*****.mol	um arquivo para cada molécula ou pseudo molécula,	
			 inclusive estrutura sólida
pasta run:
	atom_atom_file
	intramolecular_file
	sorb_sorb_file
	set_path
	fugacity.dat (opcional)
	ctr.mapmaker; ctr.mapdisplay; ctr.gcmc; ctr.post	
	            (arquivos de controle para cada operação)
!----------------------------------------
O tutorial de Düren disponibiliza uma pasta para simulação pasta com arquivos exemplo, porém os arquivos estão nomeados de maneira um pouco diferente da usada neste guia.
Em concordância com as pastas criadas nesse guia, o arquivo set_path deverá indicar os seguintes endereços:
!----------------------------------------
	#!/bin/sh
	export ATOMSDIR=~/music4/sim1/atoms
	export MOLSDIR=~/music4/sim1/molecules
	export PMAPDIR=~/music4/sim1/pmaps
!----------------------------------------
O arquivo de controle ctr.mapmaker deve ser editado para fazer referência aos nomes corretos dos arquivos adequados da pasta run.
São necessários um mapa para cada tipo de interação e cada molécula de adsorbato; pmap para interações de Lenard Jones e emaps para interações com cargas. Cada geração de pmap ou emap deve usar uma molécula sonda (probe atom) com apenas NCOUL ON ou apenas COUL ON.
Execução
Trabalhando dentro da pasta run, para o executável acessar os arquivos da pasta corretamente:
Usar o comando source no arquivo set_path
!----------------------------------------
	source set_path
!----------------------------------------
Executar o arquivo compilado usando o arquivo de controle,
No caso exemplo de acordo com os nomes utilizados neste guia:
!--------------------------------------
	~/music4/musicmapmaker.exe ctr.mapmaker
!--------------------------------------
O programa irá gerar o mapa de potencial de acordo com as instruções do arquivo de controle, e o arquivo sili.xyz com as posições dos átomos da estrutura
!----------------------------------------
	*****.pmap
	sili.xyz
!----------------------------------------
O arquivo .pmap deve ser copiado para a pasta pmaps
Compilação e execução do musicmapdisplay:
Copiar o driver do mapdisplay (music_mapdisplay.F90) para a pasta source, renomeando-o para music.F90
./makemake
editar o Makefile da mesma forma
compilar
rodar com o arquivo de controle adequado
!----------------------------------------
	~/music4/musicmapdisplay.exe ctr.mapdisplay
!----------------------------------------
será gerado o arquivo silimap.xyz contendo a visualização do mapa de potencial criado.
Compilação e execução do musicgcmc
copiar o driver do gcmc (music_gcmc.F90) da mesma forma
./makemake
editar o Makefile novamente da mesma forma
compilar
rodar com o arquivo de controle adequado, incluindo arquivo de saída para registro de informações de probabilidade de aceitação obtidas durante a simulação.
!----------------------------------------
~/music4/musicgcmc.exe ctr.gcmc > music.log
!----------------------------------------
São gerados diversos arquivos *****.con, a serem analisados pelo pós processamento, ****.res que podem ser utilizados como condição inicial para outra simulação, finalconfig.xyz, contendo a visualização da última configuração amostrada e um arquivo de controle novo new.ctr.
O arquivo music.log pode ser acompanhado durante a simulação, exibindo inicialmente os dados de entrada, e fornecendo informações sobre quantidade de passos já simulados e taxa de aceitação de movimentos.
Para geração de uma isoterma pode ser utilizado um arquivo externo contendo uma lista de fugacidades a serem utilizadas em simulações sucessivas, que deve estar contido na pasta run e cujo nome deve ser indicado no arquivo de controle da simulação.
Compilação e execução do musicpostproc:
copiar o driver do pós processamento (music_post.F90)
./makemake
editar o makefile da mesma forma
compilar
rodar com o arquivo de controle adequado
!----------------------------------------
	~/music4/musicpostproc.exe ctr.post
!----------------------------------------
São gerados um arquivo *****.post para a simulação e um arquivo isotherm.***** para cada adsorbato. O arquivo post contém as informações de energia potencial e número de moléculas obtidas nas simulações divididas em blocos de acordo com o especificado no arquivo de controle. O arquivo isotherm lista as fugacidades fixadas e número médio de moléculas calculado para cada simulação, para a geração de um gráfico de isoterma de adsorção a partir do conjunto de simulações.
A página de Düren também disponibiliza um algoritmo extra para cálculo de fugacidade dos gases utilizando EdE PengRobinsons para gerar a lista de fugacidades.
link:
!----------------------------------------
	http://www.see.ed.ac.uk/~tduren/research/fugacity/
!----------------------------------------
A autora também disponibiliza algoritmo para conversão da quantidade em quantidade absoluta (calculada pelo programa) para quantidade em excesso, (variável de fato observada experimentalmente) usando densidade da fase gasosa e volume de poros, o que é importante para poros grandes.
!----------------------------------------
	http://www.see.ed.ac.uk/~tduren/research/excess/Introduction.html
!----------------------------------------
Há exemplos de arquivos de entrada no site da documentação do programa de Snurr,
!----------------------------------------
	http://zeolites.cqe.northwestern.edu/Music/Documentation/node42.html
!----------------------------------------
e também no site de tutorial de Düren já referenciado.
Os arquivos de controle são divididos em seções, a maioria das linhas necessárias estão explicadas na parte de arquivos de entrada da documentação da página do programa.
!----------------------------------------
	http://zeolites.cqe.northwestern.edu/Music/Documentation/node43.html
!----------------------------------------
