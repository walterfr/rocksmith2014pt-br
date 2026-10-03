# AGENTS.md — Diretrizes Técnicas e Regras Críticas de Modding

Este documento serve como especificação técnica obrigatória para qualquer agente de IA ou desenvolvedor trabalhando na tradução e modificação de arquivos de localização do **Rocksmith 2014 Remastered**.

---

## 1. Arquitetura e Fragilidade do Leitor CSV (`maingame.csv`)

O leitor de textos do Rocksmith 2014 é extremamente primitivo e **não suporta o padrão RFC 4180**:
1. **Proibição Absoluta de Vírgulas nos Textos:**
   * A engine separa colunas usando uma divisão ingênua por vírgula (`split(',')`).
   * Se qualquer texto contiver uma vírgula (mesmo entre aspas), a coluna é deslocada para o idioma vizinho (ex.: Alemão ou Italiano), corrompendo a tabela e **travando o jogo em loop no carregamento do perfil**.
   * **Regra:** Nunca coloque vírgulas na coluna da tradução. Substitua por espaços ou reformule a frase.
2. **Proibição de Aspas Duplas (`"`):**
   * Não use aspas duplas em strings da tradução. Use aspas simples (`'`).
   * O módulo `csv` padrão do Python tenta colocar aspas automáticas se houver vírgulas ou quebras de linha. Sempre salve o arquivo unindo as 9 colunas manualmente com `",".join(row) + "\r\n"` e `QUOTE_NONE`.
3. **Integridade das Demais Colunas (0, 1, 2, 4, 5, 6, 7, 8):**
   * As outras colunas (Inglês original, Francês, Alemão, Espanhol original, etc.) **devem permanecer 100% idênticas ao original**.
   * Ferramentas e mods de injeção (como o RSMods) buscam strings exatas em inglês na memória para aplicar ganchos (*hooks*, como Fast Load e bypass de DLCs). Alterar o texto em inglês quebra esses hooks.
4. **Total de Linhas e Colunas:**
   * O arquivo tem exatamente **20.143 linhas**.
   * Cada linha deve ter exatamente **9 colunas** (8 vírgulas delimitadoras por linha, totalizando **161.144 vírgulas** no arquivo inteiro).

---

## 2. Regras de Empacotamento do `cache4.7z` e `cache.psarc`

O arquivo `cache.psarc` armazena 9 entradas fundamentais:
- Entry 0: `NamesBlock.bin`
- Entry 1: `cache0.7z`
- Entry 2: `cache1.7z`
- Entry 3: `cache3.7z`
- Entry 4: `cache4.7z` (Localização e UI de boot)
- Entry 5: `cache6.7z`
- Entry 6: `cache7.7z`
- Entry 7: `cache8.7z`
- Entry 8: `sltsv1_aggregategraph.nt`

### ⚠️ Erro Fatal: Descompactar o `cache4.7z` em arquivos soltos
- O `cache4.7z` contém 23 arquivos: 22 assets essenciais do jogo (incluindo o vídeo de introdução `introsequence.gfx`, telas de splash `ubisoft_logo.png.dds`, fontes e scripts de comportamento) e apenas 1 arquivo de texto (`localization\maingame.csv`).
- **NUNCA** descompacte o `cache4.7z` para uma pasta e reempacote com `7za a ... *`. Isso adiciona registros de diretório (`Attributes = D`) e altera o método de compressão dos vídeos, fazendo o jogo **travar imediatamente na tela inicial (palheta travada)**.
- **Procedimento Correto (In-Place Update):**
  Parta sempre do `cache4.7z` limpo extraído do backup original e utilize o comando de atualização in-place:
  ```powershell
  & "C:\Program Files\7-Zip\7z.exe" u cache4.7z "localization\maingame.csv" -m0=lzma:d=23
  ```
  Isso mantém todos os 22 assets binários 100% intocados e sem registros de pastas extras.

---

## 3. Sincronização Obrigatória com o RSMods

Se o usuário tiver o mod **RSMods** instalado:
- O RSMods possui um hook ativo que injeta textos customizados a partir de:
  `W:\SteamLibrary\steamapps\common\Rocksmith2014\RSMods\CustomMods\maingame.csv`
- Ao compilar uma nova tradução, esse arquivo **DEVE ser atualizado simultaneamente** com a versão limpa do `maingame.csv`.
- Se esse arquivo ficar desatualizado ou contiver aspas/vírgulas inválidas, o RSMods injetará a versão corrompida e causará travamentos mesmo que o `cache.psarc` esteja perfeito.

---

## 4. Script Mestre de Validação e Deploy

Para compilar e implantar qualquer nova tradução com segurança total, execute sempre o script mestre:
```powershell
pwsh -ExecutionPolicy Bypass -File .\rebuild_and_deploy_perfect.ps1
```

### Checklist Automatizado Pré-Deploy (Python)
Antes de qualquer deploy, valide a integridade do CSV:
```python
with open('work/maingame.csv', 'r', encoding='utf-8') as f:
    text = f.read()
    assert text.count('""') == 0, "Erro: Aspas duplas detectadas no CSV!"
    assert text.count(',') == 161144, f"Erro: Contagem de vírgulas incorreta! ({text.count(',')})"
    assert len(text.splitlines()) == 20143, "Erro: Contagem de linhas incorreta!"
print("CSV 100% íntegro e seguro para a engine!")
```
