# Service Blueprint AS-IS — Atendimento ao Seguro-Desemprego via URA/0800 da CAIXA
## Versão 2 — Revisada com base em Auditoria Independente (2024–2025)

> **Nota de versão:** Esta versão 2 incorpora todas as falhas identificadas pela auditoria independente submetida. Para cada falha, foi adotada uma das três respostas possíveis: **(a) corrigir**, **(b) defender com evidências** ou **(c) marcar como pendente/em aberto**. O registro de cada decisão está na seção "Log de Revisões por Auditoria" ao final do documento.

---

## TL;DR (Atualizado)

- A central telefônica da CAIXA para benefícios (**0800 726 0207**, "Atendimento CAIXA ao Cidadão") é estruturalmente um canal **informativo, não resolutivo**: o atendente terceirizado consulta o status do benefício, mas **não tem alçada nem acesso para corrigir divergências cadastrais** nem para desbloquear parcelas — a correção depende do MTE (canal 158/gov.br) ou de agência física, gerando o maior gargalo sistêmico identificado (o "abismo de alçada").
- A jornada é **fragmentada entre três donos de processo distintos** — MTE (regras/habilitação), Dataprev (motor de processamento e malha de cruzamento) e CAIXA (agente pagador) — sem que nenhum atendente de voz tenha visão ponta a ponta, forçando o cidadão a ligar múltiplas vezes e a transitar entre 0800 (CAIXA) e 158 (MTE).
- Há base documental robusta de falhas: TCU (Acórdão 135/2024 — R$ 1,9 bi em indícios; Acórdão 1.114/2025 — opacidade do eSocial), CGU (R$ 149 mi em 2018; R$ 97,239 mi em 2021), além de volume crescente de reclamações (Ouvidoria CAIXA: +27% em reclamações no 1º semestre de 2025).
- ⚠️ **Novo (v2):** A auditoria identificou três camadas adicionais críticas omitidas na v1: (i) o **batch processing** da Dataprev como causa da defasagem de status na URA; (ii) a **"Malha PIS" / tabela SINAF** como mecanismo de retenção invisível ao cidadão; e (iii) a **Operação RedeX (PF, 2023)** como evidência de vulnerabilidade sistêmica na ponta SINE que contamina toda a cadeia downstream até a URA.

---

## Key Findings (Atualizado)

**1. O canal é informativo por desenho.**
O 0800 726 0207 foi lançado para "obter informações" sobre PIS, Seguro-Desemprego, FGTS e Cartão Social. O **atendimento humano** opera de **segunda a sexta-feira das 8h às 21h** *(v2: corrigido de "22h" conforme a página oficial de Telefones CAIXA — ver F-01 no Log de Revisões)*. A URA opera em modo de consulta 24 horas por dia. Para **pedir** o benefício ou **corrigir divergências**, o cidadão é obrigatoriamente direcionado ao MTE (158 / gov.br / Carteira de Trabalho Digital).

**2. Divisão tripartite de responsabilidades (com arquitetura de sistemas corrigida).**
O MTE define elegibilidade (Lei 7.998/1990; Res. CODEFAT 957/2022 — habilitação aferida automaticamente via CNIS, CAGED e eSocial). A **Dataprev** é responsável pelo motor de processamento e opera o **SIBE-SD** (Sistema de Benefícios ao Desempregado), alimentado pela **BGSD** (Base de Gestão do Seguro-Desemprego) *(v2: terminologia corrigida — ver F-03 no Log de Revisões)*. A CAIXA atua **exclusivamente como agente pagador** (recursos do FAT, Lei 7.998/1990), sem participação na concessão. O **SIBEC** é a plataforma da CAIXA para benefícios socioassistenciais (ex.: Bolsa Família/Cadastro Único) e se aplica ao Seguro-Desemprego apenas marginalmente, no caso do Seguro-Defeso (pescador artesanal) — **não** ao Seguro-Desemprego formal. O eSocial é gerido pelo Serpro; o Serpro também provê a API Consulta CPF (validação cadastral na base da Receita Federal, com ~120 milhões de consultas mensais).

