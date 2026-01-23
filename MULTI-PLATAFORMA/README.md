# Multiplataforma (Dart e Flutter)
Este guia documenta o processo de utilização de um container docker para isolar o ambiente de desenvolvimento, permitindo criar, testar e buildar sem precisar instalar as ferramentas na máquina física.

## 1. Configuração do Ambiente CLI (Aliases)
Será criada uma alias para criar um apelido ao comando e facilitar o uso no terminal (`.bashrc` ou `zshrc`).
Obs: Será utilizada a imagem (NÃO OFICIAL) cirrusci/flutter:stable por ser a mais estável e mantida para ambientes de CI/CD e Docker.
```
# Responsável por executar comandos do Flutter com o seu usuário atual para evitar problemas de permissão
alias d-flutter='docker run -it --rm -u $(id -u):$(id -g) -e HOME=/tmp -v "$(pwd)":/app -w /app cirrusci/flutter:stable bash -c "git config --global --add safe.directory /sdks/flutter && flutter \$@"'

# Executa comandos puramente Dart
alias d-dart='docker run -it --rm -u $(id -u):$(id -g) -v "$(pwd)":/app -w /app cirrusci/flutter:stable dart'

# Abre um terminal interativo (Bash) dentro do ambiente Flutter
alias d-shell='docker run -it --rm -u $(id -u):$(id -g) -v "$(pwd)":/app -w /app cirrusci/flutter:stable bash'
```
Explicação do principais parâmetros:
* `-u $(id -u):$(id -g)`: Faz com que os arquivos criados pelo Docker pertença ao seu usuário da máquina e não ao root do container.
* `-v "$(pwd)":/app`: Faz o mapeamento da pasta atual da sua máquina para o diretório /app do container.
* `--rm`: Garante que o container seja deletado ao finalizar o comando, economizando espaço em disco.

## 2. Desenvolvendo com VS Code (Extensão: Dev Containers)
É recomendado instalar a extensão `Dev Containers` para ter acesso a suporte da IDE (Autocomplete, Refatoração e Linting).

### Configuração do Projeto
* #### 1. Crie uma pasta chamada `.devcontainer` na raiz do seu projeto.
* #### 2. Crie o arquivo `devcontainer.json`.
* #### 3. Coloque este código abaixo dentro do arquivo `devcontainer.json`:
```
{
  "name": "Flutter Docker Environment",
  "image": "cirrusci/flutter:stable",
  "customizations": {
    "vscode": {
      "extensions": [
        "dart-code.flutter",
        "dart-code.dart-code"
      ]
    }
  },
  "remoteUser": "root",
  "workspaceFolder": "/workspace",
  "mounts": [
    "source=${localWorkspaceFolder},target=/workspace,type=bind"
  ]
}
```
### Como utilizar:
* Abra o projeto no Vs Code.
* Clique no ícone da extensão do `Dev Containers` e selecione "Reopen in Container".
* Vs Code agora opera de dentro do container com todas as ferramentas instaladas.

## 3. Comandos de Gerenciamento e Uso
| Ação | Comando |
| :--- | :--- |
| Criar novo app | `d-flutter create <nome_do_app>` |
| Baixar dependências | `d-flutter pub get` |
| Limpar build/cache | `d-flutter clean` |
| Verificar saúde do SDK | `d-flutter doctor` |
| Corrigir permissões | `sudo chown -R $USER:$USER .` |

## 4. Rodando o App (Flutter WEB)
Por o Docker criar container de forma isolada, a visualização precisará ser feita via Web Server. <br>
`docker run -it --rm -p 8080:8080 -v "$(pwd)":/app -w /app cirrusci/flutter:stable flutter run -d web-server --web-hostname 0.0.0.0 --web-port 8080`. <br>
Após executar o comando anterior, acesse em seu navegador: `http://localhost:8080`

## 5. Docker para para Produção (Dockerfile)
Este Dockerfile foi adaptado para gerar builds leves utilizando Multi-stage Build. Obs: Estes comentários são para explicar o passo a passo, mas pode ficar a vontade para remove-los. Todo comentário começa com este símbolo (`#`)

```
############################
#  STAGE-1: BUILD PROCESS  #
############################


# Define a imagem base
# Flutter e Dart instalados
# Atribui apelido 'build' para captura de dados
FROM cirrusci/flutter:stable AS build

# Cria e entra na pasta /app dentro do container
# Todas as atividades acontecerão neste diretório
WORKDIR /app

# Copia todos os arquivos onde está o Dockerfile
# Move para dentro da pasta /app do container
COPY . .

# Baixa as dependências do projeto
# Compila o app para a plataforma web
# Resultado gerado em build/web
RUN flutter pub get && flutter build web

#############################
#  STAGE-2: RUNNER PROCESS  #
#############################

# Inicia uma imagem Nginx
# Servidor web
# Minimalista
FROM nginx:alpine

# Vai até o estágio anterior (--from=build)
# Pega apenas a pasta onde o site foi compilado
# Copia para a pasta padrão do Nginx
# Deixa o SDK do Flutter e o código-fonte para trás
COPY --from=build /app/build/web /usr/share/nginx/html

# Informa a porta de conexão do container
EXPOSE 80

# Define o comando que mantém o servidor rodando
# daemon off para que o container continue ativo
CMD ["nginx","-g","daemon off;"]
```

## 6. Ciclo de Vida e Manutenção
* Para containers travados: `docker stop $(docker ps -q)`.
* Atualizar o Flutter: `docker pull cirrusci/flutter:stable`.
* Limpar Lixo do Docker: `docker system prune` (remove containers parados e imagens sem uso).