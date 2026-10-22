# Diretrizes de Contribuição

Bem-vindo! Toda ajuda para lapidar a tradução do Rocksmith 2014 para o Português do Brasil é super bem-vinda. Se você encontrou algum texto que ficou esquisito, não coube na tela, ou possui um erro de tradução musical (ex: traduziram "play" como "jogar" no meio de uma frase musical), veja como é fácil consertar.

---

## 🏳️‍🌈 Versionamento (Pride Versioning 0.3.0)

Este projeto adota o [Pride Versioning 0.3.0](https://pridever.org/).
O formato das versões segue `PROUD.DEFAULT.SHAME`:
- **PROUD (Orgulho):** Avanços substanciais que elevam a qualidade da tradução.
- **DEFAULT (Padrão):** Contribuições do dia a dia, melhorias incrementais e revisões de frases.
- **SHAME (Vergonha):** Correções de bugs embaraçosos, termos bizarros que escaparam ou correções de emergência.

---

## Como os textos são estruturados

O jogo armazena todos os seus textos no arquivo `maingame.csv`. 
O arquivo não tem cabeçalho e cada linha possui 9 colunas no total (8 vírgulas delimitadoras).

A estrutura das colunas é:
1. **ID do texto:** Ex: `6506`
2. **Inglês (Original):** `You can't play [game name]...`
3. **Francês:** (mantido idêntico ao original)
4. **Tradução PT-BR:** (substitui o slot do Espanhol)
5. **Italiano:** (mantido idêntico ao original)
6. **Alemão:** (mantido idêntico ao original)
7. **Japonês:** (mantido idêntico ao original)
8. **Coluna extra do jogo:** (conteúdo interno; mantido idêntico ao original)
9. **Vazia:** (mantida vazia)

---

## Regras Críticas de Tradução (Evite Crashes!)

1. **PROIBIDO VÍRGULAS NA COLUNA DA TRADUÇÃO:**
   A engine do Rocksmith 2014 usa `split(',')` ingênuo. Qualquer vírgula adicional desloca as colunas para o idioma vizinho e **trava o jogo no carregamento do perfil**. Substitua vírgulas por espaços ou reformule a frase.
2. **NUNCA use aspas duplas (`"`):**
   Use apenas aspas simples (`'`). Aspas duplas forçam geradores de CSV a colocar delimitadores que quebram o leitor da engine.
3. **NÃO altere textos entre colchetes `[...]` ou chaves `{...}`:** 
   O Rocksmith utiliza variáveis internas como `[game name]`, `[player name]`, `[#Key]` e tags de formatação como `{C}`, `{L}`, `{X}`, `{pad:a}`. Eles **precisam ser mantidos exatamente como no original**, caso contrário o jogo trava.
4. **Termos musicais técnicos:** Mantenha nomes de técnicas universais em inglês quando for a convenção brasileira (*Hammer-on*, *Pull-off*, *Bend*, *Palm Mute*, *Slide*, *Tapping*).
5. **Comprimento:** Tente não escrever frases excessivamente longas para não estourar caixas de diálogo na interface.

Consulte o arquivo [AGENTS.md](AGENTS.md) para detalhes técnicos de arquitetura e empacotamento.

---

## Como Enviar uma Correção

### Método Fácil (Via Navegador no GitHub)

1. Vá até o arquivo [`maingame.csv`](maingame.csv) neste repositório.
2. Clique no ícone de "Lápis" (Edit this file).
3. Use `Ctrl+F` para localizar a frase que deseja corrigir.
4. Altere **apenas** o texto da 4ª coluna (PT-BR), garantindo que **não adicionou vírgulas nem aspas duplas**.
5. Crie uma nova branch e abra o **Pull Request**.

### Método Avançado (Testando Localmente no Jogo)

1. Faça um fork e clone o repositório.
2. Edite o `maingame.csv` localmente.
3. Use o procedimento documentado em [AGENTS.md](AGENTS.md) (in-place update com `7z u cache4.7z localization\maingame.csv -m0=lzma:d=23`).
4. Execute `rebuild_and_deploy_perfect.ps1` e teste dentro do Rocksmith 2014.
5. Se aprovado, abra o Pull Request!