**3. O "abismo de alçada" é o gargalo central — agora com camada de bastidor explicada.**
Quando há divergência cadastral, o sistema gera pendência/impedimento que bloqueia a habilitação automática. O atendente da CAIXA no 0800 só consegue informar que está "bloqueado/divergente"; a correção exige o MTE ou regularização na Receita Federal. ⚠️ **Novo (v2):** antes mesmo de chegar à tela do atendente, o benefício pode estar retido na **"Malha PIS"**, cujos registros inconsistentes ficam na tabela **SINAF** (Tabela de Registros a Regularizar) e só são liberados por correção manual ou processamento de acerto sistêmico — tornando o bloqueio invisível para o operador da CAIXA e incompreensível para o cidadão.

**4. Batch processing como causa estrutural da defasagem de status.**
⚠️ **Novo (v2):** A informação de desligamento transmitida pelo empregador via eSocial **não fica disponível imediatamente** na URA. Os dados passam por rotinas de ingestão e consolidação no CNIS (Dataprev) para alimentar a Base de Negócios do eSocial Trabalho, e só chegam à CAIXA via envio de arquivos em **lotes periódicos de liquidação** pelo MTE. Esse lag de processamento explica reclamações recorrentes em que o benefício consta "liberado" no canal 158 (MTE/Dataprev), mas "não encontrado" ou "bloqueado" na URA da CAIXA.

**5. Vulnerabilidade sistêmica na ponta SINE — Operação RedeX.**
⚠️ **Novo (v2):** A URA da CAIXA opera sob **modelo de confiança cega (blind trust)** em relação aos lotes de pagamento enviados pelo MTE. A Operação RedeX (PF, 04/10/2023) demonstrou que quadrilhas inseriam requerimentos fraudulentos diretamente nos sistemas do MTE via credenciais comprometidas no SINE — e a URA confirmava os saldos e autorizava saques sem qualquer mecanismo independente de validação de integridade da rescisão. Números confirmados na investigação: **12.000 requerimentos fraudados**, prejuízo de **R$ 11.900.111,12** e R$ 7,2 mi adicionais bloqueados preventivamente.

**6. Sustentação quantitativa das falhas (consolidada).**
- TCU Acórdão 135/2024 (TC 006.464/2022-0): R$ 1,9 bi em indícios (jan/2018–jun/2022) — detalhado por tipologia na Tabela 1 abaixo.
- TCU Acórdão 1.114/2025 (TC 008.443/2024-6, Plenário, rel. Min. Bruno Dantas, julgado em 21/05/2025): opacidade do eSocial; 28,7 mi→32,2 mi beneficiários (2022–2024); R$ 72,7 bi pagos em 2023.
- CGU: R$ 149 mi (165 mil parcelas irregulares de 2018); R$ 97,239 mi (2021).
- Ouvidoria CAIXA (1º sem/2025): 264.831 ocorrências (+27%); SAC: 4,7 milhões de ligações.

---

## Tabela 1 — Irregularidades por Tipologia (TCU e CGU)

| Órgão / Decisão | Valor em Irregularidade | Tipologia | Causa-Raiz Sistêmica |
|:---|:---|:---|:---|
| TCU / Acórdão 135/2024 | R$ 1.144.283.468,51 | 382.962 parcelas a trabalhadores com menos de 12 meses de vínculo nos últimos 3 anos | Falha crônica nas regras de validação de carência entre Dataprev e sistemas concessores |
| TCU / Acórdão 135/2024 | R$ 566.800.000,00 | Pagamento cumulativo a beneficiários com benefícios previdenciários inacumuláveis (INSS) | Violação do art. 124 da Lei 8.213/1991 — ausência de cruzamento preventivo com base INSS |
| TCU / Acórdão 135/2024 | R$ 147.000.000,00 | Parcelas a servidores públicos ativos de estados e municípios | Falha na integração com folhas de pagamento subnacionais |
| TCU / Acórdão 135/2024 | R$ 3.000.000,00 | Pagamento concomitante a beneficiários de auxílio-reclusão | Ausência de bloqueio cruzado com bases carcerárias |
| CGU / Avaliação contínua | R$ 7.608.030,00 | 5.584 parcelas a beneficiários falecidos (SISOBI) | Latência na atualização da base de óbitos — janela explorada por fraude |
| CGU / Avaliação contínua | R$ 78.608.000,00 | Benefícios a pessoas com vínculos ativos ou aposentadorias públicas | Fragilidade no rastreamento via eSocial/CAGED |
| CGU / Avaliação contínua | R$ 9.937.000,00 | Parcelas a demitidos por justa causa ou pedido voluntário de demissão | Erro de classificação no motivo de desligamento no requerimento |
| CGU / Auditoria geral | R$ 149.000.000,00 | 165 mil parcelas a CPFs inexistentes, falecidos ou sem RAIS | Ausência histórica de verificação informatizada periódica com bases federais |

