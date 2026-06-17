# Diagrama AS-IS — Jornada de Atendimento ao Seguro-Desemprego
## Service Blueprint via URA/0800 CAIXA (Shostack, 1984)
### Parte D — Diagrama de Relações

> Gerado a partir dos itens identificados na Parte C (`C_blueprint_asis.md`).
> Renderiza diretamente no GitHub com Mermaid.

---

## Legenda de Cores

| Cor | Tipo de Nó |
|:---|:---|
| Vermelho claro | F⊗ — Fail point estrutural (falha de design) |
| Amarelo claro | F — Fail point contingente (falha de execução) |
| Azul claro | Losango de decisão (D1–D4, B4) |
| Verde claro | S1 — Saída com output cumprido |
| Vermelho claro | S2, S3 — Saídas com output não cumprido |
| Cinza | E1 — Entrada |

**Setas sólidas `-->` :** fluxo sequencial entre etapas
**Setas pontilhadas `-.->` :** conexões cruzando a linha de visibilidade (cidadão ↔ sistema)

---

## Diagrama

```mermaid
flowchart TD
    E1(["📞 E1 · Cidadão disca 0800 726 0207"])

    subgraph ACIMA["▲  ACIMA DA LINHA DE VISIBILIDADE  —  Interface de Voz"]
        P1["P1 · Saudação automática\nURA / CAIXA"]
        P2["P2 · Navegação menu DTMF\nF⊗ L1 — barreira Perfil B"]
        P3["P3 · Autenticação CPF + data nasc.\nF L3 — CPF irregular\n⚠ Perfil C autentica normalmente"]
        D1{"D1 · URA resolve\nsem transbordo?"}
        P4a["P4a · Status entregue pela URA\nF⊗ L2 — motivo omitido\nInformação não acionável"]
        P4b["P4b · Fila de espera ACD\nF · T=?"]
        P5["P5 · Atendimento humano\nOperador / base CAIXA"]
        D2{"D2 · Atendente tem\ndiagnóstico suficiente?"}
        P6a["P6a · Orientação específica\n+ protocolo"]
        P6b["P6b · Encaminhamento genérico\nMTE / 158 ou agência\nF⊗ · Protocolo sem valor no MTE\nT=? · 10–120 dias"]
        D3{"D3 · Cidadão age\nno canal indicado?"}
        P7a["P7a · Resolve via\n158 / gov.br / agência\nPerfil A"]
        P7b["P7b · Escalada\nOuvidoria · PROCON\nConsumidor.gov · Perfil B"]
        D4{"D4 · Escalada de\n2º nível resolve?"}
    end

    subgraph ABAIXO["▼  ABAIXO DA LINHA DE VISIBILIDADE  —  Processos de Suporte"]
        B1["B1 · API Consulta CPF\nSerpro / Receita Federal\nF — indisponibilidade"]
        B3a["B3a · Declaração eSocial\nSerpro · T=?"]
        B3b["B3b · Extração Dataprev\neSocial Negócio · T=?"]
        B3c["B3c · Consolidação CNIS\nDataprev / INSS · T=?"]
        B3d["B3d · Processamento BGSD/SIBE-SD\nDataprev / MTE · T=?"]
        B3e["B3e · Lote enviado à CAIXA\nF⊗ Batch sem SLA · T=?\nR$ 1,9 bi · TCU 135/2024"]
        B4{"B4 · Malha PIS/SINAF\nregistro retido?"}
        BSINAF["BSINAF · Retenção indefinida\nF⊗ · Sem notificação\nR$ 97,2 mi · CGU 2021"]
        B2["B2 · Base de pagamentos\nCAIXA · T=?"]
        B5["B5 · Latência SISOBI\nF⊗ · Parcelas a falecidos\nR$ 7,6 mi · CGU 2021"]
    end

    S1(["✅ S1 · Informação acionável entregue"])
    S2(["⚠️ S2 · F⊗ · Encaminhamento não acionável\nProtocolo sem resolução"])
    S3(["❌ S3 · F⊗ · Judicialização"])

    %% Fluxo principal — acima da linha de visibilidade
    E1 --> P1 --> P2 --> P3 --> D1
    D1 -->|"Sim"| P4a
    D1 -->|"Não"| P4b
    P4a --> D3
    P4b --> P5 --> D2
    D2 -->|"Sim — raro"| P6a
    D2 -->|"Não — F⊗ abismo de alçada"| P6b
    P6a --> D3
    P6b --> D3
    D3 -->|"Sim · Perfil A"| P7a
    D3 -->|"Não · F⊗ · Perfil B"| P7b
    P7a --> S1
    P7b -->|"sem acesso a canais"| S2
    P7b -->|"tenta escalada"| D4
    D4 -->|"Sim"| S1
    D4 -->|"Não"| S3

    %% Pipeline de dados — abaixo da linha de visibilidade
    B3a --> B3b --> B3c --> B3d --> B3e --> B4
    B4 -->|"Não"| B2
    B4 -->|"Sim · F⊗"| BSINAF

    %% Conexões cruzando a linha de visibilidade (pontilhadas)
    P3 -.->|"valida CPF"| B1
    B1 -.->|"resultado"| P3
    D1 -.->|"consulta status"| B2
    B2 -.->|"retorna status"| D1
    B3e -.->|"alimenta base"| B2
    B5 -.->|"óbito não atualizado"| B2

    %% Estilos por tipo de nó
    classDef failEstr fill:#ffd7d7,stroke:#cc0000,color:#000000
    classDef failCont fill:#fff4cc,stroke:#cc9900,color:#000000
    classDef decis fill:#dce8ff,stroke:#3366cc,color:#000000
    classDef saidaOk fill:#d4edda,stroke:#1a7a3a,color:#000000
    classDef saidaFail fill:#ffd7d7,stroke:#cc0000,color:#000000
    classDef entrada fill:#e8e8e8,stroke:#666666,color:#000000

    class P2,P4a,P6b,B3e,BSINAF,B5 failEstr
    class P3,P4b,B1 failCont
    class D1,D2,D3,D4,B4 decis
    class S1 saidaOk
    class S2,S3 saidaFail
    class E1 entrada
```

