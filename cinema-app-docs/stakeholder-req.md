# Requisitos de Negócio — App de Pré-Produção Audiovisual

## 1. Contexto e Origem da Ideia

**Stakeholder principal:** Sofia, 21 anos, estudante de Cinema na Belas Artes e Assistente de Direção (AD) em produções audiovisuais.

**Problema observado:** A informação de pré-produção de um projeto audiovisual fica espalhada em múltiplas ferramentas desconectadas (pastas do Google Drive, grupos de WhatsApp, documentos avulsos), tornando difícil para o AD e para a equipe acompanhar o que cada departamento está fazendo e em que etapa está.

**Visão da solução:** Um aplicativo web que funcione como uma "bíblia de produção online interativa", centralizando o trabalho de pré-produção de todos os departamentos em um único espaço organizado, colaborativo e acessível.

---

## 2. Objetivos de Negócio

1. **Centralizar** toda a informação de pré-produção em um único local, eliminando dispersão entre Drive, WhatsApp e documentos avulsos.
2. **Organizar** o trabalho por departamento (direção, fotografia, arte, som, produção, etc.), respeitando a estrutura real de uma equipe audiovisual.
3. **Dar visibilidade** ao diretor e demais membros da equipe sobre o andamento de cada área, sem precisar perguntar individualmente.
4. **Facilitar a colaboração** entre departamentos que dividem objetivos comuns (ex.: fotografia + arte + cenografia em uma mesma cena).
5. **Reduzir o esforço operacional do AD**, que hoje atua como ponte manual entre departamentos.

---

## 3. Personas

| Persona | Descrição | Necessidade principal |
|---|---|---|
| **AD (Assistente de Direção)** | Coordena a pré-produção, faz a ponte entre departamentos. | Visão geral do projeto e do progresso de cada área. |
| **Diretor(a)** | Acompanha a visão criativa do projeto. | Visualizar entregas e status de cada departamento de forma rápida. |
| **Chefe de departamento** (fotografia, arte, som, etc.) | Lidera uma área específica. | Espaço próprio para organizar seu trabalho, documentos e cronograma. |
| **Membro de equipe** | Colabora dentro de um departamento. | Ver objetivos, tarefas e poder contribuir/comentar. |

---

## 4. Requisitos Funcionais

### 4.1 Estrutura por Departamento
- O app deve permitir criar **um espaço dedicado por departamento** dentro de um projeto.
- Cada espaço deve conter: **objetivos, próximos passos, etapas, progresso e documentos** da área.
- Exemplo de uso: o diretor de fotografia acessa seu espaço e encontra mapa de luz, locação, data, cronograma da cena, visão do diretor e resumo das reuniões.

### 4.2 Gestão de Projetos
- Suportar **múltiplos projetos** simultâneos.
- Cada projeto deve ter uma página inicial com **visão consolidada** dos departamentos e seus status.

### 4.3 Documentos e Materiais
- Permitir adicionar **moodboards, planilhas, resumos e qualquer documento** relevante.
- Documentos podem ser anexados como **links** (sem necessidade de upload obrigatório).
- Cada item deve poder ser organizado dentro do espaço do departamento.

### 4.4 Papéis e Checklists
- Cada usuário deve poder indicar seu **papel** dentro do projeto.
- Cada papel deve ter **objetivos visíveis em formato de checklist**.
- Tarefas devem poder ser **marcadas como concluídas** pelo responsável.

### 4.5 Colaboração Entre Áreas
- Dois ou mais departamentos devem poder **compartilhar um mesmo objetivo ou espaço de trabalho** quando colaborarem (ex.: fotografia + cenografia).
- Permitir **comentários e sugestões** em tarefas e documentos de outros membros.
- Outros usuários devem poder **conferir entregas** e deixar feedback.

### 4.6 Acompanhamento e Progresso
- O app deve mostrar o **progresso de cada área** de forma clara (ex.: tarefas concluídas vs. pendentes).
- O diretor deve conseguir **acompanhar todas as áreas** sem precisar entrar em conversas paralelas.

---

## 5. Requisitos Não Funcionais

- **Usabilidade:** Interface simples, organizada e com estética clean. O público-alvo são estudantes e profissionais criativos, não usuários técnicos.
- **Acessibilidade:** Aplicação **web**, acessível por qualquer navegador, sem necessidade de instalação.
- **Organização visual:** Layout que separe claramente áreas/departamentos e facilite a navegação entre eles.
- **Colaboração em tempo (quase) real:** Mudanças feitas por um membro devem ser visíveis para os demais sem fricção.

---

## 6. Fora do Escopo (versão inicial)

- Integração nativa com Google Drive, Notion ou outras ferramentas (links manuais bastam no MVP).
- Funcionalidades de produção/filmagem propriamente dita (foco é **pré-produção**).
- Gestão financeira, contratos ou folha de pagamento.
- App mobile nativo (foco inicial é web responsivo).

---

## 7. Critérios de Sucesso

1. Uma equipe de pré-produção consegue **substituir** o uso disperso de Drive + WhatsApp pelo app.
2. O diretor consegue, em **uma única tela**, entender o status de todos os departamentos.
3. Cada chefe de departamento consegue **organizar seu trabalho** sem treinamento técnico prévio.
4. O AD relata **redução do tempo gasto** coordenando informações manualmente entre áreas.