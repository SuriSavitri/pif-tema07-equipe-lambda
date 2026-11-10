# ROTEIRO DA APRESENTAÇÃO

**Seminário Técnico: Programação Funcional com Haskell**
Programação Imperativa e Funcional | CESAR School | 2026.2 | Prof. Roni Maciel

**Tema 07:** Tuplas como registros em um boletim escolar
**Integrantes:** Letícia Carvalho, Mateus de Lira, Suri Savitri e Sofia Drunen
**Quem apresenta:** Letícia e Mateus
**Arquivo dos slides:** `Slides_Tema07_Haskell_v2.pptx` 


## SLIDE 1: Capa | Letícia

"Boa Noite! Somos a equipe do Tema 07: tuplas como registros em um boletim escolar."

## SLIDE 2: Equipe | Letícia

"Somos a Letícia, o Mateus, a Suri e a Sofia. "
- "Quem somos e o que cada um fez"
---
## PARTE 1: INTRODUÇÃO | Letícia

### SLIDE 3: O que é uma tupla em Haskell?
- "Uma tupla guarda valores de **tipos diferentes** juntos. O tamanho é fixo, e esse tamanho faz parte do tipo."
- Apontar o exemplo do cartão azul: `("Ana", 8.0, 9.0, 7.0)` tem o tipo `(String, Double, Double, Double)`: nome e três notas, aridade 4.
- "Quem vem de C pode pensar num `struct`, mas com uma diferença: aqui os campos **não têm nome**, são identificados pela posição."
- "E uma função pode devolver vários valores de uma vez, devolvendo uma tupla."

### SLIDE 4: Por que tuplas importam?
Responder as três perguntas do professor, uma por cartão:
1. **Problema que resolve:** "Guardar os dados de uma entidade, como aluno, item ou tarefa, sem precisar declarar um tipo novo."
2. **Por que importa na programação funcional:** "Funções puras recebem e devolvem valores. A tupla devolve vários resultados de uma vez, sem alterar nada, o que em C costuma exigir ponteiro ou variável global."
3. **Onde aplicar:** "Registros pequenos, retornos múltiplos e listas de tuplas como tabelas: boletim, estoque, carrinho."



---

## PARTE 2: INVESTIGAÇÃO TÉCNICA | Mateus

### SLIDE 5: Como extraímos os campos?
- "A sintaxe é o **pattern matching**: o padrão `(n, _, _, _)` separa a tupla já na definição da função."
- Passar pelos três itens da direita:
  1. **Assinatura:** o tipo descreve os quatro campos, na ordem.
  2. **Padrão e curinga:** cada posição vira uma variável; o `_` ignora o campo que não interessa. Mostrar `nome` (pega o primeiro) e `media` (ignora o nome, usa as três notas).
  3. **Pureza e imutabilidade:** "A função só lê a tupla e devolve outro valor. Para mudar uma nota, criamos outra tupla; a original nunca é alterada. É a relação direta com a programação funcional."
- Apontar a faixa do GHCi: `media ("Ana", 8, 9, 7)` → `8.0`.

### SLIDE 6: O que descobrimos no GHCi?
 Mostrar cada descoberta com a saída real do GHCi (ao vivo ou print).

1. **`fst` e `snd` são para pares**. "O tipo de `fst` é `(a, b) -> a`, então exige exatamente dois elementos. Com os 4 campos do boletim dá erro de tipo; só o pattern matching resolve." *Ler em voz alta a mensagem de erro.*
2. **Tuplas muito grandes**. "O GHC aceita tuplas grandes, mas `show` e `==` só existem até 15 elementos. Com 16, a tupla existe mas não dá para imprimir." *Mostrar `:info Show` e o erro da tupla de 16 elementos.*
3. **Campos sem nome**. "Trocar a nota 1 com a nota 2 **compila** e dá resultado errado, porque os dois campos têm o mesmo tipo. O compilador não percebe. Isso nos leva à análise crítica."

---

## PARTE 3: DEMONSTRAÇÃO PRÁTICA | Letícia e Mateus

> **Regra do professor:** anunciar a entrada e a saída esperada **antes** de executar; chamar a turma para prever; fonte legível.

### SLIDE 7: Exemplo 1, o conceito no GHCi
Letícia: "Este arquivo isola o conceito. Antes de cada comando, vocês dizem o que acham que vai sair."

