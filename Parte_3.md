# Trabalhando com texto

Nesse capítulo a gente vai ver alguns comandos para tratar texto. Geralmente em
shell é bem comum a gente usar comandos pra transformar o texto até conseguir
extrar a informação que a gente que.

Pra conseguir isso a gente precisa filtrar e transformar o texto até chegar no
resultado que a gente espera.

Eu vou usar um comentario com uma seta depois do comando pra mostar a saída que é esprada
por exemplo `echo 1 2 3 # -> 1 2 3`, `echo $((1 + 1)) # -> 2`


## Filtrando verticalmente com grep

O grep é um dos comandos mais comuns. Com ele você vai conseguir filrar somente
as linhas que contenham tal padrão.

```
COMANDO | grep PATTERN
```

Exemplo

```
echo "foo\nboo\nbar\ntar" | grep oo # -> foo
                                    # -> boo
```

Para inverter, ou seja, filtrar só as linhas que não tenham o padrão você pode
usar o `-v`

```
echo "foo\nboo\nbar\ntar" | grep -v oo # -> bar
                                       # -> tar
```

## Substituindo com tr

Pra fazer substituições simples você pode usar o `tr`. Lembra do PATH, a gente
exibia ele com `echo` mas era horrivel de ler, então tenta o seguinte

```
echo foo:bar | tr ':' '\n' # -> foo
                           # -> bar
```

Isso é bem util pra mostrar o PATH de um jeito mais legivel

```
echo $PATH | tr ':' '\n'
```

## Subtituindo com sed

Para coisas mais complexas você pode usar `sed` pra fazer uma substituição usando regex

```
echo foo bar | sed 's/o/0/g' # -> f00 bar
echo foo bar | sed 's/^./4/' # -> 400 bar
```

## Cortando colunas com cut

O cut separa a entrada em colunas e você pode selecionar só as que você quer eliminando o resto. Por exemplo

```
echo foo bar tar zar | cut -f1   -d' ' # -> foo
echo foo bar tar zar | cut -f2-  -d' ' # -> bar tar zar
echo foo bar tar zar | cut -f2-3 -d' ' # -> bar tar
```

O `-f` é  de _field_ e `-d` de _delimiter_.

## Invertendo a entrada com rev

```
echo 1 2 3 | rev # -> 3 2 1
```

## Filtrando com AWK

AWK, é uma linguagem de programção a parte. Geralmente eu uso somente o básico
dela. Quando os outros comandos não dão conta.

Por exempo, no exeplo anterior com `cut`, se a gente quisesse pegar a ultima
columa a gente podeira usar `echo foo bar tar zar |  cut -f4 -d ' '`. Mas e se o
numero de colunas variar?

O jeito mais simples de fazer isso é usando `rev` pra reverter as colunas.

```
echo "foo bar tar zar\ntick tack toe" | rev | cut -f1 -d' ' | rev # -> zar
                                                                       toe
```

Mas com AWK a gente pode fazer numa comando só

```
echo "foo bar tar zar\ntick tack toe" | awk '{print $NF}' # -> zar
                                                          # -> toe
```

Nesse caso `$NF` é a ultima coluna. Pra saber mais sobre AWK eu recomendo
[essa](https://www.gnu.org/software/gawk/manual/gawk.html) página.

Ná prática a gente acaba decorando algumas receitas. Você pode elimitar um grep
também.

```
echo "foo bar tar zar\ntick tack toe" | grep foo | awk '{print $NF}' # -> zar
```

Pode virar

```
echo "foo bar tar zar\ntick tack toe" |  awk '/foo/ {print $NF}' # -> zar

```

Em questão de performance, raramente isso vai fazer alguma diferença, mas menos
comandos geralmente é mais bom conhecer as alternativas.
