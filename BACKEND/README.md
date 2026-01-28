# Ferramentas Dockerizadas para Backend
Esta parte do guia mostrará como criar ambientes dockerizados para desenvolvimento, build e execução de suas aplicações, sendo as ferramentas mencionadas: Java e Python.

## Java
Java é uma linguagem de programação orientada a objetos fortemente tipada, suas principais caracteristicas são sua segurança e robustez, desempenho, independente de plataforma e como mencionada anteriormente, orientação a objetos.

## 1. Configuração do ambiente CLI
Será criada uma função para facilitar a execução dos programas no terminal (`.bashrc` ou `zshrc`).
```
# Função para utilizar no dia a dia
d-java() {
  docker run -it --rm -v "$(pwd)":/app -v "$HOME/.m2":/root/.m2 -w /app eclipse-temurin:21-jdk bash
}
```
### Explicação dos trechos do comando docker:
* `-it`: Mantém o terminal interativo para utilizar o bash.
* `--rm`: Remove o container de forma automática assim que o usuário sair dele, mantendo o seu sistema limpo.
* `-v "$(pwd)":/app`: Monta sua pasta atual no diretório `/app` do contaiener.
* `-v "$HOME/.m2":/root/.m2`: Compartilha o cache do maven para não precisar baixar todas as dependências do zero todas as vezes.
* `-w /app`: Define o diretório de trabalho inicial como `/app`.
* `eclipse-temurin:21-jdk`: Utiliza uma imagem oficial do OpenJDK 21 pela distribuição Temurin.

Após isso, você poderá executar esse comando toda vez que for escrito `d-java`.
