# Agendaí — Documento do Produto

Versão de 01/11/2026 · Mateus Dias Herculano

## Visão do produto

O Agendaí (nome provisório) é um sistema de agendamento online para pequenos negócios que atendem com hora marcada (barbearias, manicures, designers de sobrancelha, cabeleireiras): o cliente marca sozinho pelo celular e o profissional para de organizar a agenda pelo WhatsApp.

**O problema.** Hoje o profissional atende e responde mensagem ao mesmo tempo. Isso gera três dores:

- **Tempo perdido:** cada horário marcado custa várias mensagens de ida e volta.
- **Conflito de horário:** dois clientes no mesmo horário, ou um horário esquecido.
- **Falta sem aviso (no-show):** o cliente não aparece, a cadeira fica vazia e o dinheiro daquele horário se perde.

**Para quem.** Negócios de 1 a 5 profissionais que atendem com hora marcada. É um projeto de estudo: o público de referência são barbearias e estúdios de unha e sobrancelha.

**Proposta de valor.**

- Para o profissional: agenda organizada sem precisar responder mensagem, menos faltas e visão do dia na palma da mão.
- Para o cliente: marcar em menos de 1 minuto, a qualquer hora, e receber lembrete antes do horário.

**Como saberemos que deu certo.** O MVP publicado e funcionando com dados de demonstração, o código no GitHub e uma demo em vídeo no LinkedIn.

## Personas e jornadas

O sistema tem dois lados: o profissional, que configura e acompanha a agenda pelo painel Angular, e o cliente, que só quer marcar o horário.

| Persona | Quem é | O que quer | Onde usa |
| --- | --- | --- | --- |
| Dono do negócio | Profissional que também administra; cadastra serviços, equipe e horários | Ver o dia, controlar faltas, não perder cliente | Painel Angular (celular e PC) |
| Profissional | Funcionário ou parceiro que atende | Saber quem é o próximo cliente e bloquear horários | Painel Angular |
| Cliente | Quem é atendido | Marcar rápido, lembrar do horário, remarcar sem constrangimento | Página pública de agendamento (depois, app Flutter) |

**Jornada do cliente (caminho feliz)**

1. Abre o link do estabelecimento, recebido no WhatsApp ou no Instagram.
2. Escolhe o serviço (ex.: corte + barba, 45 min; ou manicure + pedicure, 1h30).
3. Escolhe o profissional ou "qualquer um".
4. Vê só os horários realmente livres e escolhe um.
5. Informa nome e telefone e confirma.
6. Recebe a confirmação e um lembrete 24h antes.

**Jornada do profissional (dia típico)**

1. Abre o painel de manhã e vê a agenda do dia.
2. Bloqueia um horário para almoço ou compromisso.
3. Marca cada atendimento como concluído ou como falta.
4. No fim do mês, vê quantos atendimentos fez e quantas faltas teve.

## Escopo do MVP

O MVP é o mínimo que um profissional conseguiria usar no dia a dia: agenda sem conflito, agendamento pelo cliente e lembrete. Todo o resto espera.

**Entra no MVP**

- [ ] Cadastro do estabelecimento, dos profissionais e dos serviços (nome, duração e preço)
- [ ] Horário de funcionamento por profissional, com intervalos e folgas
- [ ] Cálculo de horários disponíveis
- [ ] Agendamento feito pelo cliente, sem conta: só nome e telefone
- [ ] Cancelamento e remarcação respeitando a antecedência mínima
- [ ] Marcar atendimento como concluído ou falta
- [ ] Lembrete automático antes do horário (primeiro por e-mail; WhatsApp depois)
- [ ] Login do profissional (Keycloak)

**Fica de fora (por enquanto)**

- Pagamento online e Pix
- Programa de fidelidade e cupons
- Controle de estoque de produtos
- Comissão de profissionais e relatórios financeiros
- Integração oficial com o WhatsApp (tem custo e burocracia; vem depois do MVP)
- App Flutter do cliente (bônus no final)

Regra do CEO: qualquer ideia nova vai para a lista "Fica de fora" até o MVP estar no ar. É isso que impede o projeto de nunca terminar.

## Linguagem ubíqua

Na documentação e nas conversas usamos o termo em português; no código e na API, sempre o nome em inglês desta tabela. Se um termo mudar, ele muda nos dois lugares.

