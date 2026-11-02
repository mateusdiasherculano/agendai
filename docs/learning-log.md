# Learning log

## Sessão 1 — Dev Container com Java 21 e Maven

- Imagem é a receita imutável, container é a instância em execução e feature é um módulo que instala algo por cima da imagem. O `devcontainer.json` só vale na criação do container: mudou o arquivo, precisa de *Rebuild Container*. O código da pasta montada vale na hora.
- Usei `java:1-21-bookworm` (Java 21 e Debian 12 fixos) e a feature de Java com `version: "none"` e `installMaven: true`, para não reinstalar o JDK que a imagem já traz. Extensão do Spring Boot fica para a Fase 2.
- Dentro de um container, `localhost` é o próprio container. Entre containers usa-se o nome do serviço do Compose. O Dev Container serve para a IDE rodar dentro do ambiente (autocompletar, debug, testes); o Compose serve para subir a infraestrutura.

**Próxima tarefa:** Compose com Postgres (Fase 0) e decidir, em um ADR, se o Dev Container fica separado do Compose ou dentro dele.

## Sessão 2 — Postgres com Docker Compose

- Containers só se enxergam pelo nome do serviço se estiverem na **mesma rede**. O Compose cria a rede para os serviços do arquivo, e o Dev Container (criado pelo VS Code) fica fora dela. Decidi pela opção B (Dev Container como serviço do Compose), a detalhar no ADR.
- O volume nomeado precisa apontar para o diretório certo de dados, que muda por versão: no Postgres 18 é `/var/lib/postgresql` (nas anteriores, `.../data`). Errar o caminho é um bug silencioso. Provei com um teste: criei uma tabela, rodei `down` (sem `-v`), subi de novo e a linha continuou lá.
- `POSTGRES_USER`/`POSTGRES_DB` só valem na primeira inicialização do volume, e o usuário criado ainda é superusuário. O `.env` alimenta o Compose, mas só o que está em `environment` chega ao container. O `docker` não existe no Dev Container, então o Compose roda no terminal do Mac. Porta ocupada (`5432` do outro projeto): troquei só o lado esquerdo do mapeamento (`5433:5432`).

**Próxima tarefa:** escrever o ADR (Dev Container separado vs. dentro do Compose) e mover o Dev Container para o `docker-compose.yml`.
