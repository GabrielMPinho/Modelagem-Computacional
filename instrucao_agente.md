# Instrução para transformar PDF de aula em material de estudo (Markdown)

Você vai me ajudar a transformar o **PDF de uma aula** em um arquivo **Markdown (.md)** de estudo, no mesmo estilo do arquivo `teoria.md` da pasta `serie_taylor`.

## Passos que você deve seguir

1. **Leia o PDF completo.** Use ferramentas de extração de texto (ex.: PyMuPDF/fitz). Se o modelo não conseguir ler PDF ou imagem diretamente, extraia o texto com Python (`pip install pymupdf` e depois `doc[i].get_text()`). Se fórmulas ou códigos vierem distorcidos, reconstrua a partir do contexto e do livro de referência (Chapra & Canale, Métodos numéricos para engenharia). Avisa se precisou reconstruir algo.
2. **Organize o conteúdo** em seções numeradas e claras, cobrindo TUDO do PDF: teoria, fórmulas, exemplos, códigos, exercícios, conclusões e referências.
3. **Escreva em português simples**, como se estivesse explicando para quem está começando. Linguagem direta, sem enrolação.
4. **Pegue todos os exemplos** e explique **linha por linha, passo a passo**, mostrando cada cálculo substituído pelos números e o resultado final.
5. **Códigos Python: reproduza EXATAMENTE como aparecem no PDF.** Não "melhore", não reformate, não mude nomes de variáveis, espaçamento, importações ou marcadores (`'ro'`, `'bd'`, etc.). Se o código do PDF estiver como imagem e o texto vier distorcido na extração, use OCR para recuperá-lo (ex.: PyMuPDF para extrair a imagem + OCR do Windows via `winrt-Windows.Media.Ocr`, ou `rapidocr_onnxruntime`), compare com o contexto matemático e deixe uma nota se reconstruiu algo. Você apenas **explica** cada linha do código (em tabela "linha por linha"), sem alterá-lo.
6. **Verifique todos os cálculos** com Python antes de escrever (ex.: `python -c "..."`) para garantir que os números estão corretos.
6. **Formatação obrigatória:**
   - Fórmulas em **LaTeX** com `$$ ... $$` (bloco) e `$ ... $` (inline).
   - **Tabelas** para resumos de derivadas, valores calculados, ordens da série e erros.
   - Código Python em blocos ```python ... ``` **idênticos ao PDF**, com tabela explicando **linha por linha** o que cada comando faz.
   - **Destaques** (`**negrito**` e blocos `>` ) para conclusões importantes e "regras de ouro".
   - Use vírgula como separador decimal (`0{,}5` dentro de LaTeX, `0,5` no texto).
   - **Sempre inclua como calcular o erro no Python** (se o PDF calcular/prever erro): um bloco de código com a fórmula aplicada (`erro = ((valor_verdadeiro - valor_aproximado) / valor_verdadeiro) * 100`) para cada ordem, explicado passo a passo, com os valores numéricos conferidos e os erros comuns (não confundir derivada com previsão; definir derivadas com `lambda x:` para não dar `TypeError: 'float' object is not callable`).
   - **Inclua os gráficos** (do PDF ou do notebook de apoio, se houver) **logo abaixo do bloco de código que o gera** — nunca em outra parte da seção. O padrão é: código → gráfico → explicação ("linha por linha" / tabela). Se uma seção tem gráfico mas não tem o código de plotagem no PDF (ex.: ordens 2 e 3 só estão no notebook), **acrescente o bloco de código de plotagem** (reproduzido do notebook) imediatamente antes do gráfico e anote que ele vem do notebook. Prefira **embutir as imagens direto no `.md`** como base64 (`![descrição](data:image/png;base64,...)`) para o arquivo ficar autocontido e renderizar em qualquer lugar; como alternativa, salve os `.png` na mesma pasta e referencie por caminho relativo. Extraia as imagens se necessário (ex.: JSON base64 de `.ipynb`, ou renderizando a página do PDF com PyMuPDF). **Após embutir um gráfico no `.md`, apague o `.png` intermediário da pasta** para não deixar arquivos órfãos.
7. **Salve** como `teoria.md` na mesma pasta do PDF.

## Estrutura recomendada do arquivo

1. Título e fonte da aula
2. O que é o assunto (explicação simples)
3. Fórmula(s) geral(is) com legenda das letras
4. Entendendo cada termo/parte
5. Exemplos principais — passo a passo (pontos escolhidos, derivadas, cálculos de cada ordem, erros)
6. Códigos em Python — explicados linha por linha
7. Gráficos do PDF/notebook **imediatamente abaixo do código que os gera** (imagens embutidas em base64; `.png` intermediários apagados da pasta)
8. Como calcular o erro no Python (código + explicação, se o PDF prevê erro)
9. Tabelas-resumo com os resultados e erros
10. Teoria de erro (quando houver)
11. Conclusões importantes
12. Referências bibliográficas do PDF

