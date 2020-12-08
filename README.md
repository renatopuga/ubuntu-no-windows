# Ubuntu no Windows
Bioinformática Use Case

# Utilizar o Ubuntu no Windows 10+

A partir da versão 10+ do MS Windows é possível utilizar o Ubuntu através do Subsistema Windows para Linux (WSL), como se o Ubuntu fosse uma aplicação instalada dentro do Windows.

> Fonte: https://docs.microsoft.com/pt-br/windows/wsl/install-win10


> A virtualização na BIOS já foi ativada.

## Como instalar o WSL e WSL2 no Windows 10+ (x64)

### Etapa 1: Habilitar o Subsistema do Windows para Linux

Abra o PowerShell como administrador (na barra de busca do windows digite PowerShell e clique com o botão direito e selecionar ***Executar como administrado)***:

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```



### Etapa 2: Atualizar para o WSL 2

> Para verificar a sua versão e o número de build, selecione a **tecla do logotipo do Windows + R**, digite **winver** e selecione **OK**



### Etapa 3 – Habilitar o recurso de Máquina Virtual

Antes de instalar o WSL 2, você precisa habilitar o recurso opcional **Plataforma de Máquina Virtual**. No PowerShell como administrador execute:

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```



### Etapa 4 – Baixar o pacote de atualização do kernel do Linux

Baixe e instale o pacote mais recente (x64):

- [Pacote de atualização do kernel do Linux do WSL2 para computadores x64](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)



### Etapa 5 – Definir WSL2 como padrão

Depois que a instalação for concluída, vá para a próxima etapa: configurar o WSL 2 como a sua versão padrão ao instalar novas distribuições do Linux. No PowerShell como administrador execute:

```powershell
wsl --set-default-version 2
```



### Etapa 6 – Instalar a distribuição do Linux de sua escolha

