# TP1 — Engenharia de Prompt e Contexto na Prática
## MindLiner — Prototipagem do Agente de IA

**Instituição:** Unicesumar — 2026  
**Grupo:** Matheus Campioto, Vinicius Seifert, Lauren Kunz  
**Opção escolhida:** Feature isolada da Escola de TI (Agente de IA do MindLiner)

---

## 1. O que o projeto faz

Prototipagem do Agente de IA do sistema MindLiner (RF-041 a RF-044). O agente recebe dados do usuário (disciplina, horas disponíveis por semana e dificuldades) e retorna um método de estudo personalizado e uma meta de estudo, sempre em formato JSON.

A feature foi implementada em HTML/JS puro, consumindo a API do Gemini diretamente no navegador. O projeto demonstra na prática três conceitos de engenharia de prompt: system prompt estruturado, técnica few-shot e curadoria de contexto.

**Modelo utilizado:** Gemini 3 Flash Preview (Google AI Studio)  
**URL publicada:** [COLE A URL DO VERCEL AQUI]

---

## 2. System Prompt

Definido e documentado antes de qualquer chamada ao modelo.

```
# System Prompt — Agente MindLiner
Você é um agente educacional do MindLiner, sistema de apoio ao estudo.
Seu objetivo é sugerir métodos de estudo personalizados e metas de estudo
com base nas informações fornecidas pelo usuário.

Regras obrigatórias:
- Suas recomendações devem ter finalidade exclusivamente educacional [RB-039]
- Nunca substitua metas definidas manualmente sem autorização explícita do usuário [RB-038]
- Utilize o histórico de estudos do usuário como base para personalização [RB-037]
- Considere sempre todas as informações fornecidas pelo usuário [RB-036]

Responda SEMPRE em JSON válido, sem texto adicional, no seguinte formato:
{
  "metodo_sugerido": {
    "nome": "string",
    "descricao": "string",
    "justificativa": "string"
  },
  "meta_sugerida": {
    "descricao": "string",
    "objetivo": "string",
    "prazo_sugerido": "string"
  },
  "observacoes": "string"
}
```

**Evidência:** `print_1_system_prompt.jpeg` — system prompt configurado no AI Studio antes das chamadas.

---

## 3. Técnica aplicada: Few-Shot + JSON Mode

**Técnica:** Few-Shot Learning  
**Por que foi escolhida:** sem exemplos, o modelo retornava texto explicativo antes e depois do JSON, quebrando o formato esperado. Com 2 exemplos de entrada/saída dentro do prompt, o modelo passou a entregar JSON puro em todas as chamadas. Os exemplos também calibraram a qualidade do campo `justificativa`, que passou a conectar o perfil do usuário à sugestão em vez de retornar texto genérico.

**Por que 2 exemplos:** um exemplo pode ser coincidência. Dois exemplos demonstram que é um padrão reconhecido pelo modelo. Mais de dois inflariam o prompt sem necessidade.

**Estrutura do prompt com few-shot:**
```
--- EXEMPLO 1 ---
Usuário informou:
{"disciplina": "Matemática", "horas_semana": 2, "dificuldades": "..."}
Resposta:
{"metodo_sugerido": {...}, "meta_sugerida": {...}, "observacoes": "..."}

--- EXEMPLO 2 ---
Usuário informou:
{"disciplina": "Programação", "horas_semana": 5, "dificuldades": "..."}
Resposta:
{"metodo_sugerido": {...}, "meta_sugerida": {...}, "observacoes": "..."}

--- AGORA RESPONDA ---
Usuário informou:
{"disciplina": "Engenharia de Software", "horas_semana": 3, "dificuldades": "..."}
```

**Evidências:**  
- `print_5_chamada_c_antes.jpeg` — prompt com exemplos few-shot visíveis antes de rodar (510 tokens)  
- `print_6_chamada_c_resposta.jpeg` — resposta em JSON puro, sem texto adicional

---

