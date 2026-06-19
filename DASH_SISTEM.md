# DASH_SISTEM – TheusMarkt
> Documento de base do projeto. Ao solicitar melhorias, não é necessário re-explicar a estrutura — apenas descreva o que quer adicionar ou corrigir.

---

## 🧠 Visão Geral

**TheusMarkt** — Plataforma web de inteligência em tráfego pago.  
Deploy: `https://theusmarkt.netlify.app`  
**Versão atual: v4.1**

---

## 🔐 Acesso

- **Senha:** `M@110388`
- Validação: `doLogin()` em `app.js`

---

## 📁 Arquivos

```
TheusMarkt/
├── index.html                  → HTML completo
├── style.css                   → Estilos dark + light v4.0
├── app.js                      → Toda a lógica JS
├── Logo_TheusMarkt.png         → Logo branca (tema escuro)
├── Logo_TheusMarkt_Preto.png   → Logo dourada/preta (tema claro)
└── DASH_SISTEM.md              → Este arquivo
```

---

## 🗂️ Páginas (v4.0)

| Página | ID | Mudanças v4.0 |
|---|---|---|
| Dashboard | `page-dashboard` | + Filtro de data calendário com botão Aplicar · PDF com gráficos via html2canvas · Top 5 ads com thumbnail na tabela |
| Campanhas | `page-campaigns` | Botão Manual removido · Botão Aplicar no date picker · Top 15 ads/campanha · Cidades nas localizações |
| Melhorias | `page-improvements` | Badge IA na sidebar |
| Análise com IA | `page-aianalysis` | — |
| Pesquisa de Mercado | `page-market` | Badge IA na sidebar |
| Contas & Tokens | `page-accounts` | Botão Editar por conta · `updateAccount()` · Developer Token visível |
| **Atualizações** | `page-schedule` | Renomeado de "Agendamento" · + slot 18:00 |

**Removido:** página Top Anúncios (`page-topads`) — ocultada, não aparece na nav

---

## 👤 Perfil na Sidebar (v4.0)

```html
<div class="sb-user-card">
  <div class="sb-user-avatar">M</div>
  <div class="sb-user-info">
    <div class="sb-user-name">Matheus Muniz</div>
    <div class="sb-user-role">Marketing Estratégico</div>
  </div>
</div>
```
- Aparece no rodapé da sidebar, acima do botão Sair
- Oculto quando sidebar está colapsada
- Avatar dourado com inicial "M"

---

## 📊 Dashboard — Filtro de Data (v4.0)

- Inputs: `#dashDateFrom` e `#dashDateTo` (tipo `date`)
- Botão: "✓ Aplicar" → `applyDashboardDate()`
- Atualiza todos os KPIs, gráficos e tabela ao aplicar
- Sincroniza com o select `#fDate` com o período mais próximo
- Inicializado com os últimos 30 dias via `initDashboardDates()`
- State: `dashDateFrom` e `dashDateTo` (vars JS, não persistidas)

---

## 📊 Dashboard — Top 5 Ads na Tabela (v4.0)

- `toggleAdsPanel(campId)` agora é `async`
- Mostra loading spinner enquanto busca thumbnails
- Chama `enrichAdsWithThumbnails(ads, token)` para campanhas Meta
- Exibe thumbnail real ou emoji fallback por posição

---

## 📄 PDF Export (v4.0)

- Função: `exportPDF()`
- **Orientação:** portrait A4 (mudou de landscape)
- **Gráficos:** capturados via `html2canvas` dos elementos `.charts-row-a .chart-box` e `.charts-row-b .chart-box`
- **Conteúdo:** Logo + KPIs linha 1 + KPIs linha 2 + até 6 gráficos (grid 3×2) + tabela de campanhas
- **Tema:** detecta `body.light` e ajusta cores
- **Footer:** "Matheus Muniz Marketing Estratégico · theusmarkt.netlify.app" em todas as páginas
- **Paginação:** automática com número de página
- Arquivo: `TheusMarkt_Dashboard_DD-MM-YYYY.pdf`

---

## 🗂️ Campanhas — Mudanças v4.0

- **Botão "+ Manual" removido** do topo
- **Botão "✓ Aplicar"** adicionado ao date picker → chama `applyDateRange()`
- **Top 15 anúncios** por campanha (era 5) em `toggleCampAds()`
- **Thumbnails reais** via Meta API com loading state e fallback por emoji
- **Localizações:** mostra cidades reais se `campaign.locationBreakdown` disponível, senão top cidades BR com nota "estimativa"

---

## ⏰ Atualizações (ex-Agendamento) — v4.0

- Título renomeado para "Atualizações Automáticas"
- **3 horários:** 08:00 · 12:00 · **18:00** (novo)
- Elemento HTML: `#nextEvening` para 18:00
- `checkScheduledUpdates()` verifica `sched_DATE_18`

---

## 💾 State (`localStorage: theusmarkt_v2`)

