# Introdução

Bash é o shell mais utilizado autualmente. Mas o que é um shell? Grosseiramente
falando shell é o software que recebe os comandos na linha de comando,
executa-os e exibe a saida.

Como tudo na vida é mais fácil entender com exemplos. Vamos por a mão na massa

# Instalação.

Se está usando Linux ou mac provavelmente você já tem Bash instalado. Você pode
executar um `bash --version` pra ver a versão. Se estiver usando Ubuntu
provavelmente deve estar usando dash. No Windows você pode usar o [Windows Layer
for Linux](https://docs.microsoft.com/pt-br/windows/wsl/install-win10) e então
dentro do Linux vai ter acesso ao bash.

* Intalando no Debian ou Ubuntu: `apt install bash`
* Archlinux: `pacman -Syu bash`
* MacOS `brew install bash`

# Rodando

Se você trabalha com desenvolvimento é possivel que esteja usando zsh ou fish.
zsh é compativel com bash, já fish não é. Quando estiver escrevendo scripts que
vão rodar em servidores você vai querer que eles sejam portaveis e provavelmente
não vai ter zsh no servidor. Boa parte do tutorial deve funcionar no zsh, mas
eu estou focando em bash por causa do scripting.

Sem mais delongas pra rodar o bash execute `bash`, bem óbvio né?

# Básico

* [Introdução](Parte_1.md)
* [Redireção, Pipes e Man](Parte_2.md)
* [Trabalhando com texto](Parte_3.md)
* [Shell scripting](Parte_4.md)

# Duvidas e sugestões

Encontrou um erro grotesco de português, ficou com alguma dúvida ou acha que ta
faltando alguma conteúdo, cria um issue que eu atualizo corrigo. PRs são bem
vindos também. 👍