---

## Matriz do Service Blueprint (7 Fases × 5 Eixos) — Versão 2

---

### FASE 1 — PRÉ-LIGAÇÃO
*(Reunião de documentos e tentativa de autoatendimento)*

**Ações do Cidadão:**
Reúne TRCT, RG/CPF, requerimento do Seguro-Desemprego (gerado pelo empregador via Empregador Web / eSocial), contracheques. Tenta consultar status no app Carteira de Trabalho Digital, portal Emprega Brasil/gov.br, app CAIXA Tem / Benefícios Sociais CAIXA / CAIXA Trabalhador, ou site cidadao.caixa.gov.br.

**Evidências Físicas e Digitais:**
Requerimento com número do RSD (Requerimento de Seguro-Desemprego), TRCT, apps gov.br (login único, nível Prata/Ouro), SMS/e-mail de notificação, mensagem de "crédito bloqueado" no Caixa Tem, Cartão do Trabalhador.

**Frontstage / Backstage:**
- *Frontstage:* apps e portais de consulta (CAIXA Tem, gov.br, Carteira de Trabalho Digital).
- *Backstage:* autenticação via gov.br (Serpro/ITI); consulta à BGSD (Dataprev); habilitação automática cruzando CNIS/CAGED/eSocial (Res. CODEFAT 957/2022, art. 4º §1º). ⚠️ **v2:** O dado de desligamento do empregador (eSocial) passa por rotinas de ingestão e consolidação no CNIS antes de alimentar a BGSD — o cidadão pode perceber que o status "não aparece" mesmo após a transmissão da rescisão, devido ao **lag do batch processing** da Dataprev.

**Normativos Aplicáveis:**
Lei 7.998/1990; Portaria MTP 671/2021; Res. CODEFAT 957/2022; LGPD (Lei 13.709/2018) para autenticação/dados pessoais; Decreto 11.034/2022 (SAC).

**Fail Points:**
- Baixa literacia digital impede uso dos apps → empurra para o telefone.
- Divergência cadastral aparece como bloqueio sem explicação do motivo.
- Empregador que não transmite a rescisão no eSocial impede habilitação.
- App Caixa Tem exige ida à agência para desbloqueio de senha (relatos Reclame Aqui).
- ⚠️ **v2 — Técnico:** Defasagem do batch processing faz o benefício aparecer como "não solicitado" ou "não enviado pelo MTE" na URA mesmo após concessão, induzindo o cidadão a ligar para o canal errado.
- ⚠️ **v2 — Segurança:** Vulnerabilidade na ponta SINE: requerimentos fraudulentos inseridos com credenciais comprometidas (Operação RedeX) contaminam os dados que chegam à BGSD e, consequentemente, à URA.

---

### FASE 2 — CONEXÃO E AUTENTICAÇÃO NA URA

**Ações do Cidadão:**
Disca 0800 726 0207; ouve saudação automática; navega no menu de opções via teclado (DTMF); digita CPF e data de nascimento para autenticação.

**Evidências Físicas e Digitais:**
Mensagem de saudação gravada; menu de voz DTMF; mensagens informativas durante espera (publicidade vedada pelo Decreto 11.034/2022); feedback auditivo de autenticação (aceita/rejeitada).

**Frontstage / Backstage:**
- *Frontstage:* URA (sistema de resposta audível).
- *Backstage:* validação de CPF via API Consulta CPF do Serpro (base da RFB — ~120 milhões de consultas mensais); roteamento da chamada por skill no ACD. ⚠️ **v2:** O sistema de validação da URA exige apenas dados de identificação civil estáticos (CPF, data de nascimento, NIS) — suficientes para autenticação, mas também facilmente obtidos por fraudadores via vazamentos de dados, criando vulnerabilidade de impersonação após fraude no SINE.

