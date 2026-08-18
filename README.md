# TP1 — Engenharia de Prompt e Contexto

## MindLiner — Prototipagem do Agente de IA

**Disciplina:** Engenharia de Prompt e Contexto  
**Instituição:** Unicesumar — 2026  
**Grupo:** Matheus Campioto, Vinicius, Lauren

## Sobre o projeto
Prototipagem da feature de Agente de IA (RF-041 a RF-044) do sistema MindLiner.
Modelo utilizado: Gemini 3.7 Flash (Google AI Studio)

## Arquivos
- `system_prompt.md` — System prompt documentado antes das chamadas
- `Prompt_Chamada_A.txt` — Prompt com contexto inflado (59 RFs)
- `Prompt_Chamada_B.txt` — Prompt com contexto cirúrgico (4 RFs + 4 RBs)
- `MindLiner_TP1_Documento.docx` — Documento com justificativas completas
- `MindLiner_TP1.pptx` — Slides da apresentação
- `print_1_system_prompt.jpeg` — Evidência: system prompt no AI Studio antes das chamadas
- `print_2_chamada_a.jpeg` — Evidência: prompt Chamada A com 1.396 tokens antes de rodar
- `print_3_chamada_b.jpeg` — Evidência: resposta Chamada A com 1.705 tokens
- `print_4_resposta_b.jpeg` — Evidência: resposta Chamada B com 2.218 tokens

## Resultado
- Chamada A (contexto inflado): 1.458 tokens de input
- Chamada B (contexto cirúrgico): 513 tokens de input
- **Redução de 65%** sem perda de qualidade
- Custo total da sessão: $0,001647
