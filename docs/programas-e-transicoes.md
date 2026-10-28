# Especificação funcional — Consulta de programas e transições municipais

**Projeto:** Gestão de Recursos Municipais — Prefeitura de São Bernardo do Campo  
**Versão:** 1.0 — 28/10/2026  
**Status:** Definições funcionais para validação; protótipo v3.6.

## 1. Princípios

O catálogo **Disponíveis** reúne programas considerados pertinentes ao Município segundo os dados e filtros efetivamente oferecidos pela fonte. Pertinência não deve ser confundida com habilitação formal, seleção ou garantia de repasse. Não presumir que todas as APIs permitem filtrar programas pelo CNPJ municipal.

O acompanhamento municipal possui fases próprias, independentes das situações oficiais de programa, proposta, seleção PAC e instrumento. Uma oportunidade pode permanecer no catálogo mesmo após iniciar acompanhamento municipal.

Os dados oficiais devem ser vinculados às entidades de origem, preservando proveniência, data de sincronização e histórico. Não copiar automaticamente o valor global de um programa para o repasse municipal.

## 2. Catálogo Disponíveis

Campos de consulta recomendados:
- Identificação: código, nome, esfera, plataforma, órgão concedente, qualificação do proponente, publicação no DOU e instituição mandatária, quando aplicável.
- Características: descrição, objeto, categoria, público, modalidade e condições gerais.
- Prazos: início e fim do **recebimento de propostas**, situação de disponibilização, eventuais prorrogações. Não confundir com vigência do programa.
- Contrapartida: regras, percentuais e exceções, quando existentes.
- Documentos: edital, portaria, anexos e orientações, com fonte e versão.
- Novo PAC: identificação de programa PAC e regras próprias. Números de proposta, seleção individual e instrumento pertencem aos registros correspondentes e não devem aparecer como ausentes no catálogo.
- Acompanhamento municipal: indicação de análise iniciada e acesso ao recurso correspondente.

**Interface:** identificação e características lado a lado, sempre expandidas; demais seções em coluna, recolhidas por padrão; controles Expandir todos e Recolher todos. Explicações operacionais devem aparecer em ajuda contextual, não como advertências técnicas permanentes.

## 3. Eventos e fases

| Fase | Entrada conceitual | Dados/ações específicas | Condição de saída sugerida |
| --- | --- | --- | --- |
| Disponíveis (catálogo, não fase) | Programa pertinente identificado na fonte | Consulta de requisitos, documentos e prazos | Servidor inicia acompanhamento municipal |
| 1 — Em Análise | Município decide avaliar formalmente | Responsáveis, providências, avaliação de requisitos e decisão | Início comprovado da preparação/cadastro da proposta ou evento equivalente |
| 2 — Em Cadastramento | Proposta em preparação ou registrada na plataforma | Identificador, plano de trabalho, anexos, submissão, diligências e análise oficial | Aprovação/aceite ou autorização de formalização, conforme modalidade |
| 3 — Em Formalização | Procedimento formalizador iniciado | Minuta, documentos, pareceres, assinatura, publicação, instrumento | Formalização concluída e requisitos da próxima etapa atendidos |
| 4 — Aguardando Execução | Instrumento ou autorização aptos, mas execução ainda não iniciada | Condições suspensivas, liberação, cronograma e planejamento | Requisitos para início dos atos de execução |
| 5 — Execução Administrativa | Preparação administrativa | Licitação, contratação, empenho, designações e demais atos | Condições para execução material/financeira |
| 6 — Execução | Execução efetiva | Medições, entregas, pagamentos, execução física e financeira | Encerramento da execução e preparação de contas |
| 7 — Prestação de Contas Final | Prestação final iniciada | Comprovação, envio, diligências e decisão final | Concluído/reprovado conforme resultado, sem criar oitava fase |

**Atenção:** A geração de número de proposta pode ocorrer antes do envio. A existência de número de instrumento pode anteceder assinatura/publicação. Eventos oficiais e regras de passagem precisam ser mapeados por plataforma, modalidade e tipo de recurso antes de qualquer automatização.

## 4. Automação de transições

1. Receber evento oficial e atualizar a entidade correspondente.
2. Identificar plataforma, tipo de transferência, modalidade e fase municipal atual.
3. Verificar se o evento é suficiente para a transição ou apenas uma evidência adicional.
4. Quando inequívoco e autorizado, executar a transição e registrar evento, fonte, horário e valores anteriores/novos.
5. Quando houver dúvida, dados ausentes ou conflito, gerar sugestão para revisão humana.
6. Nunca retroceder ou avançar silenciosamente por inferência baseada somente na existência de um identificador.
7. Permitir correção justificada e trilha de auditoria.

## 5. Cadastro manual

O cadastro manual pode conter apenas os dados conhecidos. Campos oficiais desconhecidos ficam pendentes, não são inventados e não impedem o acompanhamento. Quando a fonte oficial identificar o recurso, deve ocorrer conciliação conforme [Especificação de Conciliação](conciliacao-recursos.md), sem duplicar o RG municipal ou apagar suas observações.

## 6. Manual do usuário — Consulta de programas

1. Abra **Em Análise → Disponíveis**.
2. Selecione **Consultar programa**.
3. Examine identificação e características; expanda prazos, contrapartida, documentos e informações PAC conforme necessário.
4. Utilize os ícones **?** para explicações de campos.
5. Se houver interesse em acompanhar a oportunidade, clique **Iniciar análise municipal**.
6. O sistema cria o recurso municipal em **Em Análise**, vinculado ao programa, sem presumir proposta apresentada.
7. Posteriormente, quando o cadastramento oficial iniciar, o recurso poderá passar à fase **Em Cadastramento**, conforme as regras de transição.

## 7. Pendências para integração definitiva

- Confirmar campos e filtros efetivamente expostos por cada fonte, inclusive elegibilidade territorial/CNPJ.
- Confirmar significados oficiais de datas e situações.
- Mapear eventos reais de proposta criada, enviada, analisada, aprovada e instrumento formalizado.
- Definir documentos obrigatórios por modalidade.
- Definir permissões e regras de transição automática versus revisão humana.
- Validar fluxos de PAC, transferências especiais, fundo a fundo e SP Sem Papel.
