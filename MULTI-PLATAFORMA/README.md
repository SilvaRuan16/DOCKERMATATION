# Multiplataforma (Dart e Flutter)

* ## Docker para Desenvolvimento CLI

  Este tópico mostra o processo de utilizar um container docker para utilizar ferramentas Multiplataforma sem precisar instalar na máquina física.

* ### Rodar o docker do flutter <br>
  `alias d-flutter='docker run -it --rm -v "$(pwd)":/app -w /app instrumentisto/flutter:stable flutter'` <br>

  #### Explicação do comando
| TRECHO                                  | FUNÇÃO                                                                                                                                                       |
| :---                                    | :---                                                                                                                                                         |
| `alias d-flutter=`                      | Define o nome do atalho. Ao digitar d-flutter no terminal, ele irá executar todo o código entre aspas.                                                       |
| `docker run`                            | Comando básico do Docker para criar e iniciar um novo container.                                                                                             |
| `-it`                                   | Abreviação de -i (interativo) e -t (tty). Permite interação com o processo dentro do container.                                                              |
| `--rm`                                  | Remove o container assim que o comando terminar de ser executado.                                                                                            |
| `-v "$(pwd)":/app`                      | Cria um volume. Ele mapeia sua pasta atual na máquina física para a pasta /app dentro do container. Assim, o código escrito na máquina aparece no container. |
| `-w /app`                               | Define o Working Directory. Inicia a execução do Docker já dentro da pasta /app.                                                                             |
| `instrumentisto/flutter:stable`         | Imagem que será utilizada (Não oficial). O Docker vai baixar esse ambiente pré-configurado com o Flutter estável instalado.                                  |
| `flutter`                               | É o comando final que será executado dentro do container.                                                                                                    |

* ### Rodar o docker do dart puro (Operações específicas) <br>
  `alias d-dart='docker run -it --rm -v "$(pwd)":/app -w /app instrumentisto/flutter:stable dart'`

  #### Explicação do comando
| TRECHO                          | FUNÇÃO                                                                                                            |
| :---                            | :---                                                                                                              |
| `alias d-dart`                  | Cria o apelido d-dart. Sempre que executar o Dart via Docker, usará esse comando.                                 |
| `docker run`                    | Inicia a criação de um novo container.                                                                            |
| `-it`                           | Permite que você veja a saída do terminal e digite comandos.                                                      |
| `--rm`                          | Autolimpeza, garante que o container seja deletado depois do uso.                                                 |
| `-v "$(pwd)":/app`              | Conecta a pasta atual ao diretório /app dentro do Docker.                                                         |
| `-w /app`                       | Define que o terminal do Docker já deve nascer dentro da pasta /app.                                              |
| `instrumentisto/flutter:stable` | É a imagem que será utilizada. O Docker vai baixar esse ambiente pré-configurado com o Flutter estável instalado. |
| `dart`                          | Diz ao container para chamar o executável dart.                                                                   |

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
