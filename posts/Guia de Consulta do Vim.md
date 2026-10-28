# Guia de Consulta do Vim

Oct 28, 2026 · @Diego Viana

## Modos e como começar

O Vim funciona por modos: você abre o arquivo no modo Normal, digita texto no modo Inserção e volta com `Esc`. Na dúvida, aperte `Esc` duas vezes para voltar ao Normal.

| Modo | Para que serve | Como entrar | Como sair |
| --- | --- | --- | --- |
| Normal | Navegar e dar comandos | Ao abrir o arquivo | — |
| Inserção | Digitar texto | `i`, `a`, `o` (ver seção Inserir) | `Esc` |
| Visual | Selecionar texto | `v`, `V` ou `Ctrl+v` | `Esc` |
| Linha de comando | Salvar, sair, buscar e substituir | `:` ou `/` | `Enter` ou `Esc` |
| Substituição | Sobrescrever caracteres | `R` | `Esc` |

| Comando no terminal | O que faz |
| --- | --- |
| `vim arquivo.txt` | Abre (ou cria) o arquivo |
| `vim +25 arquivo.txt` | Abre já na linha 25 |
| `vim -R arquivo.txt` ou `view arquivo.txt` | Abre só para leitura |
| `vim -O a.txt b.txt` | Abre dois arquivos lado a lado |
| `vimtutor` | Tutorial interativo de \~30 minutos que já vem com o Vim |

## Salvar e sair

Todos estes comandos são digitados no modo Normal e confirmados com `Enter`. O mais usado é `:wq` (salvar e sair); para sair sem salvar, `:q!`.

| Comando | O que faz |
| --- | --- |
| `:w` | Salva |
| `:w novo.txt` | Salva com outro nome |
| `:q` | Sai (só funciona se não houver alterações pendentes) |
| `:q!` | Sai descartando as alterações |
| `:wq` ou `:x` | Salva e sai |
| `ZZ` | Salva e sai (sem `:`) |
| `ZQ` | Sai sem salvar (sem `:`) |
| `:wa` / `:qa` / `:wqa` | Salva / sai / salva e sai de todos os arquivos abertos |
| `:w !sudo tee %` | Salva um arquivo que você abriu sem permissão de root |
| `:e!` | Descarta as alterações e recarrega o arquivo do disco |

## Movimentação do cursor

No modo Normal, `h j k l` movem o cursor sem tirar as mãos do teclado (as setas também funcionam). Quase todo movimento aceita um número antes: `5j` desce 5 linhas, `3w` avança 3 palavras.

| Comando | Movimento |
| --- | --- |
| `h` / `l` | Esquerda / direita |
| `j` / `k` | Baixo / cima |
| `w` / `b` | Próxima palavra / palavra anterior |
| `e` | Fim da palavra |
| `0` | Início da linha |
| `^` | Primeiro caractere não vazio da linha |
| `$` | Fim da linha |
| `gg` | Primeira linha do arquivo |
| `G` | Última linha do arquivo |
| `42G` ou `:42` | Vai para a linha 42 |
| `Ctrl+f` / `Ctrl+b` | Página para baixo / para cima |
| `Ctrl+d` / `Ctrl+u` | Meia página para baixo / para cima |
| `H` / `M` / `L` | Topo / meio / base da tela |
| `%` | Par correspondente de `( ) [ ] { }` |
| `{` / `}` | Parágrafo anterior / próximo |
| `f` + letra | Pula até a próxima ocorrência da letra na linha (`;` repete) |
| `ma` / `` `a `` | Marca a posição como “a” / volta a ela |
| `Ctrl+o` / `Ctrl+i` | Volta / avança no histórico de saltos |

## Inserir texto

Cada comando abaixo leva do modo Normal para o modo Inserção em um ponto diferente; `Esc` volta ao Normal.

| Comando | Onde começa a digitar |
| --- | --- |
| `i` | Antes do cursor |
| `a` | Depois do cursor |
| `I` | No início da linha |
| `A` | No fim da linha |
| `o` | Em uma nova linha abaixo |
| `O` | Em uma nova linha acima |
| `s` | Apaga o caractere e começa a digitar |
| `S` ou `cc` | Apaga a linha inteira e começa a digitar |
| `R` | Sobrescreve os caracteres (modo Substituição) |

Dentro do modo Inserção: `Ctrl+w` apaga a palavra anterior, `Ctrl+u` apaga até o início da linha e `Ctrl+n` completa a palavra com termos do arquivo.

## Editar: apagar, copiar, colar, desfazer

Os comandos de edição seguem a regra **operador + movimento**: `d` (apagar), `y` (copiar) e `c` (trocar) combinam com qualquer movimento. Exemplos: `dw` apaga uma palavra, `y$` copia até o fim da linha, `c3w` troca 3 palavras. Dobrar o operador age na linha inteira (`dd`, `yy`, `cc`).

| Comando | O que faz |
| --- | --- |
| `x` | Apaga o caractere sob o cursor |
| `dd` | Apaga (recorta) a linha |
| `3dd` | Apaga 3 linhas |
| `dw` | Apaga até o início da próxima palavra |
| `D` ou `d$` | Apaga até o fim da linha |
| `dG` | Apaga da linha atual até o fim do arquivo |
| `diw` / `ci"` | Apaga a palavra inteira / troca o texto entre aspas |
| `yy` | Copia a linha |
| `yw` | Copia a palavra |
| `p` / `P` | Cola depois / antes do cursor |
| `r` + letra | Troca um caractere pela letra |
| `J` | Junta a linha de baixo à atual |
| `>>` / `<<` | Aumenta / diminui a indentação |
| `~` | Alterna maiúscula/minúscula |
| `u` | Desfaz |
| `Ctrl+r` | Refaz |
| `.` | Repete o último comando de edição |
| `"ayy` / `"ap` | Copia para o registrador “a” / cola dele |
| `"+y` / `"+p` | Copia / cola usando a área de transferência do sistema (se o Vim tiver suporte) |

