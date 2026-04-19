# Reflexão — Lab 07: Introdução ao Flutter
## LAMD 60445 — Engenharia de Software — PUC Minas
**Professor:** Cristiano de Macedo Neto  

**Aluno(a):**  Paloma Dias de Carvalho

**Matrícula:**  587900

**Data de entrega:**  19/04/2026

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
Durante o laboratório ficou clara a diferença na prática: o ItemCard é um StatelessWidget porque ele só exibe informações que recebe prontas, nome e preço, sem precisar guardar nenhum estado interno. Já a ListaItensScreen precisou ser StatefulWidget porque ela busca dados da rede, armazena a lista e precisa se atualizar quando o usuário clica em refresh ou adiciona um item novo.

**Conexão teórica:**
> _Relacione ao conceito de imutabilidade e reatividade. Consulte: BAILEY, 2023, Cap. 3 — Widgets e State._

**Resposta:**
O setState começa a ser uma limitação quando a aplicação cresce. Se duas telas diferentes precisam do mesmo dado, não tem como compartilhar esse estado de forma simples, cada widget vive no seu próprio mundo. Como Bailey (2023, Cap. 6) explica, o setState é adequado para estado local e simples, mas se torna um problema arquitetural em aplicações com múltiplas fontes de dados e telas interdependentes.

---

### Q2 — Desempenho do rebuild

**Pergunta:** A árvore de widgets do Flutter é reconstruída a cada chamada de `setState`. Por que isso não causa problema de desempenho na maioria dos casos? Que mecanismo interno do Flutter evita reconstruções desnecessárias?

**Evidência observada:**
> _Ao pressionar o botão do ContadorWidget múltiplas vezes rapidamente, a interface travou? O framerate caiu? O que o Flutter Developer Tools mostraria se você o abrisse?_

O Flutter não redesenha tudo do zero a cada setState. Ele mantém uma árvore interna de elementos e compara o que mudou com o que estava antes, atualizando só o necessário. Na prática, durante o lab, percebi que ao clicar em refresh apenas a lista era atualizada, sem piscar a AppBar ou qualquer outro elemento da tela.

**Conexão teórica:**
> _Pesquise sobre a "Element Tree" e o algoritmo de reconciliação ("diffing") do Flutter. Consulte: Flutter Team. Flutter architectural overview. docs.flutter.dev, 2026._

**Resposta:**

Segundo a Flutter Team (2026), o framework mantém três árvores paralelas, Widget, Element e RenderObject, e o processo de reconciliação garante que apenas os nós realmente alterados sejam reconstruídos, tornando o mecanismo eficiente mesmo em
interfaces complexas.

---

### Q3 — Flutter e o padrão MVC

**Pergunta:** Compare o modelo de widgets do Flutter com o padrão MVC discutido na Unidade 3 (Lab 05). O que corresponderia ao Model, View e Controller no código que você desenvolveu?

**Evidência observada:**
> _Identifique no seu projeto: qual arquivo contém a lógica de negócio? Qual contém a apresentação visual? Existe um arquivo que medeia os dois?_

```
Olhando para o código, dá para enxergar o MVC claramente: a classe Item e o ItemService funcionam como o Model, guardando os dados e a lógica de comunicação. Os widgets como ItemCard e ListaItensScreen são a View, responsáveis só por mostrar as informações. E os métodos _recarregar() e _salvar() fazem o papel do Controller, reagindo às ações do usuário e orquestrando as
atualizações.
```

**Conexão teórica:**
> _Consulte: MARTIN, Robert C. Arquitetura Limpa. Alta Books, 2019, Cap. 22 — The Clean Architecture. Compare com a separação MVC descrita no roteiro da Unidade 3._

**Resposta:**

Martin (2019, Cap. 22) reforça que essa separação de responsabilidades é fundamental para manter o código testável e sustentável a longo prazo.
---

## Seção 2 — Comunicação Distribuída

### Q4 — Future, async/await e o FutureBuilder

