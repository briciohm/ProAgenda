# ProAgenda
> **Agenda inteligente para profissionais autônomos** — tatuadores, eletricistas, bartenders, esteticistas e outros prestadores de serviço que trabalham sozinhos.
O ProAgenda é uma aplicação **front-end single-page** construída em **HTML5 semântico, CSS3 moderno (variáveis, Grid, Flexbox) e JavaScript puro (Vanilla JS)**, sem frameworks de UI e sem dependências de build. Todo o sistema — autenticação, calendário semanal, gestão de agendamentos, clientes, serviços e acessibilidade — está contido em um único arquivo `index.html`, com uma camada de API simulada que reproduz o contrato REST previsto para o back-end Node.js.
---
## Sumário
1. [Visão geral](#1-visão-geral)
2. [Acesso de demonstração](#2-acesso-de-demonstração)
3. [Como executar](#3-como-executar)
4. [Mapa de telas e rotas](#4-mapa-de-telas-e-rotas)
5. [Funcionalidades detalhadas](#5-funcionalidades-detalhadas)
   - 5.1 [Autenticação e recuperação de acesso](#51-módulo-1--autenticação-e-recuperação-de-acesso)
   - 5.2 [Dashboard e calendário semanal](#52-módulo-2--dashboard-e-calendário-semanal)
   - 5.3 [Gestão de agendamentos (CRUD)](#53-módulo-3--gestão-de-agendamentos-crud)
   - 5.4 [Clientes](#54-módulo-4a--clientes)
   - 5.5 [Serviços](#55-módulo-4b--serviços)
   - 5.6 [Barra de acessibilidade](#56-módulo-5--barra-de-acessibilidade)
6. [Design system](#6-design-system)
7. [Responsividade](#7-responsividade)
8. [Regras de negócio e validações](#8-regras-de-negócio-e-validações)
9. [Acessibilidade](#9-acessibilidade)
10. [Arquitetura e organização do código](#10-arquitetura-e-organização-do-código)
11. [Modelo de dados](#11-modelo-de-dados)
12. [Persistência e dados de exemplo](#12-persistência-e-dados-de-exemplo)
13. [Integração com back-end Node.js](#13-integração-com-back-end-nodejs)
14. [Contrato da API](#14-contrato-da-api)
15. [Fluxos de uso passo a passo](#15-fluxos-de-uso-passo-a-passo)
16. [Checklist de testes](#16-checklist-de-testes)
17. [Limitações conhecidas](#17-limitações-conhecidas)
18. [Próximos passos e roadmap](#18-próximos-passos-e-roadmap)
19. [Navegadores suportados](#19-navegadores-suportados)
20. [Créditos e licença](#20-créditos-e-licença)
---
## 1. Visão geral
### Problema
Profissionais autônomos costumam controlar horários em cadernos, conversas de WhatsApp ou planilhas paralelas. Isso gera **conflitos de horário, esquecimentos, perda de receita e dificuldade de visualizar a própria produtividade**. Muitas soluções de mercado são complexas, caras ou voltadas a clínicas e equipes — não a quem trabalha sozinho.
### Proposta
Uma agenda enxuta, de alto contraste e amigável para usuários com pouca familiaridade tecnológica, que reúne em um só painel:
- **Calendário semanal** com horários livres e ocupados;
- **Receita bruta da semana** calculada automaticamente;
- **Resumo diário** com próximo horário e horas reservadas;
- **Cadastro completo** de clientes e catálogo de serviços;
- **Acessibilidade nativa**, com aumento de fonte persistente em todo o layout;
- **Experiência mobile-first**, pois o autônomo consulta a agenda principalmente pelo celular durante o trabalho.
### Diferenciais implementados
| Diferencial | Como funciona |
|---|---|
| Fonte escalável de verdade | Todo o CSS usa `rem`; o A+ altera apenas o `font-size` de `<html>` e o layout inteiro cresce proporcionalmente |
| Mobile-first real | Sidebar vira drawer hambúrguer + bottom navigation fixa com 5 atalhos em telas ≤ 768 px |
| Horário livre clicável | Cada intervalo vago da timeline abre o formulário com data e hora já preenchidas |
| Preenchimento inteligente | Serviço selecionado preenche automaticamente duração, término e valor |
| Cliente reaproveitado | Digitar um nome existente carrega telefone/e-mail; nome novo é cadastrado automaticamente ao salvar |
| Proteção contra conflito | A API valida sobreposição de horários antes de criar ou editar um agendamento |
| Receita confiável | Cancelados não entram na receita bruta, no resumo do dia nem nas horas reservadas |
| Preferências persistentes | Sessão, base local e tamanho da fonte sobrevivem ao recarregamento |
---
## 2. Acesso de demonstração
O sistema inicia na tela de login com uma conta de exemplo já criada.
| Campo | Valor |
|---|---|
| **E-mail** | `ana@proagenda.com` |
| **Senha** | `123456` |
| **Perfil** | Ana Ribeiro — Tatuadora & Piercer |
> O e-mail já vem preenchido no formulário. Também é possível criar uma nova conta em **Criar conta**, com nome, e-mail, telefone, atividade e senha (mínimo de 6 caracteres).
### Recuperação de senha (demonstração)
O código OTP de 4 dígitos é gerado aleatoriamente pelo servidor simulado e **exibido em um toast** logo após a solicitação (por exemplo: `Código enviado! (demo: 4821)`). Em produção, o código deve ser enviado exclusivamente por e-mail, com validade de 5 minutos.
---
## 3. Como executar
### Opção A — abrir diretamente
1. Baixe o arquivo `index.html`.
2. Dê duplo clique (ou arraste para o navegador).
3. Pronto: não há instalação, build ou dependências obrigatórias.
### Opção B — servidor local (recomendado)
```bash
# Python 3
python -m http.server 5500
# ou Node.js (npx)
npx serve .
```
Acesse `http://localhost:5500`.
### Dependências externas (CDN)
| Recurso | Uso | Obrigatório? |
|---|---|---|
| Google Fonts — **Inter** | Tipografia principal | Recomendado (há fallback para system-ui) |
| Tailwind CSS Browser 4 | Disponibilizado conforme requisito do projeto; a interface usa CSS próprio | Não — a interface funciona 100% sem ele |
> Não há `npm install`. Para publicar, basta hospedar o `index.html` em GitHub Pages, Vercel, Netlify ou qualquer servidor estático.
---
## 4. Mapa de telas e rotas
O roteamento é feito por **hash** (`window.location.hash`), com guarda de autenticação: rotas privadas redirecionam para `#/login` quando não há sessão; usuário logado que acessa login/cadastro é levado ao dashboard.
| Rota | Tela | Autenticação | Principais recursos |
|---|---|---|---|
| `#/login` | Entrar na agenda | Pública | E-mail, senha com olho, login social, link de recuperação, link de cadastro |
| `#/cadastro` | Criar conta | Pública | Nome, e-mail, telefone com máscara, atividade, senha |
| `#/recuperar` | Recuperar acesso (3 etapas) | Pública | E-mail → OTP de 4 dígitos com cronômetro → nova senha + confirmação |
| `#/dashboard` | Calendário semanal | Privada | Faixa de dias, timeline, cards, painel de métricas, busca, notificações |
| `#/agendamentos` | Todos os compromissos | Privada | Histórico em tabela, busca global, editar e excluir |
| `#/agendamento` | Novo agendamento | Privada | Formulário em grid de 2 colunas; aceita `?data=AAAA-MM-DD&hora=HH:MM` |
| `#/agendamento?id=<id>` | Gerenciar/editar | Privada | Resumo do agendamento, zona de risco, campos editáveis, salvar alterações |
| `#/clientes` | Clientes | Privada | Cards, busca com debounce, histórico, cadastrar/editar/excluir |
| `#/servicos` | Serviços | Privada | Tabela, resumo do catálogo, cadastrar/editar/excluir |
### Navegação
- **Desktop (> 768 px):** sidebar fixa com perfil, Calendário, Compromissos (badge), Clientes, Serviços e Sair.
- **Mobile (≤ 768 px):** sidebar como drawer acionado pelo hambúrguer + **bottom navigation** fixa: Agenda, Compromissos, Clientes, Serviços e Novo.
- **Topbar:** busca rápida (`Ctrl+K` / `⌘K`), notificações com painel, menu do usuário e botão **+ Novo compromisso**.
---
## 5. Funcionalidades detalhadas
### 5.1 Módulo 1 — Autenticação e recuperação de acesso
#### Login
- Card centralizado em layout de duas colunas (painel institucional + formulário).
- Campos de **e-mail** e **senha** com `autocomplete` correto.
- Botão de **mostrar/ocultar senha** (ícone de olho alterna entre `eye` e `eyeOff`, com `aria-label` dinâmico).
- Validação em JavaScript:
  - campo vazio → mensagem "Informe seu e-mail" / "Informe sua senha";
  - e-mail fora do padrão → mensagem abaixo do campo;
  - credencial inválida retornada pela API → borda vermelha no input + mensagem de erro;
  - senha incorreta e e-mail inexistente têm mensagens distintas.
- Botões de **login social** (Google, Apple, Facebook) com identidade visual das marcas; exibem aviso de disponibilidade na versão conectada ao back-end.
- Link **Esqueci minha senha** e **Criar conta**.
- Ao autenticar: token + usuário são salvos em `localStorage` e o usuário é redirecionado ao dashboard.
#### Cadastro
- Nome completo, e-mail, telefone com máscara, atividade profissional (Tatuador, Eletricista, Bartender, Esteticista, Outro) e senha.
- Validações de nome, formato de e-mail, senha mínima de 6 caracteres e bloqueio de e-mail duplicado (HTTP 409 no mock).
- Criação bem-sucedida autentica o usuário e abre o dashboard.
#### Recuperação de senha em três etapas
| Etapa | Tela | Comportamento |
|---|---|---|
| **1. E-mail** | Solicitação | Valida o formato; chama `POST /api/auth/reset-code`; gera código de 4 dígitos com validade de 5 minutos |
| **2. Código OTP** | 4 inputs independentes | Ao digitar um dígito, o foco avança automaticamente; `Backspace` em campo vazio volta ao anterior; cronômetro regressivo de **30 s** para reenvio; erro em vermelho se o código estiver incompleto/inválido |
| **3. Nova senha** | Criação e confirmação | Exige mínimo de 6 caracteres e igualdade entre os dois campos; atualiza a senha no banco simulado e redireciona ao login |
- Indicador visual de progresso (bolinhas numeradas, verdes quando concluídas).
- Preenchimento automático do último dígito tenta enviar o formulário.
- Botão **Reenviar agora** só é habilitado após o cronômetro zerar.
- Link **Voltar para o login** em todas as etapas.
---
### 5.2 Módulo 2 — Dashboard e calendário semanal
#### Cabeçalho e navegação
- **Perfil do usuário** no topo da sidebar: avatar com iniciais, nome e cargo.
- Links com ícones + **badge numérico** de compromissos ativos em "Compromissos".
- **Busca rápida** "Buscar evento, cliente ou serviço":
  - dropdown com até 6 resultados globais (cliente, data, hora e serviço);
  - no dashboard, filtra a timeline do dia selecionado em tempo real;
  - na tela de compromissos, filtra a tabela completa sem recarregar a tela;
  - atalho de teclado `Ctrl+K` / `⌘K`; fechada ao clicar fora.
- **Notificações:** painel com próximo compromisso, total de compromissos ativos e status do catálogo.
- **Menu do usuário:** nome, e-mail e ação de logout.
- Botão de destaque **+ Novo compromisso**.
#### Seletor semanal interativo
- Exibição do intervalo por extenso: *"12 – 17 de outubro"*, com mês/ano quando o intervalo cruza meses ou anos.
- Botões **← / →** para navegar semanas e **Hoje** para retornar à semana atual.
- Faixa horizontal **DOM a SÁB** (segunda a sábado) com:
  - dia da semana abreviado;
  - número do dia;
  - contagem de compromissos abaixo de cada dia;
  - dia selecionado em **azul vibrante** com sombra;
  - marcador de "hoje".
- Ao trocar de semana, o dia selecionado acompanha a segunda-feira da nova semana.
#### Linha do tempo diária
- Grade das **07:00 às 20:00** (14 blocos de uma hora), com horário alinhado à esquerda.
- **Cards de agendamento** em azul degradê:
  - horário de início e fim;
  - nome do cliente (clicável → edição);
  - tipo de serviço;
  - observação resumida (quando houver);
  - valor cobrado;
  - status "Cancelado" em card neutro, quando aplicável;
  - menu **⋮** com **Editar**, **Cancelar** e **Excluir**.
- **Horários livres**: bloco tracejado "Horário disponível — clique para agendar às HH:00", que abre o formulário com data e hora preenchidas.
- Agendamentos que atravessam mais de um bloco aparecem no bloco correspondente, sem sobreposição visual (empilhamento vertical quando necessário).
#### Painel lateral de métricas
| Card | Conteúdo |
|---|---|
| **Receita bruta da semana** | Soma dos valores dos serviços não cancelados na semana + total de serviços + horas reservadas + barra de progresso em relação à meta semanal de R$ 2.500,00 |
| **Resumo do dia** | Total de compromissos, horas reservadas, ticket médio e destaque **Próximo horário** (próximo evento futuro ou o primeiro do dia) |
| **Clientes mais atendidos** | Ranking dos três clientes com mais atendimentos na semana |
Todos os valores são recalculados dinamicamente em JavaScript a cada criação, edição, cancelamento, exclusão ou troca de semana.
---
### 5.3 Módulo 3 — Gestão de agendamentos (CRUD)
#### Criar novo agendamento
Formulário em **grid de duas colunas** com:
| Campo | Recurso |
|---|---|
| Nome completo do cliente | `datalist` com clientes cadastrados; autocompleta telefone/e-mail; cria cliente novo automaticamente |
| Telefone | Máscara dinâmica `(XX) XXXXX-XXXX` |
| E-mail | Validação de formato |
| Tipo de serviço | `<select>` populado pela API; preenche duração, término e valor |
| Data | Input `type="date"` |
| Início / Fim | Inputs `type="time"`; fim recalculado pela duração do serviço |
| Valor a cobrar | Máscara monetária em Real |
| Observações | Área de texto livre |
| Ações | Cancelar (volta à agenda) e **Confirmar agendamento** |
- Validação de campos obrigatórios com mensagens por campo.
- Alerta geral "Revise os campos destacados".
- Conflito de horário retornado pela API com HTTP 409 e mensagem contendo o intervalo conflitante.
- Feedback de sucesso em toast e retorno à semana/data do novo compromisso.
#### Editar / reagendar
- Estrutura dividida:
  - **esquerda:** resumo visual do agendamento (avatar, cliente, telefone, serviço, data, horário, duração, valor e status) + **zona de risco** com Cancelar agendamento e Excluir;
  - **direita:** formulário com clientes, serviço, data, início, fim, valor e observações + botão **Salvar alterações**.
- Salvar atualiza o registro na API, recalcula as métricas e exibe toast de sucesso.
- Cancelar altera o status para `cancelado`, mantém o histórico e libera o horário na agenda.
#### Excluir / cancelar com confirmação
- **Modal de exclusão** com fundo escurecido (`rgba` + `backdrop-filter: blur`), título "Excluir agendamento?" e aviso em destaque de que a ação é irreversível.
- Dois botões claros: **Cancelar** (neutro) e **Excluir** (vermelho).
- O DELETE só é disparado após o clique no botão vermelho.
- **Modal de cancelamento** separado, com aviso de que o horário será liberado e a ação não conta mais na receita.
- Exclusão de cliente e de serviço também possuem modal próprio de confirmação.
#### Lista completa de compromissos
- Tabela com data, horário, cliente, serviço, valor, status e ações.
- Busca global aplicada em tempo real.
- Total de registros e soma em serviços ativos.
- Ações rápidas de editar e excluir por linha.
---
### 5.4 Módulo 4A — Clientes
- Listagem em **cards** com avatar de iniciais, nome, telefone, e-mail, observações e total de atendimentos.
- **Busca por nome, telefone ou e-mail** com debounce de 220 ms — sem recarregar a página nem perder o foco.
- Modal de **novo cliente / editar cliente** com validação de nome, telefone (DDD + número) e e-mail.
- Modal de **histórico de atendimentos** com total de atendimentos, total investido e tabela de serviços por data.
- Exclusão com confirmação; agendamentos antigos permanecem no histórico.
- Contador de clientes cadastrados.
---
### 5.5 Módulo 4B — Serviços
- Catálogo em tabela com nome, categoria, duração (minutos e horas) e preço padrão.
- Modal de cadastro/edição com validação de nome, duração mínima de 15 minutos e preço maior que zero.
- **Resumo do catálogo**: serviços ativos, duração média e ticket médio.
- Os dados alimentam automaticamente o formulário de agendamento e o cálculo da receita semanal.
- Exclusão permitida apenas para serviços sem agendamentos vinculados (HTTP 409 caso contrário, com mensagem explicativa).
- Categorias disponíveis: Tatuagem, Eletricista, Bartender, Esteticista e Geral.
---
### 5.6 Módulo 5 — Barra de acessibilidade
Componente **flutuante e fixo**, presente em todas as telas:
| Controle | Função |
|---|---|
| **A+** | Aumenta a fonte um nível |
| **A−** | Diminui a fonte um nível |
| **A** | Restaura o padrão de 16 px |
| **Ícone (i)** | Abre modal explicando os recursos de acessibilidade |
**Como funciona tecnicamente**
- Toda a tipografia e os espaçamentos usam `rem`.
- O A+ / A− altera **somente** `document.documentElement.style.fontSize`.
- Escala de 6 níveis: `16 → 17,5 → 19 → 20,5 → 22 → 23,5 px`.
- A preferência é gravada em `localStorage` na chave `pa_font_size` e reaplicada no carregamento (`initFontSize`).
- A alteração é anunciada em região `aria-live` ("Tamanho da fonte ajustado para X por cento").
- O layout não quebra em telas pequenas, pois o grid é fluido e os cards reorganizam-se verticalmente.
- Em mobile, a barra reposiciona-se acima da bottom navigation.
---
## 6. Design system
### Paleta de cores
| Token | Valor | Aplicação |
|---|---|---|
| `--pa-primary` | `#2563EB` | Ações principais, abas/dias ativos, cards de agendamento |
| `--pa-primary-dark` | `#1D4ED8` | Hover e ênfase |
| `--pa-primary-light` | `#3B82F6` | Gradiente dos cards |
| `--pa-primary-soft` | `#DBEAFE` | Fundos suaves, itens ativos da navegação |
| `--pa-bg` | `#F3F4F6` | Fundo da aplicação |
| `--pa-surface` | `#FFFFFF` | Cards, painéis, sidebar |
| `--pa-text` | `#0F172A` | Texto principal (alto contraste) |
| `--pa-text-soft` | `#334155` | Texto secundário |
| `--pa-muted` | `#64748B` | Texto de apoio |
| `--pa-border` | `#E2E8F0` | Bordas discretas |
| `--pa-danger` | `#DC2626` | Erros, exclusão, cancelamento |
| `--pa-success` | `#16A34A` | Avatares, confirmações, sucesso |
| `--pa-warning` | `#D97706` | Avisos |
### Tipografia
- Fonte principal: **Inter** (fallback `Segoe UI`, `system-ui`, `Helvetica Neue`, sans-serif).
- Escala em `rem`, pesos 400/500/600/700/800.
- Linha de base 1,55; títulos com `letter-spacing` negativo para leitura compacta.
- Classes utilitárias: `.t-xs`, `.t-sm`, `.t-base`, `.t-lg`, `.t-xl`, `.t-2xl`, `.muted`, `.soft`, `.bold`, `.semi`.
### Formas e elevação
- `border-radius`: 12 px (`0.75rem`) em cards e painéis; 8 px em inputs/botões; 16 px em modais e cards de autenticação.
- Sombras suaves e em camadas: `0 1px 2px` (repouso), `0 6px 18px` (cards), `0 24px 48px` (modais).
- Sidebar com largura fixa de `15.5rem`; topbar com fundo translúcido e `backdrop-filter: blur(10px)`.
### Componentes
Botões (primário, outline, soft, danger, ghost, block, sm), inputs com estados `focus`/`error`/`disabled`, selects, textareas, mensagens de erro inline, alertas (danger, success, info, warning), badges, avatares, cards de métrica, steps, toasts, dropdowns, modais, timeline e tabelas.
---
## 7. Responsividade
| Breakpoint | Comportamento |
|---|---|
| **> 1100 px** | Dashboard em 2 colunas (timeline + painel lateral); edição em 2 colunas |
| **≤ 1100 px** | Painel de métricas desce para baixo da timeline; edição empilha resumo e formulário |
| **≤ 900 px** | Painel institucional da autenticação é ocultado; formulário ocupa a tela |
| **≤ 768 px** | Sidebar vira drawer; bottom navigation fixa; topbar compacta; busca ocupa a linha; barra A11y sobe para não colidir; botões "Novo compromisso" e textos auxiliares ocultados; formulários em 1 coluna; faixa de dias compacta; toasts ocupam a largura |
| **≤ 420 px** | Inputs OTP e espaçamentos reduzidos para caber sem rolagem horizontal |
Não há rolagem horizontal indevida em nenhuma tela; as únicas rolagens internas são as da sidebar e das tabelas (`.table-wrap`).
---
## 8. Regras de negócio e validações
### Validações de formulário
| Regra | Implementação |
|---|---|
| E-mail | Regex `^[^\s@]+@[^\s@]+\.[a-zA-Z]{2,}$` |
| Telefone | Pelo menos 10 dígitos; máscara `(XX) XXXXX-XXXX` |
| Senha | Mínimo de 6 caracteres |
| Confirmação de senha | Igualdade com o primeiro campo |
| OTP | Exatamente 4 dígitos; validade de 5 minutos |
| Horário | Término obrigatoriamente posterior ao início |
| Valor | Maior que zero; aceita vírgula ou ponto decimal |
| Duração do serviço | Mínimo de 15 minutos |
| Campos vazios | Mensagem específica abaixo de cada campo e destaque visual |
| Conflito de horário | Sobreposição com outro agendamento não cancelado na mesma data bloqueia o salvamento |
### Cálculos
```
Duração de um agendamento (min) = fim - início
Receita bruta da semana        = Σ preço dos agendamentos não cancelados da semana
Horas reservadas               = Σ durações / 60
Ticket médio do dia            = receita do dia / quantidade de agendamentos ativos
Horas da semana                = segunda-feira até sábado (6 dias)
```
### Regras adicionais
- Cancelados **não contam** receita, horas reservadas, contagem de compromissos ativos e ranking de clientes.
- Um agendamento novo pode sobrescrever visualmente um horário já ocupado apenas se não houver sobreposição real; conflitos são bloqueados.
- Cliente digitado no formulário que já existe na base é reutilizado (comparação por nome, sem diferenciar maiúsculas).
- Cliente novo é criado automaticamente com observação "Cadastrado via agendamento".
- Alterar o início recalcula o término conforme a duração do serviço.
- Alterar o serviço recalcula término e valor apenas se o valor ainda não tiver sido editado manualmente.
- Serviço vinculado a agendamentos não pode ser excluído.
- Excluir cliente preserva os agendamentos no histórico (apenas desvincula o cadastro).
- A sessão persiste até o logout ou limpeza do armazenamento.
### Máscaras em JavaScript
```js
maskPhone('11988124477') // → (11) 98812-4477
maskMoney('450.5')      // → 450,50
parseMoney('1.250,00')  // → 1250
```
---
## 9. Acessibilidade
Recursos implementados (alinhados à WCAG 2.1 e ao requisito do Projeto Integrador):
1. **Fonte escalável em `rem`** — A+, A− e restaurar, com persistência (`pa_font_size`) e limite seguro de 6 níveis.
2. **Alto contraste** — texto principal `#0F172A` sobre branco; azul `#2563EB` sobre branco para ações.
3. **Rótulos ARIA** em todos os botões de ícone (`aria-label`), navegação (`aria-label`), toolbar, modais (`role="dialog"`, `aria-modal`, `aria-label`).
4. **Estados anunciados** — `aria-live="polite"` em toasts e nas mudanças de fonte; `aria-expanded` em menus; `aria-pressed` nos dias da semana; `aria-current="page"` no item ativo.
5. **Navegação por teclado** — todos os controles são botões/links reais; ordem de tabulação natural; `Ctrl+K`/`⌘K` foca a busca; `Esc` fecha modais, menus e dropdowns.
6. **Foco visível** — `:focus-visible` com contorno azul de 2 px e offset.
7. **Foco automático contextual** — primeiro campo do login, primeiro input OTP, primeiro campo da nova senha e primeiro elemento acionável de cada modal.
8. **OTP acessível** — inputs numéricos com `inputmode="numeric"`, `autocomplete="one-time-code"` e rótulos "Dígito 1…4"; foco salta ao digitar e retorna com Backspace.
9. **Mensagens de erro associadas** ao campo correspondente via bloco de erro exibido logo abaixo e classe de erro no input.
10. **Movimento controlado** — animações curtas (fade/pop/slide) sem elementos piscando.
11. **Leitores de tela** — ícones decorativos com `aria-hidden="true"`; conteúdo textual real em todos os cards e tabelas.
12. **Sem dependência de hover** — todas as ações também são acessíveis por toque e teclado.
---
## 10. Arquitetura e organização do código
A entrega é **single-file** (`index.html`), mas o código é organizado em seções comentadas equivalentes à estrutura de pastas sugerida no projeto:
| Seção no `index.html` | Arquivo sugerido no projeto | Responsabilidade |
|---|---|---|
| Utilitários gerais | `js/utils.js` | Datas, moeda, escaping, IDs, navegação de semana |
| Validators | `js/validators.js` | Regex, máscaras de telefone/dinheiro, parsing |
| Mock server + cliente HTTP | `js/api.js` | Rotas REST, latência, token, erros HTTP |
| Sessão | `js/auth.js` | Login, cadastro, recuperação, OTP, guarda de rotas |
| Estado global | `js/state.js` | Rota, semana, dia, busca, edição, contadores |
| Toasts e modais | `js/components.js` | Feedback e diálogos acessíveis |
| Acessibilidade | `js/accessibility.js` | A+/A−/A e `localStorage` |
| Ícones | `assets/img/*.svg` | Biblioteca SVG inline |
| Shell / Views / CRUD | `js/agenda.js`, `js/clientes.js`, `js/servicos.js` | Renderização e eventos |
| Design system | `css/variables.css`, `css/reset.css`, `css/layout.css`, `css/components.css`, `css/pages/*` | Todos os estilos estão no `<style>` inicial |
### Padrões técnicos
- **Async/await** com `fetch` centralizado (`apiRequest`); em modo mock, resolve via roteador interno com latência simulada de 180–400 ms.
- **Delegação de eventos** no `document` (`click`, `input`, `change`, `submit`, `keydown`) — os elementos renderizados dinamicamente já funcionam sem nova ligação.
- **Renderização por template string** com escaping (`esc()`) de todo dado vindo do usuário.
- **Roteador SPA** por hash com `handleRoute()` e guarda de autenticação.
- **Debounce** na busca de clientes para evitar recarregamentos excessivos.
- **Nenhum `innerHTML` com conteúdo não escapado** em campos de texto do usuário.
---
## 11. Modelo de dados
```js
// Agendamento
{
  id: "ag_xxx",
  date: "2026-02-16",        // ISO yyyy-mm-dd
  start: "09:00",
  end: "11:00",
  clientId: "c1",
  clientName: "",            // usado quando o cliente não está no cadastro
  serviceId: "s1",
  price: 450,
  status: "confirmado",      // confirmado | cancelado
  notes: "Projeto floral no antebraço."
}
// Cliente
{
  id: "c1",
  name: "Mariana Alves",
  phone: "(11) 98812-4477",
  email: "mariana.alves@email.com",
  notes: "Prefere atendimento no período da tarde."
}
// Serviço
{
  id: "s1",
  name: "Tatuagem personalizada",
  duration: 120,             // minutos
  price: 450,
  category: "Tatuagem"
}
// Usuário (mock local — em produção nunca armazenar senha em texto puro)
{
  id: "u1",
  name: "Ana Ribeiro",
  role: "Tatuadora & Piercer",
  email: "ana@proagenda.com",
  password: "123456"
}
```
### Relacionamentos
```
Cliente 1 ─── N Agendamento N ─── 1 Serviço
```
---
## 12. Persistência e dados de exemplo
| Chave | Conteúdo | Formato |
|---|---|---|
| `pa_db_v1` | Serviços, clientes, agendamentos e usuários | JSON (`localStorage`) |
| `pa_session_v1` | Token + dados do usuário logado | JSON (`localStorage`) |
| `pa_font_size` | Tamanho da fonte em px | string numérica (`localStorage`) |
### Seed inicial
- **8 serviços:** Tatuagem personalizada, Piercing, Manutenção elétrica, Instalação de chuveiro, Bartender para evento, Limpeza de pele, Design de sobrancelha, Massagem relaxante.
- **6 clientes:** Mariana Alves, Rafael Nogueira, Juliana Prado, Diego Ferraz, Camila Bastos e Bruno Tavares.
- **12 agendamentos** distribuídos entre a semana atual e as duas semanas seguintes, com valores de R$ 70 a R$ 450, incluindo um bartender de 3 horas.
### Como restaurar a demonstração
Abra o console do navegador (F12) e execute:
```js
localStorage.removeItem('pa_db_v1');    // apaga a base simulada
localStorage.removeItem('pa_session_v1');// encerra a sessão
location.reload();                       // recria o seed e volta ao login
```
Para apenas sair da conta, use **Sair da conta** na sidebar ou no menu do usuário.
---
## 13. Integração com back-end Node.js
O arquivo possui uma camada de API pronta para produção, localizada no início do bloco de Mock Server:
```js
const API_BASE = '';      // ex.: 'http://localhost:3000'
const USE_MOCK  = true;   // trocar para false para usar o back-end real
```
Com `USE_MOCK = false`, todas as operações chamam `fetch(API_BASE + path)` enviando `Content-Type: application/json` e o cabeçalho `Authorization: Bearer <token>`. Respostas não-2xx viram exceções com `status` e `message`, exibidas nos formulários/toasts.
### Compatibilidade de payload
- Datas em **ISO `yyyy-mm-dd`** (inputs nativos).
- Horários em **`HH:MM`** (24 h).
- Preços em **número** (não string formatada).
- IDs de clientes e serviços referenciados por `clientId` e `serviceId`.
### Recomendações de implementação Node.js
- **Express** + rotas REST idênticas às documentadas na seção 14;
- **JWT** no login, com middleware de autenticação;
- **bcrypt/argon2** para hash de senhas — nunca armazenar texto puro;
- **PostgreSQL/MySQL + Prisma/Sequelize** para persistência;
- **Zod/Joi** para validação no servidor (nunca confiar só no front);
- **Nodemailer + serviço SMTP** para envio real do código OTP (com expiração e rate limit);
- **CORS** liberando a origem do front-end;
- **Rate limiting** em `/api/login` e nas rotas de recuperação;
- Migração do token de `localStorage` para cookie `httpOnly` quando houver back-end (mitiga XSS);
- Validação transacional de conflito de horário no banco para concorrência.
---
## 14. Contrato da API
### Autenticação
| Método | Rota | Corpo | Sucesso | Erros |
|---|---|---|---|---|
| POST | `/api/login` | `{ email, password }` | `200 { token, user }` | `401` e-mail não encontrado / senha incorreta |
| POST | `/api/auth/register` | `{ name, email, phone, role, password }` | `201 { token, user }` | `400` validação / `409` e-mail já cadastrado |
| POST | `/api/auth/reset-code` | `{ email }` | `200 { sent, expiresIn, demoCode }` | `400` e-mail inválido |
| POST | `/api/auth/verify-code` | `{ email, code }` | `200 { valid:true }` | `400` código inválido/expirado |
| POST | `/api/auth/reset-password` | `{ email, password, confirmation }` | `200 { updated:true }` | `400` senha curta / senhas diferentes |
### Agendamentos
| Método | Rota | Parâmetros / Corpo | Sucesso |
|---|---|---|---|
| GET | `/api/agendamentos` | Query `inicio`, `fim` (`yyyy-mm-dd`, opcionais) | Lista enriquecida com `client` e `service` |
| POST | `/api/agendamentos` | `{ clientId, clientName, serviceId, date, start, end, price, notes }` | `201` agendamento criado |
| PUT | `/api/agendamentos/:id` | Mesmos campos + `status` | `200` agendamento atualizado |
| PATCH | `/api/agendamentos/:id/status` | `{ status: "confirmado" \| "cancelado" }` | `200` status atualizado |
| DELETE | `/api/agendamentos/:id` | — | `200 { deleted:true }` |
Erros comuns: `404` não encontrado; `409` conflito de horário com mensagem contendo o intervalo ocupado.
### Clientes
| Método | Rota | Corpo | Sucesso |
|---|---|---|---|
| GET | `/api/clientes` | Query `q` (busca) | Lista + `total` de atendimentos + `last` |
| POST | `/api/clientes` | `{ name, phone, email, notes }` | `201` cliente criado |
| PUT | `/api/clientes/:id` | `{ name, phone, email, notes }` | `200` cliente atualizado |
| DELETE | `/api/clientes/:id` | — | `200 { deleted:true }` |
### Serviços
| Método | Rota | Corpo | Sucesso |
|---|---|---|---|
| GET | `/api/servicos` | — | Lista de serviços |
| POST | `/api/servicos` | `{ name, duration, price, category }` | `201` serviço criado |
| PUT | `/api/servicos/:id` | `{ name, duration, price, category }` | `200` serviço atualizado |
| DELETE | `/api/servicos/:id` | — | `200 { deleted:true }`; `409` se houver agendamentos vinculados |
### Exemplo de chamada
```js
const res = await fetch('http://localhost:3000/api/agendamentos?inicio=2026-02-16&fim=2026-02-21', {
  headers: { Authorization: `Bearer ${token}` }
});
if (!res.ok) throw new Error((await res.json()).message);
const agendamentos = await res.json();
```
---
## 15. Fluxos de uso passo a passo
### Fluxo A — Criar um agendamento
1. Faça login com `ana@proagenda.com` / `123456`.
2. No dashboard, clique em um **horário disponível** (a data e a hora já vêm preenchidas) ou em **+ Novo compromisso**.
3. Digite o nome do cliente — se já existir, telefone e e-mail são preenchidos.
4. Escolha o serviço: duração, término e valor são calculados.
5. Ajuste data, horários, valor e observações, se necessário.
6. Clique em **Confirmar agendamento**.
7. O sistema valida os campos, verifica conflito, cria (ou reaproveita) o cliente, salva o agendamento e retorna à agenda da data.
8. O card aparece na timeline e a receita/contadores são atualizados.
### Fluxo B — Reagendar
1. Clique no **nome do cliente** no card (ou em ⋮ → Editar, ou na lista de Compromissos).
2. Na tela de gerenciamento, confira o resumo à esquerda.
3. Altere data/horário/serviço e clique em **Salvar alterações**.
4. Em caso de conflito, uma mensagem explica o intervalo ocupado e nada é gravado.
### Fluxo C — Cancelar ou excluir
1. No card, abra o menu **⋮**.
2. **Cancelar** → modal de aviso → confirmar. O card fica neutro, o horário é liberado e o valor sai da receita.
3. **Excluir** → modal "Excluir agendamento?" com aviso irreversível → **Excluir** remove definitivamente.
### Fluxo D — Recuperar a senha
1. Em `#/login`, clique em **Esqueci minha senha**.
2. Informe o e-mail e envie o código.
3. Copie o código exibido no toast de demonstração e digite os 4 dígitos nos quadrados (o foco avança sozinho).
4. Se precisar, aguarde 30 s e clique em **Reenviar agora**.
5. Defina e confirme a nova senha; faça login novamente.
### Fluxo E — Aumentar a fonte
1. Localize a barra flutuante **A− | A | A+**.
2. Clique em **A+** até o tamanho desejado.
3. Navegue entre as telas — o tamanho escolhido permanece.
4. Clique em **A** para voltar ao padrão.
### Fluxo F — Cadastrar cliente/serviço
1. Acesse **Clientes** (ou **Serviços**) na sidebar/bottom nav.
2. Clique em **Novo cliente** / **Novo serviço**.
3. Preencha o modal e salve. Os dados ficam imediatamente disponíveis no formulário de agendamento.
4. A edição reutiliza o mesmo modal; a exclusão pede confirmação.
---
## 16. Checklist de testes
### Autenticação
- [ ] Login com e-mail/senha corretos redireciona ao dashboard.
- [ ] Senha errada exibe borda vermelha e mensagem abaixo do campo.
- [ ] E-mail em formato inválido é bloqueado antes da chamada à API.
- [ ] Olho alterna a visibilidade da senha e o rótulo ARIA.
- [ ] Cadastro valida e-mail duplicado e senha curta.
- [ ] Recuperação: OTP avança o foco, apaga com Backspace, valida cronômetro e reenvio.
- [ ] Nova senha divergente exibe "As senhas não conferem".
- [ ] Logout limpa a sessão e volta ao login.
### Agenda
- [ ] Dias exibem contagem correta de compromissos.
- [ ] Setas navegam semanas e mantêm a faixa de datas coerente em viradas de mês/ano.
- [ ] "Hoje" retorna à semana atual e seleciona o dia atual.
- [ ] Horários vagos abrem formulário com data/hora corretas.
- [ ] Receita da semana considera apenas ativos e atualiza após criar/editar/cancelar/excluir.
- [ ] Resumo do dia mostra total, horas e próximo horário.
- [ ] Busca filtra a timeline e a lista de compromissos, sem perder o foco.
- [ ] Notificações e menu do usuário abrem/fecham ao clique e com `Esc`.
### CRUD
- [ ] Criação com serviço válido calcula término e valor.
- [ ] Conflito de horário é bloqueado com mensagem clara.
- [ ] Edição salva alterações e reflete no dashboard.
- [ ] Modal de exclusão: Cancelar preserva; Excluir remove.
- [ ] Cancelar mantém o registro e libera o horário.
- [ ] Cliente novo é criado e reaproveitado em novos agendamentos.
- [ ] Serviço vinculado não pode ser excluído.
- [ ] Exclusão de cliente pede confirmação.
### Acessibilidade e responsividade
- [ ] A+ aumenta proporcionalmente todo o layout sem quebrar.
- [ ] Preferência de fonte persiste após recarregar.
- [ ] A restaura 16 px e é salvo.
- [ ] Navegação completa por Tab; foco sempre visível.
- [ ] `Esc` fecha modais/menus; `Ctrl+K` foca a busca.
- [ ] Em 768 px, sidebar vira drawer e a bottom nav aparece.
- [ ] Em 390 px, nenhuma tela tem rolagem horizontal.
- [ ] Leitores de tela anunciam toasts e mudanças de fonte.
---
## 17. Limitações conhecidas
| Limitação | Impacto / observação |
|---|---|
| **Somente front-end** | A autenticação e os dados são simulados; não há servidor real nesta entrega |
| **Senhas em texto puro no mock** | Aceitável apenas para demonstração local; nunca usar em produção |
| **Token sem validação real** | O mock não verifica expiração nem assinatura JWT |
| **OTP exibido no toast** | Recurso didático; em produção deve ir apenas por e-mail/SMS |
| **Dados no navegador** | Limpar o armazenamento apaga tudo; não há sincronização entre dispositivos |
| **Agenda de segunda a sábado** | Domingos não aparecem na faixa semanal (conforme protótipo) |
| **Horário local** | Não há suporte a fusos horários ou múltiplos profissionais |
| **Sem upload de imagens** | Avatares são gerados por iniciais |
| **Login social visual** | Botões de Google/Apple/Facebook ainda não integram OAuth |
| **Sem notificações reais** | O painel é informativo; não há push, e-mail ou WhatsApp |
| **Sem paginação** | As listagens carregam todos os registros do mock |
| **Idioma único** | Interface e dados de exemplo em português do Brasil |
---
## 18. Próximos passos e roadmap
### Curto prazo
- Back-end Express + banco relacional, JWT e hash de senha.
- Envio real de OTP por e-mail com expiração e rate limit.
- Paginação e ordenação das listagens.
- Testes automatizados (Vitest/Jest + Playwright) e validação de HTML/contraste.
### Médio prazo
- Confirmação e lembrete automático por **WhatsApp/e-mail**.
- Integração com **Google Calendar** (importar/exportar).
- Relatórios de receita por período, serviço e cliente (exportação CSV/PDF).
- Perfil editável, foto do profissional e horários de funcionamento configuráveis.
- Suporte a domingo e bloqueio de feriados/folgas.
### Longo prazo
- Multi-profissional por estabelecimento, com papéis e permissões.
- Agendamento público por link (cliente escolhe horário livre).
- Pagamentos e sinal via **Pix/cartão**.
- Conversão em **PWA** com instalação e modo offline.
- Conformidade **LGPD** (consentimento, exclusão de dados, auditoria).
---
## 19. Navegadores suportados
- **Chrome / Edge** 100+
- **Firefox** 100+
- **Safari** 15.4+
- Tablets e celulares Android/iOS modernos
Recursos utilizados: CSS Grid, Flexbox, custom properties, `backdrop-filter` (com degradê de fallback), `datalist`, inputs nativos `date`/`time`, `localStorage`, `URLSearchParams`, `fetch` e optional chaining.
---
## 20. Créditos e licença
- **Projeto:** ProAgenda — Projeto Integrador
- **Contexto acadêmico:** UNIVESP
- **Escopo:** front-end em HTML5, CSS3 e JavaScript puro, alinhado aos protótipos e requisitos do relatório, com adaptação para as áreas de tatuagem, elétrica, bartender e estética.
- **Tipografia:** Inter (SIL Open Font License) via Google Fonts.
- **Uso:** acadêmico e educacional. A marca, os dados de exemplo e os textos são fictícios.
---
### Suporte e manutenção
Toda a configuração sensível está concentrada no topo do bloco JavaScript de `index.html`:
```js
const API_BASE = '';      // URL do back-end quando USE_MOCK = false
const USE_MOCK  = true;   // alterna entre demonstração e API real
const DB_KEY    = 'pa_db_v1';
const SESSION_KEY = 'pa_session_v1';
const FONT_KEY  = 'pa_font_size';
```
Para adicionar uma nova rota, crie a tela (função `viewAlgumaCoisa`), registre o `case` em `handleRoute()` e inclua o link na sidebar/bottom navigation. Para um novo endpoint, adicione a rota no objeto `mockServer` e crie o método correspondente em `api`.
> **ProAgenda** — menos caderno, mais agenda. Sua rotina organizada, do primeiro horário ao último café.