| Termo | Significado | No código (inglês) |
| --- | --- | --- |
| Estabelecimento | O negócio (barbearia, estúdio de unhas, salão); dono dos profissionais e serviços | `Business` |
| Profissional | Quem presta o serviço (barbeiro, manicure, designer de sobrancelha) | `Professional` |
| Serviço | O que é oferecido, com duração e preço (ex.: corte, barba, manicure, design de sobrancelha) | `ServiceOffering` |
| Expediente | Horário de trabalho de um profissional em um dia da semana | `WorkingHours` |
| Bloqueio | Intervalo em que o profissional não atende (almoço, folga, compromisso) | `BlockedPeriod` |
| Horário disponível | Janela livre calculada a partir do expediente, bloqueios e agendamentos | `AvailableSlot` |
| Agendamento | Reserva de um serviço com um profissional em data e hora | `Appointment` |
| Cliente | Quem agenda, identificado pelo telefone | `Customer` |
| Falta (no-show) | Cliente não compareceu e não cancelou | `AppointmentStatus.NO_SHOW` |
| Antecedência mínima | Tempo mínimo antes do horário para cancelar ou remarcar sem penalidade | `CancellationPolicy` |
| Lembrete | Aviso enviado ao cliente antes do horário | `Reminder` |

## Regras de negócio

Cada regra vira pelo menos um teste escrito antes do código (TDD). Os valores marcados como *padrão* são palpites iniciais: cada estabelecimento vai poder configurar, e eles podem ser revistos a qualquer momento.

**Agenda e disponibilidade**

1. **RN01** Um profissional não pode ter dois agendamentos com horários sobrepostos.
2. **RN02** Um agendamento só pode começar e terminar dentro do expediente do profissional naquele dia.
3. **RN03** Um agendamento não pode cair em um bloqueio do profissional.
4. **RN04** A duração do agendamento é a duração do serviço escolhido; o fim é calculado, nunca informado pelo cliente.
5. **RN05** Os horários oferecidos ao cliente andam em intervalos fixos (*padrão: 15 min*).
6. **RN06** Não é possível agendar no passado nem com menos de uma antecedência mínima (*padrão: 30 min*).
7. **RN07** Não é possível agendar além de uma janela máxima no futuro (*padrão: 30 dias*).
8. **RN08** Se o cliente escolher "qualquer profissional", o sistema atribui o primeiro profissional livre que oferece aquele serviço.

**Cancelamento e remarcação**

9. **RN09** O cliente pode cancelar ou remarcar até uma antecedência mínima (*padrão: 2 h* antes).
10. **RN10** Depois desse prazo, só o profissional pode cancelar.
11. **RN11** Remarcar é cancelar o horário antigo e criar um novo de forma atômica: se o novo horário não estiver livre, o antigo continua valendo.

**Comparecimento e faltas**

12. **RN12** Só é possível marcar falta ou concluído depois do horário de início.
13. **RN13** Um cliente com faltas acima de um limite (*padrão: 3 faltas em 90 dias*) fica bloqueado para agendar online e precisa falar com o estabelecimento.
14. **RN14** O profissional pode desbloquear um cliente manualmente.

**Lembretes**

15. **RN15** Todo agendamento confirmado gera um lembrete (*padrão: 24 h antes*); se o agendamento for feito com menos de 24 h, o lembrete sai 2 h antes.
16. **RN16** Agendamento cancelado cancela o lembrete pendente.

**Cliente**

17. **RN17** O cliente é identificado pelo telefone; o mesmo telefone no mesmo estabelecimento é sempre o mesmo cliente.
18. **RN18** Um cliente não pode ter mais de um agendamento futuro em aberto no mesmo estabelecimento (*padrão*; evita reserva "por garantia").

## Bounded contexts e eventos de domínio

O sistema começa com três microsserviços. A Agenda é o núcleo e concentra as regras difíceis; Cadastro e Notificações são de apoio.

```
   [Painel Angular]     [Página de agendamento]       [Keycloak]
     profissional          cliente, sem login        login OAuth2/JWT
           |                       |                        |
           v                       v                        | valida JWT
   [            Spring Cloud Gateway              ] <-------+
           |                       |
           v                       v
   [catalog-service]       [scheduling-service]       [notification-service]
     Business                Appointment (núcleo)       Reminder
     Professional            BlockedPeriod              EmailNotification
     ServiceOffering         Customer (no-shows)
     WorkingHours            AvailableSlot
     Postgres próprio        Postgres próprio           Postgres próprio
           |                       ^ |                        ^
           | publica               | | publica e consome      | consome
           v                       | v                        |
   [ Kafka: ServiceOfferingChanged · AppointmentConfirmed · AppointmentCancelled · NoShowRecorded ]
```

