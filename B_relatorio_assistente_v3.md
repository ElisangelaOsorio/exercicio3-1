# Service Blueprint AS-IS — Atendimento ao Seguro-Desemprego via URA/0800 da CAIXA
## Versão 3 (Final) — 2024–2025
### Diagnóstico Interno de Consultoria

> **Histórico de versões:**
> - **v1** — Relatório original de pesquisa
> - **v2** — Revisão com base na 1ª Auditoria Independente (10 falhas endereçadas)
> - **v3 (este documento)** — Revisão com base na 2ª Auditoria Independente (3 falhas endereçadas). Cada decisão está registrada no **Log de Revisões** ao final, com código G-01 a G-03.

---

## Log de Revisões v2 → v3 (Segunda Auditoria)

| ID | Falha Apontada | Decisão | Ação Tomada |
|:---|:---|:---:|:---|
| **G-01** | Custódia do eSocial atribuída incorretamente à Dataprev — correto: Serpro desenvolve/mantém a infraestrutura; Dataprev apenas extrai e transforma dados na base "eSocial Negócio" | **(a) Corrigir** | Arquitetura reescrita em Key Findings §2, Fase 1 (Backstage), Fase 3 (Backstage) e Análise Narrativa (Camada 1). Adicionada nota sobre Decreto 8.373/2014 e Contrato MTE–Dataprev 17/2021. |
| **G-02** | Métrica "+27% na Ouvidoria CAIXA" com rótulo impreciso — correto: +27% refere-se ao **total agregado de todos os canais** (208.438 → 264.831); a Ouvidoria interna propriamente dita **caiu 33%** (11.886 → 7.926); PROCON subiu +9% (19.501 → 21.351); Consumidor.gov subiu +59% (16.113 → 25.690) | **(a) Corrigir** | Tabela de reclamações adicionada com desagregação por canal. TL;DR e Key Findings §6 reescritos com rótulos corretos. |
| **G-03** | Fail points de segurança (Operação RedeX e Acórdão TCU 1.114/2025) insuficientemente detalhados; menções a SISOBI e "base transacional" da CAIXA atribuídas ao Acórdão 1.114/2025 indevidamente (essas referências não constam desse acórdão) | **(a) Corrigir + Expandir** | Seção de Fail Points de Segurança expandida. Referências a SISOBI e "base transacional da CAIXA" desvinculadas do Acórdão 1.114/2025 e corretamente atribuídas a auditorias anteriores da CGU/TCU. Mecanismo de blind trust e ausência de validação biométrica/FGTS em tempo real detalhado. |

---

## TL;DR (Versão Final)

- A central telefônica da CAIXA para benefícios (**0800 726 0207**, "Atendimento CAIXA ao Cidadão") é estruturalmente um canal **informativo, não resolutivo**: o atendente terceirizado consulta o status do benefício, mas **não tem alçada nem acesso para corrigir divergências cadastrais** nem para desbloquear parcelas — a correção depende do MTE (canal 158/gov.br) ou de agência física, gerando o maior gargalo sistêmico identificado: o **"abismo de alçada"**.
- A jornada é **fragmentada entre três donos de processo distintos** — MTE (regras e habilitação), Dataprev (motor de processamento e malha de cruzamento) e CAIXA (agente pagador) — sem que nenhum atendente de voz tenha visão ponta a ponta, forçando o cidadão a transitar entre 0800 (CAIXA) e 158 (MTE) sem resolução.
- Há base documental robusta de falhas sistêmicas: TCU (Acórdão 135/2024 — R$ 1,9 bi em indícios; Acórdão 1.114/2025 — restrições de acesso ao eSocial Negócio), CGU (R$ 149 mi em 2018; R$ 97,239 mi em 2021).
- As reclamações dos clientes da CAIXA cresceram **27% no total agregado** em 2025 (todos os canais: 208.438 → 264.831 ocorrências), com o Consumidor.gov liderando a alta (+59%: 16.113 → 25.690). *(v3: rótulo corrigido — ver G-02)*
- O gargalo central opera em **quatro camadas sobrepostas**: temporal (batch processing), cadastral (Malha PIS/SINAF), governança (abismo de alçada) e segurança (blind trust na origem SINE — evidenciado pela Operação RedeX, PF 2023).

---

## Key Findings (Versão Final)

### 1. O canal é informativo por desenho
O 0800 726 0207 foi lançado para fornecer **informações** sobre PIS, Seguro-Desemprego, FGTS e Cartão Social. O **atendimento humano** opera de **segunda a sexta-feira das 8h às 21h, e aos sábados das 10h às 16h** *(corrigido na v2 — F-01)*. A URA opera em modo de consulta eletrônica 24h. Para **solicitar** o benefício ou **corrigir divergências**, o cidadão é obrigatoriamente direcionado ao MTE (158 / gov.br / Carteira de Trabalho Digital). O suporte via WhatsApp da CAIXA opera pelo número exclusivo **0800 104 0104** — distinto do canal de voz *(corrigido na v2 — F-02)*.

### 2. Divisão tripartite de responsabilidades — arquitetura de sistemas correta (v3)
O MTE define elegibilidade (Lei 7.998/1990; Res. CODEFAT 957/2022 — habilitação aferida automaticamente via CNIS, CAGED e eSocial). A cadeia de dados funciona em três camadas distintas:

