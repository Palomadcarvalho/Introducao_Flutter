# Reflexão — Lab 07: Introdução ao Flutter
## LAMD 60445 — Engenharia de Software — PUC Minas
**Professor:** Cristiano de Macedo Neto  
**Aluno(a):**  
**Matrícula:**  
**Data de entrega:**  

---

> **Instruções de preenchimento**
>
> Cada resposta deve conter **dois elementos obrigatórios**:
> 1. **Evidência observada** — cite o que você viu no terminal, na interface ou no código durante a execução. Exemplos: latências do smoke_test.py, comportamento do hot reload, mensagem de erro exibida, status HTTP retornado.
> 2. **Conexão teórica** — relacione a evidência a um conceito acadêmico com referência bibliográfica. Use o formato: (AUTOR, Ano, p./Cap. X) ou (AUTOR, Ano).
>
> Respostas sem evidência ou sem referência receberão pontuação parcial.

---

## Seção 1 — Arquitetura de Widgets

### Q1 — StatelessWidget vs. StatefulWidget

**Pergunta:** Explique com suas palavras a diferença entre `StatelessWidget` e `StatefulWidget`, usando como exemplo os widgets criados nas Atividades 4.1 e 4.2. Em qual situação o uso de `setState` se torna uma limitação arquitetural?

**Evidência observada:**
> _Descreva aqui o que você observou ao interagir com o ContadorWidget e os ItemCard. O que mudava? O que permanecia estável? O rebuild do contador afetou os cards ao redor?_

```
[Cole aqui uma captura de terminal ou descrição do comportamento observado]
```

**Conexão teórica:**
> _Relacione ao conceito de imutabilidade e reatividade. Consulte: BAILEY, 2023, Cap. 3 — Widgets e State._

**Resposta:**

---

### Q2 — Desempenho do rebuild

**Pergunta:** A árvore de widgets do Flutter é reconstruída a cada chamada de `setState`. Por que isso não causa problema de desempenho na maioria dos casos? Que mecanismo interno do Flutter evita reconstruções desnecessárias?

**Evidência observada:**
> _Ao pressionar o botão do ContadorWidget múltiplas vezes rapidamente, a interface travou? O framerate caiu? O que o Flutter Developer Tools mostraria se você o abrisse?_

```
[Cole aqui observação sobre o comportamento de desempenho]
```

**Conexão teórica:**
> _Pesquise sobre a "Element Tree" e o algoritmo de reconciliação ("diffing") do Flutter. Consulte: Flutter Team. Flutter architectural overview. docs.flutter.dev, 2026._

**Resposta:**

---

### Q3 — Flutter e o padrão MVC

**Pergunta:** Compare o modelo de widgets do Flutter com o padrão MVC discutido na Unidade 3 (Lab 05). O que corresponderia ao Model, View e Controller no código que você desenvolveu?

**Evidência observada:**
> _Identifique no seu projeto: qual arquivo contém a lógica de negócio? Qual contém a apresentação visual? Existe um arquivo que medeia os dois?_

```
[Mapeamento: Model → ___, View → ___, Controller/Presenter → ___]
```

**Conexão teórica:**
> _Consulte: MARTIN, Robert C. Arquitetura Limpa. Alta Books, 2019, Cap. 22 — The Clean Architecture. Compare com a separação MVC descrita no roteiro da Unidade 3._

**Resposta:**

---

## Seção 2 — Comunicação Distribuída

### Q4 — Future, async/await e o FutureBuilder

**Pergunta:** O método `listarItens()` retorna um `Future<List<Item>>`. O que aconteceria com a interface se a chamada HTTP bloqueasse a thread principal (UI thread)? Como o `FutureBuilder` resolve esse problema?

**Evidência observada:**
> _Observe as latências registradas pelo smoke_test.py (coluna entre parênteses). Imagine que a UI ficasse congelada durante esse tempo. O que o usuário veria? O que o FutureBuilder exibe durante o estado `ConnectionState.waiting`?_

```
Latências observadas no smoke_test.py:
  GET /health:  _____ ms
  GET /itens:   _____ ms
  POST /itens:  _____ ms
```

**Conexão teórica:**
> _Dart usa um modelo de single-threaded event loop, similar ao JavaScript. Consulte: Dart Team. Asynchronous programming: futures, async, await. dart.dev, 2026. Compare com o asyncio do Python estudado no Lab 04._

