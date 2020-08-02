# Outros comandos

A gente viu os comandos `cd`, `pwd`, `ls`, `which` até agora. Vamos ver mais
alguns comandos


* `cat ARQUIVO1 ARQUIVO2 ... ARQUIVON`: Exibe o conteúdo de um arquivo. Quando
  você passa vários arquivos ele concatena o conteudo dos arquivos
* `touch ARQUIVO`: Cria um arquivo vazio ou atualiza o tempo de modificação de
  um arquivo já existente
* `cp SRC DEST`: Copia um arquivo da origem para o destino
* `mv SRC DEST`: Move um arquivo da origem para o destino. Esse comando também é
  usado para renomear arquivos.
* `grep PATTERN ARQUIVO`: Busca as linhas no `ARQUIVO` que contenham o
  `PATTERN`.
* `ps`: Exibe os processos da sessão, pra mostrar todos os processos `ps -ef` ou
  `ps aux`, o último costuma ser mais portavel.

# Redireção

As vezes você pode querer salvar a saída de um comando. Pra fazer isso você pode
usar o `>` no shell para redirecionar a saida para um arquivo. Por exemplo. Para
litar todos processos rodando `ps aux`, pra salvar a saída no arquivo
`processos.txt`:  `ps aux > processos.txt`. Se você executar isso vai perceber
que nenhuma saída é impressa no terminal, isso porque ela foi redirecionada para
o arquivo `processos.txt` se você executar `cat processos.txt` vai ver a saída
do comando anterior. Se você só os processos que contenham `python` você pode
fazer `grep python processos.txt`

# Saída padrão e Erro padrão

Quando você executa um comando e ele imprime alguma coisa no terminal existe
duas formas dele fazer isso. Ele pode usar a saída padrão, ou a saída de erro
padrão. Na literatura você vai encontrar isso como nome _standart output_  ou
_stdout_ e _startard error_ ou _stderr_. A _stderr_ é usada para reportar erros.
O nome certo pra esses caras é _stream_ são _streams_ diferentes, a tradúção
literal de _stream_ é fluxo. São fluxos diferentes com propósitos diferentes.
Quando você cria um processo, qualquer processo, normalmente esses fluxos são
representados pelos _file descriptors_ 1 (_stdout_) e 2 (_stderr_). Eu não vou
entrar no detalhe do que são _file descriptors_ você só precisa saber que 1 é
_stdout_ e 2 é _stderr_. Aproveitando o gancho 0 é a _stdin_ ou entrada padrão,
que é a stream que recebe dados, geralmente tá ligada ao teclado.

Beleza, mas na prática com isso funciona? A gente viu que pode redirecinar a
saida de um comando para um arquivo, mas por exemplo se você tentar salvar um
erro, `ls arquivo_que_nao_existe > ls-erro.txt`. Se você abrir olhar o arquivo
`ls-erro.txt` não vai ter nada dentro dele. Isso porque a mensagen de erro
impressa pelo `ls` foi enviada para a _stderr_ e não para a _stdout_ e o `>`
rediciona somente a `stdout` para o arquivo. Okay, é aqui que saber aqueles
numeros vale alguma coisa, você pode redirecionar a _stderr_ para um arquivo
usando `2> ls-erro.txt`. Viu? `2` significa `stderr`. Isso quer dizer que você
pode fazer o seguinte também `ls /tmp arquivo_que_nao_existe > ls.out 2>
ls.err` (as extensões de arquivos nos *nix não importam eu vou falar disso
depois). Dessa maneira você salvou ambos, _stdout_ e _stderr_ em arquivos
diferentes.

E se eu quiser redirecionar a _stderr_ pra _stdout_ eu posso? A resposta é sim!
E a sintaxe é '2>&1', olha o 1 e o 2 aí. Isso quer dizer, redireciona o _file
descriptor_ 2 para o _file descriptor_ 1.

E se eu quiser fazer isso e redirecionar tudo pro mesmo arquivo eu posso? Pode!
A sintaxe é feia mas é bem comum em shell scripts

`ls /tmp arquivo_que_nao_existe > ls.out_and_err 2>&1`.

Agora se você tem um comando que recebe a entrada. Por exemplo o comando `read`
lê a _stdin_ e cria uma variável com o que você digitou. Roda isso aqui por
exemplo `read AGE`, aí digita seu nome e depois `echo $AGE`, você vai ver o
resultado do que digitou. Agora executa o seguinte `echo 100000000 > saldo.txt`.
Você criou o arquivo `saldo.txt` com o conteudo `100000000`. Okay agora `read
SALDO < saldo.txt` e finalmente `echo $SALDO # -> 100000000` você acabou de
redirecionar o conteudo do arquivo `saldo.txt` para o comando `read` assim
criando a variável `SALDO` com o conteudo do mesmo.

Essa parte é bem estranhinnha do shell, mas é de suma importancia que se entenda
isso porque é a base pra combinar os comandos usando o pipe `|`

# Pipe e redireção

A grande sacada do shell script é que você pode combinar os comandos. Cada
comando é muito simples e genérico sozinho, mas quando combinados eles podem
fazer coisas bem específicas.

Pipe é o caracter `|` e o que ele faz é pegar o _stdout_ do comando a esquerda e
direcionar para a _stdin_ do comando a direita.

Lembra que a gente criou um arquivo com a lista de processos `ps -ef >
processos.txt` e depois filtrou eles por `python` com `grep python
processos.txt`. Pois bem, a gente pode fazer tudo numa tacada só com o pipe.

`ps -ef | grep python`

Ué? Mas eu não tinha que passar o arquivo para o grep? É aí que ta a sacada. A
maioria dos comandos que aceitam arquivos usam o _stdin_ caso você não passe
arquivo nenhum. Outra forma de simbolizar _stdin_ em argumentos é simplesmente
`-`, o grep funciona assim também então, `ps -ef | grep python` é equivalente a
`ps -ef | grep python -`. Mas esse `-` não é padrão, depende do comando
implementar ou não.

# Man e Help

E pra finalizar a parte 2, a gente vai falar de muitos outros comandos aqui. A
quantidade beira o infinito. O jeito mais fácil de descobrir como usar um
comando é lendo o manual dele. Geralemnte você consegue fazer isso usando o
comando `man`, por exemplo `man grep`, `man cat`, `man ps`. Infelismente
distribuir manuais dessa forma ta caindo em desuso então pode ser que você
encontre comandos que não tem um manual passivel de ser lido por linha de
comando com o `man`, mas geralmente os comandos tem um `--help` que exibe
informações de ajuda.

Além do `man`, o `info <COMMAND>` pode ser usado pra maioria dos comandos que
vem com o Linux e vai ter informações mais detalhadas que o `man`.