**Camada A — Infraestrutura do eSocial (Serpro):** O eSocial — Sistema de Escrituração Digital das Obrigações Fiscais, Previdenciárias e Trabalhistas — foi instituído pelo **Decreto nº 8.373/2014** e é desenvolvido e mantido tecnologicamente pelo **Serpro**, que opera o ambiente nacional (data lake de eventos XML declarados pelos empregadores). O TCU, no Acórdão 1.114/2025, refere-se ao eSocial textualmente como "sistema gerido pelo Serpro". A CAIXA participa do Comitê Gestor do eSocial exclusivamente na qualidade de agente operador do FGTS (Lei 8.036/90), não respondendo pela infraestrutura nacional. *(v3: correção G-01)*

**Camada B — Processamento derivado (Dataprev / "eSocial Negócio"):** A **Dataprev** extrai os dados do data lake do Serpro e os transforma em uma base relacional intermediária denominada **"eSocial Negócio"**, utilizada para cruzamentos de elegibilidade de benefícios. A Dataprev opera essa transformação sob o **Contrato MTE–Dataprev nº 17/2021** (firmado em 25/05/2021; prorrogado). Além disso, a Dataprev gerencia operacionalmente o **CNIS** (Cadastro Nacional de Informações Sociais) — base distinta do eSocial, gerida institucionalmente pelo INSS, que consolida vínculos e remunerações do trabalhador para fins de concessão de benefícios. O CNIS contém mais de 35 bilhões de registros laborais. *(v3: correção G-01)*

**Camada C — Concessão e pagamento (Dataprev/MTE → CAIXA):** A **Dataprev** opera o **SIBE-SD** (motor de processamento do Seguro-Desemprego), alimentado pela **BGSD** (Base de Gestão do Seguro-Desemprego). A CAIXA atua **exclusivamente como agente pagador** (recursos do FAT, Lei 7.998/1990), sem participação na concessão. O **SIBEC** é a plataforma da CAIXA para benefícios socioassistenciais (Bolsa Família/Cadastro Único), aplicável ao Seguro-Desemprego apenas no caso do Seguro-Defeso (pescador artesanal) — **não** ao Seguro-Desemprego formal. O **Serpro** também provê a API de Consulta CPF para validação cadastral na base da Receita Federal (~120 milhões de consultas/mês). *(corrigido na v2 — F-03)*

### 3. O "abismo de alçada" — com todas as camadas de bastidor
Quando há divergência cadastral, o sistema gera pendência que bloqueia a habilitação automática. O atendente da CAIXA no 0800 só consegue informar o bloqueio; a correção exige o MTE ou a Receita Federal. Antes mesmo de chegar à tela do atendente, o benefício pode estar retido na **"Malha PIS"**, cujos registros inconsistentes ficam na tabela **SINAF** (Tabela de Registros a Regularizar) e só são liberados por correção manual ou acerto sistêmico — tornando o bloqueio invisível tanto para o operador quanto para o cidadão. *(incorporado na v2 — F-05)*

### 4. Batch processing como causa estrutural da defasagem de status
A informação de desligamento transmitida pelo empregador via eSocial **não fica disponível imediatamente** na URA. Os dados percorrem o seguinte caminho: declaração do empregador no eSocial (Serpro) → extração e transformação pela Dataprev no "eSocial Negócio" → consolidação no CNIS → processamento na BGSD/SIBE-SD → envio em **lotes periódicos de liquidação** pelo MTE à CAIXA. Cada etapa tem latência própria. Resultado: o cidadão pode ver o benefício como "liberado" no canal 158 (MTE/Dataprev) e como "não encontrado" ou "bloqueado" na URA da CAIXA. *(incorporado na v2 — F-04)*

### 5. Vulnerabilidade sistêmica na ponta SINE — Operação RedeX (PF, 2023)
A URA da CAIXA opera sob **modelo de confiança cega (blind trust)** em relação aos lotes de pagamento enviados pelo MTE/Dataprev: **não realiza cruzamentos biométricos independentes nem validação concomitante de recolhimentos ativos de FGTS** antes de disponibilizar saldos para consulta e autorizar saques. A **Operação RedeX** (Polícia Federal, 04/10/2023) demonstrou que quadrilhas inseriam requerimentos fraudulentos nos sistemas do MTE via credenciais comprometidas no SINE — e a URA confirmava saldos e autorizava saques sem qualquer mecanismo de validação da integridade da rescisão. Uma vez inserido o cadastro fraudulento com credenciais legítimas, a URA executa corretamente dados falsos. *(incorporado na v2 — F-06; expandido na v3 — G-03)*

Números confirmados pela investigação policial:
- **12.000 requerimentos fraudados** de Seguro-Desemprego
- Prejuízo comprovado: **R$ 11.900.111,12**
- Prejuízo adicional evitado: **R$ 7,2 milhões** (bloqueio preventivo de saques após deflagração)

