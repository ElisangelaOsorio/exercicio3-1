# Service Blueprint AS-IS — Atendimento ao Seguro-Desemprego via URA/0800 da CAIXA (2024–2025)

## TL;DR
- A central telefônica da CAIXA para benefícios (**0800 726 0207**, "Atendimento CAIXA ao Cidadão") é estruturalmente um canal **informativo, não resolutivo**: o atendente terceirizado consulta o status do benefício, mas **não tem alçada nem acesso para corrigir divergências cadastrais** nem para desbloquear parcelas — a correção depende do MTE (canal 158/gov.br) ou de agência, gerando o maior gargalo sistêmico (o "abismo de alçada").
- A jornada é **fragmentada entre três donos de processo distintos** — MTE (regras/habilitação), Dataprev (motor de processamento e malha de cruzamento) e CAIXA (agente pagador) — sem que nenhum atendente de voz tenha visão ponta-a-ponta, forçando o cidadão a ligar múltiplas vezes e a transitar entre 0800 (CAIXA) e 158 (MTE).
- Há base documental robusta de falhas: TCU (Acórdão 135/2024 — R$ 1,9 bi em indícios; Acórdão 1.114/2025 — opacidade do eSocial), CGU (R$ 149 mi referentes a 2018; R$ 97,239 mi referentes a 2021), além de volume de reclamações crescente (Ouvidoria CAIXA: +27% em reclamações no 1º semestre de 2025).

## Key Findings

**1. O canal é informativo por desenho.** O 0800 726 0207 foi lançado para "obter informações" sobre PIS, Seguro-Desemprego, FGTS e Cartão Social. Segundo o gerente nacional de Telesserviços da CAIXA à época do lançamento, Ricardo Vieira, a expectativa era de **250 mil ligações por dia**, com **1.500 operadores** "com treinamento específico para atender aos cidadãos". O atendimento eletrônico (URA) é 24/7 para consultas; o atendimento humano funciona em horário restrito. (Observação: o material de lançamento noticiou "de segunda a sexta-feira, das 8h às 22h, e sábado, das 10h às 16h", enquanto a página atual de atendimento da CAIXA indica 8h às 21h em dias úteis — há divergência entre as fontes.) Para **pedir** o benefício ou **corrigir divergências**, o cidadão é direcionado ao MTE (158 / gov.br / Carteira de Trabalho Digital).

**2. Divisão tripartite de responsabilidades.** O MTE define elegibilidade (Lei 7.998/1990; Portaria MTP 671/2021; Resolução CODEFAT 957/2022 — habilitação aferida "de forma automática pelo sistema seguro-desemprego" via CNIS, CAGED e eSocial). A Dataprev é "responsável pelo sistema que analisa se o cidadão atende às exigências" e faz a verificação online em tempo real (eram "mais de 3 milhões de pedidos" em 2023, 75,3% digitais). A CAIXA é apenas o **agente pagador** (recursos do FAT, Lei 7.998/1990). O eSocial é gerido pelo Serpro; o Serpro também provê a API Consulta CPF (validação cadastral na base da Receita Federal, com média de ~120 milhões de consultas mensais).

**3. O "abismo de alçada" é o gargalo central.** Quando há divergência cadastral (nome/CPF/data de nascimento/nome da mãe/sexo entre as bases RFB, CNIS e requerimento), o sistema gera pendência/impedimento que **bloqueia a habilitação automática**. O atendente da CAIXA no 0800 só consegue informar que está "bloqueado/divergente"; a correção exige o MTE (158/SINE/Superintendência) ou regularização do CPF na Receita. As respostas-padrão da CAIXA no Reclame Aqui confirmam esse roteiro: orientam o cidadão a "entrar em contato com o Ministério do Trabalho e Emprego pelo número de teleatendimento 158".

**4. Sustentação quantitativa das falhas.** TCU Acórdão 135/2024 (R$ 1,9 bi em indícios, jan/2018–jun/2022); TCU Acórdão 1.114/2025 (opacidade do eSocial/Serpro); CGU (R$ 149 mi referentes a 2018, incluindo pagamentos a CPFs de falecidos; R$ 97,239 mi referentes a 2021). Ouvidoria CAIXA: **264.831 ocorrências** no 1º semestre de 2025 (+27% sobre 2024); SAC CAIXA: **4,7 milhões de ligações** no 1º semestre de 2025.