Nenhum serviço lê o banco do outro. A Agenda mantém uma cópia local dos serviços e expedientes, atualizada pelo evento `ServiceOfferingChanged`, e as Notificações só reagem a eventos.

**Agregado principal: Agendamento (`Appointment`).** É ele que garante as RN01 a RN12. Ciclo de vida:

1. **CONFIRMED** — criado pelo cliente ou pelo profissional.
2. **CANCELLED** — pelo cliente dentro do prazo (RN09) ou pelo profissional a qualquer momento (RN10).
3. **COMPLETED** ou **NO_SHOW** — marcado depois do horário de início (RN12); NO_SHOW conta para o bloqueio (RN13).

**Ordem de construção:** primeiro a Agenda inteira como um único serviço; depois a extração do Cadastro; por último as Notificações. Assim você aprende o que é um bounded context antes de pagar o custo de separar.

## Perguntas para validar com profissionais (opcional)

Não é obrigatório: o projeto segue com os valores *padrão*. Se um dia quiser validar o domínio, converse 15 minutos com 2 ou 3 profissionais de ramos diferentes (um barbeiro e uma manicure, por exemplo). As respostas confirmam ou corrigem os valores *padrão* acima. Anote tudo; isso também vira material para entrevistas ("validei o domínio com usuários reais").

**Como funciona hoje**

- [ ] Como você marca horário hoje? Quantas mensagens por dia isso dá?
- [ ] Quantos clientes faltam sem avisar por semana? O que você faz quando isso acontece?
- [ ] Já aconteceu de marcar dois clientes no mesmo horário?

**Regras**

- [ ] Quais serviços você oferece e quanto tempo cada um leva de verdade?
- [ ] Você atende de hora em hora, de meia em meia ou de 15 em 15 minutos?
- [ ] Com quanto tempo de antecedência o cliente pode cancelar sem te prejudicar?
- [ ] Você bloquearia um cliente que falta muito? Depois de quantas faltas?
- [ ] Aceita encaixe (cliente que chega sem marcar)? Como fica a agenda?
- [ ] O cliente escolhe o profissional ou tanto faz?

**Uso**

- [ ] Você usaria isso pelo celular ou pelo computador?
- [ ] O lembrete ajudaria? Por e-mail funciona ou só WhatsApp?
- [ ] Já usou algum app de agendamento? Por que parou (ou por que não usa)?

## Decisões de arquitetura e stack

A stack segue o que as vagas de Java pleno pedem, com uma regra a mais: nada instalado na máquina além do Docker e do editor.

| Área | Decisão | Por quê |
| --- | --- | --- |
| Linguagem | Java 21 + Spring Boot | Versão mais pedida nas vagas |
| Build | Maven | Padrão no mercado corporativo |
| Banco | PostgreSQL, um banco por serviço + Flyway | Isolamento entre microsserviços; migrations versionadas |
| Arquitetura interna | Clean Architecture + DDD (domínio sem Spring nem JPA) | O domínio é testado sem subir nada |
| Testes | TDD com JUnit 5 + AssertJ + Mockito; Testcontainers na integração | Pirâmide de testes, citada nas vagas |
| API | REST seguindo a RFC 9110; erros com RFC 9457 (`ProblemDetail`); ETag para concorrência | Boas práticas de HTTP de verdade |
| Maturidade | Richardson nível 3 (HATEOAS) no `scheduling-service`; nível 2 nos demais | Aprendizado sem gastar energia onde o mercado não cobra |
| Mensageria | Kafka (modo KRaft) | Muito pedido em vagas internacionais |
| Entrada | Spring Cloud Gateway | Ponto único para o front |
| Segurança | Keycloak (OAuth2/OIDC) + resource server JWT | Padrão de mercado; login não é programado à mão |
| Front | Angular, painel enxuto | Par mais comum com Java nas vagas full stack |
| Ambiente | Dev Container + Docker Compose | Nada instalado na máquina |
| Orquestração | Kubernetes local com kind + Helm (fase final) | Roda dentro do Docker |
| CI | GitHub Actions | Build e testes a cada push |
| Observabilidade | Actuator + Micrometer + Prometheus + Grafana + OpenTelemetry | Diferencial em vagas internacionais |