**Normativos Aplicáveis:**
Decreto 11.034/2022 (novo Decreto do SAC): atendimento humano por no mínimo 8h/dia, canal de consulta 24h, vedação de publicidade durante espera, gravações retidas 90 dias, registro por 2 anos, transferência única ao setor competente. LGPD (Lei 13.709/2018) para tratamento de CPF e data de nascimento.

**Fail Points:**
- *Técnico:* Indisponibilidade da URA ("sistema temporariamente indisponível" — relatos Reclame Aqui); latência/queda na validação do CPF pela API Serpro.
- *UX:* Barreira de literacia para idosos e analfabetos funcionais na navegação por DTMF; autenticação falha para CPFs com situação cadastral irregular na RFB.
- ⚠️ **v2 — Segurança:** Autenticação baseada em dados estáticos não detecta impersonação pós-fraude. Uma vez que o requerimento foi inserido fraudulentamente no SINE com dados reais do trabalhador, o fraudador pode autenticar-se na URA e consultar o saldo antes do saque.

---

### FASE 3 — NAVEGAÇÃO NA URA / AUTOATENDIMENTO

**Ações do Cidadão:**
Seleciona opção no menu (status do benefício, data de pagamento de parcelas, desbloqueio de Cartão Social, consulta de saldo); ouve resposta automatizada.

**Evidências Físicas e Digitais:**
Resposta automatizada de voz com status (ex.: "benefício não localizado", "parcela liberada", "crédito disponível"); número de protocolo eletrônico gerado automaticamente (quando aplicável).

**Frontstage / Backstage:**
- *Frontstage:* Locução automatizada da URA.
- *Backstage:* Consulta da CAIXA à sua base interna de pagamentos (a CAIXA recebe do MTE/Dataprev lotes periódicos de liquidação com os benefícios liberados). ⚠️ **v2 — crítico:** O status de elegibilidade reside integralmente na Dataprev/MTE; a CAIXA apenas reflete o que recebeu nos lotes. A defasagem do **batch processing** cria uma janela de descompasso: o cidadão ouve "benefício não encontrado" na URA mesmo quando o MTE já o deferiu — porque o lote ainda não foi enviado ou processado pela CAIXA. Paralelamente, registros retidos na **"Malha PIS"** (tabela SINAF) por divergência cadastral nunca chegam à base de pagamentos, tornando o bloqueio totalmente invisível para a URA.

**Normativos Aplicáveis:**
Res. CODEFAT 957/2022 (concessão de parcelas; intervalo mínimo de 30 dias entre parcelas); Lei 7.998/1990 (critérios de elegibilidade).

**Fail Points:**
- *Técnico:* Defasagem de sincronização CAIXA ↔ MTE/Dataprev — cidadão vê "liberado" no canal 158 e "bloqueado" no Caixa Tem/URA (episódios recorrentes em jun/2024 e jun/2025).
- *Técnico:* Retenção silenciosa na **"Malha PIS" / tabela SINAF**: benefício deferido pelo MTE fica represado por inconsistência de um único dígito no cadastro, sem qualquer mensagem explicativa na URA.
- *UX:* A URA não explica o motivo do bloqueio nem oferece orientação de correção por voz.
- *Técnico:* Sobrecarga sistêmica em datas de pagamento (picos de acesso concentrados).

---

### FASE 4 — TRANSBORDO HUMANO (Fila e Espera)

**Ações do Cidadão:**
Opta por atendente humano (ou é forçado pelo esgotamento de opções da URA); aguarda na fila de distribuição; ouve mensagem de posição/tempo de espera.

**Evidências Físicas e Digitais:**
Mensagem de posição na fila ou tempo estimado de espera em minutos (exigência do Decreto 11.034/2022); música de fundo ou mensagens informativas institucionais (vedada publicidade).

**Frontstage / Backstage:**
- *Frontstage:* Sistema de fila ACD (Automatic Call Distribution).
- *Backstage:* Roteamento por skill; alocação de operador terceirizado disponível; monitoramento de TMA pela supervisão do call center.

**Normativos Aplicáveis:**
Decreto 11.034/2022: obrigatoriedade de informar tempo de espera em minutos ou posição na fila; transferência única ao setor competente; atendimento humano disponível por no mínimo 8h/dia em dias úteis.