### 6. Sustentação quantitativa (consolidada e com rótulos corrigidos)
- **TCU Acórdão 135/2024** (TC 006.464/2022-0, rel. Min. Marcos Bemquerer): R$ 1,9 bi em indícios (jan/2018–jun/2022) — detalhado por tipologia na **Tabela 1**
- **TCU Acórdão 1.114/2025** (TC 008.443/2024-6, Plenário, rel. Min. Bruno Dantas, 21/05/2025): restrições de acesso ao eSocial Negócio; base com >2 TB; R$ 72,7 bi pagos em 2023 (R$ 47,7 bi SD + R$ 25 bi abono); universo de 28,7 mi → 32,2 mi beneficiários (2022–2024)
- **CGU**: R$ 149 mi (165 mil parcelas irregulares, 2018); R$ 97,239 mi (2021)
- **Reclamações CAIXA (1º sem/2025):** total agregado de todos os canais: **264.831 ocorrências (+27%)**; detalhado na **Tabela 2** *(v3: rótulo corrigido — G-02)*

---

## Tabela 1 — Irregularidades por Tipologia (TCU e CGU)

| Órgão / Decisão | Valor em Irregularidade | Tipologia | Causa-Raiz Sistêmica |
|:---|---:|:---|:---|
| TCU / Acórdão 135/2024 | R$ 1.144.283.468,51 | 382.962 parcelas a trabalhadores com menos de 12 meses de vínculo nos últimos 3 anos | Falha crônica nas regras de validação de carência entre Dataprev e sistemas concessores |
| TCU / Acórdão 135/2024 | R$ 566.800.000,00 | Pagamento cumulativo a beneficiários com benefícios previdenciários inacumuláveis (INSS) | Violação do art. 124 da Lei 8.213/1991 — ausência de cruzamento preventivo com base INSS |
| TCU / Acórdão 135/2024 | R$ 147.000.000,00 | Parcelas a servidores públicos ativos de estados e municípios | Falha na integração com folhas de pagamento subnacionais |
| TCU / Acórdão 135/2024 | R$ 3.000.000,00 | Pagamento concomitante a beneficiários de auxílio-reclusão | Ausência de bloqueio cruzado com bases carcerárias |
| CGU / Avaliação contínua (2021) | R$ 7.608.030,00 | 5.584 parcelas a 2.349 requerimentos de beneficiários falecidos | Latência na atualização da base de óbitos — janela explorada por fraude |
| CGU / Avaliação contínua (2021) | R$ 78.608.000,00 | Benefícios a pessoas com vínculos ativos ou aposentadorias públicas | Fragilidade no rastreamento via eSocial/CAGED |
| CGU / Avaliação contínua (2021) | R$ 9.937.000,00 | Parcelas a demitidos por justa causa ou pedido voluntário de demissão | Erro de classificação no motivo de desligamento no requerimento |
| CGU / Auditoria geral (2018) | R$ 149.000.000,00 | 165 mil parcelas a CPFs inexistentes, falecidos ou sem registro na RAIS | Ausência histórica de verificação informatizada periódica com bases federais |

---

## Tabela 2 — Reclamações da CAIXA por Canal (1º Semestre 2024 vs. 2025)

*Fonte: Relatório Institucional de Ouvidoria CAIXA — 1º Semestre de 2025 (caixa.gov.br/Downloads/caixa-ouvidoria/Relatorio-Institucional-1-Sem-2025.pdf), em cumprimento ao Art. 13 da Resolução CMN nº 4.860/2020.*

| Canal | 1º Sem 2024 | 1º Sem 2025 | Variação | Observação |
|:---|---:|---:|:---:|:---|
| **TOTAL (todos os canais)** | **208.438** | **264.831** | **+27%** | Número correto para a afirmação "+27%" *(v3: rótulo corrigido — G-02)* |
| Consumidor.gov | 16.113 | 25.690 | **+59%** | Canal externo com maior crescimento |
| PROCON | 19.501 | 21.351 | +9% | *(v3: a 2ª auditoria rotulou esses números como "Ouvidoria interna" — incorretamente)* |
| SAC CAIXA | 129.109 | 147.342 | +14% | Principal canal por volume |
| BACEN | 31.344 | 37.886 | +21% | — |
| Reclame Aqui | — | 24.508 | (novo) | Canal incorporado ao monitoramento em 2025 |
| **Ouvidoria interna (canal)** | **11.886** | **7.926** | **−33%** | A Ouvidoria interna propriamente dita **caiu** *(v3: G-02)* |
| **Ranking BACEN** | — | **11º–13º lugar** | Melhora | Menor índice histórico da CAIXA: 22,91–24,82 reclamações/milhão de clientes |

> ⚠️ **Nota v3 (G-02):** A 2ª auditoria apontou que "+27% na Ouvidoria CAIXA" seria incorreto. A verificação técnica confirmou que o número 264.831 e o percentual +27% **são corretos**, mas referem-se ao **total agregado de todos os canais**, não exclusivamente à Ouvidoria interna. A Ouvidoria interna propriamente dita **caiu 33%**. Os números 19.501 → 21.351 (+9%) citados pela 2ª auditoria como "Ouvidoria interna" correspondem, na realidade, ao canal **PROCON**. O crescimento de 59% no Consumidor.gov (16.113 → 25.690) está correto. Os 27% **não se referem** à Caixa Residencial (seguros habitacionais) nem a dados da Febraban.

---

## Matriz do Service Blueprint — 7 Fases × 5 Eixos (Versão Final)

