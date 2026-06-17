# Transcript da Sessão /grill-me
## Service Blueprint AS-IS — Atendimento ao Seguro-Desemprego via URA/0800 da CAIXA
### Parte C — Sessão de Elaboração Conceitual

> **Metodologia:** /grill-me — entrevista adversarial para resolução progressiva de cada ramificação do design tree, uma decisão por vez.
> **Contexto:** `B_relatorio_assistente_v3.md` (versão final do deep research)
> **Framework alvo:** Service Blueprint de Shostack (1984)

---

## Questão 1 — Escopo da jornada mapeada

**Pergunta:**
Na metodologia de Shostack, o blueprint começa no primeiro ponto de evidência física que o cliente toca e termina na entrega do resultado prometido — ou na falha definitiva.

Minha recomendação: O escopo deve ser restrito ao canal 0800/URA da CAIXA, com a Pré-Ligação como contexto de entrada e o encerramento da chamada como saída. O Pós-Atendimento (Ouvidoria, PROCON, Judiciário) seria um blueprint separado — ou no máximo uma "sombra" fora do canvas principal.

Você quer que o blueprint AS-IS termine no encerramento da chamada (Fase 6), tratando o pós-atendimento como out-of-scope? Ou quer incluir as fases de escalada (Fase 7) como parte integral da jornada mapeada?

**Resposta:** Incluir as fases de escalada como parte integral.

---

## Questão 2 — Segmentação do cidadão-ator

**Pergunta:**
Na metodologia de Shostack, a linha de ações do cliente pressupõe um perfil de ator por blueprint. O relatório v3 menciona pelo menos três perfis com jornadas materialmente diferentes:

- **Perfil A** — Cidadão com literacia digital
- **Perfil B** — Cidadão sem literacia digital (idoso, trabalhador rural, analfabeto funcional)
- **Perfil C** — Vítima de fraude (caso pós-Operação RedeX)

Minha recomendação: Construir o blueprint com o Perfil B como ator principal. Os desvios dos Perfis A e C seriam anotados como variantes na mesma linha.

Você concorda em usar o cidadão sem literacia digital (Perfil B) como ator principal?

**Resposta:** Lembrando que estamos usando a metodologia de G. Lynn Shostack (1984). Usar o cidadão sem literacia digital (Perfil B) como ator principal da linha de ações. Os desvios dos Perfis A e C seriam anotados como variantes na mesma linha, não blueprints separados.

---

## Nota intercalada — Esclarecimento metodológico

**Nota do assistente:**
As 5 camadas e 3 linhas divisórias mencionadas inicialmente são de Bitner, Booms & Tetreault (1992), não do artigo original de Shostack (1984) — que só formalizou a linha de visibilidade e o flowchart de processos. Se você estiver usando Shostack 1984 com as camadas de Bitner, está usando a versão híbrida que virou padrão de mercado. Isso muda alguma coisa para você, ou seguimos com o híbrido?

**Resposta da aluna:** Muda, quero usar apenas Shostack.

---

## Questão 3 — Distribuição da arquitetura tripartite nas camadas do backstage

**Pergunta:**
O serviço envolve três organizações distintas — MTE, Dataprev e CAIXA — mas o cidadão interage com uma só interface (o 0800 da CAIXA). Duas opções:

- **Opção 1** — Backstage único da CAIXA: tudo abaixo da linha de visibilidade é backstage da CAIXA; MTE/Dataprev/Serpro aparecem como processos de suporte externo.
- **Opção 2** — Backstage tripartite explícito: o backstage é subdividido em três swimlanes (CAIXA / Dataprev / Serpro).

Minha recomendação: Opção 2 — revela o "abismo de alçada" como falha estrutural entre organizações separadas.

Você adota backstage tripartite ou backstage único?

**Resposta:** Prefiro a opção 1.

---

## Questão 4 — Posicionamento da linha de visibilidade

**Pergunta:**
Em Shostack 1984, a linha de visibilidade separa o que o cidadão percebe do que acontece nos bastidores. Três opções:

- **Opção A** — Linha logo após a interface de voz: URA e atendente acima; consultas sistêmicas abaixo.
- **Opção B** — Linha no atendente humano: URA acima; atendente e sistemas abaixo.
- **Opção C** — Linha no resultado entregue: todo o processo abaixo.