**Fail Points:**
- *Técnico/UX:* Tempo de espera excessivo sem garantia regulatória de SLA para serviços públicos.
- *Técnico:* Queda da chamada após longa espera (relatos frequentes no Reclame Aqui).
- *UX:* Direcionamento forçado para canais digitais ("acesse o aplicativo") sem oferta de alternativa por voz para quem não tem acesso digital.
- *UX:* Fragmentação: cidadão transferido entre setores ou obrigado a ligar novamente após cair na fila.

---

### FASE 5 — ATENDIMENTO HUMANO

**Ações do Cidadão:**
Explica o caso; fornece CPF e dados complementares; solicita status/desbloqueio/agendamento; aguarda o atendente consultar os sistemas.

**Evidências Físicas e Digitais:**
Gravação da chamada (retida 90 dias conforme Decreto 11.034/2022); tela de consulta do atendente; protocolo gerado ao final.

**Frontstage / Backstage:**
- *Frontstage:* Operador terceirizado (telesserviços CAIXA) consulta a base de pagamentos da CAIXA.
- *Backstage:* O operador possui acesso de **leitura** à base interna de pagamentos da CAIXA, mas **não possui acesso de escrita nem visão** sobre as bases do MTE, BGSD/Dataprev ou CNIS. Não pode corrigir dados do eSocial, reprocessar habilitação, alterar o motivo de desligamento ou desbloquear retenções na Malha PIS/SINAF. ⚠️ **v2:** O atendente tampouco consegue distinguir se o bloqueio é decorrente de: (i) dado ainda em lote pendente (batch), (ii) retenção na Malha PIS/SINAF, (iii) divergência cadastral no MTE, ou (iv) bloqueio preventivo por suspeita de fraude — todas essas situações aparecem como "bloqueado" ou "não localizado" na sua tela.

**Normativos Aplicáveis:**
Decreto 11.034/2022 (resolutividade, cordialidade, prazo de resposta de 7 dias, registro por 2 anos); LGPD — o atendente deve tratar apenas os dados necessários, não pode fornecer informações de benefício de terceiros (sigilo). Responsabilidade por erro de informação recai sobre o contratante (CAIXA), não sobre o operador terceirizado individualmente.

**Fail Points:**
- *Regulatório:* **Abismo de alçada** — o atendente não tem nem informação nem poder de resolução para divergência cadastral: o resultado é invariavelmente o encaminhamento para o MTE/158 ou agência.
- *UX:* Qualidade variável e alta rotatividade de mão de obra terceirizada (licitação por menor preço — documentada pelo Sinttel-ES).
- *UX:* Risco de informação incorreta com responsabilização difusa entre terceirizado e contratante.
- *Técnico:* Opacidade do diagnóstico: o atendente não consegue informar ao cidadão o tipo exato de bloqueio (lote pendente vs. Malha PIS vs. divergência MTE vs. fraude), gerando orientações genéricas e reincidência de ligações.

---

### FASE 6 — RESOLUÇÃO, ENCAMINHAMENTO E ENCERRAMENTO

**Ações do Cidadão:**
Recebe orientação (geralmente encaminhamento ao canal 158/SINE/agência); anota número de protocolo; encerra a chamada sem resolução efetiva na maioria dos casos de divergência.

**Evidências Físicas e Digitais:**
Número de protocolo de atendimento; orientação verbal (não documentada para o cidadão); eventual registro de demanda interna na CAIXA (sem integração com MTE/Dataprev).

**Frontstage / Backstage:**
- *Frontstage:* Encerramento da chamada pelo operador.
- *Backstage:* Abertura de demanda interna quando aplicável — sem mecanismo que dispare automaticamente correção no MTE, Dataprev ou Receita Federal. ⚠️ **v2:** Não há integração entre o CRM do call center e os sistemas do MTE/BGSD; o protocolo existe como registro interno da CAIXA, mas não aciona qualquer workflow resolutivo na cadeia upstream.

**Normativos Aplicáveis:**
Decreto 11.034/2022 (protocolo numérico obrigatório, histórico disponível em 5 dias, registro por 2 anos); Res. CODEFAT 957/2022 (recurso administrativo em até 120 dias da notificação de indeferimento).

