# Introdução

Finalmente a gente chegou no shell scripting. Aqui eu to cobrindo o bash, mas
qualquer coisa que você pode fazer no terminal usando seu shell preferido pode
ser scriptável.

Um script nada mais é que um arquivo com uma lista de comandos a serem
executadas dentro dele e a primeira linha que fala quem é o interpretador que vai
executar essa lista.

Vamos começar simples

Crie o seguinte arquivo, `hello.sh`

```
#!/bin/bash

echo Hello World
```

Agora de permissão de execução pra ele `chmod +x hello.sh`. E finalmente
execute: `./hello.sh # -> Hello World`. Yay, você fez seu primeiro shell
script. Como você pôde ver não tem nada de especial nele a não ser a primeira
linha. O nome dela é _shebang_ a propósito.

Quando você executa um comando, que é um arquivo de texto, e começa com essa
linha o shell pensa _"Hmm, vou pegar esse arquivo e passar pro
comando depois do #!_. E é literalmente isso que ele faz.

Você pode criar o script abaixo pra provar isso pra você mesmo.

```
#!/bin/cat

Ihaa eu sou foda!
```

Resumindo executar um `arquivo.sh` com `#!/bin/bash` no começo equivale a rodar
`bash arquivo.sh`, isso pode ser util se você quiser fazer ferramentas que funcionem
como linguagens de script, tudo o que você precisa fazer é um comando que le os
comandos de um arquivo e usar a shebang pra executa-lo com um script.

# Shell scripting

Se você vai fazer scripts você vai querer fazer coisas como, if, while, for,
definir funcões etc. São coisas bem simples de fazer em shell só muda um pouco
da sintaxe. O restante desse capítulo vai focar nessas construções, nos
capitulos seguintes a gente vai ver mais receitas de com resolver problemas
específicos combinando comandos. Shell scripting é basicamente a junção dessas
instruções de controle com a combinação dos comandos. Eu não vou cobrir o bash
inteiro aqui até porque tem muita coisa.  Eu deixei um link para o manual do
bash que é bem completo, praticamente tudo que eu aprendi foi do manual e até
hoje considero a melhor fonte pra aprender bash.

## Condicionais e status de saída

A primeira coisa a se entender é o que é uma condição o shell. Toda vez que
você executa um comando no shell ele sai com algum resultado, que pode ser
sucesso (0) ou falha (literalmente qualquer valor menos 0). Esse valor fica
salvo na variável $?.

Por exemplo, execute o comando `mais_10mil_na_conta`, seria muito bom se um
comando assim existisse, mas infelizmente ele não existe. Se você executar `echo
$?` provavelmente você vai receber 127 como resposta. Essa variável foi
confiugrada pelo shell, já que o comando não existe ele não tem com _"sair com
falha"_. Agora tenta esse `ls arquivo_que_nao_existe` e então `echo $?`, deve
printar 1 na saída, nesse caso o `ls` saiu com valor 1 (erro) e o shell pegou
esse valor e colocou na variável `$?`.

Mas o que isso tem a ver com condicionais? Bom basicamente tudo! Porque o shell
vai considerar qualquer saída com 0 (sucesso) como verdadeiro e qualquer outra
coisa como falso. Isso é meio confuso no começo mas quer dizer que você pode
usar qualquer comando como a condição de um if ou um while e ele só vai entrar
no if ou permanecer no while enquanto o comando sair com sucesso (0).

## if

A sintaxe do `if` do bash é a seguinte

```
if COND; then
  ...
elif COND; then
  ...
else
  ...
fi
```

`fi`? Sim `fi`, `if` ao contrário, eu não fiz as regras, eu só sigo elas. Na
parte da condição você pode colocar quaquer comando, como eu disse anteriormente.

Então, por exemplo:

```
if grep -q daniel /etc/passwd; then
  echo "Existe um usuário chamado daniel"
fi
```

_O -q é pra pro grep não imprimir nada quando encontrar o padrão, a gente não
quer o nosso script printe `daniel::/home/daniel:/bin/bash` ou algo do tipo_

