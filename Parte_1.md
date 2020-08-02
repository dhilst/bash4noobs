# Hello world

Execute `echo hello world`. Você vai a saida "hello world"

Para executar um comando você simplesmente digita ele no terminal e da enter. O
bash vai criar um novo processo executar o comando que você passou e exibir a
saida.

# O básico de navegação

Nesse ponto a gente vai aprender como se virar na linha de comando antes de ir
pro shell scripting. Os três comandos básicos que todo mundo precisa saber são
`cd`, `ls` e `pwd`.

`pwd` vai mostrar em que diretório você ta no momento. Assim como você navega em
pastas usando o Explorer você também pode navegar usando a linha de comando.
Novamente `pwd` é o diretório corrente, na literatura é comunmente chamdo de
_CWD_ _(current working directory)_.

Para navegar entre os diretórios você usa `cd` e passa o diretório como
argumento `cd <DIRETORIO>`.  Exemplos, `cd /tmp`, `cd ~/`, `cd ..`, `cd .`.
Diferente do Windows, que tem letras do tipo `C:/`, `D:/` em sistemas derivados
do Unix só existe um diretório raiz e é sempre `/`. A partir desse diretório
você pode chegar em qualquer outro no sistema.

Para ir para o direório anterior na hirerquia você usa `..`. Então pra subir um
diretório, `cd ..`. Pra subir dois `cd ../..`. Pra subir dois diretórios e
depois entrar no diretório teste `cd ../../test`.

Já `.` significa o mesmo diretório. Confuso? Eu vou explicar. `foo/bar/.` é
igual, `foo/bar`. Então `foo/bar/./tar` é igual `foo/bar/tar`. Mas pra que
serve isso? Em alguns casos existe uma ambiguidade, e o ponto serve pra resolver
essa ambiguidade. A gente vai ver mais disso depois.

### Direferença entre caminho relativo e absoluto

Caminhos absolutos começam com `/`, então `/root`, `/etc/pki/tls/certs`,
`/var/log/nginx/errors.log`, `/var/log/../www/html` são exemplos de caminhos
absolutos. Eles partem do sempre `/` e não são influenciados pelo diretório
corrente.

Caminhos relativos dependem do diretório corrente. então
`Downloads/minecraft.jar`, `Music/ilarilarie.mp3`, `../test/testFoo.js` são
exemplos de caminhos relativos. Eles funcionam concatenando concatenando o
diretorio corrente a eles. Então se você ta em `/home/gecko`, o diretório
`Downloads/minecraft.jar` é o mesmo que `/home/gecko/Downloads/minecraft.jar`.

### Listando diretórios

Para ver o que tem nos diretórios (não é pasta!) você usa o comando `ls`,
(mnemonico para _list_).  Executa aí pra você ver o resultado. Ele vai mostra o
conteudo do diretório que você tá.

Esse é um bom momento pra introduzir flags e argumentos. Se você quiser listar o
conteúdo de um diretório sem ter que navegar até ele você pode passar o
diretório como argumento. Por exemplo, `ls /tmp` vai listar tudo o que tem no
`/tmp`.  Assim como você pode passar a pasta como argumento você também pode
passar algumas flags que mudam a saida dos comandos, no caso do `ls` você pode
usar `-l` (mnemoico pra long) pra mostrar mais informações sobre o conteudo das
pastas

Beleza, então a gente já sabe que:

* O shell executa os comandos que a gente passa pra eles e exibe a saída
* A gente pode navegar entre os diretórios com `cd`
* Pode ver qual o diretório corrente com `pwd`
* E pode ver o que tem nos diretórios com o comando `ls`

# Variavies de ambiente (Environment Variables)

Variaveis de ambiente são mais o menos como variáveis em linguagem de programção
mas algumas são um pouco mágicas, o termo correto é "especiais", mas mágica soa
mais legal.

Pra criar uma variável você faz `<VARIAVEL>=<VALOR>`, exemplo `NOME=Daniel`. Pra
expandir uma variável você usa sifrão, ou dolar por exemplo: `$NOME`. Se você
executar isso provavelmente vai tomar um erro `Daniel: command not found`, isso
porque o bash expandiu `$NOME` pra `Daniel` e tentou executar `Daniel` como se
fosse um comando. Pra mostrar o conteúdo de uma variavel você pode usar `echo
$NOME` por exemplo.