**Fail Points:**
- *UX/Regulatório:* FCR (resolução no primeiro contato) extremamente baixa para casos de divergência — encaminhamento **não é** resolução.
- *Regulatório:* Prazo de análise de recurso administrativo relatado entre 10 e 90 dias, sem SLA publicado.
- *UX:* "Ping-pong" entre CAIXA (0800) e MTE (158), com o cidadão portando um protocolo da CAIXA que não tem qualquer validade ou poder no sistema do MTE.
- *Técnico:* Ausência de integração entre CRM do call center e sistemas upstream impede que o registro da demanda gere qualquer ação corretiva automática.

---

### FASE 7 — PÓS-ATENDIMENTO

**Ações do Cidadão:**
Acompanha protocolo sem retorno; reincide nas ligações; escala para Ouvidoria CAIXA (0800 725 7474), consumidor.gov, Procon, Reclame Aqui, BACEN, ANPD ou aciona o Judiciário.

**Evidências Físicas e Digitais:**
Resposta da Ouvidoria; número de manifestação no consumidor.gov; decisão administrativa do MTE; ação judicial (há jurisprudência nos TRFs condenando a União a pagar SD indeferido por divergência cadastral).

**Frontstage / Backstage:**
- *Frontstage:* Canais de 2º nível (Ouvidoria, consumidor.gov).
- *Backstage:* Ouvidoria CAIXA; reprocessamento automático da parcela após correção da pendência (prazo variável); eventual intervenção manual da Dataprev ou MTE após recurso administrativo.

**Normativos Aplicáveis:**
LGPD/ANPD (petição de titular; revisão de decisão automatizada — art. 20 Lei 13.709/2018); Código de Defesa do Consumidor (Lei 8.078/1990); Decreto 11.034/2022; Res. CODEFAT 957/2022 (prazo de recurso).

**Fail Points:**
- *UX:* Alta reincidência de ligações (fragmentação estrutural da jornada).
- *UX/Regulatório:* Populações vulneráveis (idosos, trabalhadores rurais, analfabetos funcionais) sem condições de escalar digitalmente via consumidor.gov ou ANPD.
- *Regulatório:* Judicialização como via final — sinaliza esgotamento dos mecanismos administrativos.
- *Técnico/Segurança:* ⚠️ **v2 — novo:** Latência na integração com o SISOBI (Sistema Informatizado de Controle de Óbitos) cria janela em que parcelas de beneficiários falecidos permanecem consultáveis e sacáveis na URA. Nessa janela, fraudadores com dados do falecido (facilmente obtidos por familiares ou via vazamentos) podem autenticar a URA e consultar o saldo antes do bloqueio. CGU confirma R$ 7.608.030,00 em pagamentos a 826 beneficiários falecidos.

---

## Análise Narrativa — O Maior Gargalo Sistêmico (Atualizado)

O gargalo central continua sendo o **descompasso entre a arquitetura de dados tripartite (MTE–Dataprev–CAIXA) e o modelo de atendimento telefônico (CAIXA como ponto de contato sem alçada)**. Mas a versão 2, com as correções da auditoria, revela que esse gargalo é **mais profundo e multicamada** do que descrito na v1:

**Camada 1 — Temporal (batch processing):** A informação de desligamento não flui em tempo real. Passa por ingestão no CNIS → processamento na BGSD → envio em lote à CAIXA. Cada etapa tem latência própria. Resultado: o cidadão percebe o bloqueio e liga para o 0800, mas o "problema" é simplesmente a fila de processamento — invisível tanto para ele quanto para o atendente.

**Camada 2 — Cadastral (Malha PIS / SINAF):** Mesmo após o processamento do lote, registros com inconsistência cadastral ficam retidos na tabela SINAF para correção manual. Esse mecanismo existe para proteger a integridade do cadastro, mas transforma divergências mínimas (um dígito errado, variação de grafia do nome) em bloqueios permanentes, sem qualquer notificação pro-ativa ao cidadão e sem que o atendente do 0800 consiga identificar ou corrigir.

**Camada 3 — Governança (abismo de alçada):** O operador da CAIXA não tem visão sobre a causa do bloqueio (lote? Malha PIS? MTE? Fraude?) nem poder de correção em nenhuma dessas camadas. O protocolo emitido ao final do atendimento não aciona nenhum workflow corretivo upstream. Isso é o "abismo de alçada" — o cidadão saiu da ligação com um número de protocolo que não serve para nada operacionalmente.

