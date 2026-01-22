# Multiplataforma (Flutter)

* ## Docker para Desenvolvimento CLI

  Este tópico mostra o processo de utilizar um container docker para utilizar ferramentas Multiplataforma sem precisar instalar na máquina física.

  Para rodar comandos do Flutter
  `alias d-flutter='docker run -it --rm -v "$(pwd)":/app -w /app instrumentisto/flutter:stable flutter'` <br>
  Para rodar comandos específicos do Dart
  `alias d-dart='docker run -it --rm -v "$(pwd)":/app -w /app instrumentisto/flutter:stable dart'`

  ### Como usar?
  * #### Criar um projeto
    `d-flutter create meu_app`
  * #### Baixar pacotes
    `d-flutter pub get`
  * #### Rodar testes
    `flutter test`
  * #### Analisar código
    `d-flutter analyze`

* ## Docker para Produção

  Este tópico mostra o processo de utilizar um container docker para buildar e rodar seu projeto através de um Dockerfile.

  * Criação do arquivo Dockerfile
    ```
    ############################
    #  STAGE-1: BUILD PROCESS  #
    ############################


    # Define a imagem base
    # Flutter e Dart instalados
    # Atribui apelido 'build' para captura de dados
    FROM instrumentisto/flutter:stable AS build

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
