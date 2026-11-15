# Pit Stop Chaos: agenda ao vivo (eventos e patrocínios)

Este arquivo controla, **sem lançar versão nova**, quais fases especiais aparecem no mapa e em que datas, e quais banners de patrocinador o jogo mostra.

O jogo baixa o arquivo `agenda.json` toda vez que abre, com no máximo 4 segundos de espera e sem enviar nenhum dado do jogador. A cópia baixada fica guardada no computador, então vale também nas próximas aberturas sem internet. Se o jogo não conseguir baixar o arquivo e não tiver nenhuma cópia guardada, ele usa as datas que vêm dentro dele.

---

## Onde o arquivo mora

O jogo só consegue baixar o arquivo de um endereço **público**. O repositório principal do jogo é privado, então a agenda mora num repositório público separado:

- **Repositório:** https://github.com/toddbrasil/PitStopChaos-AoVivo
- **Endereço que o jogo lê:** https://raw.githubusercontent.com/toddbrasil/PitStopChaos-AoVivo/main/agenda.json

**Como mudar a agenda:** abra o `agenda.json` no GitHub, clique no lápis (Edit), altere e salve com "Commit changes".

**Fonte da verdade:** a cópia em `AoVivo/` no projeto é o que o jogo traz embutido. Quando mudar a agenda publicada, copie a mudança para lá também, para a próxima build já sair com ela.

**Onde o endereço está configurado:** no gerador (`GeradorFases.cs`), que o grava no catálogo (`Catalogo_Jogo.asset`).

> O GitHub pode levar uns 5 minutos para servir o arquivo novo pelo endereço raw.

---

## Eventos

Cada evento tem estes campos:

| Campo | O que é |
|---|---|
| `id` | Nome fixo do evento. Não mude. |
| `nome` | Nome legível, só para você se orientar. |
| `implementado` | `true` se o jogo já tem a fase desse evento. Serve de controle: um evento não implementado nunca abre nada, mesmo com `ativo` ligado. |
| `ativo` | `true` para o evento abrir no período. `false` desliga, mesmo dentro das datas. |
| `fase` | Id da fase especial no jogo (`especial_natal`, `especial_halloween`, `especial_saci`). Vazio se ainda não existe. |
| `inicio` / `fim` | Período em que a fase fica aberta, com o fim incluído (veja abaixo). |
| `observacao` | Anotação livre. |

### Formato das datas

- **`"MM-DD"`** repete todo ano. Exemplo: `"12-01"` a `"01-06"` (o fim pode cair no ano seguinte).
- **`"AAAA-MM-DD"`** vale só naquele ano. Use para datas móveis (Carnaval, Páscoa, Dia das Mães, Dia dos Pais) e para eventos únicos (Copa, Olimpíadas).
- **Vazio nos dois campos:** o evento fica aberto o tempo todo enquanto `ativo` for `true`.

### O que o jogador vê

Um evento implementado, ativo e dentro das datas abre a fase dele no mapa com a placa **NOVA!** (**NEW!** em inglês). Fora das datas, a fase aparece como "em breve".

### Exemplos

- **Desligar o Halloween este ano:** `"ativo": false` no evento `halloween`.
- **Estender o Natal até 10 de janeiro:** `"fim": "01-10"`.
- **Carnaval de 2028:** troque as datas do evento `carnaval_2027` (o `id` pode ficar) para as de 2028.

### Eventos futuros

A lista já traz todos os eventos previstos, com `implementado: false`: ano novo, carnaval, Dia da Mulher, Páscoa, São João, as quatro estações, aniversário do jogo, férias, início da temporada de corridas, namorados, crianças, mães, pais, família, avós, trabalho, finados, consciência negra, juventude, Copas, Olimpíadas, Paralimpíadas, Pan e campeonato mundial.

Também há 10 vagas genéricas de cada categoria: `evento_generico_N`, `evento_saude_N`, `evento_internacional_N`, `evento_patrocinio_N` e `evento_comunidade_N`.

**Para lançar um evento novo:**
1. Crie a fase especial no jogo (gerador) e publique a atualização (Steam, por exemplo).
2. Na agenda, no evento correspondente, preencha `fase` com o id da fase nova, ponha `implementado: true`, `ativo: true` e as datas.

---

## Patrocínios

O jogo tem 8 marcas: `Saideken`, `Toddex`, `OleoFortes`, `RedBoi`, `TurboCola`, `Pneuzao`, `BancoImprevisto` e `PitStopChaos`. Cada uma aparece nas placas da fase (no muro e nas laterais).

Para trocar o banner de **uma** marca, edite só a entrada dela:

| Campo | O que é |
|---|---|
| `marca` | Qual marca substituir. Não mude o nome. |
| `ativo` | `true` liga a substituição. |
| `banner` | Endereço público de um PNG de **1024×256**, usado em português. |
| `banner_en` | Endereço do PNG em inglês. Vazio usa o mesmo de `banner`. |
| `inicio` / `fim` | Período do patrocínio, no mesmo formato dos eventos. Vazio nos dois vale sempre. |

O banner pode morar no mesmo repositório público da agenda, por exemplo:

```
https://raw.githubusercontent.com/<usuario>/PitStopChaos-AoVivo/main/banners/MinhaMarca.png
```

As marcas sem substituição continuam com a arte original. Fora do período, ou com `ativo: false`, a arte original volta.

### Parede do título e do lobby

A parede de fundo do menu e do lobby é uma imagem única, com todas as marcas montadas em grade. Para trocá-la, use o bloco `parede`:

- `imagem`: PNG de 2048×1024 que emenda nas bordas.
- `imagem_en`: a versão em inglês.

Para gerar uma parede nova trocando só uma marca, rode o script de arte com um logo próprio (PNG transparente de 1024×512):

```
python -I Ferramentas/gerar_patrocinadores.py . --substituir Saideken=caminho/do/logo.png
```

O script grava em `Assets/_Projeto/Arte/Patrocinadores/` os banners e as paredes nas duas línguas. Suba no repositório da agenda o `Parede_Patrocinadores.png` (e o `_EN`) e o banner da marca, e aponte os endereços na agenda.

---

## Conferir antes de salvar

- **JSON válido:** use um validador de JSON. Uma vírgula sobrando invalida o arquivo, e o jogo passa a ignorar o arquivo novo (continua com a última cópia boa).
- **Datas:** no formato `MM-DD` ou `AAAA-MM-DD`, com zero à esquerda (`03-08`, nunca `3-8`).
- **Imagens:** PNG, em endereço público. Teste o endereço numa aba anônima do navegador.