Todo o código é escrito em inglês: classes, métodos, variáveis, serviços (`catalog-service`, `scheduling-service`, `notification-service`), eventos e mensagens de commit. A documentação e a conversa com os usuários continuam em português; a ponte entre as duas é a tabela da linguagem ubíqua.

Decisões importantes ficam registradas como ADRs (Architecture Decision Records) na pasta `docs/adr`. Isso mostra maturidade de pleno para quem avaliar o projeto.

## Roadmap

Com 30 minutos por dia útil (cerca de 2h30 por semana), o MVP fica pronto em março de 2027 e o projeto completo em julho de 2027.

| Fase | Período | O que entra | Marco |
| --- | --- | --- | --- |
| 0 · Fundação | 02/11/2026 (dia livre) | Ambiente no Docker, CI e primeiro teste verde | Repositório público no GitHub |
| 1 · Domínio da Agenda | nov–dez/2026 · ~7 semanas | Agregados e RN01–RN18 com TDD, sem banco e sem Spring | Suíte de testes de domínio completa |
| 2 · API REST | jan/2027 · ~4 semanas | Postgres, Flyway, RFC 9457, ETag, HATEOAS, Testcontainers | API documentada (OpenAPI) rodando no Compose |
| 3 · Front e segurança | fev–mar/2027 · ~7 semanas | Painel Angular, página do cliente e login com Keycloak | **MVP no ar** |
| **Gate** | — | — | **MVP no ar com dados de demonstração + demo em vídeo** |
| 4 · Microsserviços | abr–mai/2027 · ~7 semanas | Extrair Cadastro, Kafka, Notificações e Gateway | Lembrete disparado por evento |
| 5 · K8s e observabilidade | jun–jul/2027 · ~7 semanas | kind, Helm, Prometheus, Grafana e OpenTelemetry | Tudo sobe no cluster com um comando |

As fases 0 a 3 entregam valor para o usuário; as fases 4 e 5 entregam o que o mercado cobra de um pleno. O gate do MVP vem antes dos microsserviços de propósito: um sistema completo e publicado vale mais em entrevista do que uma arquitetura pela metade.

Cada fase vira um milestone no GitHub Projects, e cada tarefa do quadro cabe em uma sessão de 30 minutos. Os prazos são estimativas; sábados ou dias livres extras só adiantam o cronograma.

## Plano do dia livre e rotina de 30 minutos

O dia livre de 02/11 serve para deixar o ambiente pronto. A partir de 03/11, os 30 minutos diários vão direto para código.

**Segunda, 02/11 (dia livre)**

- [ ] Manhã: ler este documento, ajustar o que não fizer sentido
- [ ] Manhã: criar o repositório `agendai` no GitHub com README, `.gitignore` e a pasta `docs/adr`
- [ ] Tarde: Dev Container com Java 21 e Maven
- [ ] Tarde: `docker-compose.yml` com PostgreSQL
- [ ] Tarde: gerar o `scheduling-service` no Spring Initializr e confirmar que ele sobe no container
- [ ] Fim do dia: primeiro teste de domínio passando (`Appointment` calcula o fim a partir da duração do serviço — RN04)
- [ ] Fim do dia: GitHub Actions rodando `mvn test` a cada push
- [ ] Fim do dia: escrever o ADR-001 (por que Clean Architecture + DDD)

**A rotina de 30 minutos (todo dia útil)**

1. **5 min:** ler a próxima tarefa do quadro (uma tarefa = uma regra ou um pedaço pequeno).
2. **20 min:** ciclo TDD — teste vermelho, código mínimo, teste verde, refatorar.
3. **5 min:** commit com mensagem clara, push e anotar no quadro qual é a próxima tarefa.

Três regras para não desistir:

- Nunca terminar o dia com teste quebrado; se o tempo acabar, apague o código não terminado.
- Faltou um dia? Tudo bem. Faltaram três seguidos? Faça só 10 minutos no quarto dia para não quebrar a corrente.
- Toda sexta, um post curto no LinkedIn ou uma linha no README sobre o que aprendeu na semana.
