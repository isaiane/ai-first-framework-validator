# ai-first-framework-validator

Skill (Codex / agente) para **auditar repositórios** contra o **AI-FIRST Framework**: estrutura, Source of Truth em `source-of-truth/`, determinismo, alinhamento com protocolos/skills e **remediação** com padrões nomeados (`RP-*`).

## O que faz

- Onboarding e auditorias contínuas de aderência ao framework  
- Verificação de regras de estrutura e de conteúdo da SoT  
- Classificação **`Status`**: `READY` | `PARTIAL` | `INVALID` (definida só no checklist, secção 13)  
- Plano de remediação ordenado com IDs de [references/remediation-patterns.md](references/remediation-patterns.md)

## Estrutura do repositório

| Caminho | Função |
|--------|--------|
| [SKILL.md](SKILL.md) | Orquestração: ordem de execução, contrato de saída, referências obrigatórias |
| [agents/openai.yaml](agents/openai.yaml) | Definição do agente OpenAI (gatilhos e `default_prompt`) |
| [references/validation-checklist.md](references/validation-checklist.md) | **Checklist normativo** (secções 0–15): escopo, aplicabilidade 0.1, checagens 1–11, paragem 12, classificação 13 |
| [references/structure-rules.md](references/structure-rules.md) | Regras de camadas e fronteiras (violações → mapear ao checklist) |
| [references/sot-rules.md](references/sot-rules.md) | Regras da SoT sob `source-of-truth/` |
| [references/remediation-patterns.md](references/remediation-patterns.md) | Padrões de correção `RP-SOT-*`, `RP-CTR-*`, etc. |

## Fonte de verdade no repositório auditado

A raiz normativa da SoT é **`source-of-truth/`**, com subdiretórios obrigatórios `_meta/`, `contracts/`, `behavior/`, `acceptance/`, `scenarios/` (detalhes no checklist e em `SKILL.md`).

## Uso

1. Copie ou referencie este skill no ambiente do agente (ex.: raiz do projeto ou pasta de skills).  
2. O agente deve seguir [SKILL.md](SKILL.md) e executar [references/validation-checklist.md](references/validation-checklist.md) **na ordem das secções**.  
3. Toda remediação deve citar pelo menos um ID `RP-*` de [references/remediation-patterns.md](references/remediation-patterns.md).

Não há binário ou servidor neste repositório: a validação é feita pelo agente contra os ficheiros do projeto alvo.