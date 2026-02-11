# Esteira DevSecOps — Piloto (CI/CD + Segurança + Qualidade)

Este repositório contém o **material do piloto de esteira DevSecOps** usado como base para padronização futura: **diagrama**, **explicação**, **VSM simples**, **proposta de KT** e **templates/guidelines**.

> **Objetivo do piloto:** reduzir risco (vulnerabilidades/misconfig/segredos), aumentar qualidade (testes/coverage), padronizar entrega (GitOps) e criar rastreabilidade ponta a ponta (SBOM + assinatura).

---

## Conteúdo

- **Diagrama principal da esteira (código → produção)**
  - `docs/diagrams/Esteira_DevSecOps_Piloto.drawio`
  - `docs/diagrams/esteira_final.png` (export para visualização rápida)
- **Texto explicativo (para entrevista/entregável)**
  - `docs/README_EXPLICACAO.md`
- **Opcionais (diferenciais)**
  - VSM simples: `docs/vsm/VSM_Simples.md`
  - KT (plano de conhecimento): `docs/kt/KT_Plano.md`
  - Templates/guidelines: `templates/` e `guidelines/`

> Observação: os caminhos acima são a estrutura recomendada.

---

## Visão geral do fluxo (o que o diagrama em draw.io mostra)

A esteira é dividida em **etapas com gates (pontos de controle)**:

1. **Commit local → PR**
2. **PR checks obrigatórios** (review + políticas de branch)
3. **CI**: build + testes + cobertura
4. **Scans**: SAST + SCA + Secrets + IaC
5. **Build da imagem + SBOM**
6. **Scan da imagem + assinatura/proveniência**
7. **Publicação no registry + atualização GitOps**
8. **Deploy em Staging + DAST + testes de integração**
9. **Promoção para Produção (blue/green ou canary) + pós-checks**

Camada transversal: **Observabilidade + Vulnerability Management + Incident Response**.

---

## Gates e critérios de bloqueio.

### Gate A — PR
Bloqueia se:
- faltou review obrigatório;
- checks não passaram;
- violação de padrão (lint/format) ou cobertura abaixo do mínimo.

### Gate B — Segurança (CI)
Bloqueia se:
- **Secrets** detectado;
- **SAST/SCA** com severidade **Critical/High** explorável sem mitigação aprovada;
- **IaC** com misconfiguration crítica (ex.: exposição pública indevida, privilégio excessivo).

### Gate C — Staging
Bloqueia se:
- DAST encontra falhas críticas;
- testes de integração/contrato falham;
- SLO mínimo não é atendido (ex.: latência/erro fora do aceitável).

### Gate D — Produção
Bloqueia/aciona rollback se:
- pós-checks falham (health/erros/latência);
- alertas críticos disparam no período de observação.

> A política real deve ser calibrada com o time (evitar falso-positivo e ruído). O piloto serve exatamente para ajustar isso com dados.

---

## Rastreabilidade e Supply Chain (SBOM + assinatura)

O piloto inclui:
- **SBOM** gerada no build (SPDX ou CycloneDX),
- **assinatura de artefato** (imagem imutável por digest),
- verificação via **admission control** no cluster (só roda artefato conforme política).

Isso fortalece a cadeia de suprimentos e facilita auditoria e resposta a CVEs.

---

## Entrega e CD com GitOps

A promoção é declarativa:
- pipeline produz um artefato imutável (imagem),
- GitOps controla o “estado desejado” por ambiente,
- rollback e audit trail ficam mais previsíveis.

Ferramentas compatíveis: :contentReference[oaicite:0]{index=0}, ou equivalente.

---

## Observabilidade e Operação

O piloto assume telemetria mínima:
- métricas (latência, taxa de erro, saturação),
- logs (com correlação),
- traces (se aplicável),
- alertas orientados a SLI/SLO.

Plataforma alvo: :contentReference[oaicite:1]{index=1} (ou distribuição compatível).

---

## Estrutura recomendada do repositório

```text
.
├─ docs/
│  ├─ diagrams/
│  │  ├─ Esteira_DevSecOps_Piloto.drawio
│  │  └─ esteira_final.png
│  ├─ vsm/
│  │  └─ VSM_Simples.md
│  ├─ kt/
│  │  └─ KT_Plano.md
│  └─ README_EXPLICACAO.md
├─ templates/
│  ├─ pipeline/
│  ├─ policies/
│  └─ repo/
├─ guidelines/
│  ├─ branching.md
│  ├─ severity_policy.md
│  └─ definition_of_done.md
└─ README.md
