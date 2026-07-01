---
title: "CineFlow Studio — Especificação de Requisitos"
author: "Sofia (AD) / demo Spec Kit"
date: "2026-07-01"
version: "1.2"
status: "draft"
locale: "pt-br"
---

## CineFlow Studio

**CineFlow Studio** é uma plataforma **web, colaborativa e responsiva** para organizar a pré-produção audiovisual em um espaço único, simples e visual. A aplicação centraliza objetivos, decisões criativas, checklists, links externos, progresso e colaboração em tempo real, substituindo fluxos fragmentados em Drive, mensagens e documentos soltos.

O produto também será usado como **demonstração prática dos modos Ask, Plan e Agent**, mostrando como um fluxo moderno de desenvolvimento assistido por IA pode transformar requisitos em primitivos de produto, economizar tokens e implementar funcionalidades avançadas com clareza.

## 1. Objetivo do produto

Em uma produção audiovisual, decisões sobre cenas, responsáveis, referências, pendências e documentos costumam ficar espalhadas. Isso cria ruído, retrabalho e baixa visibilidade para direção, assistência de direção e chefes de área.

O CineFlow Studio resolve esse problema com uma interface focada em **objetivos de pré-produção**. Cada objetivo reúne contexto, responsáveis, status, decisões, comentários e links externos. A plataforma não tenta ser um repositório de arquivos; ela funciona como uma camada inteligente de coordenação e acompanhamento.

## 2. Objetivo da demonstração

A aplicação deve ser construída para demonstrar, de forma clara e didática, três modos de trabalho com IA:

- **Ask**: exploração rápida de requisitos, dúvidas de produto, explicações de componentes, decisões de UX e análise de trade-offs.
- **Plan**: decomposição de funcionalidades em etapas, desenho dos primitivos, identificação de riscos, definição de validações e preparação da implementação.
- **Agent**: execução autônoma e assistida de tarefas, criação de arquivos, implementação incremental, validação, correção e entrega de funcionalidades avançadas.

A demonstração deve enfatizar:

- Criação de **primitivos de produto** reutilizáveis: projeto, área, objetivo, checklist, decisão, comentário, documento externo, template e presença.
- **Economia de tokens** por meio de contexto enxuto, componentes reutilizáveis, requisitos bem estruturados e separação clara entre regras, dados e interface.
- Implementação progressiva de funcionalidades simples primeiro e recursos avançados depois.
- Capacidade de transformar uma especificação textual em uma experiência funcional, moderna e responsiva.

## 3. Usuários e papéis

Usuários principais:

- **Diretor**
- **Assistente de Direção (AD)**
- **Chefe de área**: fotografia, arte, som, figurino, produção, montagem, locação ou outras áreas configuráveis.
- **Membro de equipe**

Regras:

- Um usuário pode ter **um ou mais papéis**.
- Um usuário pode participar de **uma ou mais áreas**.
- A interface adapta menus, checklists, filtros e permissões conforme os papéis atribuídos.
- Quando o usuário tem mais de um papel, a navegação deve permitir alternar entre visões sem duplicar informação.

## 4. Autenticação e perfil

Requisitos:

- Para a demonstração, o login deve ser **simples, local e sem dependência obrigatória de SSO externo**.
- A tela inicial deve permitir escolher um perfil demo pré-configurado, como Diretor, AD, Chefe de Fotografia, Chefe de Arte ou Membro de Equipe.
- O usuário também pode informar nome e selecionar papéis manualmente, sem criar conta real.
- O perfil selecionado define objetivos visíveis, permissões, checklist inicial e visão padrão.
- Dados de sessão podem ser mantidos localmente durante a demo.
- A arquitetura deve permitir adicionar **Google OAuth** no futuro, mas isso não é requisito obrigatório para o MVP local.

## 5. Primitivos do produto

O sistema deve ser modelado a partir dos seguintes primitivos:

### Projeto

Representa uma produção audiovisual. Contém áreas, colaboradores, objetivos, templates, links, decisões e progresso geral.

### Área

Representa um núcleo de trabalho da produção. Cada área possui objetivos, checklist, progresso, documentos externos, decisões e comentários. Áreas devem ser visualmente separadas, mas conectadas quando compartilham objetivos.

### Objetivo

É a unidade central do produto. Representa uma entrega concreta da pré-produção, como mapa de luz, moodboard, pesquisa sonora, conceito de figurino, plano de locação ou referência de cena.

Cada objetivo possui uma área principal e pode ser compartilhado com outras áreas.

### Checklist

Lista operacional de objetivos por área, com status, responsáveis e progresso.

### Decisão

Registro estruturado de uma decisão criativa, técnica ou logística, sempre com data, autor e contexto.