---

### FASE 1 — PRÉ-LIGAÇÃO
*(Reunião de documentos e tentativa de autoatendimento)*

**Ações do Cidadão:**
Reúne TRCT, RG/CPF, requerimento do Seguro-Desemprego (gerado pelo empregador via Empregador Web / eSocial), contracheques. Tenta consultar status no app Carteira de Trabalho Digital, portal Emprega Brasil/gov.br, app CAIXA Tem, CAIXA Trabalhador ou site cidadao.caixa.gov.br.

**Evidências Físicas e Digitais:**
Requerimento com número do RSD, TRCT, apps gov.br (login único, nível Prata/Ouro), SMS/e-mail de notificação, mensagem de "crédito bloqueado" no Caixa Tem, Cartão do Trabalhador.

**Frontstage / Backstage:**
- *Frontstage:* apps e portais de consulta (CAIXA Tem, gov.br, Carteira de Trabalho Digital).
- *Backstage:* autenticação via gov.br (Serpro/ITI); consulta à BGSD (Dataprev/SIBE-SD); habilitação automática cruzando CNIS, CAGED e eSocial (Res. CODEFAT 957/2022, art. 4º §1º).
- ⚠️ *v3 — Fluxo técnico completo (G-01):* O dado de desligamento declarado pelo empregador **no eSocial (infraestrutura Serpro)** passa por extração e transformação pela Dataprev no "eSocial Negócio" → consolidação no CNIS → processamento na BGSD/SIBE-SD → envio em **lotes periódicos de liquidação** pelo MTE à CAIXA. O cidadão pode perceber que o status "não aparece" mesmo após a rescisão ser transmitida, devido ao lag de cada etapa desse batch pipeline.

**Normativos Aplicáveis:**
Lei 7.998/1990; Decreto 8.373/2014 (eSocial); Portaria MTP 671/2021 (e alterações vigentes); Res. CODEFAT 957/2022; LGPD (Lei 13.709/2018); Decreto 11.034/2022 (SAC).

**Fail Points:**
- *UX:* Baixa literacia digital impede uso dos apps → empurra para o telefone como canal primário.
- *UX:* Divergência cadastral aparece como bloqueio genérico sem indicação do motivo.
- *Técnico:* Empregador que não transmite a rescisão no eSocial (Serpro) impede que a informação flua até a BGSD e, consequentemente, até a URA.
- *UX:* App Caixa Tem exige ida à agência para desbloqueio de senha (relatos Reclame Aqui).
- ⚠️ *v3 — Técnico (G-01):* Defasagem do batch pipeline (Serpro → Dataprev/eSocial Negócio → CNIS → BGSD → CAIXA) faz o benefício aparecer como "não solicitado" ou "não enviado pelo MTE" na URA mesmo após concessão, induzindo o cidadão a ligar para o canal errado.
- ⚠️ *v3 — Segurança (G-03):* Requerimentos fraudulentos inseridos com credenciais comprometidas no SINE (Operação RedeX) contaminam os dados que percorrem esse pipeline e chegam à BGSD/CAIXA, sem qualquer validação independente de integridade na ponta CAIXA.

---

### FASE 2 — CONEXÃO E AUTENTICAÇÃO NA URA

**Ações do Cidadão:**
Disca 0800 726 0207; ouve saudação automática; navega no menu via teclado (DTMF); digita CPF e data de nascimento para autenticação.

**Evidências Físicas e Digitais:**
Mensagem de saudação gravada; menu de voz DTMF; mensagens informativas durante espera (publicidade vedada pelo Decreto 11.034/2022); feedback auditivo de autenticação (aceita/rejeitada).

**Frontstage / Backstage:**
- *Frontstage:* URA (sistema de resposta audível automático).
- *Backstage:* Validação de CPF via **API Consulta CPF do Serpro** (base da Receita Federal — ~120 milhões de consultas/mês); roteamento da chamada por skill no ACD.
- ⚠️ *v3 — Segurança (G-03):* O sistema de autenticação da URA exige apenas dados de identificação civil **estáticos** (CPF, data de nascimento, NIS) — suficientes para identificação, mas também facilmente obtidos por fraudadores via vazamentos de dados ou por familiares de falecidos. A URA **não realiza cruzamento biométrico independente nem validação concomitante de recolhimentos ativos de FGTS** antes de disponibilizar saldos. Um requerimento fraudulento inserido no SINE com dados reais do trabalhador autenticará normalmente na URA.

**Normativos Aplicáveis:**
Decreto 11.034/2022 (novo Decreto do SAC): atendimento humano ≥8h/dia, canal de consulta 24h, vedação de publicidade na espera, gravações retidas 90 dias, registro por 2 anos, transferência única ao setor competente. LGPD (Lei 13.709/2018) para tratamento de CPF e data de nascimento.

**Fail Points:**
- *Técnico:* Indisponibilidade da URA ("sistema temporariamente indisponível" — relatos Reclame Aqui); latência/queda na validação do CPF via API Serpro.
- *UX:* Barreira de literacia para idosos e analfabetos funcionais na navegação por DTMF.
- *Técnico:* Autenticação falha para CPFs com situação cadastral irregular na RFB (suspenso, cancelado).
- ⚠️ *v3 — Segurança (G-03):* Autenticação por dados estáticos não detecta impersonação pós-fraude no SINE. O fraudador que inseriu um requerimento fictício com dados reais da vítima pode autenticar-se na URA e verificar o saldo antes do saque indevido.

