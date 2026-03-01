Este laboratório cobriu os pilares operacionais de um administrador de sistemas Red Hat, focando na eficiência da linha de comando e na gestão de recursos do sistema.

**Tópicos explorados:**

- **Navegação e Estrutura**: Uso de caminhos absolutos vs. relativos e criação de árvores de diretórios complexas com 
    
- **Produtividade no Shell**: Criação em massa de arquivos usando expansão de chaves `{ }` e manipulação com metacaracteres `*`. 
    
- **Localização de Recursos**: Uso avançado do comando `find` com filtros de nome, tamanho e tempo de modificação. 
    
- **Processamento de Texto**: Filtragem de arquivos de configuração com `grep`, uso de expressões regulares básicas (`^`, `$`) e encadeamento de comandos com Pipes (`|`). 
    
- **Persistência de Dados**: Redirecionamento de saída (`>`) para criação de relatórios e documentação. 
    
- **Segurança e Posse**: Ajuste de permissões numéricas (`chmod`) e alteração de donos e grupos (`chown`). 
    
- **Atalhos do Sistema**: Criação de links simbólicos (`ln -s`) para otimização de fluxo de trabalho.

---

Como você criaria um diretório chamado `desafio_rhcsa` dentro da sua pasta pessoal (home), garantindo que o comando funcione independentemente de em qual pasta você esteja agora?

**Caminho absoluto:** indica **o caminho completo do arquivo desde a raiz do sistema**. Sempre começa com `/`.  

```
owerbt@localhost:/etc$ mkdir /home/owerbt/desafio_rhcsa

owerbt@localhost:~$ ls /home/owerbt/
 desafio_rhcsa   Desktop     Downloads     Music      Public   rhcsa-practice-lab   teste1                Videos
 desafio_Rhcsa   Documents  'hola mundo'   Pictures   rhcsa    Templates            testesegunrança.txt
```

**Caminho relativo:** indica **o caminho do arquivo a partir da pasta atual**. Não começa com `/`

Agora, vamos ao próximo nível de eficiência. Imagine que você precisa criar uma estrutura de pastas para organizar seus estudos, como `~/rhcsa/projetos/scripts`, mas as pastas intermediárias (`rhcsa` e `projetos`) ainda não existem.

**Qual "flag" (opção) do comando `mkdir` permite criar toda essa árvore de diretórios de uma vez só, sem que o sistema retorne um erro dizendo que o diretório pai não existe?**

`owerbt@localhost:/$ mkdir -p /home/owerbt/rhcsa/projetos/scripts`

**`-p`** → cria **toda a hierarquia de pastas** se ainda não existir.

Usando sintaxi de par de chaves para expandir sequencia de valores , permitindo criar multiplis arquivos.

```
owerbt@localhost:~$ touch /home/owerbt/rhcsa/projetos/scripts/script{1..3}.sh

owerbt@localhost:~$ ls -l /home/owerbt/rhcsa/projetos/scripts/

total 0
-rw-r--r--. 1 owerbt owerbt 0 fev 28 22:12 script1.sh
-rw-r--r--. 1 owerbt owerbt 0 fev 28 22:12 script2.sh
-rw-r--r--. 1 owerbt owerbt 0 fev 28 22:12 script3.sh

owerbt@localhost:~$ 

```

Agora que temos nossos arquivos, vamos praticar a organização e o uso de **metacaracteres (wildcards)**. Wildcards são símbolos que representam outros caracteres, facilitando a manipulação de grupos de arquivos. Os mais comuns são:

- `*`: Representa zero ou mais caracteres.
    
- `?`: Representa exatamente um caractere.
    

Imagine que você recebeu a tarefa de criar um backup desses scripts em uma nova pasta.

**O desafio agora é:**

1. Crie um diretório chamado `backups` dentro de `~/rhcsa/`.
    
2. Copie todos os arquivos que terminam com `.sh` da pasta `scripts` para a pasta `backups`.
    

**Qual comando você usaria para copiar todos os arquivos `.sh` de uma vez só, sem listar um por um, utilizando o wildcard `*`?**