### Documento externo

Link para arquivos em serviços como Google Drive, Figma, Dropbox, Notion, Milanote ou outras ferramentas. A plataforma **não hospeda arquivos** no MVP.

### Comentário

Mensagem contextual ligada a um objetivo. Pode conter menções, reações simples e histórico.

### Template

Modelo reutilizável para tipos de objetivo, com orientações, estrutura esperada e checklist interno opcional.

### Presença

Indicação de pessoas ativas em um objetivo, reduzindo trabalho duplicado e facilitando colaboração.

## 6. Ciclo de vida do objetivo

Estados obrigatórios:

- **Não iniciado**
- **Em andamento**
- **Concluído**

Comportamentos:

- Ao marcar como **Em andamento**, o usuário passa a aparecer como colaborador ativo.
- Se várias pessoas estiverem trabalhando no mesmo objetivo, todos os indicadores de presença devem aparecer.
- Ao marcar como **Concluído**, o objetivo muda visualmente de estado e passa a contar para o progresso da área.
- Cor nunca deve ser o único indicador de status; sempre usar texto e ícone junto.

## 7. Detalhe do objetivo

Ao abrir um objetivo, o usuário deve ver:

1. **Resumo**: título, status, área principal, áreas compartilhadas, prazo, fase, cena ou locação relacionada.
2. **Orientação do template**: descrição curta sobre o que normalmente se espera daquela entrega.
3. **Visão do diretor**: notas conceituais, intenção criativa e links de referência.
4. **Decisões definidas**: lista cronológica de decisões com data, autor e descrição.
5. **Links e documentos externos**: URL, descrição curta, autor e data da última atualização.
6. **Comentários contextuais**: thread do objetivo, com menções e reações leves.
7. **Colaboradores ativos**: presença em tempo real ou último indicador conhecido.

## 8. Checklist, progresso e supervisão

Cada área deve ter uma checklist central com objetivos, status, responsáveis, presença e progresso.

O percentual de progresso da área deve ser calculado automaticamente a partir dos objetivos concluídos.

Diretor e AD têm acesso ao **modo supervisão**, com:

- Visão consolidada de todas as áreas.
- Percentual de progresso por área.
- Acesso a objetivos, decisões e documentos concluídos.
- Possibilidade de comentar e apoiar qualquer objetivo.
- Restrição para não editar documentos inacabados, exceto quando estiverem colaborando ativamente no objetivo.

## 9. Permissões

| Ação | Membro da área | Chefe de área | Diretor / AD |
| --- | --- | --- | --- |
| Ver objetivos próprios | Sim | Sim | Sim |
| Ver objetivos de outras áreas | Opcional | Sim | Sim |
| Marcar objetivo como em andamento | Sim, se atribuído | Sim | Sim |
| Marcar objetivo como concluído | Sim, se atribuído | Sim | Sim |
| Comentar | Sim | Sim | Sim |
| Registrar decisões | Sim | Sim | Sim |
| Editar templates do projeto | Não | Parcial | Sim |
| Configurar áreas e papéis | Não | Não | Sim |

Permissões devem ser simples, previsíveis e fáceis de explicar durante a demonstração.

## 10. Colaboração em tempo real e autosave

Requisitos:

- Todas as edições relevantes devem salvar automaticamente.
- Mudanças de status, comentários, decisões e presença devem atualizar em tempo real quando possível.
- A interface deve mostrar estados de carregamento, salvamento, erro e sucesso.
- Em caso de falha de rede, o usuário deve receber feedback claro e poder tentar novamente.

## 11. Integrações com APIs externas

O produto deve considerar integração com APIs externas desde o desenho inicial, mesmo que nem todas sejam implementadas no MVP.

Integrações prioritárias:

- **Google Drive** para links de documentos e referências.
- **Figma** para links de moodboards, identidade visual e referências de arte.
- **Google OAuth** como evolução futura para autenticação real.
- **Dropbox ou OneDrive** como opções futuras para documentos externos.
- **APIs de notificação** para alertas contextuais futuros.

Regras:

- A plataforma guarda metadados e links, não arquivos.
- Integrações devem ser desacopladas por adaptadores ou serviços internos.
- Falhas de APIs externas não devem quebrar a navegação principal.

## 12. Personalização

O sistema deve permitir personalização por projeto:

- Nome, capa e descrição do projeto.
- Áreas ativas da produção.
- Tipos de objetivo.
- Templates por tipo de objetivo.
- Cores auxiliares por área, mantendo a paleta principal.
- Preferência de visualização: minhas tarefas, minha área, supervisão ou todos os objetivos.

Personalização deve ampliar a utilidade sem deixar a interface complexa.

## 13. UI / UX