**Pergunta:** O método `listarItens()` retorna um `Future<List<Item>>`. O que aconteceria com a interface se a chamada HTTP bloqueasse a thread principal (UI thread)? Como o `FutureBuilder` resolve esse problema?

**Evidência observada:**
> _Observe as latências registradas pelo smoke_test.py (coluna entre parênteses). Imagine que a UI ficasse congelada durante esse tempo. O que o usuário veria? O que o FutureBuilder exibe durante o estado `ConnectionState.waiting`?_

```
Se a chamada HTTP bloqueasse a thread principal, o app simplesmente travaria enquanto esperasse a resposta do servidor, o usuário ficaria olhando para uma tela congelada sem poder fazer nada. O FutureBuilder resolve isso da seguinte forma: ele mostra um indicador de carregamento enquanto aguarda e atualiza a tela automaticamente quando os dados chegam, sem travar nada.
```

**Conexão teórica:**
> _Dart usa um modelo de single-threaded event loop, similar ao JavaScript. Consulte: Dart Team. Asynchronous programming: futures, async, await. dart.dev, 2026. Compare com o asyncio do Python estudado no Lab 04._

**Resposta:**

Bailey (2023, Cap. 6) destaca que toda comunicação de rede em Dart é intrinsecamente assíncrona através de Futures, e o FutureBuilder integra essa assincronicidade diretamente ao ciclo de vida da interface.
---

### Q5 — HTTP vs. gRPC para clientes móveis

**Pergunta:** Compare a comunicação HTTP/REST implementada neste laboratório com a comunicação gRPC do Lab 03. Quais são as vantagens e desvantagens de cada abordagem para um sistema distribuído com clientes móveis?

**Evidência observada:**
> _No Lab 03, como era feita a serialização dos dados? Neste lab, como os dados são transmitidos? Qual é a diferença no payload (tamanho, legibilidade)?_

```
Na prática, o HTTP com JSON foi bem mais simples de implementar e de depurar, dava para ver a resposta direto no terminal. O gRPC que usamos no Lab 03 é mais eficiente em termos de tamanho de dados e velocidade, mas exige mais configuração e o contrato precisa ser definido antes com Protocol Buffers. Para um app móvel consumindo uma API pública, o REST é mais prático. Para comunicação interna entre serviços com alto volume, o gRPC leva vantagem.
```

**Conexão teórica:**
> _Consulte: Birrell, A. D.; Nelson, B. J. Implementing Remote Procedure Calls. ACM Transactions on Computer Systems, 1984. Para REST: Fielding, R. T. Architectural Styles and the Design of Network-based Software Architectures. PhD Thesis, UC Irvine, 2000._

**Resposta:**
Essa troca entre simplicidade e desempenho é discutida por Martin (2019, Cap. 22), que recomenda escolher o protocolo de acordo com os requisitos reais do sistema.
---

### Q6 — O endereço 10.0.2.2 e as Falácias de Deutsch

**Pergunta:** O código `ItemService` usa `10.0.2.2` para o emulador Android. O que esse detalhe revela sobre as diferenças de ambiente de rede entre emuladores e dispositivos físicos? Como isso se relaciona com as Falácias da Computação Distribuída de Deutsch (1994)?

**Evidência observada:**
> _O que acontece se você usar `localhost` em um emulador Android em vez de `10.0.2.2`? Tente e registre o erro produzido pelo FutureBuilder._

```
Esse detalhe do 10.0.2.2 me chamou atenção. No emulador funcionou perfeitamente, mas se eu tentasse rodar no meu celular físico, não funcionaria — porque o celular não sabe o que é o "localhost" do meu computador. Precisaria usar o IP real da máquina na rede Wi-Fi.
```

**Conexão teórica:**
> _Consulte: Deutsch, L. Peter. The Eight Fallacies of Distributed Computing. Sun Microsystems, 1994. Identifique especificamente qual(is) falácia(s) este problema ilustra._