Abra a [Microsoft Store](https://aka.ms/wslstore) e escolha sua distribuição do Linux favorita, no nosso caso o Ubuntu.



![Exibição das distribuições do Linux na Microsoft Store](https://docs.microsoft.com/pt-br/windows/wsl/media/store.png)



![Distribuições do Linux na Microsoft Store](https://docs.microsoft.com/pt-br/windows/wsl/media/ubuntustore.png)



### Etapa 7 – Configurar uma nova distribuição

Na primeira vez que você iniciar uma distribuição do Linux recém-instalada, uma janela de console será aberta e será solicitado que você aguarde um ou dois minutos para que os arquivos sejam descompactados e armazenados em seu PC. Todas as futuras inicializações deverão levar menos de um segundo.

Em seguida, você precisará criar uma conta de usuário e uma senha para sua nova distribuição do Linux.



![Desempacotamento do Ubuntu no console do Windows](https://docs.microsoft.com/pt-br/windows/wsl/media/ubuntuinstall.png)



![Well Done GIFs - Get the best GIF on GIPHY](https://media3.giphy.com/media/YRuFixSNWFVcXaxpmX/giphy.gif)



### Onde está o C:\ ?

O C: do seu Windows é montado como uma partição dentro do Ubuntu e fica em `/mnt/c`

> O c:\Users\puga que é seu HOME no Windows não é o mesmo do Ubuntu

```bash
# comando listar as partições existentes
# df: comando que lista as partições
# -h: listar de forma que humano entenda (MB, GB ou TB)
df -h
....
```

> O C: fica /mnt/c

```bash
# entrando no C: do seu Windows
# no caso da Puga é puga
cd /mnt/c/puga
```



## Criando Diretório para Bioinfo

Puga decidiu criar um diretório na Área de Trabalho (Desktop) chamado **bioinfo**:

```bash
# entrando o diretorio Desktop
cd /mnt/c/puga/Desktop

# mkdir: criando o diretorio bioinfo
mkdir bioinfo

# entrando no diretório bioinfo
cd bioinfo
```



### Criando Estrutura de Diretórios

```bash
# estrutura de diretorios
# app: coloque aqui os programas instalados (fora do apt-get install)
# bam: coloque os arquivos .bam e .bai 
# instalacao: arquivos de instalacao .exe ou .gz que não quer deletar
# docs: suas documentacoes ou helps

mkdir app bam instalacao docs
```



### Ubuntu Update e Upgrade (~3-8min)

Vamos atulizar as bibliotecas no sistema para pode instalar os softwares de bioinformática.

> **sudo:** é o comando para acionar os poderes de **s**uper **u**suário (como por exemplo para instalar coisas)

```bash
# atualiza a lista de repositórios
sudo apt-get update

# atualiza os programas mais novos
sudo apt-get upgrade
```



### Instalando bwa, samtools e freebayes (~2min)

Podemos executar em um comando a instalação dos programas juntos

```bash
sudo apt-get install -y bwa samtools freebayes
```



### Instalando Annovar (use apenas para pesquisa) 

Faça download do arquivo do `annovar.tar.gz`: 

* Google Drive: 
  * https://drive.google.com/drive/folders/1FjReBQrm-JVGgCMZDtYaufoR4JzjPlTz 



Salve no seu diretório `app`. Agora, acesse o terminal do Ubuntu, vá até o diretório `app` e digite os comandos:

```bash
# tar -zxvf
# avisos: pode aparecer alguns avisos de conflito (ignore)

# entrar no diretorio app
cd /mnt/c/puga/Desktop/bioinfo/app/annovar

# listar conteudo do diretorio
ls -1 
annovar.tar.gz

# descompactar 
tar -zxvf annovar.tar.gz

# entrar no diretorio
cd annovar

# listar conteudo do diretorio
ls -1 
```



### Download dos Bancos de Dados do Annovar (~8-20min)

Lista dos bancos de dados disponíveis para o Annovar: https://annovar.openbioinformatics.org/en/latest/user-guide/download/.



> Verificar sempre se existe novas versões para os bancos de dados

```bash
# entrar no diretorio do annovar
cd /mnt/c/puga/Desktop/bioinfo/app/annovar

# clinvar
perl annotate_variation.pl -buildver hg19 -downdb -webfrom annovar clinvar_20200316  humandb/

# gnomad
perl annotate_variation.pl -buildver hg19 -downdb -webfrom annovar gnomad211_genome humandb/
```



### Download do Genoma de Referência (hg19)

> Bucket do Google Cloud para as referências do Broad Institute



Nesse bucket do Google/Broad tem todas as referências e os indexação dessa versão do genoma do BWA (Homo_sapiens_assembly19.fasta.64.*). Todas as referências tem um tamanho de **~30GB.**



* gcp-public-data--broad-references/hg19/v0
  * https://console.cloud.google.com/storage/browser/gcp-public-data--broad-references/hg19/v0?pageState=(%22StorageObjectListTable%22:(%22f%22:%22%255B%255D%22))&prefix=&forceOnObjectsSortingFiltering=false



### Chamar Variantes apenas no chr22

Para não travar o computador, principalmente quando trabalhamos com arquivo grandes, podemos dividir a chamada de variantes por cromossomos e no final podemos juntar os arquivos `.vcf` em um único para cada amostra ou um único para todas as amostras. Vamos fazer um teste com o `chr22` na amostra `CP8.bam`:



#### 1. Download do arquivo .fa chr22

* Clique no link e salve os arquivos `references_hg19_chr22.fa` e `references_hg19_chr22.fa.fai` no diretório `/mnt/c/puga/Desktop/bioinfo/referencia`
  * https://drive.google.com/drive/folders/1_1ZwKQB6XC9ObYRUYt3AvlpS2h3AueF2 



#### 2. Chamar Variantes com Freebayes

```bash
# entrar no diretorio bioinfo
cd /mnt/c/puga/Desktop/bioinfo/

# rodar freebayes apenas para o chr22 (Ver opções)
freebayes -f reference/references_hg19_chr22.fa -F 0.01 -C 1 --pooled-continuous --region 22 bam/CP8.bam > CP8.chr22.vcf
```



#### 3. Anotar com Annovar

```bash
# entrar no diretorio bioinfo
cd /mnt/c/puga/Desktop/bioinfo/

# anotar apenas com RefSeq
perl app/annovar/table_annovar.pl  --vcfinput  CP8.chr22.vcf  app/annovar/humandb/ -buildver hg19 -out CP8.chr22 -remove -protocol refGene -operation g -nastring .
```



#### 4. Visualizar o resultado hg19.multianno.txt

```bash
# visualizar com less
# -S: linha única sem quebra
# -N: números no começo das linhas
# Para sair aperte a tecla 'q'

less -SN CP8.chr22.hg19_multianno.txt 
```