Além de usar comandos você pode usar expressões de comparação no lugar do `COND`
envolvidas em colchetes, `[ EXPR ]`, ou ainda `[[ EXPR ]]`. A diferença do
primeiro e do segundo é que o segundo é específico do bash enquanto o primeiro
é o definido pelo padrão POSIX. Se você sabe que vai rodar o script em um bash
(se a tiver `#!/bin/bash` na sheebang) então é seguro usar `[[ EXPR ]]`. Na
minha experiência raramente você vai precisar de um script que seja portável o
a ponto de exigir o `[ EXPR ]`, por isso eu recomendo o uso da forma com
colchetes duplos. Pra testar se um arquivo existe por exemplo

```
if [[ -e /arquivo ]]; then
  ...
fi
```

Pra testar um valor por igualdade

```
if [[ "$NOME" == "Daniel" ]]; then
  echo "é Daniel"
fi
```

Pra testar um valor numérico

```
if [[ $IDADE -eq 33 ]]; then
  echo "ta idoso hein!"
fi
```

Tudo isso você pode encontrar no [manual](https://linux.die.net/man/1/bash).
Busque por `[[`. Se você estiver usando o comando man use `/` para procurar.

Referência: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Conditional-Constructs

## while e for

O while é parecido com o if

```
while COND; do
  ...
done
```

Para interromper o loop, pode usar `break`, e pra pular pra próxima iteração
pode usar `continue`.

Um comando que eu uso muito é

```
while ! ssh root@host; do
  sleep 1;
done
```

Aqui `!` é uma negação, inverte o valor de `$?`. Esse comando pode ser lido
basicamente como, equanto não conseguir conectar na maquina `host` como `root`,
espere um segundo, e tente de novo. Imagina que você rebootou a maquina e quer
conectar nela, em vez de ficar executando o comando de novo e de novo e de novo
eu uso esse tipo de truque e espero a maquina voltar.

Já a sintaxe do `for` é a seguinte

```
for I in LIST_EXPR; do
  ...
done
```

Eu usei `LIST_EXPR` pra representar uma expressão que expande para uma lista.
Por padrão qualquer coisa separa por espaços é interpretado como uma lista,
por exemplo

```
for I in Daniel Alex Bruno; do
  echo "Olá $I"
done
```

Isso vai printar

```
Olá Daniel
Olá Alex
Olá Bruno
```

Pra declarar um array em bash você usa a seguinte sintaxe `V=(A B C)`. E pra
expandir todos elementos `$V[*]`

```
NOMES=(Daniel Alex Bruno)
for N in $NOMES[*]; do
  echo "Olá $N"
done
```

Pra expandir só um arugmento você pode usar `${NOME[N]}` por exemplo pra obter o
primeiro valor do array `${NOMES[0]}`.

_PS: No zsh os arrays começam em 1, no bash em 0!_

No bash é possivel expandir numeros também usando a syntaxe {N..M}, inclusive.
Execute `echo {0..10}`. O for entender bem isso, é possivel fazer loops dessa
forma

```
for i in {1..3}; do
  echo "$i"
done
```

Bom aqui tem outra varável magica do Bash, a IFS, define o separador usado na
hora de iterar no for. Você pode mudar ela pra `:` por exemplo

```
IFS=: for p in $PATH; do echo $p; done
```

Referência: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Looping-Constructs

## case

O `case` é o parente do `switch` encontrado em linguagens C-like.

```
case EXPR in;
  opt1)
    ...
    ;;
  opt2)
    ...
    ;;
  *)
    ...
esac
```

Aqui `esac` é `case` ao contrário. Assim com o `if/fi`. No lugar da expressão
você pode colocar uma variavél.

```
case $NOME in
  Daniel)
    echo "Eae Daniel"
    ;;
  Alex)
    echo "Fala Alex"
    ;;
  *)
    echo "Te conheço?"
    ;;
esac
```

Os valores em `X)` pode conter `*` pra como se fosse um padrão, ex:


```
case $NOME in
  A*)
    echo "Seu nome começa com A"
    ;;
  B)
    echo "Seu nome começa com B"
    ;;
  *)
    echo "Seu nome não começa com A nem com B"
    ;;
esac
```

## Funções

Pra definir uma função a sintaxe é

```
NOME() {
  ...
}
```

Ou

```
function NOME {
  ...
}
```

Os argumentos das funções são as variávies `$1`, `$2`, `$3` e assim por diante.
Por exemplo

```
hello() {
  echo "Olá $1, você tem $2 anos"
}

hello Daniel 33 # -> Olá Daniel, você tem 33 anos
```

Da pra ver que pra chamar uma função é só passar os argumentos separados por
espaço. Isso cria uma ambiquidade, como você vai saber se é um comando um uma
função. Bom você pode usar `type X`

```
$ type hello
hello is a function
hello ()
{
    echo "Olá $1, você tem $2 anos"
}
```

Outra pegadinha é que se você não passar nenhuma variável ele não vai reclamar,
as variaveis vão simplesmente ficar vazias dentro da função.

```
hello #-> Olá , você tem  anos
```

Isso é bem ruim. Pra descobrir se uma variavel tá vazia você pode usar `[[ -z
$V ]]`, vamos corrigir a nossa função.

```
hello() {
  if [[ -z "$1" ]]; then
    echo "Você não tem nome não?"
    return
  fi

  if [[ -z "$2" ]]; then
    echo "Você não tem idade não?"
    return
  fi

  echo "Olá $1, você tem $2 anos"
}
```

Nesse caso deu pra ver também que a gente pode usar `return` pra retornar de uma
função. Você pode retornar um valor se quiser também.

```
um() {
  return 1
}
```

E esse valor vai ser atribuido à `$?`, ou seja pode ser usado como condições

```
maior() {
  if [[ $1 -ge 18 ]]; then
    return 0
  else
    return 1
  fi
}

if maior 18; then echo "Já pode combrar birita"; fi
```

A ideia de que 0 é verdadeiro é meio contra intuitiva, mas você se acostuma com
o tempo.

Outra variável importante é `$@` ela expande para todas os argumentdos da função
chamada, por exeplo

```
foo() {
  echo $@ | rev
}

foo 1 2 3 # -> 3 2 1
```

Para criar variaveis locais dentro de funções use a keyword local

```
foo() {
  local mylocalvar=1
  echo $mylocalvar
}
```

Variaveis locais não seguem a convenção de serem maiúsculas.

Referência: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Shell-Functions

## Combinadores && (AND) e || (OR)

O operador `A && B` só vai executar `B` se `A` for verdadeiro, ou seja, se A
sair com sucesso. Isso permite a gente escrever codigos bem enxutos, as vezes
até ilegíveis.

```
grep -q daniel /etc/passwd && echo "Tem um usuário chamado daniel"
```

Já o `A || B` (OR) é o oposto

```
grep -q daniel /etc/passwd || echo "NÃO tem um usuário chamado daniel"
```

No shell `:` quer dizer true, então você pode fazer o seguinte

```
: && echo verdadeiro
! : || echo falso
```

Lembra que o `!` é negação? Ele inverte o resultado, se `:` é verdadeiro `! :` é
falso.

## Agrupando comandos

Você pode agrupar comandos usando chaves `{ ...;  }`.

Exemplo

```
: && { echo "Verdadeiro"; echo "Também conhecido como True"; }
```

Mais uma vez isso permite você escrever scripts bem enxutos. Geralmente usar
`if` deixa o codigo mais legível.


## Substituição de comandos

Essa é uma das partes mais legais. Você pode usar ``comando`` ou `$(comando)`
pra substituir a saida de um comando. Por exemplo

```
echo "Agora são $(date +%H) horas e $(date +%M) minutos"
```

Pensa nisso como uma interpolação. Você pode usar isso pra pegar o resultado de
um comando numa variável por exemplo

```
NOW="$(date)"
```

Pra concatenar o caminho corrente no nome de um arquivo `$(pwd)/meu_arquivo`.

Iterar sobre os arquivos em `/tmp`, `for i in $(ls /tmp); do echo $i; done`

Referência: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Command-Substitution

## Mais variáveis especiais

Algumas variávies são especiais dentro de um script. Assim funções você pode
receber os argumentos com `$1`, `$2`, `$3`, ... `$n`. A `$0` guarda o nome do
script. `$#` a quantidade de arugmentos passados pro script. `$@` expande pra todos
os argumentos passados pro script ou função separados por espaço.

Além dessas existem outras. `$$` guarda o PID do bash que ta executando.

## Process substitution

Vamos supor que você tenha um comando que le um arquivo, e que você queira passar a
saída de um outro comando para o primeiro. A primeira coisa que a gente pensa é usar
pipe `comando1 | comando2` mas pra isso o `comando2` precisa ler da entrada padrão
o que acontece se ele não suportar isso?

Bom você pode redirecionar pra um arquivo e usar o arquivo como argumento, `comando1 > saida; comando2 saida`,
mas tem um jeito melhor que isso.

```
comando2 <(comando1)
```

A sintaxe `<(comando)` executa o `comando` a cria um pseudo arquivo que que pode ser passado
como parametro para outros comandos, vamos fazer um teste.

```
cat <(ps aux)
```

Pra ver o "arquivo" que ele gera você pode usar ls

```
ls <(ps) # -> /proc/self/fd/11
```

Referência: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Process-Substitution

## Expansões de parametros

_Atenção: Isso é uma funcionalidade exclisiva do bash, não vai funcionar no zsh_

É possivel fazer algumas mágicas com bash durante a expansão das variáveis

```
FOO="foo:bar:tar"

# Converter para maiúsculas
echo ${FOO^^} # -> FOO:BAR:TAR

# Só a primeira maiúscula
echo ${FOO^^} # -> Foo:bar:tar

# Remove tudo antes do primeiro :
echo ${FOO#*:} # -> bar:tar

# Remove tudo antes do ulitmo :
echo ${FOO##*:} # -> tar

Remove tudo antes do primeiro :
echo ${FOO%%:*} # -> foo
```

A explicação aqui é `#` serve pra remover um prefixo e `%` pra remover suffixo.
Quando duplicados eles ficam _eager_, e removem até o ultimo ou primeiro padrão
encontrado. O `*` fica do lado do que vai ser removido, é equivalente ao `*.txt`
por exemplo num comando, o `*` vai dar match em tudo antes de `.txt`.

Isso é util pra pegar o nome do diretorio corrente por examplo sem o caminho `echo ${PWD##*/}`
Ou pra pegar caminho do diretorio pai ao diretorio corrente `echo ${PWD%/*}`

Ainda é possivel substituir padrões

```
FOO="foo:bar:tar"

echo ${FOO/oo/00} # -> f00:bar:tar
```

Definir variaveis valores padrão

```
echo ${BAR:-123}
```

Referência: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Shell-Parameter-Expansion

## Expressões aritiméticas

Para executar expressões aritiméticas no bash usa-se a sintaxe $((expression))
por exemplo `echo $((1 + 1 / 1 * 1))`

Copiado descaradamente do manual:

Operador|Descrição
----|----
id++ id--|Pvariable post-increment and post-decrement
- +|unary minus and plus
++id --id|variable pre-increment and pre-decrement
! ~|logical and bitwise negation
**|exponentiation
* / %|multiplication, division, remainder
+ -|addition, subtraction
<< >>|left and right bitwise shifts

Referência: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Shell-Arithmetic

## `source` e carregando variáveis de outros arquivos

Você já deve ter executado `source algum_arquivo.sh` ou `. algum_arquivo.sh`.
Qual a diferença de executar um script assim `. script.sh` ou assim
`./script.sh`?

Basicamente, quando você executa com `./` o bash cria um novo processo e
executa o script pra você, isso é o mesmo que a gente tem visto sempre, isso
quer dizer que as variáveis e os comandos no `script.sh` não vão podem editar,
carregar ou fazer nada na sua sessão.

Quando você executa um script assim `. script.sh` você está executando o script
no mesmo contexto que o seu shell. Diferente do que a gente viu antes, nenhum
processo novo é criado. Isso quer dizer que o `script.sh` pode declarar
variáveis, editar, declarar funções etc.

Esse tipo de recurso é muito usado para carregar variáveis de ambiente na
sessão, e agora você sabe porque isso funciona e `./script.sh` nunca
funcionaria.

Outro uso comum é para carregar funções, é comum colocar todas as suas funções
num arquivo separado e executar `source myfunctions.sh` em varios scritps
diferentes para fazer uso dessas funções.

Referência: https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Bourne-Shell-Builtins