---

### FASE 3 — NAVEGAÇÃO NA URA / AUTOATENDIMENTO

**Ações do Cidadão:**
Seleciona opção no menu (status do benefício, data de pagamento de parcelas, desbloqueio de Cartão Social, consulta de saldo); ouve resposta automatizada.

**Evidências Físicas e Digitais:**
Resposta automatizada de voz com status (ex.: "benefício não localizado", "parcela liberada", "crédito disponível"); número de protocolo eletrônico gerado automaticamente quando aplicável.

**Frontstage / Backstage:**
- *Frontstage:* Locução automatizada da URA.
- *Backstage:* Consulta da CAIXA à sua base interna de pagamentos — que reflete apenas o que foi recebido via lotes de liquidação do MTE/Dataprev.
- ⚠️ *v3 — Técnico (G-01 e G-03):* O status de elegibilidade reside na Dataprev/BGSD; a CAIXA apenas reflete os lotes recebidos. A cadeia de latência do batch pipeline (Serpro → Dataprev/eSocial Negócio → CNIS → BGSD → CAIXA) cria janela de descompasso. Paralelamente, registros retidos na **"Malha PIS"** (tabela SINAF) por divergência cadastral nunca chegam à base de pagamentos, tornando o bloqueio completamente invisível para a URA. Registros fraudulentos que superaram todos os controles upstream aparecem na URA como legítimos e com saldo disponível.

**Normativos Aplicáveis:**
Res. CODEFAT 957/2022 (concessão de parcelas; intervalo mínimo de 30 dias entre parcelas); Lei 7.998/1990 (critérios de elegibilidade).

**Fail Points:**
- *Técnico:* Descompasso de sincronização CAIXA ↔ MTE/Dataprev — cidadão vê "liberado" no canal 158 e "bloqueado" ou "não localizado" na URA (episódios recorrentes em jun/2024 e jun/2025).
- *Técnico:* Retenção silenciosa na **"Malha PIS" / tabela SINAF**: benefício deferido pelo MTE retido por inconsistência mínima (um dígito errado, variação de grafia), sem mensagem explicativa alguma na URA.
- *UX:* A URA não explica o motivo do bloqueio nem oferece orientação de correção por voz.
- *Técnico:* Sobrecarga sistêmica em datas de pagamento — picos de acesso concentrados causam degradação de desempenho.
- ⚠️ *v3 — Segurança (G-03):* Benefícios gerados por requerimentos fraudulentos (pós-Operação RedeX) aparecem na URA com saldo disponível — a URA não possui mecanismo independente de validação de integridade da rescisão.

---

### FASE 4 — TRANSBORDO HUMANO (Fila e Espera)

**Ações do Cidadão:**
Opta por atendente humano (ou é forçado pelo esgotamento de opções da URA); aguarda na fila de distribuição; ouve mensagem de posição/tempo de espera.

**Evidências Físicas e Digitais:**
Mensagem de posição na fila ou tempo estimado de espera em minutos (exigência do Decreto 11.034/2022); música de fundo ou mensagens informativas institucionais (publicidade vedada).

**Frontstage / Backstage:**
- *Frontstage:* Sistema de fila ACD (Automatic Call Distribution).
- *Backstage:* Roteamento por skill; alocação de operador terceirizado disponível; monitoramento de TMA e SLA pela supervisão do call center contratado.

**Normativos Aplicáveis:**
Decreto 11.034/2022: obrigatoriedade de informar tempo de espera em minutos ou posição na fila; transferência única ao setor competente; atendimento humano disponível por no mínimo 8h/dia em dias úteis. Após as 21h (dias úteis) e fora do horário de sábado (10h–16h), apenas a URA está disponível — bloqueios complexos não encontram transbordo humano fora desses horários.

**Fail Points:**
- *Técnico/UX:* Tempo de espera excessivo; queda da chamada após longa espera (relatos frequentes).
- *UX:* Direcionamento forçado para canais digitais ("acesse o aplicativo") sem oferta de alternativa por voz para cidadãos sem acesso digital.
- *UX:* Fragmentação: cidadão transferido entre setores ou obrigado a reiniciar o atendimento.
- *Regulatório:* Ausência de SLA público para tempo de espera em serviços públicos (o Decreto 11.034/2022 exige informar o tempo, não limita seu valor).

---

### FASE 5 — ATENDIMENTO HUMANO

**Ações do Cidadão:**
Explica o caso; fornece CPF e dados complementares; solicita status, desbloqueio ou agendamento; aguarda o atendente consultar os sistemas.

**Evidências Físicas e Digitais:**
Gravação da chamada (retida 90 dias conforme Decreto 11.034/2022); tela de consulta do atendente; protocolo gerado ao final do atendimento.