```js
state = {
  accounts:     [],  // { id, name, platform, token, accId, active, isDemo, lastSync, developerToken? }
  campaigns:    [],  // { id, name, accountId, objective, status, budget,
                     //   spend, revenue, clicks, impressions, conversions,
                     //   reach, frequency, ctr, platform, source, lastSync,
                     //   genderBreakdown?, ageBreakdown?, locationBreakdown? }
  ads:          [],  // { id, campaignId, name, spend, revenue, clicks,
                     //   impressions, conversions, frequency, thumbUrl, source }
  competitors:  [],
  updateLog:    [],
  aiAnalyses:   {},
  anthropicKey: '',
  charts:       {}
}
```

**Tema:** `localStorage('theusmarkt_theme')` → `'dark'` | `'light'`

---

## 🌗 Tema e Logo

- Botão sidebar footer: ☀️ / 🌙
- Dark → `Logo_TheusMarkt.png`, Light → `Logo_TheusMarkt_Preto.png`
- IDs: `#loginLogo`, `#sidebarLogo`
- Funções: `applyTheme(t)`, `toggleTheme()`

---

## 📡 APIs

| Plataforma | Função | Notas |
|---|---|---|
| Meta Ads v21.0 | `syncMetaAccount(acc)` | Token + act_XXXXX · CORS registrado |
| Google Ads v17 | `syncGoogleAccount(acc)` | OAuth2 + Developer Token obrigatório |
| TikTok Ads v1.3 | `syncTikTokAccount(acc)` | Access Token + Advertiser ID |
| Meta Demographics | `fetchMetaDemographics()` | gender + age breakdowns |
| Meta Thumbnails | `enrichAdsWithThumbnails()` | `creative{thumbnail_url}` |

---

## 🔧 Funções Principais

| Função | O que faz |
|---|---|
| `initApp()` | Init + carrega tema + remove DEMOs |
| `applyDashboardDate()` | Aplica filtro de data no dashboard |
| `initDashboardDates()` | Inicializa inputs de data |
| `renderDashboard()` | KPIs + gráficos + tabela |
| `toggleAdsPanel(campId)` | Async, loading, thumbnails, top 5 |
| `renderCampaigns()` | Página completa de campanhas |
| `toggleCampAds(campId)` | Top 15 ads com thumbnails |
| `renderTopLocations(camps)` | Cidades reais ou estimativa |
| `exportPDF()` | PDF portrait A4 com gráficos html2canvas |
| `renderSchedule()` | 08:00 + 12:00 + 18:00 |
| `checkScheduledUpdates()` | Verifica 3 slots por dia |
| `showSyncError(id,msg,plat)` | Erro por plataforma com dicas específicas |
| `openEditAccount(id)` | Pré-preenche modal para editar conta |
| `updateAccount(id)` | Salva edição sem criar conta nova |

---

## 🎨 Design System

| Token | Dark | Light |
|---|---|---|
| `--bg` | `#0a0d14` | `#f0f2f7` |
| `--bg2` | `#10141f` | `#ffffff` |
| `--bg3` | `#161b2a` | `#f4f6fb` |
| `--accent` | `#f0c040` | `#f0c040` |
| `--purple` | `#a78bfa` | `#8b5cf6` |
| Título | `Syne 700/800` | idem |
| Corpo | `DM Sans 400/500` | idem |

---

## 📋 Histórico de Versões

| Versão | Data | Mudanças |
|---|---|---|
| v1.0 | Mai/2026 | Dashboard inicial |
| v2.0 | Mai/2026 | Meta Ads API + IA Claude |
| v2.1 | Mai/2026 | Correção erro #100 + diagnóstico |
| v3.0 | Mai/2026 | Logo · 10 KPIs · Painel ads · PDF |
| v3.1 | Mai/2026 | Tema claro/escuro |
| v3.2 | Mai/2026 | Gráficos Gênero + Faixa Etária |
| v3.3 | Mai/2026 | Google Ads + TikTok Ads API |
| v3.4 | Mai/2026 | Logo dupla por tema |
| v3.5 | Mai/2026 | Página Campanhas completa + thumbnails |
| v3.6 | Mai/2026 | Erro sync por plataforma + botão Editar conta |
| **v4.0** | **Mai/2026** | Dashboard: filtro de data · PDF com gráficos · thumbnails · Campanhas: sem botão manual · top 15 ads · cidades · Sidebar: perfil · badge IA · Atualizações: 18h · Top Anúncios removido |
| **v4.1** | **Jun/2026** | Login versão v4.0 · Filtro 7/14/30/60/90 dias removido do sistema todo · `getActiveDays()` e `getActiveDateRange()` substituem fDate · Padrão: mês atual · Perfil sidebar removido · Toggle switch tema ☀️/🌙 deslizante · Cards de Atualizações compactos 3 em linha · Sidebar collapsed oculta toggle label |

---

## 📌 Pendências Futuras

- [ ] Dados reais de localização por cidade via Meta API (`region` breakdown)
- [ ] LinkedIn Ads API
- [ ] Alertas automáticos ROAS abaixo de threshold
- [ ] Comparativo real com período anterior (dados históricos)
- [ ] Importação CSV/XLSX

---

*Sempre atualize este arquivo ao adicionar funcionalidades.*  
*TheusMarkt v4.1 · Junho/2026*