## Buscar e substituir

Busque com `/termo` e pule entre resultados com `n`; substitua no arquivo todo com `:%s/velho/novo/g`.

| Comando | O que faz |
| --- | --- |
| `/termo` | Busca para frente |
| `?termo` | Busca para trás |
| `n` / `N` | Próximo / anterior resultado |
| `*` / `#` | Busca a palavra sob o cursor para frente / para trás |
| `:noh` | Remove o destaque da busca |
| `:s/velho/novo/` | Substitui a 1ª ocorrência na linha atual |
| `:s/velho/novo/g` | Substitui todas na linha atual |
| `:%s/velho/novo/g` | Substitui todas no arquivo |
| `:%s/velho/novo/gc` | Substitui no arquivo pedindo confirmação a cada uma |
| `:10,20s/velho/novo/g` | Substitui só entre as linhas 10 e 20 |
| `:%s/velho/novo/gi` | Substitui ignorando maiúsculas/minúsculas |
| `:g/termo/d` | Apaga todas as linhas que contenham o termo |

Na substituição com `c`, responda `y` (sim), `n` (não), `a` (todas) ou `q` (parar).

## Modo visual (selecionar)

Selecione com `v`, mova o cursor para estender a seleção e aplique um comando sobre ela.

| Comando | O que faz |
| --- | --- |
| `v` | Seleciona por caractere |
| `V` | Seleciona linhas inteiras |
| `Ctrl+v` | Seleciona um bloco (colunas) |
| `gv` | Reseleciona a última seleção |
| `d` / `y` / `c` | Apaga / copia / troca a seleção |
| `>` / `<` | Indenta / desindenta a seleção |
| `U` / `u` | Converte para maiúsculas / minúsculas |
| `:` | Executa um comando só nas linhas selecionadas (ex.: `:s/a/b/g`) |

Para comentar várias linhas de uma vez: `Ctrl+v`, desce com `j`, aperta `I`, digita `#` e finaliza com `Esc`.

## Vários arquivos, janelas e abas

Abra outro arquivo com `:e`, divida a tela com `:sp`/`:vsp` e alterne entre janelas com `Ctrl+w` seguido de uma direção.

| Comando | O que faz |
| --- | --- |
| `:e arquivo` | Abre outro arquivo |
| `:ls` | Lista os arquivos abertos (buffers) |
| `:bn` / `:bp` | Próximo / anterior arquivo aberto |
| `:b 2` | Vai para o buffer número 2 |
| `:sp arquivo` | Divide a tela na horizontal |
| `:vsp arquivo` | Divide a tela na vertical |
| `Ctrl+w` `w` | Alterna entre as janelas |
| `Ctrl+w` `h j k l` | Vai para a janela à esquerda / abaixo / acima / à direita |
| `Ctrl+w` `q` | Fecha a janela atual |
| `:tabnew arquivo` | Abre o arquivo em uma nova aba |
| `gt` / `gT` | Próxima / anterior aba |
| `:r arquivo` | Insere o conteúdo de outro arquivo abaixo do cursor |
| `:!comando` | Executa um comando do shell (ex.: `:!ls`) |
| `:r !date` | Insere a saída de um comando no texto |

## Configurações úteis e dicas

As opções `:set` valem só para a sessão atual; para torná-las permanentes, coloque-as (sem os `:`) no arquivo `~/.vimrc`.

| Comando | O que faz |
| --- | --- |
| `:set number` / `:set nonumber` | Mostra / esconde o número das linhas |
| `:set relativenumber` | Números relativos à linha atual |
| `:syntax on` | Ativa o realce de sintaxe |
| `:set hlsearch` | Destaca os resultados da busca |
| `:set ignorecase smartcase` | Busca sem diferenciar maiúsculas, exceto se o termo tiver maiúscula |
| `:set paste` | Cola texto do terminal sem bagunçar a indentação |
| `:set tabstop=4 shiftwidth=4 expandtab` | Tabulação de 4 espaços |
| `:set list` | Mostra caracteres invisíveis (tabs, fim de linha) |
| `:help assunto` | Abre a ajuda (ex.: `:help :s`) |

Exemplo de `~/.vimrc` mínimo:

```
set number
syntax on
set hlsearch
set ignorecase smartcase
set tabstop=4 shiftwidth=4 expandtab
```

Dicas rápidas:

- Travou ou não sabe em que modo está? `Esc` `Esc` e depois `:q!` sai sem estragar nada.
- A tela congelou depois de `Ctrl+s`? Aperte `Ctrl+q` para destravar o terminal.
- O `vi` em sistemas mínimos pode não ter todos os recursos do Vim (abas, `Ctrl+v`, desfazer múltiplo); os comandos básicos de modos, movimento, edição, busca e salvar funcionam em ambos.
- Rode `vimtutor` uma vez: em meia hora os comandos deste guia viram memória muscular.
