# AMBIENTE DE DESENVOLVIMENTO DOCKERIZADO
Este guia fornece instruções para implantação, gerenciamento e limpeza de ambientes de desenvolvimento utilizando o docker. <br>
Essa documentação contém:
* Explicação de cada arquivo e comando
* Locais de pesquisas: Docker Hub, Pesquisas no Google, Youtube, Reddit e Gemini.

Esta documentação visa fornecer executaveis CLI (COMMAND LINE INTERFACE) e arquivos DOCKER para as seguintes áreas e suas ferramentas:
* MULTIPLATAFORMA (Flutter)
* BACKEND         (Java, Python e Dart)
* DATABASE        (Postgresql, Mysql/MariaDB, Sqlite, H2)

Obs: Você pode usar a imagem docker via CLI para rodar alguma ferramenta que não tem na sua máquina, ou você pode baixar diretamente, nesses casos vai do seu critério.
---
## COMANDOS GLOBAIS DE MONITORAMENTO
| Ação            | Comando                               | Descrição                                              |
| :---            | :---                                  | :---                                                   |
| Status Global   | `docker ps -a`                        | Lista todos os containers e seus estados (Up, Exited)  |
| Uso de Recursos | `docker stats`                        | Monitora consumo de CPU e Memória em tempo real        |
| Ver Imagens     | `docker images`                       | Lista todas as imagens baixadas localmente             |
| Ver Logs        | `docker logs -f <nome_do_container>`  | Mostra todos os logs do container em execução          |
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
---
## COMANDOS DE ACESSO AO BASH
| Ação             | Comando                                    | Descrição                                         |
| :---             | :---                                       | :---                                              |
| Acessar Bash     | `docker exec -it <nome_do_container> bash` | Abre terminal interativo bash do container        |
| Acessar SH       | `docker exec -it <nome_do_container> sh`   | Abre um terminal simples                          |
| Sair do Terminal | `exit`                                     | Sai do modo interativo e volta para sua máquina   |