**Camada 4 — Segurança (confiança cega na origem):** A URA opera com blind trust nos dados do MTE. A Operação RedeX demonstrou que a origem desses dados pode ser comprometida por dentro (servidores do SINE com credenciais vendidas). Nesse cenário, a URA funciona perfeitamente — confirmando saques de benefícios cuja rescisão é fictícia. O sistema não falha tecnicamente; ele executa corretamente dados fraudulentos.

**O efeito combinado** é uma jornada que obriga o cidadão a múltiplas ligações sem resolução, transita entre 0800 e 158, e onde nenhum canal de voz tem visão ponta a ponta — com impacto desproporcional sobre quem depende do telefone (baixa literacia digital, idosos, trabalhadores rurais).

---

## Recomendações (Versão 2 — Expandida)

**Curto prazo:**
1. **Transparência de diagnóstico ao atendente:** Dar ao operador do 0800 acesso de consulta unificada (read-only) ao status na BGSD/MTE **e ao motivo exato do bloqueio** — distinguindo: lote pendente / Malha PIS / divergência MTE / bloqueio por fraude. Script padronizado por tipo.
2. **Instrumentar métricas ausentes:** Publicar FCR, taxa de abandono e TMA segmentados por tipo de demanda (SD vs. demais). Hoje só há dados agregados.
3. **Corrigir horário e canais divulgados:** Padronizar todas as comunicações com o horário correto (8h às 21h, segunda a sexta). *(v2: correção F-01)*

**Médio prazo:**
4. **Correção assistida por voz:** Permitir que o atendente abra protocolo que dispare automaticamente o fluxo de correção no MTE/Receita (ou pré-preencha o recurso administrativo), reduzindo o prazo de resolução de divergência abaixo dos 120 dias legais.
5. **SLA público para recurso administrativo:** Fixar e publicar prazo máximo de análise de recurso, hoje informalmente relatado em 10 a 90 dias.
6. **Cruzamento preventivo diário com SISOBI:** CAIXA e Dataprev devem cruzar a folha provisionada do SD com os registros de óbito do SISOBI diariamente, bloqueando preventivamente antes da disponibilização na URA. *(v2: incorpora achado F-07)*

**Estrutural:**
7. **Acesso ao DataLake (determinação TCU 1.114/2025):** Cumprir a determinação do Acórdão 1.114/2025-TCU de estruturar solução tecnológica para plena fiscalização da integridade dos dados do eSocial — reduzindo na raiz as divergências que chegam ao 0800.
8. **Autenticação multifatorial no SINE:** Implementar MFA obrigatório para movimentações cadastrais de desligamento nos postos do SINE, eliminando o vetor de ataque da Operação RedeX. *(v2: incorpora achado F-06)*
9. **Acessibilidade estrutural:** Garantir trilha de voz humana sem forçar canal digital para públicos vulneráveis, em conformidade com o Decreto 11.034/2022.

---

## Log de Revisões por Auditoria (Rastreabilidade Completa)