**Resposta:**
Isso ilustra bem a primeira falácia de Deutsch (1994): "a rede é confiável e transparente". Na prática, pequenos detalhes de ambiente como esse podem quebrar completamente a comunicação entre sistemas distribuídos, provando que a transparência de rede nunca deve ser assumida.
---

## Seção 3 — Serialização e Contratos de API

### Q7 — Serialização manual: fragilidade e alternativas

**Pergunta:** A classe `Item` implementa `fromJson` e `toJson` manualmente. Por que a serialização manual é considerada frágil em projetos grandes? Que alternativas o ecossistema Dart oferece para geração automática de código de serialização?

**Evidência observada:**
> _Adicione um campo extra no JSON retornado pelo Flask (ex.: `"estoque": 10`) sem atualizar a classe `Item` no Flutter. O que acontece? Adicione um campo ao `fromJson` com um typo intencional (ex.: `json['nomeee']`) e observe o erro em runtime._

```
A serialização manual funciona bem para projetos pequenos como esse do lab, mas em um projeto com dezenas de modelos se torna arriscada. Qualquer mudança no JSON da API exige atualizar o fromJson na mão, e é fácil esquecer um campo ou errar o tipo.
```

**Conexão teórica:**
> _Pesquise os pacotes `json_serializable` e `freezed` no pub.dev. Compare a abordagem de geração de código com a serialização manual. Consulte: pub.dev/packages/json_serializable._

**Resposta:**

O Dart Team (2026) recomenda pacotes como json_serializable que geram esse código automaticamente a partir de anotações, eliminando esse risco e garantindo consistência entre o contrato da API e os modelos do cliente.
---

### Q8 — Versionamento de API e compatibilidade de contratos

**Pergunta:** O que aconteceria se a API Flask adicionasse um novo campo **obrigatório** ao JSON de resposta sem atualizar o cliente Flutter? Que estratégia de versionamento de API minimizaria esse risco?

**Evidência observada:**
> _Modifique o `app.py` para adicionar um campo `"descricao"` como obrigatório no `POST /itens`. Execute o formulário Flutter sem alterar o payload. Registre o que acontece._

```
Se a API adicionasse um campo obrigatório sem avisar, o app poderia quebrar na hora de fazer o parse do JSON ou retornar dados errados sem dar nenhum erro visível. A forma de evitar isso é versionar a API — usando /v1/itens e /v2/itens por exemplo, assim clientes antigos continuam funcionando normalmente enquanto os novos adotam o contrato atualizado.
```

**Conexão teórica:**
> _Consulte: Richardson, Leonard; Amundsen, Mike. RESTful Web APIs. O'Reilly, 2013. Cap. sobre evolução de API. Pesquise também sobre Semantic Versioning (semver.org) aplicado a contratos REST._

**Resposta:**
Martin (2019, Cap. 22) trata esse problema como parte do design de interfaces estáveis em arquiteturas distribuídas, onde mudanças
de contrato devem ser graduais e retrocompatíveis.
---

## Seção 4 — Reflexão Arquitetural

### Q9 — Limites do setState em escala

**Pergunta:** Considere uma aplicação com 10 telas e 5 serviços diferentes que compartilham dados (ex.: carrinho de compras visível em 3 telas diferentes). Por que o `setState` tornaria-se problemático como mecanismo de gerenciamento de estado? Descreva brevemente como Provider ou BLoC resolveria esse problema.

**Evidência observada:**
> _No código atual, tente fazer o ContadorWidget exibir o número de itens listados pela `ListaItensScreen`. O que você precisaria fazer para compartilhar esse estado? Quantas camadas da árvore de widgets precisariam ser modificadas?_

```
Com poucas telas o setState é suficiente, mas imaginando 10 telas e 5 serviços diferentes, compartilhar estado entre elas exigiria passar dados por construtores em cascata — o que fica inviável rapidamente. O Provider resolve isso colocando
o estado em um lugar central acessível por qualquer widget da árvore. O BLoC vai além, separando completamente a lógica de negócio da interface usando streams, o que facilita muito os testes automatizados.
```