---

## Details — Matriz do Service Blueprint (7 Fases × 5 Eixos)

### FASE 1 — PRÉ-LIGAÇÃO (reunião de documentos e tentativa de autoatendimento)
- **Ações do Cidadão:** Reúne TRCT, RG/CPF, requerimento do Seguro-Desemprego (gerado pelo empregador via Empregador Web), contracheques. Tenta consultar status no app Carteira de Trabalho Digital, portal Emprega Brasil/gov.br, app CAIXA Tem / Benefícios Sociais CAIXA / CAIXA Trabalhador, ou site cidadao.caixa.gov.br.
- **Evidências Físicas/Digitais:** Requerimento (nº do RSD), TRCT, apps gov.br (login único, nível Prata/Ouro), SMS/e-mail de notificação, mensagem de "crédito bloqueado" no Caixa Tem.
- **Frontstage/Backstage:** Frontstage = apps e portais. Backstage = autenticação via gov.br (Serpro/ITI); consulta à base do Seguro-Desemprego (Dataprev); habilitação automática cruzando CNIS/CAGED/eSocial (Res. CODEFAT 957/2022, art. 4º §1º).
- **Normativos:** Lei 7.998/1990; Portaria MTP 671/2021; Res. CODEFAT 957/2022; LGPD (Lei 13.709/2018) para autenticação/dados.
- **Fail Points:** Baixa literacia digital impede uso dos apps → empurra para o telefone. Divergência cadastral aparece como bloqueio sem explicação clara. Empregador que não transmite a dispensa no eSocial impede habilitação. App Caixa Tem exige ida à agência para desbloquear senha (relatos no Reclame Aqui).

### FASE 2 — CONEXÃO E AUTENTICAÇÃO NA URA
- **Ações do Cidadão:** Disca 0800 726 0207; ouve saudação automática; navega no menu; digita CPF e data de nascimento.
- **Evidências:** Mensagem de saudação, menu de voz (DTMF), mensagens informativas durante espera (publicidade vedada — Decreto 11.034/2022).
- **Frontstage/Backstage:** Frontstage = URA. Backstage = validação de CPF (API Consulta CPF do Serpro, na base da RFB); roteamento da chamada.
- **Normativos:** Decreto 11.034/2022 (novo Decreto do SAC): atendimento humano por no mínimo 8h/dia, canal 24h, vedação de publicidade na espera, gravação mantida por 90 dias, registro por 2 anos, transferência única ao setor competente. LGPD para tratamento de CPF/data de nascimento.
- **Fail Points:** Indisponibilidade da URA ("sistema temporariamente indisponível" — relato Reclame Aqui); latência/queda na validação de CPF; autenticação falha para quem tem CPF suspenso na RFB; barreira para idosos/analfabetos funcionais na navegação por DTMF.

### FASE 3 — NAVEGAÇÃO NA URA / AUTOATENDIMENTO
- **Ações do Cidadão:** Seleciona opção (status do benefício, parcelas, data de pagamento, desbloqueio de Cartão Social).
- **Evidências:** Resposta automatizada de voz com status; nº de protocolo eletrônico.
- **Frontstage/Backstage:** Frontstage = locução automatizada. Backstage = consulta da CAIXA à sua base interna de pagamento (a CAIXA recebe do MTE/Dataprev a informação de parcelas liberadas); o status de elegibilidade reside na Dataprev/MTE, não na CAIXA.
- **Normativos:** Res. CODEFAT 957/2022 (parcelas; intervalo de 30 dias entre parcelas); Lei 7.998/1990.
- **Fail Points:** Defasagem entre o que o MTE "emitiu" e o que a CAIXA "recebeu" → cidadão vê "liberado" no 158 e "bloqueado" no Caixa Tem (casos recorrentes em jun/2024 e jun/2025). A URA não explica o motivo do bloqueio nem oferece correção por voz. Sobrecarga em datas de pagamento.

