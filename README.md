# Parvia — landing page

Site estático (HTML/CSS/JS puro, sem build). Deploy na Vercel.

```
index.html          página principal
privacidade.html    → serve em /privacidade
termos.html         → serve em /termos
vercel.json         cleanUrls (tira o .html da URL) + headers de segurança e cache
sitemap.xml         URLs limpas
robots.txt
site.webmanifest
assets/             favicon, logo, og-image, apple-touch-icon
```

---

## 1. O que editar

Tudo que muda no dia a dia está em **um único bloco** no fim do `index.html`:

```js
var PARVIA = {
  whatsapp: "5511913307906",
  mensagem: "Oi! Vim pelo site e quero a auditoria gratuita...",
  prazo:    "15 minutos",
  pixel: "",     // ← ID do Meta Pixel
  ga4:   "",     // ← ID do GA4 (G-XXXXXXXXXX)
  contadores: { ligado: false, itens: [...] }
};
```

Cor do botão principal: as duas últimas variáveis do bloco `:root` no topo
do `index.html` (`--cta` e `--cta-dark`). Estão em vinho. Para testar verde
de WhatsApp, troque por `#128C7E` e `#0B6B60`.

---

## 2. Antes de subir o primeiro anúncio

- [ ] **Meta Pixel** — Gerenciador de Eventos → criar Pixel → copiar o ID (16 dígitos)
      → colar em `PARVIA.pixel`.
- [ ] **Verificar o domínio na Meta** — Business Manager → Segurança da marca →
      Domínios → adicionar `parvia.com.br` → método "meta-tag" → descomentar
      a linha `facebook-domain-verification` no `<head>` e colar o código.
      Sem isso você não configura os eventos priorizados e perde otimização no iOS.
- [ ] **Configurar eventos priorizados** (Aggregated Event Measurement) com
      `Lead` em primeiro lugar.
- [ ] **Objetivo da campanha**: "Cadastros" (Leads) → destino WhatsApp ou Site.
      No conjunto, otimize por `Lead`.
- [ ] **UTMs em todos os anúncios** (campo "parâmetros de URL"):
      ```
      utm_source=facebook&utm_medium=paid&utm_campaign={{campaign.name}}&utm_content={{ad.name}}
      ```
- [ ] **Search Console** — descomentar `google-site-verification`, ou verificar
      por DNS. Depois enviar `https://parvia.com.br/sitemap.xml`.
- [ ] **GA4** (opcional, mas ajuda a fechar a conta de CPL) — colar o ID em `PARVIA.ga4`.

---

## 3. Eventos que a página dispara sozinha

| Evento Meta   | Evento GA4       | Quando                                       |
|---------------|------------------|----------------------------------------------|
| `PageView`    | `page_view`      | ao abrir a página                            |
| `ViewContent` | `ver_exemplos`   | quando rola até a seção de exemplos          |
| `Lead`        | `generate_lead`  | em **todo** clique de WhatsApp                |

Cada botão manda também `source` (hero, exemplos, final, footer, flutuante),
então dá para saber qual bloco da página converte.

### Código de origem no WhatsApp

A página lê `utm_*`, `fbclid` e `gclid`, guarda a primeira visita da sessão e
anexa um código curto no fim da mensagem pré-preenchida:

```
Oi! Vim pelo site e quero a auditoria gratuita...  [facebook-audit-video-a-k3f9]
```

Esse código é a coluna **"origem"** da sua planilha, preenchida sozinha. Quando
o lead cai no WhatsApp você já sabe de qual anúncio veio, sem depender do
relatório da Meta.

---

## 4. Números na faixa escura

Os quatro números publicados hoje são verificáveis: três são compromissos de
atendimento (grátis, 15 min, 18 pontos) e o quarto é dado público de mercado.

Quando tiver histórico real, ligue os contadores:

```js
contadores: {
  ligado: true,
  itens: [
    { n: "112",     rot: "apólices auditadas" },
    { n: "R$ 1.340", rot: "economia média por ano" },
    { n: "15 min",  rot: "prazo de resposta" },
    { n: "R$ 0",    rot: "custo da auditoria" }
  ]
}
```

**Só com números reais.** Resultado próprio inventado é publicidade enganosa
(CDC, art. 37) e é uma das causas mais comuns de reprovação de conta no Meta Ads.

---

## 5. Deploy

A Vercel publica a cada `git push` na branch `main`. Nada para configurar:
`vercel.json` já cuida das URLs limpas.

Depois do primeiro deploy com `vercel.json`, force um refresh de cache
(Ctrl+Shift+R) — o redirecionamento de `/index.html` para `/` fica em cache
no navegador.
