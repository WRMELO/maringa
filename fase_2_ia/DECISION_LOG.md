# DECISION LOG — MARINGA_F4

Registro de decisões do Owner com contexto, alternativas e justificativa.

| ID | Data | Decisão | Contexto | Alternativas | Escolha | Justificativa |
|----|------|---------|----------|--------------|---------|---------------|
| D-001 | 2026-03-24 | Criar diretório fase_2_ia com sistema de governança para desenvolvimento de IA/DS no F4 | Três análises independentes (por LLMs distintas) convergiram em propostas de Data Science e IA para o Forno F4. Owner decidiu iniciar novo ciclo de desenvolvimento formal, separado do diagnóstico (fase_1_diagnostico). Sistema de governança replicado do modelo USA_OPS (Fábrica US), que provou eficácia em 30 decisões e dezenas de tasks ao longo de 15 dias de desenvolvimento intenso. | A) Continuar dentro de fase_1_diagnostico (mistura diagnóstico com desenvolvimento) B) Criar diretório dedicado fase_2_ia com governança formal (separação de concerns, rastreabilidade) | B | Owner determinou separação clara entre diagnóstico (fase 1) e desenvolvimento (fase 2). Governança formal garante rastreabilidade e disciplina comprovadas no USA_OPS. Dados brutos permanecem em fase_1_diagnostico/dados/ (readonly). |