### FASE 4 — TRANSBORDO HUMANO (Fila e Espera)
- **Ações do Cidadão:** Opta por atendente (ou é forçado quando a URA não resolve); aguarda na fila.
- **Evidências:** Mensagem de posição na fila/tempo estimado (exigência do Decreto 11.034/2022); música/mensagens informativas.
- **Frontstage/Backstage:** Frontstage = fila de distribuição (ACD). Backstage = roteamento por skill; alocação de operador terceirizado.
- **Normativos:** Decreto 11.034/2022 (informar tempo de espera em minutos ou posição; transferência única).
- **Fail Points:** Tempo de espera excessivo; queda após espera; direcionamento forçado a canais digitais; fragmentação (transferência entre setores/sistemas).

### FASE 5 — ATENDIMENTO HUMANO
- **Ações do Cidadão:** Explica o caso; fornece CPF/dados; pede status/desbloqueio.
- **Evidências:** Gravação da chamada (retida 90 dias); tela do atendente; protocolo.
- **Frontstage/Backstage:** Frontstage = operador terceirizado (telesserviços CAIXA). Backstage = consulta à base de pagamento da CAIXA; **sem acesso de escrita** às bases do MTE/Dataprev. O operador não pode corrigir CNIS/eSocial/cadastro nem reprocessar habilitação.
- **Normativos:** Decreto 11.034/2022 (resolutividade, cordialidade, prazo de 7 dias para resposta); LGPD (tratamento de dados sensíveis; dados de terceiros — a CAIXA recusa informações sobre benefício de terceiros por sigilo); responsabilidade do contratante por erro de informação do terceirizado.
- **Fail Points:** **Abismo de alçada** — atendente não resolve divergência cadastral nem desbloqueio; qualidade variável e rotatividade da mão de obra terceirizada (precarização documentada pelo Sinttel-ES, com sucessivas trocas de empresas via licitação por menor preço); "SAC da Caixa não soube me orientar" (relatos Reclame Aqui); risco de informação incorreta com responsabilização difusa.

### FASE 6 — RESOLUÇÃO, ENCAMINHAMENTO E ENCERRAMENTO
- **Ações do Cidadão:** Recebe orientação (geralmente encaminhamento ao 158/SINE/agência); anota protocolo.
- **Evidências:** Nº de protocolo; eventual registro de demanda; orientação verbal.
- **Frontstage/Backstage:** Frontstage = encerramento da chamada. Backstage = abertura de demanda interna (quando aplicável); sem integração que dispare correção no MTE/Dataprev.
- **Normativos:** Decreto 11.034/2022 (protocolo numérico, histórico em 5 dias, registro por 2 anos); Res. CODEFAT 957/2022 (recurso administrativo em até 120 dias da notificação).
- **Fail Points:** Resolução no primeiro contato baixa para casos de divergência (encaminhamento ≠ resolução); cidadão precisa abrir recurso administrativo (prazo de análise relatado entre 10 e 90 dias) ou ir presencialmente; "ping-pong" entre CAIXA e MTE.

### FASE 7 — PÓS-ATENDIMENTO
- **Ações do Cidadão:** Acompanha protocolo; reincide ligações; escala para Ouvidoria CAIXA (0800 725 7474), consumidor.gov, Procon, Reclame Aqui, BACEN, ANPD ou Justiça.
- **Evidências:** Resposta da Ouvidoria; manifestação em consumidor.gov/Procon; ação judicial (há jurisprudência — ex. TRF-4 — condenando a União a pagar SD indeferido por divergência cadastral).
- **Frontstage/Backstage:** Frontstage = canais de 2º nível. Backstage = Ouvidoria CAIXA; reprocessamento automático após correção da pendência (alguns dias úteis).
- **Normativos:** LGPD/ANPD (petição de titular; revisão de decisão automatizada — art. 20); Código de Defesa do Consumidor; Decreto 11.034/2022.
- **Fail Points:** Reincidência alta (fragmentação); populações vulneráveis (idosos, trabalhadores rurais, analfabetos funcionais) sem condições de escalar digitalmente; judicialização como via final.

---

## O Maior Gargalo Sistêmico — Narrativa

O gargalo estrutural não é a fila nem o TMA: é o **descompasso entre a arquitetura de dados (tripartite MTE–Dataprev–CAIXA) e o modelo de atendimento telefônico (CAIXA como ponto de contato sem alçada)**. A habilitação é "aferida de forma automática pelo sistema seguro-desemprego" (Res. CODEFAT 957/2022) cruzando CNIS, CAGED e eSocial. Quando esse cruzamento aponta divergência cadastral, gera-se uma pendência que **só pode ser sanada na origem do dado** (Receita Federal para o CPF; MTE/SINE para o requerimento; empregador para o eSocial). A CAIXA, porém, é onde o dinheiro "aparece" (ou não) — e por isso é o canal que o cidadão liga. O operador da CAIXA vê o bloqueio mas não tem como corrigi-lo, criando o "abismo de alçada".

