# Neon Finanças Pessoais

Dashboard pessoal de finanças em arquivo único (`index.html`), sem build, sem servidor e sem backend. **100% local**: os dados ficam em IndexedDB no seu navegador e nada é enviado à internet.

## Como usar

1. Abra o `index.html` no navegador (ou hospede em qualquer lugar estático).
2. Vá até a aba **Importação**.
3. Escolha a origem:
   - **Conta corrente · OFX/OFC**: exporte o extrato no app do Itaú (`Extrato → Conta corrente → exportar como OFX`).
   - **Fatura do cartão ou planilha · Excel**: exporte a fatura no app do Itaú (`Fatura → exportar Excel`) **ou** baixe a sua planilha do Google (`Arquivo → Baixar → XLSX`) com a aba **Lançamento**.
4. Arraste o arquivo para a área indicada (ou clique para escolher).
5. Revise as linhas (categorias já preenchidas automaticamente pelas **regras** e pelos valores fixos vindos da planilha) e clique em **Confirmar importação**.
6. Lançamentos, edição e exclusão manual: aba **Lançamentos** → **+ Novo lançamento** (ou ✎/× na linha).

### Instalar como app no Android (PWA)

1. Hospede a pasta em qualquer servidor HTTPS (GitHub Pages, Netlify, Vercel, etc.).
2. No celular Android, abra o endereço no **Chrome**.
3. Toque no menu ⋮ → **"Adicionar à tela inicial"** (ou **"Instalar app"**).
4. Pronto: abre em tela cheia, com ícone próprio, como um app nativo.

O service worker pré-carrega a página, o manifest, os ícones e as bibliotecas CDN (Chart.js e SheetJS) — depois da primeira visita, o painel funciona **offline**.

> Em `file://` a PWA não registra o service worker (exige HTTPS), mas o painel funciona normalmente no navegador.

**Como publicar no GitHub Pages (grátis):**
1. Crie um repositório no GitHub e suba todo o conteúdo desta pasta (o `index.html` na raiz).
2. Repositório → **Settings → Pages** → em *Source* escolha **Deploy from a branch** → `main` + `/ (root)` → **Save**.
3. Aguarde alguns minutos e acesse `https://SEU_USUARIO.github.io/REPOSITORIO/` — é só abrir no Chrome do celular e instalar.

## Importação e dados

- **OFX/OFC (conta corrente)**: o painel lê as movimentações (`STMTTRN`) e usa o `FITID` para **não duplicar** lançamentos já importados. Valores negativos viram **Gasto**; positivos, **Receita**.
- **Excel (fatura do cartão ou planilha "Lançamento")**: a primeira planilha do arquivo é lida e as colunas são detectadas automaticamente. Na fatura do Itaú (**Data**, **Lançamento**, **Valor**) lançamentos viram **Gasto** e valores negativos viram **Receita**. Na planilha do Google, as colunas **Data, Descrição, Valor, Tipo, Tipo2, Categoria, Subcategoria, Conta/Cartão, Forma Pagamento** já vêm preenchidas e são respeitadas (as regras preenchem apenas o que faltar; receitas/investimentos sem categoria usam a subcategoria como categoria). A deduplicação usa `data + valor + descrição` (a linha é marcada como "duplicado" e pode ser desmarcada antes de salvar).
- **Revisão antes de salvar**: cada linha da importação é editável (data, descrição, tipo, classe, categoria, subcategoria, conta, forma e valor) e as duplicadas vêm desmarcadas — desmarque também o que não quiser salvar e confirme.
- **Regras de categorização**: palavra-chave na descrição → preenche tipo, classe, categoria, conta e forma. A ordem das regras define a prioridade. Edite em **Importação → Regras**; as alterações valem para a próxima importação.
- **Lançamentos manuais**: crie, edite e exclua diretamente na aba **Lançamentos**. Categoria, subcategoria, conta e forma usam **valores fixos** (os da planilha + os já usados na base); escolha **"Outra…"** para digitar um valor livre. A deduplicação manual por descrição é a mesma dos dados importados.
- **Filtros na lista de lançamentos**: barra acima da tabela filtra por data (ex.: `15/01`, `01/2025` ou `2025`), descrição, tipo, classe, categoria, subcategoria, conta/cartão, forma e faixa de valor.
- **Exportar Excel**: botão **"Exportar Excel"** na aba Lançamentos gera um `.xlsx` com as mesmas colunas da planilha "Lançamento", respeitando o período e os filtros ativos.

## Funcionalidades

- **Navegação por páginas**: painel lateral com páginas (Dashboard, Receitas, Gastos, Lançamentos, Gestão de Metas, Projeções, Planejamento Gastos, Leituras e Importação), com opção de **ocultar** o painel. No celular, vira um menu gaveta.
- **Visão geral**: receita, despesa, investimento, saldo e "saúde financeira" (0–100), com médias e destaques do período.
- **Filtros**: por mês e por ano (cabeçalho do painel).
- **Temas**: Neon (padrão), Claro e Neutro — a escolha fica salva no navegador.
- **Evolução mensal**: receita × despesa em eixos independentes + saldo em barras.
- **Comparativo anual**: ano atual × ano anterior, mês a mês, com seletor de métrica.
- **Análises**: ranking por grupo e categoria, donut por grupo, despesas por forma de pagamento, canais de receita.
- **Metas**: poupança (%), aporte mensal e reserva de emergência; metas por categoria de gasto.
- **Projeções e simulações**: caixa (crescimento da receita/despesa, saldo inicial, horizonte) e investimentos (juros compostos, aporte e taxa anual).
- **Planejamento de gastos**: **previsto** mensal (Jan–Dez) por categoria e subcategoria, com totais e saldo por categoria.
- **Leituras (insights)**: cards automáticos com destaques do período.

## Dependências

- [Chart.js](https://www.chartjs.org/) via CDN (jsdelivr).
- [SheetJS](https://sheetjs.com/) (`xlsx.full.min.js`) via CDN (jsdelivr) — usado só para ler os Excel do cartão. Depois da primeira carga, fica no cache do service worker e funciona offline.

## Privacidade e limitações

- **Nenhum dado sai do navegador**: sem planilha, sem login, sem servidor. Exclua a base apagando os dados do site no navegador (Privacidade → Apagar dados do site).
- Para a deduplicação por `FITID` funcionar bem, importe cada período **uma vez** (reimportar o mesmo arquivo apenas desmarca as duplicadas).
- O leitor de Excel precisa de internet **na primeira visita** (para baixar o SheetJS); depois disso funciona offline.
