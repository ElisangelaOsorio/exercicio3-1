# Service Blueprint AS-IS — Shostack (1984)
## Atendimento ao Seguro-Desemprego via URA/0800 da CAIXA
### Parte C — Elaboração Conceitual

---

## Decisões Conceituais (síntese da sessão /grill-me)

| Dimensão | Decisão |
|:---|:---|
| **Metodologia** | Shostack (1984) — flowchart com linha de visibilidade única, fail points, tolerâncias de tempo e custo social por falha |
| **Output prometido** | Informação acionável que permita ao cidadão resolver o problema |
| **Ator principal** | Cidadão sem literacia digital (Perfil B) — telefone como canal primário e único |
| **Variantes** | Perfil A bifurca em D3 (caminho "Sim"); Perfil C anotado em P3 (autentica normalmente com dados reais da vítima) |
| **Escopo** | E1 (discagem do 0800) → S1/S2/S3 (inclui escalada e judicialização) |
| **Linha de visibilidade** | Interface de voz (URA + atendente) acima; pipeline de dados e consultas sistêmicas abaixo |
| **Backstage** | Único (CAIXA); MTE/Dataprev/Serpro como processos de suporte encadeados abaixo da linha |
| **Fail points** | F⊗ = estrutural (falha de design); F = contingente (falha de execução) |
| **Tolerância temporal** | T=valor (SLA publicado) ou T=? (sem SLA público — ausência de transparência é achado analítico) |
| **Custo social** | Anotado nos fail points com dado documental disponível |
| **Pontos de decisão** | D1 (URA resolve?), D2 (diagnóstico suficiente?), D3 (cidadão age?), D4 (escalada resolve?) |
| **Malha PIS/SINAF** | Losango de decisão (B4); F⊗ no caminho "Sim" (retenção indefinida sem notificação) |

---

## Convenções

| Símbolo | Significado |
|:---|:---|
| **F⊗** | Fail point estrutural — falha inerente ao design; não resolvível sem redesenho do serviço |
| **F** | Fail point contingente — falha de execução; corrigível dentro do design atual |
| **T=X** | Tolerância temporal com norma ou SLA publicado |
| **T=?** | Sem SLA público — ausência de transparência é achado analítico |
| **D1–D4** | Losangos de decisão |
| **B** | Passos abaixo da linha de visibilidade (processos de suporte) |
| **S1–S3** | Estados finais (saídas do serviço) |

---

## Tabela do Blueprint

### ▲ Acima da Linha de Visibilidade — Interface de Voz

| # | Etapa / Passo | Ator / Sistema | Tipo | Fail Point | Tolerância (T) | Custo Social |
|:--|:--|:--|:--:|:--|:--|--:|
| **E1** | Cidadão disca 0800 726 0207 | Cidadão (Perfil B) | Entrada | — | — | — |
| **P1** | Saudação automática da URA | URA / CAIXA | Processo | — | T = imediato | — |
| **P2** | Navegação no menu DTMF (seleção por teclado) | Cidadão / URA | Processo | **F⊗ L1** — menu por teclado exclui estruturalmente Perfil B (idosos, analfabetos funcionais); barreira de acesso por design | T = ? | — |
| **P3** | Autenticação: CPF + data de nascimento | Cidadão / URA | Processo | **F L3** — CPF irregular na RFB bloqueia autenticação sem rota alternativa; ⚠ *Perfil C: requerimento fraudulento autentica normalmente com dados reais da vítima* | T = ? | — |
| **D1** | URA resolve a consulta sem transbordo humano? | URA / Sistema CAIXA | Decisão | — | — | — |
| **P4a** | D1=Sim · Status entregue pela URA | URA / CAIXA | Processo | **F⊗ L2** — URA anuncia "benefício bloqueado" sem identificar a causa (batch pendente / Malha PIS / divergência MTE / fraude); informação não acionável para Perfil B | T = ? | — |
| **P4b** | D1=Não · Fila de espera ACD | Sistema ACD / CAIXA | Processo | **F** — queda de chamada após espera prolongada; Decreto 11.034/2022 exige informar posição na fila, mas não fixa tempo máximo | T = ? | — |
| **P5** | Atendimento humano — operador consulta base de pagamentos CAIXA | Operador terceirizado / CAIXA | Processo | — | T = gravação retida 90 dias; registro por 2 anos (Decreto 11.034/2022) | — |
| **D2** | Atendente tem diagnóstico suficiente para informação acionável? | Operador / Sistema CAIXA | Decisão | **F⊗** — abismo de alçada: operador vê "bloqueado" sem distinguir as 4 causas (batch / Malha PIS / divergência MTE / fraude); opacidade de diagnóstico de 4 camadas | — | — |
| **P6a** | D2=Sim (raro) · Orientação específica + protocolo | Operador / CAIXA | Processo | — | T = registro por 2 anos | — |
| **P6b** | D2=Não (regra) · Encaminhamento genérico MTE/158 ou agência + protocolo | Operador / CAIXA | Processo | **F⊗** — protocolo emitido pela CAIXA não aciona workflow resolutivo no MTE; ping-pong estrutural 0800 (CAIXA) ↔ 158 (MTE) | T = ? · recurso: 10–90 dias na prática; teto legal: 120 dias (Res. CODEFAT 957/2022) | — |
| **D3** | Cidadão consegue agir no canal indicado? | Cidadão | Decisão | **F⊗** no caminho "Não" — ponto de abandono estrutural: Perfil B não tem acesso digital para agir via 158/gov.br | — | — |
| **P7a** | D3=Sim (Perfil A) · Resolve via 158 / gov.br / agência física | Cidadão (Perfil A) | Processo | — | — | — |
| **P7b** | D3=Não (Perfil B) · Escalada: Ouvidoria CAIXA / PROCON / Consumidor.gov | Cidadão (Perfil B) / Ouvidoria | Processo | **F** — populações vulneráveis sem condições de escalar digitalmente; judicialização como via final | T = ? | — |
| **D4** | Recurso ou escalada de 2º nível resolve? | Ouvidoria / MTE / Dataprev | Decisão | — | T = ? | — |

