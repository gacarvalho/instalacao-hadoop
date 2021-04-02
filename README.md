## 🎲 INSTALAÇÃO DO HADOOP - SISTEMA OPERACIONAL UBUNTU 

O objetivo deste material é auxiliar na instalação do framework Hadoop!

### 📌 Passo 1: Pré Requisito - JAVA

O primeiro passo é instalar o JAVA na sua máquina: Máquina Virtual Java.  

```bash
# Primeiro abra o terminal com o atalho ( CTRL + ALT + T )

$ sudo add-apt-repository ppa:webupd8team/java

$ sudo apt-get update

$ sudo apt-get install oracle-java8-installer

# Agora aplique o comando no terminal para saber qual é a versão do java instalado!

$ java -version

```

### 📌 Passo 2: Pré Requisito - SSH



```bash
# Com o terminal aberto, aplique o seguinte comando:

$ sudo apt-get install openssh-server

$ sudo service ssh start

# Para a configuração do SSH você poderá seguir os passos deste artigo: https://sempreupdate.com.br/como-ativar-e-instalar-o-ssh-no-ubuntu/

```

### 📌 Passo 3: Pré Requisito - USUÁRIO

Para o desenvolvimento da instalação de acordo com este tutorial, você deve criar um usuário exclusivo para trabalhar com o Hadoop.

```bash
# Usuário: hduser
# Senha: 
```

### 📌 Passo 4: Colocando o USUÁRIO no grupo Hadoop

Essa etapa é opcional! 

```bash
$ sudo addgroup hadoop

$ sudo adduser --ingroup hadoop hduser
```

> 💬 Vale lembrar que o Hadoop utiliza SSH para gerenciar os nós! 
> 💬 Mesmo para localhost, é indicado configurar o acesso ao localhost para o usuário que foi criado: hduser sem senha.
> 💬 Vamos considerar que o SSH já está executando na sua máquina! 


### 📌 Passo 5: CONECTANDO USUÁRIO E GERANDO CHAVE SSH

Um passo muito importante será aplicado agora, pois será conectado com o usuário hduser, gerando uma chave SSH para esse mesmo usuário com a senha vazia. 

```bash
# Conectando usuário
$ su - hduser

# Gerando chave SSH para o usuário hduser
$ ssh-keygen - t rsa -P ""
```

### 📌 Passo 6: Habilitar o SSH com a chave

Este passo tem como objetivo habilitar o SSH para acessar a máquina local com a chave recentemente criada.

```bash
$ cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys
```

### 📌 Passo 7: Testando a conexão com a máquina 

Vamos realizar o teste com a conexão para a máquina local do usuário hduser!

```bash
$ ssh localhost
```
> 💬 Caso o sistema não solicite a senha quer dizer que deu tudo CERTO! 
> 💬 Caso o sistema solicite a senha é recomendado que você faça todos os passos novamente!

### 📌 Passo 8: INSTALAÇÃO DO RECURSO 'ABRIR COMO ADMINISTRADOR'

As vezes precisamos abrir pastas ou editar arquivos e não temos as devidas permissões para fazer a tarefa. Nessa situação, você precisa abrir um terminal e executar o Nautilus como root, o que não é muito produtivo. Para realizar a instalação aplique os seguintes comandos no terminal!

```bash
# Instalar o recurso
$ sudo apt install nautilus-admin

# Reiniciar o recurso instalado
$ nautilus -q

# Fonte do artigo: encurtador.com.br/mnuI6
```
### 📌 Passo 9: DESABILITAR O IPV6

Abra o arquivo com um editor de texto com o recurso de administrador e aplique o seguinte comando no fim do arquivo:


```bash
# Endereço do arquivo: /etc/sysctl.conf

################################# disable ipv6

$ net.ipv6.conf.all.disable_ipv6 = 1

$ net.ipv6.conf.default.disable_ipv6 = 1

$ net.ipv6.conf.lo.disable_ipv6 = 1
```

Logo após, reinicie a máquina!

### 📌 Passo 10: VERIFICANDO SE O IPV6 ESTÁ DESABILITADO

```bash
$ cat /proc/sys/net/ipv6/conf/all/disable_ipv6
```
> 💬 Caso o comando retorne 0 (zero), o ipv6 está habilitado! 
> 💬 Se retornar 1 (um), o ipv6 está desabilitado! 

Lembrando que o nosso objetivo é que o sistema retorne 1!


### 📌 Passo 10: DOWNLOAD DO HADOOP

Você poderá baixar o arquivo pelo o link
```bash
# Link para download: https://hadoop.apache.org/release/2.7.3.html
```

> 💬 SUGESTÃO: Descompactar no diretório /usr/local/

```bash
$ cd /usr/local

$ sudo tar xzf hadoop-2.7.3.tar.gz

# Este comando está trocando o nome do arquivo original para apenas 'hadoop' para simplificar
$ sudo mv hadoop-2.7.3 hadoop

# Já este comando estamos permitindo permissão a essa pasta recentimente criada!
$ sudo chown -R hduser:hadoop hadoop
```