A experiência deve ser **moderna, bonita, simples, intuitiva e responsiva**.

Direção visual:

- Fundo principal branco.
- Layout limpo, com bastante respiro e hierarquia clara.
- Componentes discretos, com bordas suaves e contraste adequado.
- Uso de cor como apoio visual, nunca como único sinal de estado.
- Navegação rápida entre projeto, áreas, objetivos e supervisão.
- Interface preparada para desktop, tablet e mobile web responsivo.

Paleta inspirada nas cores do logo Microsoft:

| Uso | Cor |
| --- | --- |
| Fundo principal | Branco `#FFFFFF` |
| Superfícies secundárias | Cinza muito claro `#F7F7F9` |
| Texto primário | Grafite `#1F2937` |
| Texto secundário | Cinza `#6B7280` |
| Ação primária / foco | Azul Microsoft `#00A4EF` |
| Alerta / em andamento | Amarelo Microsoft `#FFB900` |
| Concluído / sucesso | Verde Microsoft `#7FBA00` |
| Atenção / bloqueio | Vermelho Microsoft `#F25022` |
| Linha e divisores | Cinza claro `#E5E7EB` |

Requisitos responsivos:

- Em desktop, priorizar visão de painel com áreas, objetivos e detalhe lateral.
- Em tablet, manter navegação por abas ou painéis colapsáveis.
- Em mobile web, exibir listas claras, cartões compactos e ações principais sempre acessíveis.
- Textos e botões devem se adaptar sem sobreposição.

## 14. Ambiente de execução local

O MVP será executado localmente para demonstração usando **Docker Desktop**.

Requisitos:

- A aplicação deve subir em ambiente local com Docker Desktop.
- O projeto deve prever um `Dockerfile` para a aplicação web.
- O projeto deve prever `docker-compose.yml` quando houver necessidade de orquestrar aplicação, API, banco local ou serviços auxiliares.
- O ambiente local deve iniciar com poucos comandos e sem dependência de serviços pagos ou credenciais externas obrigatórias.
- Dados de demonstração devem ser carregados automaticamente ou disponibilizados por seed local.
- O login demo deve funcionar offline, sem SSO externo.
- A documentação deve indicar como subir, parar e reiniciar a aplicação localmente.

## 15. Funcionalidades avançadas para demonstrar

Funcionalidades que enriquecem a demonstração:

- Filtros por área, status, responsável, prazo e colaboração ativa.
- Modo supervisão com visão consolidada de progresso.
- Indicadores de presença no objetivo.
- Templates editáveis por projeto.
- Registro estruturado de decisões.
- Comentários contextuais com menções.
- Autosave com feedback visual.
- Integração com links externos.
- Painel de economia de contexto da demo, mostrando como os primitivos reduzem repetição na implementação.
- Fluxo guiado que mostre quando usar Ask, Plan e Agent durante a criação de uma funcionalidade.

## 16. Fora de escopo do MVP

- Orçamento, contabilidade e prestação de contas.
- Gestão de elenco.
- Chat genérico fora do contexto de objetivos.
- Hospedagem de arquivos.
- Aplicativo mobile nativo.
- Edição colaborativa de arquivos externos dentro da plataforma.
- SSO obrigatório com provedor externo na primeira versão da demo.
- Deploy em cloud na primeira versão da demo.

## 17. Critérios de sucesso

O MVP será considerado bem-sucedido se permitir:

- Criar um projeto audiovisual.
- Configurar áreas e papéis.
- Criar, listar, filtrar e abrir objetivos.
- Alterar status de objetivos.
- Registrar decisões e comentários.
- Adicionar links externos.
- Visualizar progresso por área.
- Alternar entre visão de membro, área e supervisão.
- Executar a aplicação localmente via Docker Desktop.
- Entrar na aplicação usando perfis demo sem configurar SSO.
- Demonstrar claramente os modos Ask, Plan e Agent no fluxo de construção do produto.
- Apresentar interface responsiva, simples e visualmente moderna.

## 18. Diferenciais

- Produto específico para pré-produção audiovisual.
- Organização centrada em objetivos, não em documentos.
- Conecta visão criativa, execução e supervisão.
- Mostra colaboração sem criar feed de ruído.
- Usa primitivos simples para acelerar desenvolvimento e reduzir contexto repetido.
- Serve como demonstração completa de desenvolvimento assistido por IA: Ask para entender, Plan para estruturar e Agent para implementar.

## 19. Referências

- [Microsoft Design](https://www.microsoft.com/design)
- [Microsoft Fluent UI](https://developer.microsoft.com/fluentui)
- [Google Identity](https://developers.google.com/identity)
- [WCAG 2.2](https://www.w3.org/TR/WCAG22/)