## 4. Curadoria de Contexto

Mesma pergunta feita duas vezes com contextos diferentes para comparar consumo de tokens.

**Chamada A — Contexto Inflado**  
Todos os 59 Requisitos Funcionais do MindLiner colados no prompt.  
→ **1.458 tokens de input**

**Chamada B — Contexto Cirúrgico**  
Apenas os 4 RFs do agente (RF-041 a RF-044) + 4 Regras de Negócio (RB-036 a RB-039).  
→ **513 tokens de input**

**Resultado: redução de 65% nos tokens de input com qualidade de resposta idêntica.**  
Os 55 RFs extras não agregaram valor — eram ruído que só aumentava o custo.

**Evidências:**  
- `print_2_chamada_a_antes.jpeg` — prompt Chamada A com 1.396 tokens antes de rodar  
- `print_3_chamada_a_resposta.jpeg` — resposta Chamada A (1.705 tokens totais)  
- `print_4_resposta_b_resposta.jpeg` — resposta Chamada B (513 tokens de input)

---

## 5. Tabela de Chamadas e Custo

Modelo: **Gemini 3 Flash Preview**  
Preços (tabela oficial — ai.google.dev/gemini-api/docs/pricing):  
- Input: $0,075 / 1M tokens  
- Output: $0,30 / 1M tokens  

> O projeto utilizou o free tier do Google AI Studio (custo real = $0). O cálculo abaixo é hipotético, como se fosse tier pago.

**Fórmula:**  
`custo = (tokens_input / 1.000.000) × $0,075 + (tokens_output / 1.000.000) × $0,30`

| Chamada | Descrição | Tokens Input | Tokens Output | Custo Input | Custo Output | Total |
|---|---|---|---|---|---|---|
| A | Contexto Inflado (59 RFs) | 1.458 | ~200 | $0,000109 | $0,000060 | $0,000169 |
| B | Contexto Cirúrgico (4 RFs + 4 RBs) | 513 | ~200 | $0,000038 | $0,000060 | $0,000098 |
| C | Few-Shot + JSON Mode | 510 | ~200 | $0,000038 | $0,000060 | $0,000098 |
| **TOTAL** | | **2.481** | **~600** | **$0,000185** | **$0,000180** | **$0,000365** |

**Evidências:** prints 2, 3, 4, 5 e 6 comprovam os contadores de tokens exibidos pelo AI Studio.

---

## 6. Arquivos do repositório

| Arquivo | Descrição |
|---|---|
| `index.html` | Aplicação web com as 3 chamadas implementadas |
| `vercel.json` | Configuração de deploy no Vercel |
| `system_prompt.md` | System prompt documentado antes das chamadas |
| `Prompt_Chamada_A.txt` | Prompt com contexto inflado (59 RFs) |
| `Prompt_Chamada_B.txt` | Prompt com contexto cirúrgico (4 RFs + 4 RBs) |
| `MindLiner_TP1_Documento.docx` | Documento com justificativas completas |
| `MindLiner_TP1.pptx` | Slides da apresentação |
| `print_1_system_prompt.jpeg` | System prompt no AI Studio antes das chamadas |
| `print_2_chamada_a_antes.jpeg` | Prompt Chamada A com contador de tokens |
| `print_3_chamada_a_resposta.jpeg` | Resposta Chamada A |
| `print_4_resposta_b_resposta.jpeg` | Resposta Chamada B |
| `print_5_chamada_c_antes.jpeg` | Prompt Chamada C (few-shot) antes de rodar |
| `print_6_chamada_c_resposta.jpeg` | Resposta Chamada C (few-shot) |

---

## 7. URL publicada

https://tp-1-engenharia-prompt.vercel.app/

---

## 8. Integrantes

| Nome | RA |
|---|---|
| Matheus Felipe Campioto Catenacci | 22014137-2 |
| Vinícius Seifert Fonceca | 21044082-2 |
| Lauren Kunz e Silva | 23118770-2 |