**Frontstage / Backstage:**
- *Frontstage:* Operador terceirizado (telesserviços CAIXA) consulta a base de pagamentos da CAIXA.
- *Backstage:* O operador possui acesso de **leitura** à base interna de pagamentos da CAIXA, mas **não possui acesso de escrita nem visão** sobre as bases do MTE, BGSD/SIBE-SD, CNIS/Dataprev, eSocial/Serpro ou Receita Federal.
- ⚠️ *v3 (G-03):* O atendente não consegue distinguir entre as quatro causas possíveis de bloqueio — (i) dado ainda em lote pendente no batch pipeline, (ii) retenção na Malha PIS/SINAF, (iii) divergência cadastral pendente no MTE, ou (iv) bloqueio preventivo por suspeita de fraude pós-RedeX — pois todas aparecem como "bloqueado" ou "não localizado" na sua tela de consulta.

**Normativos Aplicáveis:**
Decreto 11.034/2022 (resolutividade, cordialidade, prazo de resposta de 7 dias, registro por 2 anos); LGPD — o atendente deve tratar apenas os dados necessários; vedado fornecer informações do benefício de terceiros (sigilo). A responsabilidade por erro de informação prestada pelo terceirizado recai sobre a CAIXA (contratante), não sobre o operador individualmente.

**Fail Points:**
- *Regulatório/UX:* **Abismo de alçada** — o atendente não tem informação diagnóstica nem poder de resolução para divergência cadastral; o resultado invariável é o encaminhamento para MTE/158 ou agência física.
- ⚠️ *v3 — Técnico (G-03):* **Opacidade de diagnóstico de quatro camadas** — o atendente não consegue informar ao cidadão qual das quatro causas de bloqueio está ativa (batch pendente / Malha PIS / divergência MTE / bloqueio por fraude), gerando orientação genérica e alta reincidência de ligações.
- *UX/Regulatório:* Qualidade variável e alta rotatividade de mão de obra terceirizada (licitação por menor preço, documentada pelo Sinttel-ES); risco de informação incorreta com responsabilização difusa.

---

### FASE 6 — RESOLUÇÃO, ENCAMINHAMENTO E ENCERRAMENTO

**Ações do Cidadão:**
Recebe orientação (geralmente encaminhamento ao canal 158/SINE/agência); anota número de protocolo; encerra a chamada **sem resolução efetiva** na maioria dos casos de divergência.

**Evidências Físicas e Digitais:**
Número de protocolo de atendimento (interno CAIXA); orientação verbal (não documentada para o cidadão); eventual registro de demanda interna — sem integração com MTE, Dataprev ou Receita Federal.

**Frontstage / Backstage:**
- *Frontstage:* Encerramento da chamada pelo operador.
- *Backstage:* Abertura de demanda interna quando aplicável — **sem mecanismo que dispare automaticamente correção no MTE, Dataprev, Serpro ou Receita Federal**. O protocolo existe como registro interno da CAIXA, mas não aciona nenhum workflow resolutivo upstream.

**Normativos Aplicáveis:**
Decreto 11.034/2022 (protocolo numérico obrigatório, histórico disponível em 5 dias úteis, registro por 2 anos); Res. CODEFAT 957/2022 (recurso administrativo em até 120 dias da notificação de indeferimento).

**Fail Points:**
- *UX/Regulatório:* FCR (resolução no primeiro contato) extremamente baixa para casos de divergência — **encaminhamento não é resolução**.
- *Regulatório:* Prazo de análise de recurso administrativo relatado entre 10 e 90 dias, sem SLA público.
- *UX:* "Ping-pong" entre CAIXA (0800) e MTE (158) — o protocolo CAIXA não tem validade operacional no sistema do MTE.
- *Técnico:* Ausência de integração entre CRM do call center e os sistemas upstream (BGSD, CNIS, Serpro) impede que o registro da demanda gere qualquer ação corretiva automática.

---

### FASE 7 — PÓS-ATENDIMENTO

**Ações do Cidadão:**
Acompanha protocolo sem retorno; reincide nas ligações; escala para Ouvidoria CAIXA (0800 725 7474), Consumidor.gov, PROCON, Reclame Aqui, BACEN, ANPD ou aciona o Judiciário.

**Evidências Físicas e Digitais:**
Resposta da Ouvidoria; número de manifestação no Consumidor.gov; decisão administrativa do MTE; ação judicial (há jurisprudência nos TRFs condenando a União a pagar SD indeferido por divergência cadastral).

**Frontstage / Backstage:**
- *Frontstage:* Canais de 2º nível (Ouvidoria CAIXA, Consumidor.gov, PROCON).
- *Backstage:* Ouvidoria CAIXA; reprocessamento automático da parcela após correção da pendência (prazo variável); eventual intervenção manual da Dataprev ou MTE após recurso administrativo.

**Normativos Aplicáveis:**
LGPD/ANPD (petição de titular; revisão de decisão automatizada — art. 20 Lei 13.709/2018); Código de Defesa do Consumidor (Lei 8.078/1990); Decreto 11.034/2022; Res. CODEFAT 957/2022 (prazo de recurso).