Minha recomendação: Opção A — mais fiel ao Shostack 1984.

Onde você posiciona a linha de visibilidade?

**Resposta:** Opção A.

---

## Questão 5 — Classificação dos fail points: estrutural vs. contingente

**Pergunta:**
Shostack 1984 marca fail points onde a execução pode falhar. Você quer distinguir entre:

- **Fail point contingente (F):** falha de execução dentro de um design correto — corrigível sem redesenhar o serviço.
- **Fail point estrutural (F⊗):** o serviço executa corretamente, mas o design produz falha inevitavelmente.

Minha recomendação: Distinguir explicitamente — F⊗ para falhas de design, F simples para falhas de execução.

Você quer diferenciar fail points estruturais de contingentes no blueprint?

**Resposta:** Quero diferenciar.

---

## Questão 6 — Tolerâncias de tempo (time standards)

**Pergunta:**
Um elemento distintivo do Shostack 1984 é a tolerância de tempo por passo. Para este blueprint, há pelo menos três situações relevantes:

- Batch pipeline (Serpro → Dataprev → CNIS → BGSD → CAIXA): latência não publicada.
- Prazo de recurso administrativo: 10–90 dias sem SLA público; teto legal: 120 dias.
- Decreto 11.034/2022: exige informar tempo de espera, mas não fixa valor máximo.

Minha recomendação: T=valor para SLAs publicados e T=? quando não há SLA público — tornando a ausência de transparência explicitamente visível.

Você quer incluir tolerâncias de tempo com essa distinção?

**Resposta:** Sim, incluir com essa distinção.

---

## Questão 7 — Pontos de decisão (diamonds) e ramificações do flowchart

**Pergunta:**
Shostack 1984 usa losangos de decisão onde o processo se bifurca. Identifiquei quatro pontos de decisão:

- **D1** — URA resolve sem transbordo?
- **D2** — Atendente tem diagnóstico suficiente?
- **D3** — Cidadão consegue agir no canal indicado? *(F⊗ no caminho "Não" para Perfil B)*
- **D4** — Recurso/escalada de 2º nível resolve?

Você confirma os quatro pontos de decisão?

**Resposta:** Confirmo os quatro.

---

## Questão 8 — Definição do output do serviço

**Pergunta:**
Shostack 1984 parte da promessa de entrega do serviço. Para este serviço há ambiguidade:

- **Output prometido (implícito):** o cidadão quer resolver o problema — desbloquear a parcela.
- **Output real entregue:** o 0800 foi desenhado para informar o status — não para resolver.

Minha recomendação: "Informação acionável que permita ao cidadão resolver o problema" — captura o gap real entre o que o serviço entrega e o que o Perfil B consegue fazer com essa entrega.

Qual é o output prometido que ancora o seu blueprint?

**Resposta:** Informação acionável faz mais sentido.

---

## Questão 9 — Granularidade do processo abaixo da linha de visibilidade

**Pergunta:**
Três opções para representar os processos sistêmicos no flowchart:

- **Opção A** — Caixa preta: um único passo "consulta base CAIXA".
- **Opção B** — Pipeline explícito: declaração eSocial → extração Dataprev/eSocial Negócio → consolidação CNIS → processamento BGSD/SIBE-SD → lote enviado à CAIXA, com T=? em cada etapa e Malha PIS/SINAF como desvio condicional.
- **Opção C** — Híbrido: apenas os passos que afetam diretamente o output.

Minha recomendação: Opção B — o batch pipeline é a causa estrutural da defasagem; torná-lo visível com T=? transforma a latência em fail points rastreáveis.

**Resposta:** Opção B.

---

## Questão 10 — Custo por passo (profit/cost analysis)

**Pergunta:**
Shostack 1984 inclui análise de custo por etapa. Para um serviço público, isso se traduz em custo social por falha. Duas opções:

- **Opção A** — Custo social por fail point: anotar o custo social nos fail points onde há dado documental (ex.: F⊗ blind trust SINE → R$ 11,9 mi; F⊗ batch → R$ 1,9 bi).
- **Opção B** — Excluir análise de custo.

Minha recomendação: Opção A — transforma o blueprint em instrumento de priorização.

