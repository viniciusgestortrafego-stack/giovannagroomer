# Landing page – Tosa Bebê Shih Tzu (Giovanna Rivaroli)

Página de vendas (VSL) do curso Tosa Bebê Shih Tzu da groomer Giovanna Rivaroli (GR Groomers, Guarulhos).
No ar em **https://groomergiorivaroli.com.br/**. Todo o conteúdo, código e mensagens ao usuário em **português do Brasil**.

## Estrutura

- `index.html`: a página inteira (HTML, CSS e JS inline, sem build e sem dependências).
- `web/`: imagens (`.jpg`) e vídeos curtos (`.mp4`) usados na página.

Não há `package.json`, framework nem testes. Para conferir, abra o `index.html` no navegador (Playwright já está instalado no ambiente cloud).

## Deploy (importante)

O site fica na **Hostinger** (não na Vercel), conectado ao GitHub com **implantação automática** da branch
**`claude/eloquent-cerf-i7qwwk`** (diretório raiz `public_html`). A Hostinger **não** publica a `main`.

Fluxo correto para toda mudança:

1. Trabalhe na branch de desenvolvimento, abra PR para `main` e faça merge (squash).
2. Atualize a branch da Hostinger com o conteúdo da `main`, via merge sem reescrever histórico:
   ```bash
   git fetch origin
   E=$(git rev-parse origin/claude/eloquent-cerf-i7qwwk); M=$(git rev-parse origin/main)
   C=$(git commit-tree "$M^{tree}" -p "$E" -p "$M" -m "Publica a versao atual da main no site (branch usada pela Hostinger)")
   git push origin $C:refs/heads/claude/eloquent-cerf-i7qwwk
   ```
3. Confirme no ar: baixe `https://groomergiorivaroli.com.br/?v=<aleatório>` e compare com `git show origin/main:index.html` (deve ser idêntico). A implantação leva segundos.

Nunca dê force-push na `claude/eloquent-cerf-i7qwwk`. Se a branch de produção da Hostinger um dia mudar para `main`, o passo 2 deixa de ser necessário (atualize este arquivo).

## Configuração (bloco `CONFIG` no `<script>` do fim da página)

Edite só esse bloco para mudar valores do dia a dia:

- `checkoutUrl`: checkout da Hotmart. Usado **só** no botão da caixa de preço.
- `whatsappUrl`: botão flutuante e link de dúvidas da oferta.
- `alunos` / `meta`: contador de alunos e barra "Rumo aos 100 alunos".
- `showViewers` / `viewersBase`: pílula "pessoas estão assistindo agora".
- `showTimer` / `timerMinutes` (5): timer "Esta condição sai do ar em…"; começa quando a página é liberada.
- `revealAt` (0.5): fração do vídeo assistido **com som** para liberar o resto da página.

## Comportamentos da página

- **VSL (hero, `#vsl`)**: player do YouTube "sem cara de YouTube" (sem controles, título, logo ou links), vídeo definido em `data-yt="tLrBf6HtF94"`.
  Começa mudo em autoplay com o aviso "Seu vídeo já começou / Clique para ativar o som"; o clique volta ao início com som.
  Clique no vídeo pausa/continua. Não substituir esse player por outro sem pedido explícito: o cliente quer exatamente este visual.
- **Barra de tempo (`#vslBar`)**: só visual, 9px, progresso não linear (`fakeProg`): chega a 55% nos primeiros 20% do vídeo e depois anda devagar até 100%.
- **Trava da página**: `body.locked` esconde tudo menos headline, vídeo e "pessoas assistindo". O player dispara o evento `vsl-reveal` ao atingir `revealAt` do tempo assistido com som; aí aparecem timer, botões e o resto. Fica salvo em `localStorage` (`gr_unlocked`). Se o player não carregar em 20s, a página libera sozinha.
- **Botões em cadeia (`.js-cta`)**: cada botão rola até o próximo; só o botão dentro de `.price-box` ("Quero aprender a técnica") vai para `checkoutUrl`. Botões depois da caixa de preço rolam de volta até ela.

## Rastreamento

- **Google Tag Manager**: `GTM-PJZ6Q3DW`. Eventos no `dataLayer`: `whatsapp_click`, `scroll_depth` (`scroll_percent`), `video_reveal`.
- **Meta Pixel**: `1634190808220269` no `<head>` (PageView), `Scroll` (`percent` 25/50/75/100) e `VideoLiberado`.
- **Nunca** coloque token de acesso da API de Conversões da Meta no código da página (ele fica público). Se precisar de CAPI, faça no servidor.

## Imagens

- Use fotos com pelo menos ~1000px de largura em destaques. As fotos de ~265×360 (`shihtzu-rosto-redondo-frente`, `shihtzu-bege-pata-cilindrica-evento`, `shihtzu-laco-preto-bandana-poa` etc.) ficam borradas em tamanho grande; só servem na galeria pequena.
- Fotos boas disponíveis: `shihtzu-laco-roxo`, `maltes-bandana`, `shihtzu-branco-laco-azul`, `shihtzu-colaboradora`, `spitz-colaboradora`, `giovanna-roseta-*`, `gr-groomers-salao`.

## Convenções

- Marcadores `class="todo"` indicam texto ainda pendente do cliente.
- Seções têm comentários `<!-- ===== N. NOME ===== -->`; mantenha esse padrão.
- Animações de entrada usam a classe `.rv` (vira `.in` ao aparecer) e respeitam `prefers-reduced-motion`.