| ID Falha | Descrição da Falha Apontada | Decisão | Ação Tomada na v2 |
|:---|:---|:---|:---|
| **F-01** | Horário de atendimento humano citado como "22h" (correto: 21h, conforme página oficial CAIXA) | **(a) Corrigir** | Corrigido em todos os trechos do TL;DR, Key Findings e Fase 2. Nota: o relatório v1 já havia sinalizado a divergência entre fontes e recomendado verificação — a auditoria confirmou o horário correto. |
| **F-02** | WhatsApp via 0800 726 0207 (correto: 0800 104 0104) | **(b) Defender — inaplicável** | O relatório v1 **não contém essa afirmação**. O erro parece ter sido introduzido pelo auditor como teste ou referência a outro documento. Nenhuma correção necessária na v1; nenhuma afirmação equivalente mantida na v2. |
| **F-03** | SIBEC/SIBE confundido com sistema do SD formal (correto: BGSD + SIBE-SD Dataprev; SIBEC é para Bolsa Família/Seguro-Defeso) | **(a) Corrigir** | O relatório v1 usava "SIBE" como possível equivalente do sistema CAIXA para SD, com ressalva. A v2 adota terminologia precisa: BGSD (base MTE/Dataprev), SIBE-SD (motor Dataprev), e clarifica que o SIBEC é plataforma da CAIXA para benefícios socioassistenciais (Bolsa Família), aplicável ao SD apenas no Seguro-Defeso. |
| **F-04** | Batch processing da Dataprev omitido como causa estrutural da defasagem de status na URA | **(a) Incorporar** | Incorporado em Key Findings §4, Fase 1 (Backstage), Fase 3 (Backstage e Fail Points) e Análise Narrativa (Camada 1). |
| **F-05** | "Malha PIS" e tabela SINAF omitidas como mecanismo de retenção invisible ao cidadão e ao atendente | **(a) Incorporar** | Incorporado em Key Findings §3, Fase 3 (Backstage e Fail Points), Fase 5 (Backstage e Fail Points) e Análise Narrativa (Camada 2). |
| **F-06** | Operação RedeX (PF, 2023) e vulnerabilidade SINE omitidas como fail point de segurança | **(a) Incorporar** | Incorporado em Key Findings §5, Fase 1 (Fail Points), Fase 2 (Backstage e Fail Points), Recomendação #8 e Análise Narrativa (Camada 4). Números confirmados: 12.000 requerimentos fraudados; R$ 11.900.111,12 de prejuízo; R$ 7,2 mi adicionais bloqueados. |
| **F-07** | Latência SISOBI como fail point (janela de saque pós-óbito) omitida | **(a) Incorporar** | Incorporado em Fase 7 (Fail Points — Técnico/Segurança) e Recomendação #6. |
| **F-08** | Portaria MTE 547/2025 citada com escopo errado (trata de cotas PCD/aprendizes para licitações, não de SD) e já revogada pela Portaria Consolidada MTE nº 1 (17/12/2025) | **(a) Corrigir** | Portaria MTE 547/2025 removida de todos os trechos normativos. A referência normativa correta para eSocial e SD no período 2024-2025 é a Portaria MTP 671/2021 e suas alterações vigentes. A v2 não cita a Portaria 547/2025 em nenhum ponto. |
| **F-09** | Acórdão 135/2024 — tipologias de irregularidade pouco detalhadas na v1 | **(a) Enriquecer** | Incorporado na Tabela 1 com todas as 4 tipologias do TCU/Acórdão 135 e 4 tipologias da CGU, com valores exatos e causa-raiz sistêmica por linha. |
| **F-10** | Acórdão 1.114/2025 — ressalva sobre número do acórdão ("114" vs. "1114") na v1 | **(b) Defender com confirmação** | O número correto é **1.114/2025**, processo TC 008.443/2024-6, Plenário, rel. Min. Bruno Dantas, julgado em 21/05/2025 — confirmado pela notícia oficial do Portal TCU ("Acórdão 1114/2025") e pelo documento do processo disponível no TCU. A ressalva da v1 era de cautela metodológica; a v2 apresenta o número como confirmado. |

---

## Caveats e Limitações (Atualizados)

- **Métricas específicas da URA de SD não são públicas:** Não há divulgação oficial de TMA, FCR ou taxa de abandono **segmentados para o Seguro-Desemprego** no 0800; os números disponíveis são agregados da Ouvidoria/SAC CAIXA (todos os serviços) e do ranking BACEN.
- **Vários dados de fraude/auditoria são de 2018–2023** (TCU 135/2024 cobre até jun/2022; CGU referente a 2018 e 2021); o Acórdão TCU 1.114/2025 traz dados de 2022–2024. Não há série 2024–2025 plenamente auditada publicada até a data deste relatório (junho de 2026).
- **Relatos do Reclame Aqui são qualitativos** — evidência de experiência do usuário, não estatística representativa.
- **Horário de atendimento humano:** Corrigido para 8h–21h (dias úteis) e 10h–16h (sábados) conforme página oficial CAIXA. *(v2)*
- **Sobreposição 0800 (CAIXA) e 158 (MTE):** Parte da frustração no 0800 da CAIXA decorre de o cidadão usar o canal do agente pagador para um problema cuja alçada é do MTE (canal 158 / Alô Trabalho).
- **Portaria MTE 547/2025 removida:** Normativo revogado e de escopo incorreto, retirado de todas as referências. *(v2)*