**Resposta:** Incluir custo social nos fail points com dados documentados.

---

## Questão 11 — Notação das variantes Perfil A e Perfil C

**Pergunta:**
Três opções para representar as variantes no flowchart:

- **Opção A** — Ramificação nos losangos: Perfil A bifurca em D3 (caminho "Sim"); Perfil C anotado no passo de autenticação.
- **Opção B** — Swimlane paralela acima da linha.
- **Opção C** — Anotações marginais em todos os pontos de divergência.

Minha recomendação: Opção A para Perfil A (bifurcação no D3 já existe); Opção C para Perfil C (contaminação silenciosa — nota marginal é mais precisa).

**Resposta:** Opção A.

---

## Questão 12 — Ponto de entrada e saída do flowchart

**Pergunta:**
Três opções de entrada:
- **E1** — Discagem do 0800
- **E2** — Percepção do bloqueio
- **E3** — Demissão do empregador

Três estados finais:
- **S1** — Informação acionável entregue
- **S2** — Encaminhamento não acionável (F⊗)
- **S3** — Judicialização (F⊗)

Minha recomendação: Entrada em E2; saída com três estados finais explícitos.

**Resposta:** Entrada em E1. Saída com os 3 estados finais explícitos.

---

## Questão 13 — Classificação dos fail points limítrofes

**Pergunta:**
Os casos limítrofes que precisavam de decisão explícita:

- **L1** — Barreira DTMF para Perfil B: F⊗ (exclui estruturalmente) ou F (falha normativa corrigível)?
- **L2** — URA não explica motivo do bloqueio: F⊗ (omissão de design) ou F (limitação técnica corrigível)?
- **L3** — CPF com situação irregular na RFB: F (falha do serviço) ou fora do escopo?

Minha recomendação: L1=F⊗, L2=F⊗, L3=fora do escopo.

**Resposta:** L1=F⊗, L2=F⊗, L3=F.

> **Nota:** L3 foi classificado como F (contingente) — o 0800 falha ao não oferecer rota de resolução para o cidadão com CPF irregular, em vez de simplesmente travar a autenticação.

---

## Questão 14 — Representação da Malha PIS/SINAF no pipeline

**Pergunta:**
Duas formas de representar a Malha PIS/SINAF no flowchart abaixo da linha de visibilidade:

- **Opção A** — Losango de decisão: "Registro retido na SINAF?" → Sim: desvio sem saída resolutiva (F⊗) / Não: prossegue ao lote CAIXA.
- **Opção B** — Fail point direto: a Malha PIS/SINAF inteira como F⊗, sem losango.

Minha recomendação: Opção A — o mecanismo em si é correto; o problema é a ausência de saída resolutiva e de notificação no caminho "Sim". O F⊗ cai no caminho "Sim sem saída", não no mecanismo todo.

**Resposta:** Opção A.

---

## Síntese Final das Decisões

| # | Dimensão | Decisão |
|:--|:---|:---|
| 1 | Escopo | Inclui escalada (Fase 7) como parte integral |
| 2 | Ator principal | Perfil B (sem literacia digital); A bifurca em D3; C anotado em P3 |
| 3 | Metodologia | Shostack (1984) puro — não Bitner (1992) |
| 4 | Backstage | Único (CAIXA) — Opção 1 |
| 5 | Linha de visibilidade | Interface de voz acima; sistemas abaixo — Opção A |
| 6 | Notação de fail points | F⊗ estrutural / F contingente |
| 7 | Tolerâncias de tempo | T=valor (SLA publicado) / T=? (sem SLA) |
| 8 | Pontos de decisão | D1, D2, D3, D4 — confirmados |
| 9 | Output prometido | Informação acionável |
| 10 | Granularidade abaixo da linha | Pipeline explícito (B3a→B3e) com Malha PIS/SINAF — Opção B |
| 11 | Custo social | Anotado nos fail points com dado documental |
| 12 | Variantes de perfil | Opção A — bifurcação no flowchart |
| 13 | Entrada / Saída | E1 (discagem) / S1, S2, S3 |
| 14 | Fail points limítrofes | L1=F⊗, L2=F⊗, L3=F |
| 15 | Malha PIS/SINAF | Losango de decisão B4 — Opção A |