Não é necessário que as variávies sejam em caixa alta, mas é uma conveção bem
difundida, eu recomendo que você a siga, mas `nome=Hilst` vai funcionar da
mesma forma, mas como o shell é _case sensitive_ essas são duas variávies
diferentes. `echo $NOME $nome # -> Daniel Hilst`.

Vamos ver algumas das variávies especiais agora,

## PATH

Essa é de long a variável mais importante

Quando o você digita um comando, por exemplo `ls` o bash executa o ls, mas o que
isso significa? Esses comandos são pequenos programas, geralmente feitos em C
por uma galera muito louca das antiga. O que é importante a gente entender é que
cada comando é um programa! Ele existe em algum lugar do filesystem. O shell
precisa encontrar esse comando na hora de executar ele, uma vez encontrado ele
cria um novo processo, executa esse comando e imprime a saída dele no terminal.

Não se preocupa com a parte do _cria um novo processo_ por enquanto. Ela vai ser
mais importate depois, mas agora foca no _precisa encontrar esse comando_. Pra
fazer isso o shell usa a variável `PATH`. Essa variavel é uma lista de caminhos
separados por dois pontos, pra ver o conteudo: `echo $PATH`

Quando você digita `echo hello world` o shell vai em cada diretório dessa lista
e procura pelo comando que você chamou. Você pode chamar o comando passando o
caminho completo também! Vamos ver como.

Existe um comando que mostra onde outros estão, o nome dele é `which`, então
execute `which ls`

No meu caso deu `/bin/ls`, pode ser que no seu caso mude. De qualquer forma o
interessante é que você pode executar o mesmo comando passando o caminho
completo dele, por exemplo no meu caso `/bin/ls` é igual só `ls`. Se o comando
que você quiser executar estiver no diretório corrente você pode usar
`./comando`.

Pra extender o PATH, ou seja, pra adicionar outro diretório para que o shell
busque os comandos nesse diretório também você pode fazer `PATH=$PATH:/home/gecko/meu_diretorio`

Você tá basicamente concatenando setando o PATH pro conteudo dele mesmo, mais o
diretório que você quer que que ele busque. Outro jeito de fazer isso, mas menos
recomendado é `PATH=/home/gecko/meu_diretorio:$PATH`. A diferença do primeiro a
ordem que os diretórios vão ser buscados. Se você colocar no começo o shell vai
procurar no `/home/gecko/meu_diretório` primeiro, se tiver um `ls` dentro do
`/home/gecko/meu_diretorio` quando você executar `ls` o shell vai executar o
`ls` de dentro do `/home/gecko/meu_diretorio` e não dentro do `/usr/bin` como é
esperado. Por isso é comum a gente colocar no final do PATH e nunca do começo.

## PWD

Essa variável é equivalente ao comando `pwd`, ela expande para o diretório
corrente, Lembra que eu falei do PATH, a gente precisou colocar
`/home/gecko/meu_diretorio` na hora de extender o PATH, isso porque se você
colocar um caminho relativo no PATH, ele só vai achar os comandos nesse
diretório quando você estiver no diretório certo, exemplo, se você fizer

`PATH=$PATH:meu_diretorio`, e dentro do `./meu_diretorio` tiver um comando,
`meu_comando`. Se você mudar de diretório `cd /tmp`  e tentar executar
`meu_commando` ele vai procurar em `/tmp/meu_diretorio/meu_comando` e obviamente
não vai funcionar.

Com essa variável `$PWD` você não consegue setar o caminho absoluto sem ter que
digitar ele todo, ex `PATH=$PATH:$PWD/meu_diretorio`

----

Existem outras variávies especiais, `LD_LIBRARY_PATH`, `MAN_PATH`, `SHELL`,
`TERM`, mas elas não são tão importante como a `PATH`. O principal é saber que
elas existem e que mudam o comportamento do shell. Isso pode ser bem estranho no
começo e foi o motivo pra eu citar isso logo de cara.

