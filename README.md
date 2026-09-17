# 🚌 Painel de Condutores

Painel web (estático, sem backend) para acompanhar a situação documental dos motoristas da frota — validade do **DER** e da **CNH**, motoristas sem cadastro, motoristas enviados ao DER aguardando confirmação, e a distribuição por filial. Os dados vêm direto de uma planilha do Google Sheets, sem precisar de servidor: é só abrir a página.

## Como funciona

O `index.html` lê uma planilha pública do Google Sheets (via `gviz`, o mesmo mecanismo usado no Google Visualization API) e monta o painel inteiro no navegador: cards de resumo, gráficos, busca, ordenação e paginação. A planilha tem **uma aba por filial**, e o painel junta todas automaticamente.

Não há build, dependências de instalação nem back-end — é um único arquivo HTML hospedado como site estático (ex.: GitHub Pages).

## Estrutura do repositório

```
├── index.html        # o painel inteiro (HTML + CSS + JS)
└── Img/
    └── favicon.jpg    # ícone da aba do navegador
```

## Configuração da planilha

No topo do `<script>` do `index.html`:

```js
const SHEET_ID = '1V-yh0iUzolsuUw6s7GKIwo37Vdgpbn8qr7kB_CZ-hHE';
const SHEET_TABS = ['CONSELHEIRO LAFAIETE', 'BELEM', 'CANAA DOS CARAJAS', 'CATALAO',
                     'ARAXA', 'CONTAGEM', 'PARAUAPEBAS', 'SERRA DO SALITRE',
                     'SETE LAGOAS', 'TUCUMA'];
```

- **`SHEET_ID`**: o ID da planilha (o trecho entre `/d/` e `/edit` na URL do Google Sheets).
- **`SHEET_TABS`**: lista com o nome exato de cada aba — cada aba representa uma filial.

**Para adicionar uma nova filial:** crie a aba na planilha com o nome da unidade e inclua esse mesmo nome na lista `SHEET_TABS`. Não precisa mexer em mais nada.

A planilha precisa estar com o compartilhamento em **"Qualquer pessoa com o link pode visualizar"**, senão a leitura falha.

### Colunas esperadas em cada aba

| Coluna na planilha       | Uso no painel                                             |
|--------------------------|------------------------------------------------------------|
| `MOTORISTAS`              | Nome do condutor                                           |
| `CPF`                      | CPF (usado também para remover duplicados entre abas)     |
| `MAT.` / `MATRÍCULA`      | Matrícula                                                  |
| `ADMISSÃO`                | Data de admissão                                           |
| `FUNÇÃO`                   | Função/cargo                                               |
| `FILIAL`                   | Preenchida automaticamente com o nome da aba               |
| `SEXO`                     | Sexo                                                        |
| `DER VALIDADE`             | Data de validade do DER                                    |
| `DIAS REST`                | Dias restantes até o vencimento do DER (se a data faltar)  |
| `STATUS DEER`              | Status textual do DER (ativo/vencido) — validado pelo painel |
| `Nº CNH` / `N° CNH`       | Número da CNH                                              |
| `CNH VALIDADE`             | Data de validade da CNH                                    |
| `DIAS REST2`               | Dias restantes até o vencimento da CNH (se a data faltar)  |
| `STATUS CNH`                | Status textual da CNH — validado pelo painel               |
| `PETICIONADO`               | Se houve peticionamento                                     |
| `DATA PETICIONAMENTO`       | Data do peticionamento                                      |
| `N. PETICIONAMENTO`         | Número do peticionamento                                     |
| `PROBLEMAS`                  | Observação de pendência (aparece como badge)                |
| `OBSERVAÇÃO`                 | Observações gerais                                            |
| `ENVIADOS`                    | Checkbox (TRUE/FALSE) — motorista enviado ao DER, aguardando confirmação de validade |

> O painel é tolerante a colunas fora de ordem ou ausentes — ele localiza cada coluna pelo **nome do cabeçalho**, não pela posição.

### Regra importante: datas vazias nunca viram "vencido"

Se a data de validade (DER ou CNH) estiver vazia, o painel classifica o motorista como **"Sem cadastro"**, mesmo que a planilha calcule um número de dias "vencido" a partir de uma data vazia (bug clássico do Sheets/Excel ao subtrair datas em branco). Isso evita alarme falso de "vencido" para quem simplesmente nunca teve o documento cadastrado.

## Funcionalidades do painel

- **Cards de resumo** clicáveis: total de condutores, DER (ativo/vencendo/vencido), CNH (ativa/vencida), sem cadastro e enviados aguardando o DER. Clicar em um card filtra a tabela; clicar de novo remove o filtro.
- **Filtro "Sem cadastro" x "Enviados (aguardando DER)"**: são mutuamente exclusivos — ativar um desativa o outro (e os filtros de DER/CNH). Cada um filtra a lista de motoristas pelo seu próprio critério.
  - *Enviados (aguardando DER)*: motorista com a coluna `ENVIADOS` marcada, mas cujo DER **ainda não está confirmado como ativo** — ou seja, já foi enviado, mas o retorno da validade ainda não veio.
- **Gráfico "Condutores por filial"**: barra clicável (filtra por aquela filial) e um botão "Ver todas as filiais" que aparece quando um filtro de filial está ativo, para limpar rapidamente.
- **Gráfico "Situação documental"**: pizza que se adapta ao filtro ativo —
  - sem filtro: visão geral (DER x CNH x sem cadastro);
  - filtro DER: só as fatias de DER;
  - filtro CNH: só as fatias de CNH;
  - filtro "Sem cadastro"/"Enviados": comparação direta entre os dois grupos.
  - As fatias também são clicáveis e aplicam o mesmo filtro do card correspondente.
- **Busca**: por nome, matrícula, CPF ou número da CNH.
- **Ordenação** por qualquer coluna da tabela (clique no cabeçalho).
- **Paginação** de 100 em 100 registros.
- **Modal de detalhes** ao clicar em qualquer motorista, com todos os dados e opção de copiar matrícula/CPF.
- **Cards responsivos** no lugar da tabela em telas pequenas (celular).
- **Atualização automática** a cada 5 minutos, além do botão "Atualizar agora".

## Como publicar (GitHub Pages)

1. Faça o commit do `index.html` e da pasta `Img/` (com o `favicon.jpg`) na branch principal.
2. No repositório, vá em **Settings → Pages**.
3. Em **Source**, selecione a branch (ex.: `main`) e a pasta `/ (root)`.
4. Salve — o GitHub gera uma URL do tipo `https://seu-usuario.github.io/seu-repositorio/`.

Não é necessário nenhum passo de build: o site é servido exatamente como está no repositório.

## Solução de problemas

- **"Não foi possível ler a planilha"**: confira se o link de compartilhamento está liberado e se os nomes em `SHEET_TABS` batem exatamente com os nomes das abas na planilha.
- **Abrindo o arquivo direto no navegador (`file://...`)**: não funciona — o navegador bloqueia esse tipo de requisição por segurança. É preciso hospedar em um servidor (GitHub Pages, Netlify etc.).
- **Uma filial não aparece**: verifique se o nome da aba na planilha é idêntico (maiúsculas/acentos) ao que está em `SHEET_TABS`.
