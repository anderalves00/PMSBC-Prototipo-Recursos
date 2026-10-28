# Especificação funcional — Conciliação de recursos e fontes governamentais

**Projeto:** Gestão de Recursos Municipais — Prefeitura de São Bernardo do Campo  
**Versão:** 1.0 — 28/10/2026  
**Situação:** Regras propostas para o sistema definitivo; simulação parcial disponível no protótipo v3.5.

## 1. Objetivo e princípio

Permitir que recursos cadastrados manualmente sejam identificados posteriormente em plataformas governamentais, mantendo um **único registro municipal**, com histórico de trabalho preservado e vínculos a uma ou mais fontes oficiais.

O cadastro municipal não deve ser apagado, substituído ou duplicado quando o registro governamental aparecer. O identificador interno (por exemplo, RG-006) permanece estável. Dados oficiais e anotações municipais são tratados separadamente, com proveniência e trilha de auditoria.

## 2. Rotina de atualização

**Exemplo de agendamento:** 05h00, diariamente, executado no servidor, independentemente de usuários conectados.

1. Consultar APIs, serviços e/ou arquivos autorizados de cada plataforma, respeitando disponibilidade, limites e permissões.
2. Registrar horário de início/fim, fonte, status, quantidade de registros e erros da sincronização.
3. Normalizar identificadores e dados oficiais sem perder o conteúdo bruto e sua origem.
4. Atualizar registros oficiais já vinculados mediante identificador inequívoco.
5. Identificar possíveis correspondências com recursos municipais sem vínculo.
6. Gerar pendências de validação e notificações aos responsáveis.
7. Disponibilizar histórico, divergências e resultados para consulta.

**Restrição:** A possibilidade de automação, abrangência e frequência precisa ser validada individualmente para Transferegov, SP Sem Papel, FNS/InvestSUS, FNAS/EstruturaSUAS e outras fontes. Não presumir APIs ou acesso público para todas elas. A ausência de dados na fonte não significa inexistência do recurso.

## 3. Critérios de conciliação

| Resultado da comparação | Comportamento recomendado |
| --- | --- |
| Correspondência inequívoca por identificador oficial já vinculado | Atualização automática do registro existente, com histórico e controle de alterações |
| Correspondência provável por emenda, exercício, órgão, objeto e valor | Notificação para validação humana; não vincular automaticamente |
| Correspondência insuficiente ou conflitante | Manter registros separados e sinalizar para investigação |

O valor, o órgão e o exercício isoladamente **não** constituem prova suficiente de identidade. Uma emenda pode gerar várias propostas ou instrumentos. A classificação deve considerar a semântica dos identificadores por plataforma, a possibilidade de relações um-para-muitos e conflitos.

### Regras adicionais

- **Idempotência:** importar novamente o mesmo dado não deve criar novas notificações ou vínculos duplicados.
- **Atualização oficial:** alterações recebidas por integração devem registrar fonte, data e valores anteriores, sem sobrescrever observações municipais.
- **Revisão humana:** confirmação ou rejeição exige registro do usuário, data/hora e justificativa.
- **Conflito:** divergência entre valores ou objeto deve ser destacada, não resolvida silenciosamente.
- **Segurança:** permissões por perfil; somente servidores autorizados podem confirmar ou rejeitar correspondências.
- **Reprocessamento:** sincronização com falha deve ser passível de nova execução; o sistema deve exibir a última sincronização bem-sucedida por fonte.

## 4. Decisões apresentadas ao usuário

1. **Confirmar vínculo:** associar o registro oficial ao recurso municipal, preservando seu identificador, histórico e dados internos; registrar a decisão e os dados vinculados.
2. **Não corresponde:** rejeitar a sugestão e registrar a justificativa. A rejeição não apaga nenhum dos registros e deve ser considerada nas próximas execuções para evitar alertas repetidos sem fatos novos.
3. **Analisar posteriormente:** manter a pendência aberta para conferência posterior, sem vinculação.

Em todos os casos, apresentar campos lado a lado e a origem de cada informação. Quando houver múltiplos candidatos, não forçar a seleção de um único registro sem análise.

## 5. Notificações

**Exemplo de mensagem:** "O recurso RG-006, cadastrado manualmente em 15/10/2026 por Fulano de Tal, apresenta possível correspondência com a demanda DEMANDA-DEMO-2026-045 no SP Sem Papel, identificada na atualização das 05h12. Validação pendente."

A notificação deve apresentar recurso municipal, fonte, identificador oficial, momento da detecção, critérios da comparação, eventuais divergências e ação **Analisar possível vínculo**.

O horário de 05h é ilustrativo e configurável. Notificações devem respeitar permissões e ser deduplicadas.