Esse desenho é agravado por três fatores documentados:
1. **Fragilidade do dado de origem (eSocial/Serpro):** o TCU (Acórdão 1.114/2025-Plenário, rel. min. Bruno Dantas) constatou "dificuldade de fiscalização do eSocial devido a problemas no acesso e na análise dos dados" e que a Dataprev teve "problemas operacionais" na base "eSocial Negócio", elevando o risco de pagamentos indevidos — e, por simetria, de falsos bloqueios. O universo cresceu de 28,7 mi (2022) para 32,2 mi (2024) de beneficiários, com R$ 72,7 bi pagos em 2023 (R$ 47,7 bi de SD + R$ 25 bi de abono).
2. **Falhas de controle interno e de governança contratual MTE–Dataprev:** o TCU (Acórdão 135/2024-Plenário, rel. min.-substituto Marcos Bemquerer Costa, unidade AudBenefícios) apontou R$ 1,9 bi em indícios de pagamentos irregulares (jan/2018–jun/2022) e cláusulas contratuais "genéricas" sem penalidades por erro grave da Dataprev.
3. **Descompasso de sincronização CAIXA↔MTE:** episódios recorrentes (jun/2024, jun/2025) em que o benefício consta "emitido/pago" no 158 mas "bloqueado" no Caixa Tem.

O efeito combinado é uma jornada que obriga o cidadão a múltiplas ligações, transitando entre 0800 e 158, sem que nenhum canal de voz tenha visão nem poder ponta-a-ponta — impacto desproporcional sobre quem depende do telefone (baixa literacia digital, idosos, trabalhadores rurais).

---

## Referências Normativas e de Controle (com números)
- **Lei 7.998/1990** — institui o Programa do Seguro-Desemprego e o FAT.
- **Portaria MTP 671/2021** (e alterações até Portaria MTE 547/2025) — eSocial; registro de admissões/desligamentos para fins de SD.
- **Resolução CODEFAT 957/2022** — concessão/processamento/pagamento; habilitação automática via CNIS/CAGED/eSocial (art. 4º §1º); recurso em até 120 dias.
- **Decreto 11.034/2022** (novo Decreto do SAC) — atendimento humano ≥8h/dia, tempo de espera informado, gravação 90 dias, registro 2 anos, resposta em 7 dias, transferência única.
- **LGPD (Lei 13.709/2018)** + guias ANPD — tratamento de dados pessoais/sensíveis, dados de terceiros, revisão de decisão automatizada (art. 20).
- **TCU Acórdão 135/2024 (Plenário)** — auditoria jan/2018–jun/2022: "possíveis pagamentos indevidos da ordem de R$ 1,9 bilhão", incluindo **R$ 1.144.283.468,51** em **382.962 parcelas** a quem tinha menos tempo de vínculo; **R$ 566,8 mi** a pensionistas do INSS; **R$ 147 mi** a servidores; **R$ 544 mil** em fraudes presumidas em contratos.
- **TCU Acórdão 1.114/2025 (Plenário)** — opacidade do eSocial; 28,7 mi→32,2 mi beneficiários (2022-2024); R$ 72,7 bi pagos em 2023 (R$ 47,7 bi SD + R$ 25 bi abono); determinou acesso ao DataLake em 10 dias. (Nota: a notícia oficial do Portal TCU registra o nº como "Acórdão 1114/2025"; o enricher sinalizou possível grafia "114/2025" — recomenda-se conferência no número do processo TC 008.443/2024-6.)
- **CGU** — auditoria divulgada em 12/02/2020: **R$ 149 mi** em 165 mil parcelas potencialmente irregulares de 2018, com "R$ 3,3 milhões pagos a 826 pessoas dadas como mortas", R$ 36,8 mi a quem já recebia INSS e R$ 105,2 mi a demitidos por justa causa. Auditoria posterior sobre o FAT (2,641 mi de requerimentos de 2021; divulgada mai/2022): **R$ 97,239 mi** em indícios — incl. ~R$ 7,608 mi a falecidos, R$ 78,6 mi a beneficiários com outro vínculo/aposentadoria, R$ 9,9 mi a demitidos por justa causa; e R$ 599,7 mi a pessoas com CPF não "regular" na RFB.
- **Operação RedeX (MTE/PF, 2023)** — quase 12 mil requerimentos fraudados; prejuízo de R$ 11.900.111,12.
- **Ouvidoria CAIXA (Relatório Institucional 1º Semestre de 2025)** — 264.831 ocorrências (+27% sobre 2024); SAC CAIXA com 4,7 milhões de ligações; ranking BACEN: 13º (1ºT/2025) e 11º (2ºT/2025); índice de reclamações por milhão de clientes 22,91 (1ºT) e 24,82 (2ºT).

