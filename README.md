<<<<<<< HEAD
# Imagens Pré-prontas de Ferramentas Utilizando o Docker
Este guia fornece instruções para implantação, gerenciamento e limpeza de ambientes de desenvolvimento utilizando o docker.
---
## Áreas e suas imagens
* ### Multi-Plataforma (Dart/Flutter)
  * ### Dart <br>
    Dart é uma linguagem de programação moderna, orientada a objetos, desenvolvida pelo Google, otimizada para criar aplicativos rápidos em múltiplas plataformas (web, mobile, desktop, servidor).
     ```
    # Specify the Dart SDK base image version using dart:<version> (ex: dart:3.10)
    FROM dart:stable AS build

    # Resolve app dependencies.
    WORKDIR /app
    COPY pubspec.* ./
    RUN dart pub get

    # Copy app source code and AOT compile it.
    COPY . .
    # Ensure packages are still up-to-date if anything has changed
    RUN dart pub get --offline
    RUN dart build cli --target bin/server.dart -o output

    # Build minimal serving image from AOT-compiled `/server` and required system
    # libraries and configuration files stored in `/runtime/` from the build stage.
    FROM scratch
    COPY --from=build /runtime/ /
    COPY --from=build /app/output/bundle/ /app/

    # Start server.
    EXPOSE 8080
    CMD ["/app/bin/server"]
     ```
     #### Criar Server App, Buildar e Rodar
      `dart create -t server-shelf myserver` <br>
      `docker build -t dart-server .` <br>
      `docker run -it --rm -p 8080:8080 --name myserver dart-server`
  * ### Flutter (Imagem não oficial)<br>
    Flutter
    é um kit de ferramentas de interface de usuário (UI) de código aberto do Google para criar aplicativos bonitos e compilados nativamente para dispositivos móveis (iOS, Android), web, desktop (Windows, macOS, Linux) e embarcados, a partir de uma única base de código.
     ```
    # Use a base image with Flutter and Android SDK pre-installed
    FROM cirrusci/flutter:latest

    # Set the working directory
    WORKDIR /app

    # Copy the project files into the container
    COPY . .

    # Fetch dependencies
    RUN flutter pub get

    # Build the Flutter web application (replace "web" with "android" or "ios" as needed, though iOS build requires specific environments)
    RUN flutter build web

    # Optional: Use a lightweight server to serve the build output
    # FROM nginx:alpine AS runner
    # COPY --from=build /app/build/web /usr/share/nginx/html
    # EXPOSE 80
    # CMD ["nginx", "-g", "daemon off;"]

     ```
     #### Buildar e Rodar
      `docker build -t my_flutter_app .` <br>
      `docker run -p 8080:80 my_flutter_app`
* ### Backend (Java, Python)
  * ### Java <br>
    Java é uma linguagem de programação amplamente utilizada para o desenvolvimento de aplicações corporativas, sistemas Android e soluções de computação em nuvem.
    #### Criação imagem:
      ```
    FROM eclipse-temurin:25
    RUN mkdir /opt/app
    COPY japp.jar /opt/app
    CMD ["java", "-jar", "/opt/app/japp.jar"]
      ```
    #### Buildar e Rodar
      `docker build -t japp .` <br>
      `docker run -it --rm japp`
   * ### Python <br>
      Python é uma linguagem de programação de alto nível, interpretada e de propósito geral, conhecida por sua sintaxe clara e legível.
      #### Criação imagem:
        ```
      FROM python:3

      WORKDIR /usr/src/app

      COPY requirements.txt ./
      RUN pip install --no-cache-dir -r requirements.txt

      COPY . .

      CMD [ "python", "./your-daemon-or-script.py" ]
        ```
     #### Buildar e Rodar
      `docker build -t my-python-app .` <br>
      `docker run -it --rm --name my-running-app my-python-app`
