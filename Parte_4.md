# Introdução

Finalmente a gente chegou no shell scripting. Aqui eu to cobrindo o bash, mas
qualquer coisa que você pode fazer no terminal usando seu shell preferido pode
ser scriptável.

Um script nada mais é que um arquivo com uma lista de comandos a serem
executadas dentro dele e a primeira linha que fala quem é o comando que vai
executar essa linha.

Vamos começar simples

Crie o seguinte arquivo, `hello.sh`


```
#!/bin/bash

echo Hello World
```

Agora de permissão de execução pra ele `chmod +x hello.sh`. E finalmente
execute: `./hello.sh # -> Hello Wolrd`. Yay, você fez seu primeiro shell
script. Como você pôde ver não tem nada de especial nele a não ser a primeira
linha. O nome dela é _shebang_ a propósito.

Quando você executa um comando, que é um arquivo de texto, e começa com essa linha o shell pensa _"Hmm, vou pegar o conteudo desse script e passar pro comando depois do #!_. E é literalmente isso que ele faz.

Você pode criar o script abaixo pra provar isso pra você mesmo.

```
#!/bin/cat

Ihaa eu sou foda!
```

# Shell scripting

Se você vai fazer scripts você vai querer fazer coisas como, if, while, for, definir funcões etc. São coisas bem simples de fazer em shell só muda um pouco da sintaxe.

## Condicionais e status de saída

A primeira coisa a se entender é o que é uma condição o shell. Toda vez que
você executa um comando no shell ele sai com algum resultado, que pode ser
sucesso (0) ou falha (literalmente qualquer valor menos 0). Esse valor fica
salvo na variável $?.

Por exemplo, execute o comando `mais_10mil_na_conta`, seria muito bom se um
comando assim existisse, mas infelismente ele não existe. Se você exeutar `echo
$?` provavelmente você vai receber 127 como resposta. Essa variavel foi
confiugrada pelo shell, já que o comando não existe ele não tem com _"sair com
falha"_. Agora tenta esse `ls arquivo_que_nao_existe` e então `echo $?`, deve
printar 1 na saída, nesse caso o `ls` saiu com valor 1 (erro) e o shell pegou esse valor e colocou na variável `$?`.

Mas o que isso tem a ver com condicionais? Bom basicamente tudo! Porque o shell vai considerar qualquer saída com 0 (sucesso) como verdadeiro e qualquer outra coisa como falso. Isso é meio confuso no começo mas quer dizer que você pode usar qualquer comando como a condição de um if ou um while e ele só vai entrar no if ou permanecer no while enquanto o comando sair com sucesso (0).

## if

A sintaxe do `if` do batch é a seguinte

```
if COND; then
  ...
elif COND; then
  ...
else
  ...
fi
```

`fi`? Sim `fi`, `if` ao contrário. Na parte da condição você pode colocar quaquer comando

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
minha experiência raramente você vai precisar de um script que seja portavel o
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

## while

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
espere um segundo, e tente de novo. Imagina que voê rebootou a maquina e quer
conectar nela, em vez de ficar executando o comando de novo e de novo e de novo
eu uso esse tipo de truque e espero a maquina voltar.

## for

A syntaxe do `for` é a seguinde

```
for I in LIST_EXPR; do
  ...
done
```

Eu usei `LIST_EXPR` pra representar uma expressão que expande para uma lista.
Por padrão qualquer coisa separa por espações é interpretado como uma lista,
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

Fácil de entender né? Mas se você fizer

```
NOMES=Daniel Alex Bruno
for N in $NOMES; do
  echo "Olá $N"
done
```

Não vai funcionar, ele vai imprimir "Olá Daniel Alex Bruno", em vez de 3 linhas.
Isso porque a variavel NOME é uma string e não um array. Pra declarar um array
em bash você usa a seguinte sintaxe `V=(A B C)`. E pra expandir todos elementos
`$V[*]`

```
NOMES=(Daniel Alex Bruno)
for N in $NOMES[*]; do
  echo "Olá $N"
done
```

Pra expandir só um arugmento você pode usar `${NOME[!]}` por exemplo pra obter o
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
as variaveis vao simplesmente ficar vazias dentro da função.

```
hello #-> Olá , você tem  anos
```

Isso é bem ruim. Pra descobrir se uma variavel tá vazia você pode usar `[[ -z
$V ]]`, vamos corrigir a nossa função.

```
hello() {
  if [[ -z $1 ]]; then
    echo "Você não tem nome não?"
    return
  fi

  if [[ -z $2 ]]; then
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


## Substituição

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

## Mais variáveis especiais

Algumas variávies são especiais dentro de um script. Assim funções você pode
receber os argumentos com `$1`, `$2`, `$3`, ... `$n`. A `$0` guarda o nome do
script. `$#` a quantidade de arugmentos passados pro script.

Além dessas existem outras. `$$` guarda o PID do bash que ta executando.

