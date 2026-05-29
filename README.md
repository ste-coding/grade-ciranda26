# CirandaMEJ'26 — Painel da Staff

Painel de grade de conteúdo do **CirandaMEJ'26**, para a staff acompanhar a programação pelo celular durante o evento. Site **estático** (HTML/CSS/JS puro, sem build) hospedado no **GitHub Pages**, com os dados da grade guardados no **JSONBin.io** — assim a edição grava direto no servidor, sem precisar commitar a cada mudança.

## 📁 Arquivos

| Arquivo | O que é |
|---|---|
| `index.html` | Painel principal (visão dos staffs) — mobile-first. Lê a grade do JSONBin. |
| `edit.html` | Editor da grade. Salva direto no JSONBin **e** baixa uma cópia de backup. |
| `data.json` | Grade inicial (semente) e backup. Não é mais a fonte de verdade no dia a dia. |
| `README.md` | Este guia. |

> **Como funciona agora:** a fonte de verdade da grade é o **bin no JSONBin**. O `index.html` lê dele; o `edit.html` grava nele. O `data.json` do repositório serve para (a) criar o bin pela primeira vez e (b) funcionar como fallback caso o bin esteja fora do ar.

---

## 🔑 Configurar o JSONBin (uma vez)

1. Crie uma conta gratuita em **https://jsonbin.io**.
2. **Crie o bin:** menu *Create Bin* → cole o conteúdo do `data.json` deste repositório → salve. Copie o **BIN ID** que aparece (algo como `665f0a...`).
3. **(Recomendado) Ative o versionamento** do bin nas opções dele — assim toda edição vira uma versão e você consegue voltar atrás se alguém salvar algo errado.
4. **Pegue as chaves** na página *API Keys*:
   - **X-Master-Key** — acesso total (leitura + escrita). Vai **só** no `edit.html`.
   - **X-Access-Key** — crie uma com permissão **somente de leitura** (*Bins Read*). Vai no `index.html` (que é o link público pra staff).
5. **Cole nos arquivos**, no bloco `CONFIG JSONBIN` no topo do `<script>`:
   - Em **`index.html`**: `BIN_ID` e `ACCESS_KEY`.
   - Em **`edit.html`**: `BIN_ID` e `MASTER_KEY`.
6. Faça commit dos dois arquivos já com as chaves e suba pro GitHub. Pronto.

> ⚠️ **Sobre segurança:** como tudo roda no navegador, a chave de escrita (`MASTER_KEY`) fica visível no código do `edit.html` pra quem inspecionar a página. Isso é aceitável aqui porque **só a staff tem o link**. Não divulgue o `edit.html` publicamente. A chave do painel (`ACCESS_KEY`) é só de leitura, então mesmo exposta não permite alterar a grade.

---

## 📱 O painel (`index.html`)

- **Relógio ao vivo** e indicador **AGORA** com a pauta no ar (piscando).
- **3 abas:**
  - **Timeline** — o dia inteiro em ordem. Pautas encerradas ficam esmaecidas, a atual com selo *NO AR*, a próxima sinalizada.
  - **Por Sala** — pautas agrupadas por sala.
  - **Agora** — o que está rolando em cada sala neste momento (e o que vem a seguir).
- **Filtro por pilar:** Voz · Eco · Acorde (ou Todos).
- Toque numa pauta para abrir o **briefing completo**.

> O status "agora / passado / próximo" é calculado pelo **horário do celular** de quem acessa. Durante o evento, atualiza sozinho.

---

## ✏️ Como editar a grade

1. Acesse o **`edit.html`** (ex.: `https://SEU-USUARIO.github.io/SEU-REPO/edit.html`).
2. Clique numa pauta para abrir o formulário inline e editar.
   - **Nova pauta:** botão *Nova pauta*.
   - **Reordenar:** *Reordenar por horário* organiza tudo pelo horário de início.
   - **Excluir:** botão dentro do formulário.
   - Obrigatórios: **início**, **fim** e **título**.
3. Clique em **Salvar alterações**. Isso:
   - grava a grade **direto no JSONBin** (o painel reflete na hora, sem commit, sem deploy);
   - baixa uma **cópia de backup** do `data.json` (rede de segurança — você pode guardar/commitar quando quiser).

### Aviso de conflito (duas pessoas editando)
Antes de gravar, o editor confere se a grade mudou no servidor desde que você abriu a página. Se outra pessoa salvou nesse meio tempo, aparece o aviso **"A grade mudou desde que você abriu. Recarregue antes de salvar"** e o salvamento é bloqueado para não apagar o trabalho do outro. Clique em **Recarregar grade** para puxar a versão atual (atenção: isso descarta as suas alterações não salvas).

---

## 🚀 Deploy no GitHub Pages

### Primeira vez

1. Crie um repositório, configure o JSONBin (seção acima) e suba os arquivos na raiz:
   ```bash
   git clone https://github.com/SEU-USUARIO/SEU-REPO.git
   cd SEU-REPO
   # copie index.html, edit.html, data.json e README.md (com as chaves já coladas)
   git add .
   git commit -m "CirandaMEJ'26 — painel inicial"
   git push origin main
   ```
2. No GitHub: **Settings → Pages → Build and deployment**.
3. Em *Source*, escolha **Deploy from a branch**, branch `main`, pasta `/ (root)`. Salve.
4. Aguarde ~1 min. Painel em `https://SEU-USUARIO.github.io/SEU-REPO/` e editor em `.../edit.html`.

### Depois do deploy
As edições da grade **não precisam mais de commit** — vão direto pro JSONBin pelo `edit.html`. Você só volta a mexer no repositório se quiser trocar o visual/código ou atualizar a semente `data.json`.

> Quer o editor numa URL limpa `/edit` em vez de `/edit.html`? Renomeie o arquivo para `edit/index.html` no repositório. O conteúdo é o mesmo.

---

## 🎨 Identidade visual

Paleta CirandaMEJ'26 — magenta `#ff1596`, navy `#012462`, amarelo `#ffe36c`, rosa claro `#ffbfcd`, vinho `#8d0b53`.
Tipografia: **Dela Gothic One** (títulos), **IBM Plex Sans** (texto), **Barriecito** (detalhes), via Google Fonts.
Pilares: **Voz** magenta · **Eco** amarelo · **Acorde** rosa claro.

## 🔧 Detalhes técnicos

- Sem frameworks, sem build. CSS/JS embutidos em cada página.
- `index.html` lê via `GET` na API do JSONBin (com `X-Access-Key`); se falhar, cai pro `data.json` local.
- `edit.html` lê via `GET` e grava via `PUT` (com `X-Master-Key`).
- O JSONBin tem **CORS habilitado**, então as chamadas funcionam direto do navegador.
- Conflitos: estratégia *last-write-wins* com aviso prévio (o editor compara uma assinatura da grade antes de gravar).
- Teste local (o `fetch` exige servidor HTTP):
  ```bash
  python3 -m http.server 8000
  # abra http://localhost:8000
  ```