* ### Database (Postgresql, Mysql, MariaDB, H2)
  * ### Postgresql <br>
    O postgresql é um sistema de banco de dados relacional de objetos focado em extensibilidade e conformidade com padrões SQL.<br>
    ### Operações de ciclo de vida:
    #### Criação e execução:
      `docker run --name postgres-db -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=adminpass -e POSTGRES_DB=main_db -p 5432:5432 -d postgres:latest`
    #### Verificar Status / Logs
      `docker ps -f "name=postgres-db"` <br>
      `docker logs -f postgres-db`
    #### Interrupção e Reinicialização
      `docker stop postgres-db` <br>
      `docker start postgres-db`
    #### Remoção Completa (Container e Imagem)
      `docker rm -f postgres-db` <br>
      `docker rmi postgres:latest`
  * ### Mysql <br>
    O Mysql é o banco de dados relacional de código aberto mais popular do mundo, amplamente utilizado em aplicações web.<br>
    ### Operações de ciclo de vida:
    #### Criação e execução:
      `docker run --name mysql-db -e MYSQL_ROOT_PASSWORD=rootpass -e MYSQL_DATABASE=app_db -p 3306:3306 -d mysql:latest`
    #### Verificar Status / Logs
      `docker ps -f "name=mysql-db"` <br>
      `docker logs -f mysql-db`
    #### Interrupção e Reinicialização
      `docker stop mysql-db` <br>
      `docker start mysql-db`
    #### Acesso direto (CLI)
      `docker exec -it mysql-db mysql -u root -p`
    #### Remoção Completa (Container e Imagem)
      `docker rm -f mysql-db` <br>
      `docker rmi mysql:latest`
  * ### Mariadb <br>
    O Mariadb é um fork do Mysql, desenvolvido pela comunidade e projetado para permanecer software livre, oferecendo alta compatibilidade e performance.
    ### Operações de ciclo de vida:
    #### Criação e execução:
      `docker run --name mariadb-db -e MARIADB_ROOT_PASSWORD=mariapass -e MARIADB_DATABASE=cloud_db -p 3307:3306 -d mariadb:latest`
    #### Verificar Status / Logs
      `docker ps -f "name=mariadb-db"` <br>
      `docker logs -f mariadb-db`
    #### Interrupção e Reinicialização
      `docker stop mariadb-db` <br>
      `docker start mariadb-db`
    #### Remoção Completa (Container e Imagem)
      `docker rm -f mariadb-db` <br>
      `docker rmi mariadb:latest`
  * ### H2 Database Engine <br>
    O H2 é um banco de dados escrito em java, extremamente leve e rápido, comumente usado para desenvolvimento, testes ou aplicações embarcadas.
    ### Operações de ciclo de vida:
    #### Criação e execução:
      `docker run --name h2-server -p 8082:8082 -p 9092:9092 -d oscarfonts/h2`
    #### Verificar Status / Logs
      `docker ps -f "name=h2-server"` <br>
      `docker logs -f h2-server`
    #### Interrupção e Reinicialização
      `docker stop h2-server` <br>
      `docker start h2-server`
    #### Remoção Completa (Container e Imagem)
      `docker rm -f h2-server` <br>
      `docker rmi oscarfonts/h2`
  
---
## COMANDOS GLOBAIS DE MANUTENÇÃO
| Ação | Comando | Descrição |
| :--- | :--- | :--- |
| Status Global | `docker ps -a` | Lista todos os containers e seus estados (Up, Exited). |
| Uso de Recursos | `docker stats` | Monitora consumo de CPU e Memória em tempo real. |
| Limpeza Geral | `docker system prune` | Remove containers parados e redes não utilizadas. |
| Ver Imagens | `docker images` | Lista todas as imagens baixadas localmente. |
---
# KIT DOCKER-COMPOSE PARA UTILIZAR VÁRIAS FERRAMENTAS EM UMA SÓ IMAGEM
Fornece um único kit com todas as ferramentas mencionadas anteriormente. Aviso (Caso você deseje utilizar somente uma única ferramenta, eu recomendo utilizar as imagens acima no primeiro tópico) / (Caso você queira usar imagens personalizadas, como somente java e h2, vocẽ pode editar a imagem do docker compose)
=======
# AMBIENTE DE DESENVOLVIMENTO DOCKERIZADO
Este guia fornece instruções para implantação, gerenciamento e limpeza de ambientes de desenvolvimento utilizando o docker. <br>
Essa documentação contém:
* Explicação de cada arquivo e comando
* Locais de pesquisas: Docker Hub, Pesquisas no Google, Youtube, Reddit e Gemini.

Esta documentação visa fornecer executaveis CLI (COMMAND LINE INTERFACE) e arquivos DOCKER para as seguintes áreas e suas ferramentas:
* MULTIPLATAFORMA (Dart/Flutter)
* BACKEND         (Java, Python e Dart)
* DATABASE        (Postgresql, Mysql, MariaDB, H2)

Obs: Você pode usar a imagem docker via CLI para rodar alguma ferramenta que não tem na sua máquina, ou você pode baixar diretamente, nesses casos vai do seu critério.
---
## COMANDOS GLOBAIS DE MONITORAMENTO
| Ação            | Comando                               | Descrição                                              |
| :---            | :---                                  | :---                                                   |
| Status Global   | `docker ps -a`                        | Lista todos os containers e seus estados (Up, Exited). |
| Uso de Recursos | `docker stats`                        | Monitora consumo de CPU e Memória em tempo real.       |
| Ver Imagens     | `docker images`                       | Lista todas as imagens baixadas localmente.            |
---
## COMANDOS DE CONTROLE
| AÇÃO            | COMANDO                               | DESCRIÇÃO                                              |
| :---            | :---                                  | :---                                                   |
| Parar           | `docker stop <id_ou_nome>`            | Para o container salvando oque precisa                 |
| Iniciar         | `docker start <id_ou_nome>`           | Inicia um container que foi parado                     |
| Reiniciar       | `docker restart <id_ou_nome>`         | Reinicia o container                                   |
| Matar           | `docker kill <id_ou_nome>`            | Mata a execução do container                           |
---
## COMANDOS DE LIMPEZA
| Ação            | Comando                               | Descrição                                              |
| :---            | :---                                  | :---                                                   |
| Remover         | `docker rm <id_ou_nome>`              | Remove um container parado                             |
| Deletar         | `docker rmi <id_ou_nome_da_imagem>`   | Deleta uma imagem da sua máquina                       |
| Limpeza Geral   | `docker system prune`                 | Apaga todos os containers, redes e cache não utilizados|
>>>>>>> bafd604 (feat: Criando estrutura da documentação em Docker)
