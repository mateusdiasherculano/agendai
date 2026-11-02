# Learning log

## Sessão 1 — Dev Container com Java 21 e Maven

- Imagem é a receita imutável, container é a instância em execução e feature é um módulo que instala algo por cima da imagem. O `devcontainer.json` só vale na criação do container: mudou o arquivo, precisa de *Rebuild Container*. O código da pasta montada vale na hora.
- Usei `java:1-21-bookworm` (Java 21 e Debian 12 fixos) e a feature de Java com `version: "none"` e `installMaven: true`, para não reinstalar o JDK que a imagem já traz. Extensão do Spring Boot fica para a Fase 2.
- Dentro de um container, `localhost` é o próprio container. Entre containers usa-se o nome do serviço do Compose. O Dev Container serve para a IDE rodar dentro do ambiente (autocompletar, debug, testes); o Compose serve para subir a infraestrutura.

**Próxima tarefa:** Compose com Postgres (Fase 0) e decidir, em um ADR, se o Dev Container fica separado do Compose ou dentro dele.