## Recommendations
1. **Curto prazo — fechar o abismo de alçada por integração de leitura:** dar ao operador do 0800 acesso de consulta unificada (read-only) ao status de habilitação do MTE/Dataprev e ao **motivo exato da pendência**, com script padronizado de orientação por tipo de divergência. Benchmark de mudança: queda na taxa de reincidência e no encaminhamento "ping-pong" CAIXA↔158.
2. **Curto prazo — instrumentar métricas hoje ausentes:** medir e publicar **FCR (resolução no 1º contato), taxa de abandono e TMA por tipo de demanda** (SD vs. demais). Hoje a Ouvidoria reporta apenas "tempo médio de resposta" em dias e volume de ligações (4,7 mi no 1º sem/2025), sem FCR nem taxa de abandono.
3. **Médio prazo — "correção assistida" por voz:** permitir que o atendente abra protocolo que dispare automaticamente o fluxo de correção no MTE/Receita (ou pré-preencha o recurso administrativo), em vez de apenas encaminhar. Threshold: reduzir o tempo médio de resolução de divergência para abaixo do prazo de recurso (120 dias).
4. **Médio prazo — acessibilidade e vulneráveis:** garantir trilha de voz humana sem forçar canal digital para públicos de baixa literacia (o Decreto 11.034/2022 exige atendimento humano por no mínimo 8h/dia); atendimento prioritário a idosos.
5. **Estrutural — governança de dados:** atacar a causa-raiz na origem (eSocial/Serpro e contrato MTE–Dataprev), conforme determinações do TCU (Acórdãos 135/2024 e 1.114/2025), reduzindo as divergências que chegam ao 0800.

## Caveats
- **SIBE ≠ sistema do call center de SD:** o "Sistema Integrado de Benefícios (SIBE)" é o sistema de concessão de benefícios do **INSS** (substituto de Prisma/Sabi/Sub), não o sistema da CAIXA para o Seguro-Desemprego. O atendimento de SD apoia-se na Base de Gestão do Seguro-Desemprego (BGSD/MTE), no processamento da Dataprev e na base interna de pagamento da CAIXA.
- **Métricas específicas da URA de SD não são públicas:** não há divulgação oficial de TMA, FCR ou taxa de abandono **segmentados para o Seguro-Desemprego** no 0800; os números disponíveis são agregados da Ouvidoria/SAC CAIXA (todos os serviços) e do ranking BACEN.
- **Vários dados de fraude/auditoria são de 2018–2023** (TCU 135/2024 cobre até jun/2022; CGU referente a 2018 e 2021), usados por ausência de série 2024-2025 mais granular; o Acórdão TCU 1.114/2025 traz os dados 2022-2024.
- **Relatos do Reclame Aqui são qualitativos** (experiência do usuário), não estatística representativa; "250 mil ligações/dia" e "1.500 operadores" referem-se ao lançamento do canal (fala de Ricardo Vieira, gerente nacional de Telesserviços da CAIXA), não necessariamente ao volume atual.
- **Conflito de horário de atendimento humano:** fontes de lançamento indicam 8h–22h (dias úteis), enquanto a página atual da CAIXA indica 8h–21h; recomenda-se confirmar o horário vigente diretamente com a CAIXA.
- Há **sobreposição com o canal 158 (Alô Trabalho/MTE)**, que é o canal correto para pedir/corrigir o benefício; parte da frustração no 0800 da CAIXA decorre de o cidadão usar o canal do agente pagador para um problema cuja alçada é do MTE.