```
owerbt@localhost:~$ mkdir /home/owerbt/rhcsa/backups ; cp /home/owerbt/rhcsa/projetos/scripts/*.sh /home/owerbt/rhcsa/backups

owerbt@localhost:~$ ls -ll /home/owerbt/rhcsa/backups/
total 0
-rw-r--r--. 1 owerbt owerbt 0 fev 28 22:27 script1.sh
-rw-r--r--. 1 owerbt owerbt 0 fev 28 22:27 script2.sh
-rw-r--r--. 1 owerbt owerbt 0 fev 28 22:27 script3.sh

```

- `;` → separa dois comandos para rodar um depois do outro
    
- `cp` → copia arquivos ou pastas de um lugar para outro
    
- `*` → coringa que representa todos os arquivos que correspondem ao padrão

Agora que temos arquivos espalhados por diferentes diretórios, um administrador precisa saber localizá-los rapidamente. O comando **`find`** é uma das ferramentas mais versáteis do Red Hat para isso.

A estrutura básica dele é: `find [onde_procurar] [critério] [o_que_procurar]`

**Cenário**: Imagine que você recebeu um sistema e não sabe onde os scripts de backup foram colocados. **Como você usaria o comando `find` para procurar por qualquer arquivo que termine com `.sh` dentro do diretório `/home/owerbt/`?**

```
owerbt@localhost:~$ find /home/owerbt -name "*.sh"
/home/owerbt/rhcsa/projetos/scripts/script1.sh
/home/owerbt/rhcsa/projetos/scripts/script2.sh
/home/owerbt/rhcsa/projetos/scripts/script3.sh
/home/owerbt/rhcsa/backups/script1.sh
/home/owerbt/rhcsa/backups/script2.sh
/home/owerbt/rhcsa/backups/script3.sh
/home/owerbt/script2.sh
/home/owerbt/script3.sh
owerbt@localhost:~$ 
```

- `/home/owerbt` → pasta onde vai procurar
    
- `-name "*.sh"` → encontra arquivos cujo nome termina com `.sh`
    
- `*` → coringa para qualquer coisa antes do `.sh`

Vamos testar uma variação comum em provas. Imagine que você precisa encontrar todos os arquivos dentro de `/home/owerbt/rhcsa` que tenham **mais de 1 MB** de tamanho.

Considerando que o parâmetro para tamanho é `-size` e o sufixo para Megabytes é `M` (e usamos `+` para "maior que"), **como você montaria esse comando de busca?**

```
owerbt@localhost:/$ find /home/owerbt//rhcsa -type f -size +1M
owerbt@localhost:/$ 

```

Além do tamanho, outro filtro essencial é o de **tempo**, muito usado para encontrar logs antigos ou arquivos modificados recentemente. O argumento `-mtime` (modification time) é usado para dias.

**Vamos testar sua lógica:** Se você precisasse encontrar arquivos que foram modificados há **menos de 2 dias** dentro de `/home/owerbt/rhcsa`, qual sinal você usaria antes do número 2 no argumento `-mtime`? 

```
owerbt@localhost:/$ find /home/owerbt//rhcsa -type f -mtime -2
/home/owerbt//rhcsa/projetos/scripts/script1.sh
/home/owerbt//rhcsa/projetos/scripts/script2.sh
/home/owerbt//rhcsa/projetos/scripts/script3.sh
/home/owerbt//rhcsa/backups/script1.sh
/home/owerbt//rhcsa/backups/script2.sh
/home/owerbt//rhcsa/backups/script3.sh
owerbt@localhost:/$ 

```

Agora, vamos avançar para um comando que você usará constantemente para ler configurações e logs: o **`grep`**. Ele serve para buscar textos específicos dentro de arquivos.

Imagine que você precisa verificar se o usuário `owerbt` está presente no arquivo de usuários do sistema, que fica em `/etc/passwd`.

**Como você usaria o `grep` para filtrar apenas a linha que contém a palavra `owerbt` dentro do arquivo `/etc/passwd`?**

```
owerbt@localhost:/$ grep owerbt /
afs/   boot/  etc/   lib/   media/ opt/   root/  sbin/  sys/   usr/   
bin/   dev/   home/  lib64/ mnt/   proc/  run/   srv/   tmp/   var/   
owerbt@localhost:/$ grep owerbt /etc/passwd
owerbt:x:1000:1000:ower baca:/home/owerbt:/bin/bash
owerbt@localhost:/$ 

```