---

## Relações Explícitas Documentadas

As setas abaixo refletem os handoffs entre atores e etapas mapeados na Parte C:

| De | Para | Tipo | Significado |
|:--|:--|:--:|:--|
| E1 | P1 | `-->` | Cidadão inicia o serviço discando o 0800 |
| P3 | B1 | `-.->` | URA solicita validação do CPF à API Serpro |
| B1 | P3 | `-.->` | Serpro retorna resultado (válido / irregular / indisponível) |
| D1 | B2 | `-.->` | URA consulta base de pagamentos da CAIXA para retornar status |
| B2 | D1 | `-.->` | Base retorna status (disponível / bloqueado / não localizado) |
| B3a→B3e | — | `-->` | Batch pipeline: eSocial (Serpro) → Dataprev/eSocial Negócio → CNIS → BGSD/SIBE-SD → lote enviado à CAIXA |
| B3e | B2 | `-.->` | Lote de liquidação alimenta a base de pagamentos da CAIXA |
| B4 | BSINAF | `-->` | Registro retido na tabela SINAF: bloqueio indefinido sem notificação (F⊗) |
| B5 | B2 | `-.->` | Latência SISOBI: óbito não atualizado mantém parcela disponível na base CAIXA (F⊗) |
| D2 | P6b | `-->` | Atendente sem diagnóstico emite encaminhamento genérico (abismo de alçada — F⊗) |
| D3 | P7b | `-->` | Perfil B não consegue agir no canal indicado → escalada obrigatória (F⊗) |
| P7b | S2 | `-->` | Perfil B sem acesso a canais de escalada encerra sem resolução (F⊗) |
| D4 | S3 | `-->` | Escalada de 2º nível falha → judicialização (F⊗) |