**Resposta:**

---

### Q5 — HTTP vs. gRPC para clientes móveis

**Pergunta:** Compare a comunicação HTTP/REST implementada neste laboratório com a comunicação gRPC do Lab 03. Quais são as vantagens e desvantagens de cada abordagem para um sistema distribuído com clientes móveis?

**Evidência observada:**
> _No Lab 03, como era feita a serialização dos dados? Neste lab, como os dados são transmitidos? Qual é a diferença no payload (tamanho, legibilidade)?_

```
[Cole aqui um trecho do JSON recebido do Flask e compare com o formato Protobuf do Lab 03]
```

**Conexão teórica:**
> _Consulte: Birrell, A. D.; Nelson, B. J. Implementing Remote Procedure Calls. ACM Transactions on Computer Systems, 1984. Para REST: Fielding, R. T. Architectural Styles and the Design of Network-based Software Architectures. PhD Thesis, UC Irvine, 2000._

**Resposta:**

---

### Q6 — O endereço 10.0.2.2 e as Falácias de Deutsch

**Pergunta:** O código `ItemService` usa `10.0.2.2` para o emulador Android. O que esse detalhe revela sobre as diferenças de ambiente de rede entre emuladores e dispositivos físicos? Como isso se relaciona com as Falácias da Computação Distribuída de Deutsch (1994)?

**Evidência observada:**
> _O que acontece se você usar `localhost` em um emulador Android em vez de `10.0.2.2`? Tente e registre o erro produzido pelo FutureBuilder._

```
Erro observado ao usar localhost no emulador:
[Cole aqui a mensagem de erro exibida na interface Flutter]

Endereço correto para emulador: 10.0.2.2:5000
Endereço correto para dispositivo físico: ___.___.___.___ (IP da máquina na rede)
```

**Conexão teórica:**
> _Consulte: Deutsch, L. Peter. The Eight Fallacies of Distributed Computing. Sun Microsystems, 1994. Identifique especificamente qual(is) falácia(s) este problema ilustra._

**Resposta:**

---

## Seção 3 — Serialização e Contratos de API

### Q7 — Serialização manual: fragilidade e alternativas

**Pergunta:** A classe `Item` implementa `fromJson` e `toJson` manualmente. Por que a serialização manual é considerada frágil em projetos grandes? Que alternativas o ecossistema Dart oferece para geração automática de código de serialização?

**Evidência observada:**
> _Adicione um campo extra no JSON retornado pelo Flask (ex.: `"estoque": 10`) sem atualizar a classe `Item` no Flutter. O que acontece? Adicione um campo ao `fromJson` com um typo intencional (ex.: `json['nomeee']`) e observe o erro em runtime._

```
Comportamento observado ao adicionar campo não mapeado no JSON:
[Descreva o que aconteceu — erro em runtime? Silêncio? Campo ignorado?]
```

**Conexão teórica:**
> _Pesquise os pacotes `json_serializable` e `freezed` no pub.dev. Compare a abordagem de geração de código com a serialização manual. Consulte: pub.dev/packages/json_serializable._

**Resposta:**

---

### Q8 — Versionamento de API e compatibilidade de contratos

**Pergunta:** O que aconteceria se a API Flask adicionasse um novo campo **obrigatório** ao JSON de resposta sem atualizar o cliente Flutter? Que estratégia de versionamento de API minimizaria esse risco?

**Evidência observada:**
> _Modifique o `app.py` para adicionar um campo `"descricao"` como obrigatório no `POST /itens`. Execute o formulário Flutter sem alterar o payload. Registre o que acontece._

```
Comportamento observado (app.py modificado, Flutter inalterado):
Status HTTP retornado: ___
Mensagem de erro exibida ao usuário: ___
```

**Conexão teórica:**
> _Consulte: Richardson, Leonard; Amundsen, Mike. RESTful Web APIs. O'Reilly, 2013. Cap. sobre evolução de API. Pesquise também sobre Semantic Versioning (semver.org) aplicado a contratos REST._

**Resposta:**

---

## Seção 4 — Reflexão Arquitetural

### Q9 — Limites do setState em escala

**Pergunta:** Considere uma aplicação com 10 telas e 5 serviços diferentes que compartilham dados (ex.: carrinho de compras visível em 3 telas diferentes). Por que o `setState` tornaria-se problemático como mecanismo de gerenciamento de estado? Descreva brevemente como Provider ou BLoC resolveria esse problema.

