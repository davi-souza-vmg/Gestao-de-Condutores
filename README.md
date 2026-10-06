# Gestão de Condutores — ATF Unificado

## Gestão de ATF's

O `Html/frota.html` é o painel único de controle de ATF's.

### ATF's cadastradas
- Cruza a aba **Frota** com a aba **ATFs** da planilha principal.
- Mostra as ATFs já cadastradas individualmente, inclusive quando a mesma frota possui vários clientes ou itinerários.
- Frotas sem ATF também podem aparecer no controle como **Sem ATF**.
- A situação de vencimento é calculada pelo registro da ATF.

### Frotas por cliente
- Agrupa os registros por frota.
- Cada cliente/itinerário/ATF permanece como uma seção independente dentro do card.
- Registros que ainda estão no processo de cadastro na planilha de apoio **ATFS** aparecem como **Em cadastro**, sem substituir uma ATF já existente.
- Ao clicar em uma seção, abre o painel com os dados do veículo e botões copiáveis para **PREF.**, **PLACA**, **LOTAÇÃO**, **ANO / MODELO**, **CHASSI** e **RENAVAN**.

A planilha **ATFS** não é usada como banco das ATFs existentes nem sobrescreve os registros oficiais. Ela serve apenas como fila de novos cadastros enquanto a ATF está sendo preparada.

`Html/atfs_clientes.html` permanece apenas como compatibilidade com links antigos e redireciona para o painel unificado.