**Fail Points:**
- *UX:* Alta reincidência de ligações — fragmentação estrutural da jornada.
- *UX/Regulatório:* Populações vulneráveis (idosos, trabalhadores rurais, analfabetos funcionais) sem condições de escalar digitalmente via Consumidor.gov ou ANPD; judicialização como via final.
- ⚠️ *v3 — Segurança (G-03, com fonte correta):* Latência na integração com o **SISOBI** (Sistema Informatizado de Controle de Óbitos, gerido pelo INSS/Dataprev) cria janela em que parcelas de beneficiários falecidos permanecem consultáveis e sacáveis nos canais CAIXA. Identificado e quantificado pela **CGU** (R$ 7.608.030,00 pagos a 2.349 requerimentos de falecidos, referentes a 2021) — **não pelo Acórdão TCU 1.114/2025**, que não menciona SISOBI. A autenticação da URA (dados estáticos) não detecta que o titular faleceu durante a janela de latência de atualização do SISOBI no CNIS.

---

## Análise Narrativa — O Maior Gargalo Sistêmico (Versão Final)

O gargalo central é o **descompasso entre uma arquitetura de dados de quatro camadas e um modelo de atendimento telefônico com alçada zero**. A v3 permite descrevê-lo com precisão técnica inédita:

**Camada 1 — Temporal (batch pipeline Serpro → Dataprev → CAIXA):** O dado de desligamento declarado no eSocial (ambiente Serpro) não flui em tempo real. Percorre: extração e transformação pela Dataprev no "eSocial Negócio" → consolidação no CNIS → processamento no SIBE-SD/BGSD → envio em lote à CAIXA. Cada etapa tem latência própria, não publicada. O cidadão percebe o bloqueio e liga para o 0800, mas o "problema" pode ser simplesmente a fila de processamento — um estado transitório que nenhum atendente pode resolver ou sequer diagnosticar com precisão.

**Camada 2 — Cadastral (Malha PIS / SINAF):** Mesmo após o processamento do lote, registros com inconsistência cadastral ficam retidos na tabela SINAF para correção manual. Esse mecanismo de proteção da integridade cadastral transforma divergências mínimas — um dígito errado, variação de grafia do nome, discrepância de data de nascimento entre eSocial e base da RFB — em bloqueios indefinidos, sem notificação proativa ao cidadão e sem que o atendente do 0800 consiga identificar ou corrigir.

**Camada 3 — Governança (abismo de alçada):** O operador da CAIXA não tem visão sobre qual das quatro causas de bloqueio está ativa (lote pendente / Malha PIS / divergência MTE / bloqueio por fraude) nem poder de correção em nenhuma delas. O protocolo emitido ao final do atendimento não aciona nenhum workflow corretivo upstream. O cidadão encerra a ligação com um número que não tem valor operacional para resolver o problema.

**Camada 4 — Segurança (blind trust na origem):** A URA opera com confiança cega nos dados recebidos do MTE/Dataprev. Não realiza cruzamentos biométricos independentes nem valida recolhimentos ativos de FGTS antes de disponibilizar saldos. A Operação RedeX demonstrou que a origem desses dados pode ser comprometida por dentro, via credenciais vendidas no SINE. O sistema não falha tecnicamente — ele executa com perfeita eficiência dados fraudulentos. A opacidade da base "eSocial Negócio" da Dataprev — que o TCU (Acórdão 1.114/2025) não conseguiu auditar plenamente por restrições de acesso e volume superior a 2 TB — agrava esse quadro: nem o órgão de controle externo consegue cruzar dados suficientes para detectar padrões de fraude preventivamente.

**O efeito combinado** é uma jornada que obriga o cidadão a múltiplas ligações sem resolução, em que cada canal de voz (0800 CAIXA e 158 MTE) tem visão parcial do problema, com impacto desproporcional sobre populações que dependem do telefone como canal primário: baixa literacia digital, idosos, trabalhadores rurais.

---

## Recomendações (Versão Final — 9 ações em três horizontes)

### Curto prazo
1. **Transparência de diagnóstico ao atendente:** Dar ao operador do 0800 acesso de consulta unificada (read-only) ao status na BGSD/MTE **e ao motivo exato do bloqueio**, distinguindo entre as quatro causas possíveis (lote pendente / Malha PIS / divergência MTE / bloqueio por fraude). Script padronizado por tipo de causa.
2. **Instrumentar métricas ausentes:** Publicar FCR, taxa de abandono e TMA segmentados por tipo de demanda (SD vs. demais). Atualmente, os dados disponíveis são apenas o total agregado da Ouvidoria/SAC CAIXA.
3. **Padronizar horário e canais divulgados:** Uniformizar todas as comunicações com o horário correto (8h–21h, dias úteis; 10h–16h, sábados) e o número correto do WhatsApp (0800 104 0104).

### Médio prazo
4. **Correção assistida por voz:** Permitir que o atendente abra protocolo que dispare automaticamente o fluxo de correção no MTE/Receita Federal (ou pré-preencha o recurso administrativo), reduzindo o prazo de resolução abaixo dos 120 dias legais.
5. **SLA público para recurso administrativo:** Fixar e publicar prazo máximo de análise de recurso, atualmente informalmente relatado entre 10 e 90 dias.
6. **Cruzamento preventivo diário com SISOBI:** CAIXA e Dataprev devem cruzar a folha provisionada do SD com os registros de óbito do SISOBI antes da disponibilização na URA, bloqueando preventivamente saques de falecidos. Fundamentação: achados CGU (R$ 7,6 mi pagos a falecidos em 2021).