### 📌 Passo 11: CRIAÇÃO DAS VARIAVEIS DE AMBIENTE

Para isso é necessário editar o arquivo .bashrc para o usuário hduser tanto para o HADOOP e JAVA. No arquivo adicione os seguintes comandos:

```bash
# Set Hadoop-relatec environment variables
$ export HADOOP_HOME=/usr/local/hadoop

# Set JAVA_HOME
$ export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```

Vale lembrar que na variavel HADOOP_HOME e JAVA_HOME você precisa apontar no local que está na sua máquina!

### 📌 Passo 12: CONFIGURANDO O HADOOP

O primeiro passo agora é criar um diretório para armazenamento de dados temporários do HADOOP, e para isso vamos criar no endereço /usr/local/tmp


```bash
$ sudo mkdir -p /usr/local/hadoop/tmp
```
Esse diretório vai ser utilizado posteriormente!

Agora vamos atribuir permissões ao novo diretório:


```bash
$ sudo chown hduser:hadoop /usr/local/hadoop/tmp
$ sudo chmod +x /usr/local/hadoop/tmp
```

Agora vamos alterar o arquivo core-site.xml que está no endereço /usr/local/hadoop/etc/hadoop/core-site.xml e aplique o seguinte comando ENTRE as tags <configuration> </configuration>

Lembrando que você deve clicar com o botão direitor do mouse e ir na opção 'ABRIR COMO ADMINISTRADOR'

```bash
  <configuration>
    <property> 
      <name>/usr/local/hadoop/tmp</name>
      <value>/app/hadoop/tmp</value>
    </property>

    <property>
      <name>fs.default.name</name>
      <value>hdfs://localhost:54310</value>
    </property>
  </configuration>
```

Logo após, vamos alterar o arquivo mapred-site.xml que está no endereço /usr/local/hadoop/etc/hadoop/mapred-site.xml.template! Não esqueça que você deve colocar o seguinte código ENTRE as tags <configuration> </configuration>

Lembrando que você deve clicar com o botão direitor do mouse e ir na opção 'ABRIR COMO ADMINISTRADOR'


```bash
  <configuration>
    <property>
      <name> mapred.job.tracker </name>
      <value> localhost:54311 </value>
    </property>
  </configuration>
```

Agora, vamos alterar o arquivo hdfs-site.xml que está no endereço usr/local/hadoop/etc/hadoop/hdfs-site.xml! Não esqueça que você deve colocar o seguinte código ENTRE as tags <configuration> 
  
  Lembrando que você deve clicar com o botão direitor do mouse e ir na opção 'ABRIR COMO ADMINISTRADOR'

  
```bash
  <configuration>
    <property>
      <name> dfs.replication </name>
      <value> 1 </value>
    </property>
  </configuration>
```

### 📌 Passo 13: FORMANDO O HDFS

Para formatar o sistema de arquivos distribuido - HDFS, você deverá aplicar o seguinte comando no seu terminal!

```bash
/usr/local/hadooṕ/bin/hadoop/namenode -format
```

A saída do comando deve ser a seguinte:

```bash
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

21/04/02 18:15:16 INFO namenode.NameNode: STARTUP_MSG: 
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = gabriel-tuk/127.0.1.1
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 2.7.3
```

### 📌 Passo 14: INICIANDO OS SERVIÇOS HADOOP


```bash
/usr/local/hadooṕ/bin/hadoop/sbin/star-all.sh
```

A saída deve ser a seguinte:

```bash
Starting namenodes on [localhost]
localhost: starting namenode, logging to /usr/local/hadoop/logs/hadoop-root-namenode-gabriel-tuk.out
localhost: starting datanode, logging to /usr/local/hadoop/logs/hadoop-root-datanode-gabriel-tuk.out
Starting secondary namenodes [0.0.0.0]
The authenticity of host '0.0.0.0 (0.0.0.0)' can't be established.
ECDSA key fingerprint is SHA256:zCoTX0sbPIug7X/QC6fW54qOMuV+WNtKJNrKWfomUck.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
0.0.0.0: Warning: Permanently added '0.0.0.0' (ECDSA) to the list of known hosts.
0.0.0.0: starting secondarynamenode, logging to /usr/local/hadoop/logs/hadoop-root-secondarynamenode-gabriel-tuk.out
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by org.apache.hadoop.security.authentication.util.KerberosUtil (file:/usr/local/hadoop/share/hadoop/common/lib/hadoop-auth-2.7.3.jar) to method sun.security.krb5.Config.getInstance()
WARNING: Please consider reporting this to the maintainers of org.apache.hadoop.security.authentication.util.KerberosUtil
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
starting yarn daemons
starting resourcemanager, logging to /usr/local/hadoop/logs/yarn-root-resourcemanager-gabriel-tuk.out
localhost: starting nodemanager, logging to /usr/local/hadoop/logs/yarn-root-nodemanager-gabriel-tuk.out
```