## Exemplo de estilo (referência)

```
## 8. Aproximação de 2ª ordem (passo a passo)

Agora adicionamos o termo com a **2ª derivada**:

$$
f(x_{i+1}) \approx f(x_i) + f'(x_i)\,h + \frac{f''(x_i)}{2!}\,h^2
$$

Substituindo os números ($h = 0{,}5$, $h^2 = 0{,}25$, $2! = 2$):

$$
f(1) \approx 2{,}9375 + 0 \cdot 0{,}5 + \frac{1{,}5 \cdot 0{,}25}{2}
$$

**Resultado: $f(1) \approx 3{,}125$.** Erro de 3,846% — melhor que os 9,6% da ordem 1.
```

## Importante

- NUNCA pule um exemplo do PDF.
- A **teoria** pode ser "mastigada"/reescrita em linguagem simples; os **códigos Python NÃO podem ser alterados** — devem sair idênticos ao PDF.
- O arquivo deve ser **completo e autocontido**: dá para estudar só pelo `.md`, sem abrir o PDF.
- Cada gráfico fica **logo abaixo do código que o plota**; códigos de plotagem que só existem no notebook são reproduzidos no `.md` (com nota) para o gráfico ter seu código acima.
- **Não deixe arquivos `.png` soltos na pasta** depois de embutir os gráficos no `.md`.
- Se algo não estiver claro no PDF, use seu conhecimento do assunto para preencher e deixe uma nota dizendo o que foi preenchido.

---

# Instrução para transformar PDF de LISTA DE EXERCÍCIOS em notebook (`.ipynb`)

Sempre que houver uma **lista de exercícios em PDF** na pasta `lista`, prepare um **notebook `.ipynb`** (mesma pasta do PDF, mesmo nome base do PDF) com este padrão fixo:

## Passos

1. **Leia o PDF completo** da lista (extraia o texto com PyMuPDF). Se expoentes/índices saírem distorcidos na extração (`e-x`, `25x3`), **use os spans com `get_text('dict')`** (tamanho/posição de fonte) para decodificar sobrescritos/subscritos; se necessário, renderize a página em PNG e use OCR. Deixe uma nota se reconstruiu algo.
2. **Verifique todos os cálculos com Python antes** (`python -c "..."`): valor verdadeiro, aproximação de cada ordem da série e erro relativo percentual $\varepsilon_t$. Confira os números que irão para as respostas.
3. **Estrutura fixa do notebook** (nesta ordem):
   - Nó **MD** com o título da lista + cabeçalho (curso, disciplina, professor) + enunciado geral.
   - Para **cada questão**: um nó **MD** com a questão completa, formatada da forma mais bonita possível, com a **função bem visível** (fórmula em bloco `$$ ... $$` com `\boxed{...}`), os **dados do problema** em lista (ponto-base, ponto de previsão, passo $h$, ordens pedidas) — e, **logo abaixo**, um **nó de código Python em branco** (para o aluno resolver).
   - **Depois de todas as questões**, um **único nó MD de "Respostas"** com **apenas tabelas diretas do que foi pedido**, uma por questão, no formato **ordem | Taylor (valor aproximado) | erro $\varepsilon_t$** — o mais direto possível, sem explicações longas. Inclua apenas uma linha curta com o **valor verdadeiro** (referência para o erro) e, somente quando o enunciado pedir explicitamente, uma frase de discussão. Nada de passo a passo nem textos no nó de respostas.
4. **Células de código ficam vazias** (`source: []`) — o aluno deve implementar a solução.
5. **Formatação obrigatória:** LaTeX com `$$ ... $$`/`$ ... $`, vírgula como separador decimal (`0{,}5`), tabelas para os resultados, `>` para conclusões importantes. **Cuidado em tabelas Markdown:** nunca usar o caractere `|` dentro de fórmulas inline (quebra as colunas) — escrever módulo como `$\vert\varepsilon_t\vert$` em vez de `$|\varepsilon_t|$`.
6. **Salve** como `NomeDaLista.ipynb` na mesma pasta do PDF. Gere o arquivo via `json` (Python) para não errar o escape de `\` do LaTeX; valide o JSON depois (`json.load`).

---

# Notas rápidas (perguntas frequentes)

- **"Tem euler no numpy?" / "como faço euler em código?"** → a resposta é **`np.e`** (a constante $e \approx 2{,}71828$, disponível em `numpy`). Não existe método de Euler pronto no numpy; quando o aluno pergunta isso, ele quer apenas o nome da constante `np.e`, e NÃO uma implementação completa do método. Não despeje o código inteiro do método de Euler — responda só `np.e` (e, se for o caso, explique brevemente o que é a constante).