## 6. Rastreabilidade e auditoria

Para cada recurso e vinculação, preservar:

- Quem cadastrou o recurso municipal e quando.
- Quando o sistema identificou a possível correspondência.
- Qual fonte e execução da sincronização identificaram o registro.
- Quais campos e regras foram usados na comparação, com valores relevantes.
- Quem confirmou ou rejeitou o vínculo, data/hora e justificativa.
- Quais identificadores oficiais foram associados.
- Quais informações oficiais foram incorporadas ou atualizadas, com valores anteriores e posteriores.
- Qual a situação da pendência, incluindo adiamentos e rejeições.
- Referência à versão dos dados oficiais e histórico de alterações.

**Exemplo:** RG-006 continua sendo RG-006 após confirmação; passa a possuir um vínculo com a demanda estadual. Isso preserva a atuação municipal anterior à existência do registro na plataforma e permite auditoria.

## 7. Modelo de dados sugerido

Entidades conceituais distintas:

- **RecursoMunicipal:** ID interno, fase municipal, objeto, valores municipais, unidade, responsáveis, observações, data e autor do cadastro.
- **ProgramaOficial:** plataforma, código, nome, descrição, regras, prazos, documentos, situação e publicação.
- **PropostaOficial:** identificadores de proposta e, quando aplicável, Novo PAC, objeto, valor, situação oficial e proponente.
- **InstrumentoOficial:** identificador, modalidade jurídica, assinatura, publicação, vigência e valores.
- **RegistroFonte:** identificador externo, sistema, payload recebido, versão e horário da sincronização.
- **VinculoRecursoFonte:** recurso municipal, tipo de registro oficial, ID externo, origem da decisão, confirmação e situação.
- **CandidatoConciliacao:** registro municipal, candidato externo, critérios, divergências, estado, justificativa e decisor.
- **ExecucaoSincronizacao:** fonte, agendamento, início, fim, resultado, contagens e erros.
- **EventoAuditoria / Notificacao:** eventos imutáveis e alertas ao usuário.

Um programa pode relacionar-se a várias propostas; uma proposta pode gerar um ou mais instrumentos conforme regras da fonte. A modelagem não deve presumir relação um-para-um universal.

## 8. Diferenças por origem

**Transferegov:** programa, proposta, instrumento e identificadores Novo PAC são conceitos distintos. A situação oficial da proposta não é a fase municipal. Identificar corretamente proponente, emenda e órgão.

**SP Sem Papel:** demanda estadual e eventuais processos/instrumentos devem manter identificadores próprios. Regras de classificação operacional não substituem os dados oficiais.

**FNS/InvestSUS e FNAS/EstruturaSUAS:** preservar os identificadores e fluxos específicos; no escopo atual, o acompanhamento destas fontes é direcionado a emendas parlamentares.

**Cadastro manual / SEI:** campos desconhecidos permanecem em branco ou pendentes; novas informações podem ser acrescentadas posteriormente, com auditoria. Não exigir número oficial inexistente no momento do cadastro.

## 9. Manual do usuário — procedimento

1. Abra **Conciliação** no menu lateral.
2. Consulte notificações de possíveis correspondências.
3. Selecione **Analisar possível vínculo**.
4. Compare os identificadores, órgão, exercício, objeto, emenda, valores e fonte, verificando divergências.
5. Registre a justificativa.
6. Escolha **Confirmar vínculo**, **Não corresponde** ou **Analisar posteriormente**.
7. Consulte o histórico da conciliação e, se confirmado, o vínculo na ficha do recurso.

**Atenção:** confirmar um vínculo não significa aprovar a proposta, formalizar o instrumento ou alterar automaticamente a fase municipal.

## 10. Situação do protótipo

A versão v3.5 implementa uma simulação **local e de sessão**, com botão de atualização, notificação, comparação, decisões, justificativa e registro de vínculo. Não implementa autenticação real, execução automática às 05h, consulta a APIs, algoritmos reais de correspondência, filas persistentes no servidor ou auditoria imutável. O registro de exemplo e seus dados são fictícios.

## 11. Critérios de aceite para o sistema definitivo

- Sincronização agendada e rastreável por fonte.
- Importação idempotente e com detecção de falhas.
- Recurso municipal não duplicado por aparecimento de registro oficial.
- Vinculação automática somente por identificador inequivocamente já associado.
- Correspondência provável submetida a revisão humana.
- Rejeição justificada e pendência adiada registradas.
- Campos oficiais protegidos e atualizados com proveniência.
- Histórico de cadastro, decisão e alteração preservado.
- Permissões e trilhas de auditoria verificáveis.
- Notificações acessíveis após login, independentemente do navegador ou sessão.