```
owerbt@localhost:/$ grep -v "#" /etc/login.defs 
```

**Saida extensa donde foram identificados espaços vazios** 

Para um administrador Red Hat, ser capaz de limpar totalmente a saída é essencial. No Linux, o símbolo **`^`** representa o início da linha e o **`$`** representa o fim da linha. Portanto, um padrão que busca "início da linha seguido imediatamente pelo fim da linha" (`^$`) identifica linhas vazias.

Podemos combinar filtros usando o caractere **`|`** (pipe), que passa a saída de um comando para a entrada do próximo.

**Tente o seguinte desafio:** Como você alteraria seu comando para que ele use o `grep -v` duas vezes (conectados por um `|`) para remover as linhas com `#` **e também** remover as linhas totalmente vazias?

_(Dica: O comando ficaria algo como `grep -v "#" /etc/login.defs | grep -v "padrão_para_linha_vazia"`).

```
owerbt@localhost:/$ grep -v "#" /etc/login.defs | grep -v "^$"
MAIL_DIR	/var/spool/mail
UMASK		022
HOME_MODE	0700
PASS_MAX_DAYS	99999
PASS_MIN_DAYS	0
PASS_MIN_LEN	8
PASS_WARN_AGE	7
UID_MIN                  1000
UID_MAX                 60000
SYS_UID_MIN               201
SYS_UID_MAX               999
SUB_UID_MIN		   524288
SUB_UID_MAX		600100000
SUB_UID_COUNT		    65536
GID_MIN                  1000
GID_MAX                 60000
SYS_GID_MIN               201
SYS_GID_MAX               999
SUB_GID_MIN		   524288
SUB_GID_MAX		600100000
SUB_GID_COUNT		    65536
PASS_CHANGE_TRIES	5
PASS_ALWAYS_WARN	yes
ENCRYPT_METHOD YESCRYPT
USERGROUPS_ENAB yes
CREATE_HOME	yes
HMAC_CRYPTO_ALGO SHA512
owerbt@localhost:/$ 

```

- `grep` → procura texto dentro de arquivos
    
- `-v` → inverte a busca, mostra linhas que **não** correspondem
    
- `"#"` → padrão, neste caso linhas com comentário
    
- `"^$"` → linhas vazias
    
- `|` → envia saída de um comando para o próximo

Agora que você gerou essa lista útil, um administrador muitas vezes precisa salvar esse resultado para documentação ou auditoria. No Linux, usamos o **redirecionamento de saída**.

Existem dois operadores principais:

- **`>`**: Cria um arquivo novo com a saída do comando (ou sobrescreve se o arquivo já existir).
    
- **`>>`**: Adiciona a saída ao final de um arquivo já existente (**append**), sem apagar o que já estava lá.
    

**Vamos praticar:** Como você faria para executar esse mesmo comando que você acabou de usar, mas em vez de mostrar o resultado na tela, salvar a saída em um arquivo chamado `config_limpa.txt` dentro da sua pasta `~/rhcsa/backups/`?

```
root@localhost:/# grep -v "#" /etc/login.defs | grep -v "^$" > /home/owerbt/rhcsa/backups/config_limpa.txt

root@localhost:/# echo /home/owerbt//rhcsa/backups/config_limpa.txt 
/home/owerbt//rhcsa/backups/config_limpa.txt
root@localhost:/# cat /home/owerbt//rhcsa/backups/config_limpa.txt
 
MAIL_DIR	/var/spool/mail
UMASK		022
HOME_MODE	0700
PASS_MAX_DAYS	99999
PASS_MIN_DAYS	0
PASS_MIN_LEN	8
PASS_WARN_AGE	7
UID_MIN                  1000
UID_MAX                 60000
SYS_UID_MIN               201
SYS_UID_MAX               999
SUB_UID_MIN		   524288
SUB_UID_MAX		600100000
SUB_UID_COUNT		    65536
GID_MIN                  1000
GID_MAX                 60000
SYS_GID_MIN               201
SYS_GID_MAX               999
SUB_GID_MIN		   524288
SUB_GID_MAX		600100000
SUB_GID_COUNT		    65536
PASS_CHANGE_TRIES	5
PASS_ALWAYS_WARN	yes
ENCRYPT_METHOD YESCRYPT
USERGROUPS_ENAB yes
CREATE_HOME	yes
HMAC_CRYPTO_ALGO SHA512
root@localhost:/# 
```