### Estrutural
7. **Acesso ao DataLake (cumprimento do Acórdão TCU 1.114/2025):** Cumprir a determinação de estruturar acesso tecnológico à base "eSocial Negócio" para órgãos de controle (item 9.1 do Acórdão), reduzindo a opacidade que impede auditorias preventivas de fraude e inconsistência cadastral.
8. **Autenticação multifatorial no SINE:** Implementar MFA obrigatório para movimentações cadastrais de desligamento nos postos do SINE — especificamente biometria e token físico vinculado à presença no posto de serviço — eliminando o vetor de ataque da Operação RedeX (12.000 requerimentos fraudados, R$ 11,9 mi de prejuízo).
9. **Acessibilidade estrutural:** Garantir trilha de voz humana sem forçar canal digital para públicos vulneráveis, em plena conformidade com o Decreto 11.034/2022 (atendimento humano ≥8h/dia).

---

## Referências Normativas e de Controle

| Instrumento | Emissão | Relevância |
|:---|:---|:---|
| Lei 7.998/1990 | — | Institui o Programa do Seguro-Desemprego e o FAT |
| Lei 8.036/1990 | — | FGTS; papel da CAIXA como agente operador |
| Lei 8.213/1991, art. 124 | — | Vedação de acumulação de benefícios previdenciários |
| Lei 13.709/2018 (LGPD) | — | Tratamento de dados pessoais; revisão de decisão automatizada (art. 20) |
| Decreto 8.373/2014 | — | Institui o eSocial; define Comitê Gestor com Serpro como mantenedor da infraestrutura nacional |
| Decreto 11.034/2022 | — | Regulamenta o SAC: atendimento humano ≥8h, informação de fila, gravação 90 dias, transferência única |
| Portaria MTP 671/2021 | — | eSocial; registro de admissões e desligamentos para fins de SD (e alterações vigentes) |
| Res. CODEFAT 957/2022 | 21/09/2022 | Habilitação automática via CNIS/CAGED/eSocial; recurso em até 120 dias |
| Contrato MTE–Dataprev 17/2021 | 25/05/2021 | Prestação de serviços de TI da Dataprev ao MTE para processamento do SD |
| Res. CMN 4.860/2020, art. 13 | — | Obrigatoriedade do Relatório Institucional de Ouvidoria semestral |
| TCU Acórdão 135/2024 | 31/01/2024 | Auditoria SD jan/2018–jun/2022; R$ 1,9 bi em indícios; TC 006.464/2022-0, rel. Min. Marcos Bemquerer |
| TCU Acórdão 1.114/2025 | 21/05/2025 | Auditoria eSocial/abono/SD; restrições de acesso ao eSocial Negócio; >2 TB; TC 008.443/2024-6, rel. Min. Bruno Dantas |
| CGU (2020, ref. 2018) | 12/02/2020 | R$ 149 mi em 165 mil parcelas irregulares de 2018 |
| CGU (2022, ref. 2021) | 2022 | R$ 97,239 mi em indícios; R$ 7,6 mi a falecidos (SISOBI) |
| PF / Operação RedeX | 04/10/2023 | 12.000 requerimentos fraudados no SINE; R$ 11,9 mi de prejuízo |

---

## Caveats e Limitações (Versão Final)

- **Métricas específicas da URA de SD não são públicas:** Não há divulgação oficial de TMA, FCR ou taxa de abandono segmentados para o Seguro-Desemprego no 0800; os números disponíveis são agregados da Ouvidoria/SAC CAIXA (todos os serviços).
- **Dados de auditoria são de 2018–2022 para a maioria dos valores financeiros:** O TCU Acórdão 135/2024 cobre até jun/2022; os dados CGU referem-se a 2018 e 2021. O Acórdão TCU 1.114/2025 apresenta dados de universo 2022–2024 mas sem novos valores de irregularidade financeira individual. Não há série 2024–2025 plenamente auditada publicada até a data deste relatório (junho de 2026).
- **Relatos do Reclame Aqui são qualitativos** — evidência de experiência do usuário, não estatística representativa.
- **Acórdão TCU 1.114/2025 — limitações:** O acórdão determina acesso ao DataLake em 10 dias (item 9.1) e cita problemas operacionais da Dataprev e volume >2 TB. **Não menciona** SISOBI, cruzamento de óbitos nem impacto na base transacional da CAIXA. Dados de pagamentos a falecidos têm como fonte correta as auditorias da **CGU**.
- **Divisão Serpro/Dataprev no eSocial:** Serpro mantém o ambiente nacional (infraestrutura); Dataprev extrai dados e opera a base "eSocial Negócio" e o CNIS. O Comitê Gestor do eSocial (Decreto 8.373/2014) é o órgão deliberativo com representação do MTE, INSS, Receita Federal e CAIXA (como agente operador do FGTS).
- **Reclamações CAIXA 2025:** O total de 264.831 e crescimento de +27% referem-se ao **total agregado de todos os canais**, não à Ouvidoria interna (que caiu 33%). O canal com maior crescimento foi o Consumidor.gov (+59%).
- **Sobreposição 0800 (CAIXA) × 158 (MTE):** Parte da frustração no 0800 decorre de o cidadão usar o canal do agente pagador para um problema cuja alçada é do MTE.