| Mateus digita | A turma prevê | Saída esperada |
|---|---|---|
| `:l Exemplo1.hs` | | carrega sem erro |
| `aluno = ("Ana", 8.0, 9.0, 7.0)` | | |
| `:t fst` | tipo de `fst` | `fst :: (a, b) -> a` |
| `nome aluno` | | `"Ana"` |
| `media aluno` | | `8.0` |
| `fst aluno` | funciona? | **erro de tipo** (ler a mensagem: tupla de 4 no lugar de um par) |

### SLIDE 8: Exemplo 2, o boletim de uma turma
Letícia: "Agora a aplicação: uma turma de cinco alunos." Mostrar o código da turma, **pedir previsão** da tabela da direita e só então executar.

| Mateus digita | Saída esperada |
|---|---|
| `:l Aplicacao.hs` | carrega sem erro |
| `boletim turma` | Ana 8.0 aprovado; Bruno 5.0 final; Carla 9.0 aprovado; Davi 3.0 final; Eva 2.0 reprovado |
| `melhorAluno turma` | `("Carla",9.0)` |

Explicar o funcionamento: `boletim` percorre a lista e, para cada tupla, devolve nome, média e situação; `melhorAluno` devolve **nome e média juntos** numa tupla.

### SLIDE 9: As cinco funções e os casos limite
- Mostrar as cinco assinaturas (`nome`, `media`, `situacao`, `boletim`, `melhorAluno`).
- Executar os **casos limite**, com a previsão da turma antes:

| Digitar | Saída esperada |
|---|---|
| `situacao 7.0` | `"aprovado"` |
| `situacao 3.0` | `"final"` |
| `situacao 2.9` | `"reprovado"` |
| `melhorAluno []` | erro claro (explicar a decisão da equipe) |
| turma com empate de médias | regra da equipe (explicar qual) |

- Executar a **função de extensão** da equipe e ler a assinatura em voz alta.
- Dizer a **situação prática** exigida: "O `melhorAluno` devolve nome e média numa única tupla, sem variável global e sem ponteiro. É exatamente o tipo de problema em que a tupla é útil."


## PARTE 4: ANÁLISE CRÍTICA | Mateus

### SLIDE 10: Tupla em Haskell × struct em C
- Apontar o código em C (`a.n1`, campos com nome, reatribuíveis) e o `media` em Haskell (pela posição, imutável).
- Percorrer a tabela: **acesso** pelo nome × pela posição; **mutabilidade** `a.n1 = 10` × criamos outra tupla; **declaração** exige `struct` × nenhuma.

### SLIDE 11: Vale a pena usar tuplas?
Passar pelos quatro quadrantes, um por vez:
- **Vantagens:** sintaxe curta, extração legível, ótima para retornos múltiplos.
- **Dificuldades:** *(trocar pelo que a equipe realmente enfrentou)* assinatura longa repetida, erros de tipo `Int` × `Double` e tupla × lista, recursão em `boletim` e `melhorAluno`.
- **Limitações:** campos sem nome, novo campo muda tudo, `fst`/`snd` só para pares.
- **Quando vale usar:** registros pequenos e de vida curta; com muitos campos, dar nomes a eles.


### SLIDE 12: Desafio rápido, qual assinatura é válida?
"Uma função `primeiraNota` recebe um aluno do boletim e devolve a primeira nota. Qual assinatura é válida? Voto de mão levantada: A, B, C ou D." *Chamar um ou dois colegas para justificar o voto.*

### SLIDE 13: Duas perguntas para a turma
1. "`fst` funciona com uma tupla de quatro campos? Por quê?"
2. "Em que momento a tupla deixa de ser uma boa escolha para um registro?"



### SLIDE 14: Respostas e discussão
- **Desafio, alternativa B:** descreve a tupla de 4 campos. A usa uma lista com tipos misturados (inválido), C usa um tipo `Tuple` que não existe e D tem só 3 campos.
- **Pergunta 1:** não. `fst :: (a, b) -> a` só aceita pares; para 4 campos usamos pattern matching.
- **Pergunta 2:** quando há muitos campos, campos do mesmo tipo fáceis de confundir ou o registro circula por muitas funções. Aí vale dar nome aos campos.



---

## ENCERRAMENTO E PERGUNTAS | Letícia

### SLIDE 15: O que levamos do tema
1. A tupla é um registro leve: campos de tipos diferentes, extraídos por pattern matching.
2. `fst` e `snd` servem só para pares, e os campos não têm nome: isso cobra seu preço.
3. Boa para registros pequenos; com muitos campos, vale dar nomes a eles.



### SLIDE 16: Referências

