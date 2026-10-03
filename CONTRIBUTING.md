# Diretrizes de Contribuição

Bem-vindo! Toda ajuda para lapidar a tradução do Rocksmith 2014 para o Português do Brasil é super bem-vinda. Se você encontrou algum texto que ficou esquisito, não coube na tela, ou possui um erro de tradução musical (ex: traduziram "play" como "jogar" no meio de uma frase musical), veja como é fácil consertar.

## Como os textos são estruturados

O jogo armazena todos os seus textos no arquivo `maingame.csv`. 
O arquivo não tem cabeçalho e cada linha possui 9 colunas no total. 

A estrutura simplificada das colunas é:
1. **ID do texto:** Ex: `6506`
2. **Inglês (Original):** Ex: `You can't play [game name]...`
3. Vazio
4. **Tradução:** (Na verdade é a coluna que o jogo lê quando configurado para espanhol/outro idioma europeu e que usamos para colocar o PT-BR). Ex: `Você não pode jogar [game name]...`
5. Vazio
6. Vazio
7. Outros idiomas...
8. Outros idiomas...
9. Outros idiomas...

## Regras de Tradução e Formatação

1. **NÃO altere textos entre colchetes `[...]` ou chaves `{...}`:** 
   O Rocksmith utiliza variáveis internas como `[game name]`, `[player name]`, `[#Key]` e tags de formatação como `{C}`, `{L}`, `{X}`. Eles **precisam ser mantidos em inglês e exatamente como no original**, caso contrário o jogo **trava (crash)** na tela de loading ou menus.
2. **Termos técnicos:** Mantenha nomes de técnicas universais em inglês quando fizer sentido (ex: *Hammer-on*, *Pull-off*, *Bend*, *Palm Mute*, *Slide*). Não traduza para "martelo" ou "fazer curva".
3. **Comprimento:** Tente não escrever frases muito maiores que o original em inglês (máximo de 30% a 40% maior), pois o texto pode ser cortado na interface do jogo.

## Como Enviar uma Correção

### Método Fácil (Via GitHub no Navegador)

1. Vá até o arquivo [`maingame.csv`](maingame.csv) neste repositório.
2. Clique no ícone de "Lápis" (Edit this file).
3. Use `Ctrl+F` (ou o campo de busca) para encontrar a frase errada que você viu no jogo.
4. Mude **apenas** o texto na 4ª coluna (a nossa coluna PT-BR).
5. Desça a página até o "Commit changes...".
6. Escreva um título curto sobre o que você arrumou (ex: `Corrigido erro na frase do Guitarcade`).
7. Clique em **Propose changes** e crie o **Pull Request**.

Assim que revisarmos a correção e virmos que as tags do jogo continuam intactas, a sua correção será aceita!

### Método Avançado (Para quem quer testar no próprio jogo)

Se você entende de modding do Rocksmith 2014, pode usar o arquivo `maingame.csv` localmente:
1. Faça um *fork* deste repositório e clone no seu PC.
2. Edite o `maingame.csv` localmente com uma ferramenta que não destrua as quebras de linha (`\r\n`), como VS Code, Notepad++ ou ferramentas de planilhas configuradas corretamente.
3. Descompacte o seu `cache.psarc` original usando ferramentas como a `Rocksmith2014PsarcLib` e `7-zip`.
4. Substitua o `maingame.csv` dentro de `cache4.7z -> localization`.
5. Reempacote o `cache4.7z` **obrigatoriamente usando o método Store (sem compressão ou `-mx=0`)**, senão o jogo travará na tela de *loading* ou nos perfis.
6. Reempacote o `cache.psarc` e jogue para testar.
7. Se funcionou e ficou bom, crie um **Pull Request** no GitHub com o seu arquivo `maingame.csv` modificado!