**Conexão teórica:**
> _Consulte: MARTIN, Robert C. Arquitetura Limpa. Alta Books, 2019. Cap. 17 — Boundaries. Pesquise: pub.dev/packages/provider e o padrão BLoC (Business Logic Component) em bloclibrary.dev._

**Resposta:**
Bailey (2023, Cap. 6) aponta que para aplicações com múltiplas fontes de dados assíncronos, soluções como Provider e BLoC são as recomendadas pela comunidade Flutter.
---

### Q10 — Jornada completa do dado e transparência de acesso

**Pergunta:** Trace a jornada completa de um dado desde o armazenamento no servidor Flask até a exibição no widget `ItemCard`. Mapeie cada etapa e identifique: onde ocorre serialização, onde ocorre comunicação de rede, onde ocorre renderização. Como esse mapeamento se relaciona com o conceito de **transparência de acesso** em sistemas distribuídos?

**Evidência observada:**
> _Execute o smoke_test.py e observe o JSON retornado. Execute o app Flutter e observe o ItemCard renderizado. O usuário final sabe que os dados vieram de uma rede? Que "magia" oculta esse detalhe?_

```
Acompanhando o caminho do dado durante o laboratório:
- O Flask armazena o item no servidor
- O ItemService dispara um GET via HTTP — aqui acontece a comunicação de rede
- A resposta JSON chega e o fromJson transforma em objeto Dart — aqui é a serialização
- O FutureBuilder repassa a lista para o ListView
- O ItemCard renderiza nome e preço na tela — aqui é a renderização
```

**Conexão teórica:**
> _Transparência de acesso é definida por Coulouris et al. como a capacidade de ocultar diferenças na representação de dados e na invocação de recursos remotos. Consulte: Coulouris, G. et al. Distributed Systems: Concepts and Design. 5. ed. Addison-Wesley, 2011, Cap. 1._

**Resposta:**
O que me pareceu interessante é que o ItemCard não faz ideia de que os dados vieram de uma rede — ele só recebe nome e preço e exibe. Isso é exatamente a transparência de acesso que os sistemas distribuídos buscam: esconder a complexidade da comunicação de quem só precisa usar o dado (FLUTTER TEAM, 2026).
---

## Seção 5 — Avaliação do Laboratório (Opcional)

> Esta seção é anônima e serve para melhoria contínua da disciplina. Sua resposta não afeta a nota.

**Q11 — Dificuldade:** O nível de dificuldade do laboratório foi:
- [ ] Muito fácil
- [ ] Adequado
- [X] Muito difícil

**Q12 — Clareza do roteiro:** As instruções foram suficientemente claras?
- [ ] Sim, totalmente
- [X] Em sua maioria, com algumas dúvidas
- [ ] Não, precisei de muita ajuda externa

**Q13 — Atividade mais relevante:** Qual atividade contribuiu mais para seu aprendizado?

> _Sua resposta:_

**Q14 — Sugestão:** Há alguma melhoria que você sugere para este laboratório?

> _Sua resposta:_ Atividades maiores como esta, o ideal seria dividir em duas semanas.

---

## Referências Utilizadas nesta Reflexão

> Liste abaixo todas as fontes que você consultou para responder às questões acima. Use o formato ABNT.

BAILEY, Thomas. **Flutter for Beginners**. 3. ed. Birmingham: Packt, 2023.

MARTIN, Robert C. **Arquitetura Limpa**. Rio de Janeiro: Alta Books, 2019.

FLUTTER TEAM. **Flutter architectural overview**. Disponível em:
https://docs.flutter.dev/resources/architectural-overview. Acesso em: abr. 2026.

DART TEAM. **A tour of the Dart language**. Disponível em:
https://dart.dev/guides/language/language-tour. Acesso em: abr. 2026.

DEUTSCH, L. Peter. **The eight fallacies of distributed computing**. Sun Microsystems, 1994.

---

*Laboratório 07 — LAMD 60445 — PUC Minas — 1º Semestre 2026*