# Export

Lembra que eu falei que quando você executa um comando o shell cria um novo
processo. Isso é uma noção importante porque quando um processo novo é executado
ele pode herdar ou não as variávies de ambiente.

Quando você executa um comando ele vai ler as variávies de ambiente do seu
shell, a gente pode chamar isso de _a sua sessão_. Entretanto, se esse comando
abrir outro shell, ele só vai ver as suas variavies de ambiente se você exportar
elas, pra exportar uma variável você  faz `export VARIAVEL`, ou numa tacada só,
pra declara exportando `export VARIAVEL=valor`

Vamos fazer uns testes, crie uma variável `NOME=Daniel`. `echo $NOME # ->
Daniel`, (to usando `-> XXX` pra representar a saída). Agora vamos executar
outro shell, e dentro dele exibir a nossa variável, a gente pode fazer isso
usando `bash -c 'echo $NOME'`. As aspas simples são importantes aqui, eu já
explico porque. Se você executou o comando não deve ter saído nada, isso porque
o novo bash não ve a variável `NOME`. Agora se você fizer `export NOME` e então
repetir o comando interior você vai ver a saída. É isso o que export faz.

# Caracteres espcias, aspas simples, duplas e ordem de expansão

Outra armadilha do shell é conhecer a ordem de expanção. O shell vai expandir
variáveis e caracteres especiais antes de passar eles pros comandos. Antes de
entrar no detalhe, a gente ainda não falou ainda desses caracteres especias.

Você provavelmente viu, e se não viu vai ver ~ e * quando lidando com shell
script. Esses caracteres tem um signficado especial pro shell.

`~` expande pra home, e é o mesmo que a variável `$HOME`.

`*` expande pra todos os arquivos no diretório corrente, pode ser combinado
com um diretório. Exemplo `ls -l Downloads/*`

Assim como variávies esses caras expandem antes de serem passados pro comando,
então quando você faz `ls ~/` o shell expande para `ls /home/geckos` (no meu
caso) e executa `ls /home/geckos`. Quando você faz `echo *` ele expande para
`echo arquivo1 arquivo2 arquivo3 ... arquivoN` onde `arquivoX` são os arquivos
no diretório corrente e só então executa o `echo`. Isso é importante porque pode
causar alguns comportamentos inesperados, por exemplo `ssh root@host -- comando`
serve pra conectar na maquina `host` como `root` e executar o comando. Mas se
você fizer `ssh root@host -- ls *`, você espera que ele execute `ls *` na
maquina `host` como usuário `root` mas o que ele vai fazer é exapndir o `*` na
sua maquina e só então ir até a outra maquina e executar `ls
arquivo_na_sua_maquina1 arquivo_na_sua_maquina2 arquivo_na_sua_maquina3` na
maquina `host`. Isso pode ser bem confuso no começo.

Já a diferença entre aspas simples e duplas é bem simples, aspas simples não
expandem variávies, são literais, aspas duplas expandem variávies'. Mas ambas
não expandem os caracteres `*`, `~` e outros carateres especiáis.

Isso explica porque era necessário aspas simples no `bash -c 'echo $NOME'`, do
contrário você estaria executando `bash -c echo Daniel`, ou seja, expandindo a
variável nome, *antes* de executar o novo bash.

# Conclusão da primeira parte

Essa introdução foi meio longa mas eu quis cobrir o que eu acredito ser as
armadilhas mais comuns do shell. Existe outras mas elas são menos frequentes.
Agora a gente pode ir pra parte mais legal.

* A gente sabe que os comandos shells são programas, que ele procura no PATH,
executa e mostra a saida.
* Que a gente pode criar as nossas proprias variáveis de ambiente.
* Que a gente pode extender o PATH para encontrar nossos próprios comandos
* Pode exportar elas pra que sejam herdadas em outras sessões de shell.
* Que o shell expande os caracteres especiais e variáveis antes de executar os
  comandos, ou seja que passa os valores expandidos pra eles
* Que pode evitar essa expansão com aspas simples, e para caracteres especiais
  duplas
* O que é diretório corrente, caminho absoluto e relativo, e como navegar nos
  diretórios.