- `grep` → procura texto dentro de arquivos
    
- `-v` → mostra linhas que **não** correspondem ao padrão
    
- `"#"` → filtra comentários
    
- `"^$"` → filtra linhas vazias
    
- `|` → envia a saída de um comando para outro
    
- `>` → salva a saída em um arquivo

**Vamos verificar os "metadados" desse arquivo para entender quem tem o controle sobre ele:**

Execute o comando `ls -l` apontando para o caminho do arquivo `config_limpa.txt`. Olhando para a saída, quais nomes aparecem nas colunas de **dono** (owner) e **grupo**?

```
owerbt@localhost:/$ ls -l /home/owerbt/rhcsa/backups/config_limpa.txt 
-rw-r--r--. 1 root root 625 Feb 28 23:32 /home/owerbt/rhcsa/backups/config_limpa.txt
owerbt@localhost:/$ 

```

O dono do arquivo e grupo é o root

isso cria um problema prático: se o usuário comum `owerbt` tentar editar ou excluir esse arquivo agora, ele poderá receber um erro de **"Permissão negada"**, mesmo o arquivo estando dentro da sua própria pasta pessoal. No exame **RHCSA**, garantir que os arquivos tenham os donos e permissões corretos é um requisito frequente.

Para ajustar isso, usamos o comando **`chown`** (_change owner_). A sintaxe básica é: `chown [usuário]:[grupo] [arquivo]`

**Como você usaria o `chown` para alterar o dono e o grupo do arquivo `config_limpa.txt` de volta para `owerbt`?**

```
owerbt@localhost:/$ chown owerbt:owerbt /home/owerbt/rhcsa/backups/config_limpa.txt 

chown: changing ownership of '/home/owerbt/rhcsa/backups/config_limpa.txt': Operation not permitted

owerbt@localhost:/$ sudo su
[sudo] password for owerbt: 
root@localhost:/# chown owerbt:owerbt /home/owerbt/rhcsa/backups/config_limpa.txt 

root@localhost:/# ls -l /home/owerbt/rhcsa/backups/config_limpa.txt 
-rw-r--r--. 1 owerbt owerbt 625 Feb 28 23:32 /home/owerbt/rhcsa/backups/config_limpa.txt
root@localhost:/# 
```

`ls -l` = Lista longa de arquivos

Olhando para o início da saída do seu comando `ls -l`:

`-rw-r--r--`

Esses 10 caracteres representam as **permissões** do arquivo:

1. **O primeiro caractere** (`-`): Indica o tipo (neste caso, um arquivo comum).
    
2. **Os próximos três** (`rw-`): Permissões do **Dono** (leitura e escrita).
    
3. **Os três do meio** (`r--`): Permissões do **Grupo** (apenas leitura).
    
4. **Os três últimos** (`r--`): Permissões de **Outros** (apenas leitura).
    

No exame **RHCSA**, voc

ê precisará alterar essas permissões usando o comando `chmod`. Uma das formas mais comuns é a **numérica**, onde:

- **4** = Leitura (r) 
    
- **2** = Escrita (w)
    
- **1** = Execução (x) 
    

Para definir as permissões, somamos os valores de cada bloco. Por exemplo, `rw-` é $4 + 2 = 6$.

**Desafio**: Como você usaria o comando `chmod` para que o **Dono** tenha permissão total (leitura, escrita e execução), enquanto o **Grupo** e **Outros** não tenham permissão nenhuma (zero acesso)?

- `chmod` → muda **permissões de arquivos ou pastas** no Linux

```
owerbt@localhost:/$ ls -l /home/owerbt/rhcsa/backups/config_limpa.txt 
-rw-r--r--. 1 owerbt owerbt 625 Feb 28 23:32 /home/owerbt/rhcsa/backups/config_limpa.txt
owerbt@localhost:/$ chmod 700 /home/owerbt/rhcsa/backups/config_limpa.txt 
owerbt@localhost:/$ ls -l /home/owerbt/rhcsa/backups/config_limpa.txt 
-rwx------. 1 owerbt owerbt 625 Feb 28 23:32 /home/owerbt/rhcsa/backups/config_limpa.txt
owerbt@localhost:/$ 
```