---

### Saídas do Serviço

| # | Estado Final | Tipo | Fail Point | Output Prometido |
|:--|:--|:--:|:--|:--:|
| **S1** | Informação acionável entregue / benefício liberado | Saída ✓ | — | Cumprido |
| **S2** | Encaminhamento não acionável — protocolo sem resolução; Perfil B sem alternativa | Saída **F⊗** | **F⊗** — output prometido não cumprido | Não cumprido |
| **S3** | Judicialização — falha sistêmica total; TRFs condenam União por SD negado por divergência cadastral | Saída **F⊗** | **F⊗** — output prometido não cumprido; única via restante para Perfil B que persistiu | Não cumprido |

---

### ▼ Abaixo da Linha de Visibilidade — Processos de Suporte

| # | Etapa / Passo | Ator / Sistema | Tipo | Fail Point | Tolerância (T) | Custo Social |
|:--|:--|:--|:--:|:--|:--|--:|
| **B1** | Validação CPF via API Consulta CPF (Serpro / Receita Federal) | Serpro | Processo | **F** — indisponibilidade da API bloqueia autenticação (resposta a P3) | T = milissegundos | — |
| **B3a** | Declaração de desligamento no eSocial pelo empregador | Serpro (infraestrutura eSocial) | Processo | — | T = ? | — |
| **B3b** | Extração e transformação Dataprev / eSocial Negócio | Dataprev | Processo | — | T = ? | — |
| **B3c** | Consolidação no CNIS | Dataprev / INSS | Processo | — | T = ? | — |
| **B3d** | Processamento BGSD / SIBE-SD | Dataprev / MTE | Processo | — | T = ? | — |
| **B3e** | Envio de lote de liquidação à CAIXA (fim do batch pipeline) | MTE → CAIXA | Processo | **F⊗** — pipeline sem SLA publicado em nenhuma etapa (B3a a B3e); defasagem gera status divergente entre canal 158 (MTE) e URA (CAIXA) | T = ? em todas as etapas | R$ 1,9 bi (TCU 135/2024) |
| **B4** | Losango: registro retido na Malha PIS / tabela SINAF? | CAIXA / MTE | Decisão | — | T = ? | — |
| **BSINAF** | B4=Sim · Retenção indefinida na tabela SINAF | CAIXA / MTE | Processo | **F⊗** — bloqueio indefinido sem notificação ao cidadão nem ao atendente; inconsistência mínima (dígito errado, variação de grafia) produz retenção opaca sem saída resolutiva automática | T = ? | R$ 97,2 mi (CGU 2021) |
| **B2** | B4=Não · Base de pagamentos CAIXA (lote disponível) | Sistema CAIXA | Processo | — | T = ? | — |
| **B5** | Latência SISOBI — óbito não atualizado (INSS / Dataprev) | INSS / Dataprev | Processo | **F⊗** — durante a janela de latência, parcelas de beneficiários falecidos permanecem consultáveis e sacáveis; URA não cruza dados de óbito independentemente | T = ? | R$ 7,6 mi (CGU 2021) |

---

## Consolidação dos Fail Points

| Cód. | Fail Point | Tipo | Etapa | Custo Social |
|:--|:--|:--:|:--|--:|
| L1 | Barreira DTMF — exclui estruturalmente Perfil B | F⊗ | P2 | — |
| L2 | URA não explica motivo do bloqueio | F⊗ | P4a | — |
| L3 | CPF irregular bloqueia sem rota alternativa | F | P3 / B1 | — |
| AA | Abismo de alçada — 4 causas invisíveis ao atendente | F⊗ | D2 | — |
| PP | Ping-pong CAIXA ↔ MTE; protocolo sem valor operacional | F⊗ | P6b | — |
| PA | Ponto de abandono estrutural — Perfil B sem canal alternativo | F⊗ | D3 | — |
| BP | Batch pipeline sem SLA em nenhuma etapa (B3a–B3e) | F⊗ | B3e | R$ 1,9 bi |
| MS | Malha PIS/SINAF — retenção indefinida sem notificação | F⊗ | BSINAF | R$ 97,2 mi |
| SO | Latência SISOBI — parcelas a falecidos | F⊗ | B5 | R$ 7,6 mi |
| QC | Queda de chamada na fila de espera | F | P4b | — |
| UA | URA fora do ar / sobrecarga em datas de pagamento | F | P1 / P4b | — |

**Total de fail points estruturais (F⊗): 8** — nenhum resolvível sem redesenho do serviço ou redistribuição de responsabilidades entre MTE, Dataprev e CAIXA.

**Total de fail points contingentes (F): 3** — corrigíveis por melhorias operacionais dentro do design atual.