**Evidência observada:**
> _No código atual, tente fazer o ContadorWidget exibir o número de itens listados pela `ListaItensScreen`. O que você precisaria fazer para compartilhar esse estado? Quantas camadas da árvore de widgets precisariam ser modificadas?_

```
Para compartilhar estado entre ContadorWidget e ListaItensScreen usando apenas setState,
eu precisaria:
  1. ___
  2. ___
  3. ___
Número de arquivos que precisariam ser alterados: ___
```

**Conexão teórica:**
> _Consulte: MARTIN, Robert C. Arquitetura Limpa. Alta Books, 2019. Cap. 17 — Boundaries. Pesquise: pub.dev/packages/provider e o padrão BLoC (Business Logic Component) em bloclibrary.dev._

**Resposta:**

---

### Q10 — Jornada completa do dado e transparência de acesso

**Pergunta:** Trace a jornada completa de um dado desde o armazenamento no servidor Flask até a exibição no widget `ItemCard`. Mapeie cada etapa e identifique: onde ocorre serialização, onde ocorre comunicação de rede, onde ocorre renderização. Como esse mapeamento se relaciona com o conceito de **transparência de acesso** em sistemas distribuídos?

**Evidência observada:**
> _Execute o smoke_test.py e observe o JSON retornado. Execute o app Flutter e observe o ItemCard renderizado. O usuário final sabe que os dados vieram de uma rede? Que "magia" oculta esse detalhe?_

```
Jornada do dado — rastreamento passo a passo:

1. [Flask]       _itens: dict  →  jsonify()            [serialização Python→JSON]
2. [Rede]        HTTP/1.1 200  →  body: bytes           [transmissão TCP/IP]
3. [Dart]        response.body →  jsonDecode()          [desserialização JSON→Map]
4. [Dart]        Map<String, dynamic> → Item.fromJson() [mapeamento para objeto]
5. [Flutter]     List<Item>    →  FutureBuilder.builder [injeção na árvore de widgets]
6. [Flutter]     Item          →  ItemCard.build()      [renderização → pixels]

Etapa de serialização: passos ___ e ___
Etapa de comunicação de rede: passo ___
Etapa de renderização: passo ___
```

**Conexão teórica:**
> _Transparência de acesso é definida por Coulouris et al. como a capacidade de ocultar diferenças na representação de dados e na invocação de recursos remotos. Consulte: Coulouris, G. et al. Distributed Systems: Concepts and Design. 5. ed. Addison-Wesley, 2011, Cap. 1._

**Resposta:**

---

## Seção 5 — Avaliação do Laboratório (Opcional)

> Esta seção é anônima e serve para melhoria contínua da disciplina. Sua resposta não afeta a nota.

**Q11 — Dificuldade:** O nível de dificuldade do laboratório foi:
- [ ] Muito fácil
- [ ] Adequado
- [ ] Muito difícil

**Q12 — Clareza do roteiro:** As instruções foram suficientemente claras?
- [ ] Sim, totalmente
- [ ] Em sua maioria, com algumas dúvidas
- [ ] Não, precisei de muita ajuda externa

**Q13 — Atividade mais relevante:** Qual atividade contribuiu mais para seu aprendizado?

> _Sua resposta:_

**Q14 — Sugestão:** Há alguma melhoria que você sugere para este laboratório?

> _Sua resposta:_

---

## Referências Utilizadas nesta Reflexão

> Liste abaixo todas as fontes que você consultou para responder às questões acima. Use o formato ABNT.

1. BAILEY, Thomas. *Flutter for beginners*. 3. ed. Birmingham: Packt, 2023.
2. MARTIN, Robert C. *Arquitetura limpa*. Rio de Janeiro: Alta Books, 2019.
3. DEUTSCH, L. Peter. *The Eight Fallacies of Distributed Computing*. Sun Microsystems, 1994.
4. FLUTTER TEAM. *Flutter architectural overview*. Disponível em: https://docs.flutter.dev/resources/architectural-overview. Acesso em: abr. 2026.
5. DART TEAM. *Asynchronous programming: futures, async, await*. Disponível em: https://dart.dev/codelabs/async-await. Acesso em: abr. 2026.
6. _(adicione outras referências que utilizou)_

---

*Laboratório 07 — LAMD 60445 — PUC Minas — 1º Semestre 2026*
