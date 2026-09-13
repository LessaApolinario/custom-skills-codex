---
name: react-test-scaffolder
description: Analise projetos React ou Next.js e crie, organize, atualize ou revise scaffolding de testes unitários e de integração com Jest/Vitest, sem implementar testes funcionais. Use para planejar casos, criar skeletons vazios, complementar suites, apontar mocks e sugerir melhorias; não use para escrever assertions, E2E ou suites completas.
---

# React Test Scaffolder

Produza uma suite de testes planejada, organizada e pronta para implementação posterior. O resultado desta skill são arquivos e cenários vazios documentados, nunca testes funcionais.

## Regra inviolável

Não implemente testes, mesmo quando o pedido usar expressões como "crie os testes", "adicione os testes que faltam" ou "revise os testes". Nesta skill, essas expressões autorizam apenas scaffolding.

Não adicione assertions, chamadas de `render` ou `renderHook`, interações com `userEvent`, mocks funcionais, fixtures executáveis ou setup real dentro de cenários novos. Não converta um skeleton existente em teste implementado.

Se o usuário pedir explicitamente implementação completa, explique que esta skill cobre somente scaffolding e não interprete o pedido como autorização para preencher os cenários.

## Prioridades

Resolva conflitos nesta ordem:

1. segurança e preservação de testes implementados;
2. convenções reais do projeto;
3. comportamento que existe no código atual;
4. qualidade e relevância dos cenários;
5. padrões desta skill.

O código atual completo é a fonte da verdade. Reanalise o projeto a cada execução; não limite a análise ao diff, aos arquivos modificados ou ao que foi observado em execuções anteriores.

## Fluxo

### 1. Detectar o projeto

Inspecione `package.json`, lockfiles, configurações, estrutura de diretórios e código para identificar:

- React, React com Vite ou Next.js;
- JavaScript ou TypeScript;
- package manager;
- App Router quando aplicável;
- organização geral do código e dos testes;
- raiz de testes existente e separação atual entre testes unitários e de integração.

Analise principalmente `.ts`, `.tsx`, `.js` e `.jsx`, ignorando dependências, builds, cobertura, artefatos gerados e outros diretórios excluídos pelo projeto.

### 2. Detectar o ambiente de testes

Antes de criar ou modificar qualquer arquivo, identifique pelo menos:

- Jest, Vitest ou outro runner já configurado;
- React Testing Library e bibliotecas auxiliares instaladas;
- setup files e configuração de ambiente;
- uso global ou importado de `describe`, `it`/`test`, `expect` e hooks;
- APIs de mock do runner, apenas para descrevê-las, nunca para implementá-las.

Use `package.json`, arquivos de configuração, scripts e imports de testes existentes como evidência conjunta. No Vitest, verifique inclusive `globals: true`; no Jest, siga a configuração e o padrão real do projeto.

Se nenhum runner estiver configurado:

1. não instale ou configure dependências;
2. não crie nem modifique arquivos;
3. informe que Jest, Vitest ou ambiente equivalente precisa ser configurado;
4. encerre a execução.

Não escolha ou migre runners automaticamente.

### 3. Detectar convenções

Mapeie todos os testes existentes e determine a convenção predominante para:

- `.test` ou `.spec`;
- `it` ou `test`;
- colocação próxima ao código, `tests`, `__tests__` ou outra estrutura;
- imports do runner e uso de globals;
- quote style, semicolons, imports, nested `describe` e setup;
- extensão coerente com o projeto e a unidade testada.

Leia por inteiro cada arquivo de teste antes de alterá-lo. Diferencie testes implementados de skeletons, relacione-os ao código de produção e detecte equivalência semântica entre cenários.

Se o runner estiver configurado, mas não houver nenhum arquivo de teste, pergunte ao usuário se prefere `.test` ou `.spec` antes de criar arquivos. Se também não houver evidência para a raiz de testes, solicite essa convenção na mesma interação. Não decida arbitrariamente entre `tests`, `__tests__` ou outra raiz. Na ausência de evidência somente para `it` versus `test`, prefira `it`.

### 4. Classificar e organizar os testes

Classifique cada arquivo planejado pelo comportamento que ele valida, não pelo tamanho do arquivo ou pela quantidade de imports.

Use **unit** quando o objetivo principal for validar uma unidade isolada e suas dependências externas puderem ser substituídas. Isso inclui, conforme o comportamento real:

- componentes isolados;
- custom hooks;
- providers e contexts isolados;
- reducers, validators, formatters, helpers e utils;
- funções de transformação e pequenas regras de negócio.

Use **integration** quando o comportamento importante depender da colaboração real entre múltiplas unidades internas, por exemplo:

- page + provider + components;
- form + hook + context;
- provider + consumer;
- parent + child behavior;
- Route Handler + service/repository, quando apropriado;
- fluxo de autenticação ou criação, edição e remoção de entidade;
- estado compartilhado e comportamento emergente da colaboração.

Um componente com imports não é automaticamente integração. Pergunte internamente:

- O comportamento pode ser validado isoladamente, substituindo limites externos? Provavelmente é unitário.
- O comportamento só faz sentido com duas ou mais partes reais colaborando? Provavelmente é integração.

Não duplique a mesma cobertura nas duas categorias. Um unit test pode validar que `FriendForm` chama `onSubmit`; um integration test só se justifica se validar algo mais amplo, como atualizar o provider e exibir o novo item na lista. Nem toda feature precisa de integração.

Não crie E2E com Playwright, Cypress ou Selenium. Nesta versão, integration tests continuam executando na stack Jest/Vitest instalada.

#### Escolher diretórios

Decida a organização nesta ordem:

1. convenção de testes existente;
2. arquitetura da aplicação;
3. separação `unit`/`integration`;
4. categorias semânticas;
5. padrão default da skill.

Quando o projeto já possuir uma organização clara, preserve-a. Não imponha a estrutura recomendada nem crie uma codebase híbrida sem consentimento.

Em projeto novo ou estrutura de testes vazia, depois de detectar ou confirmar `<test-root>`, prefira:

```text
<test-root>/
  unit/
    components/
    hooks/
    providers/
    utils/
  integration/
    components/
    pages/
    routes/
    flows/
```

Crie somente diretórios que receberão arquivos. Para React, `pages` e `routes` normalmente não são necessários; para Next.js, use-os quando o comportamento justificar.

Escolha a subcategoria semanticamente:

- unit: `hooks`, `providers`, `utils` ou, por padrão, `components`;
- integration: `pages`, `routes`, `flows` ou, por padrão, `components`.

Essa é uma heurística, não uma classificação rígida por filename. Não espelhe cegamente toda a árvore `src`; reduza profundidade quando categorias como `unit/utils` comunicarem o propósito. Se a aplicação for organizada por feature/domain, preserve categorias como `unit/friends`, `unit/auth` ou `integration/friends` quando isso tornar a navegação mais coerente.

Nomes como `.unit.test` e `.integration.test` são desnecessários quando o diretório já informa a categoria. Continue seguindo `.test` ou `.spec` conforme o projeto.

#### Preservar e migrar estruturas existentes

Se os testes existentes estiverem em uma pasta plana ou em categorias sem `unit`/`integration`:

- não mova testes implementados;
- continue colocando novos arquivos na estrutura atual enquanto a migração não for autorizada;
- sugira no resumo a organização futura em `unit/` e `integration/` quando houver benefício claro;
- se houver muitos testes e a reorganização for valiosa, peça aprovação antes de mover qualquer arquivo implementado.

Movimentos podem quebrar imports relativos, snapshots, paths, configurações, scripts e CI. Mesmo quando o conteúdo permanecer intacto, mover ou reclassificar um teste implementado requer aprovação explícita.

Skeletons comprovadamente vazios criados pela skill podem ser movidos automaticamente quando a categoria correta estiver clara e todos os imports puderem ser atualizados com segurança. Em caso de dúvida, preserve e sinalize.

Se um teste implementado parecer mal classificado, não o mova. Registre no resumo o caminho atual, a categoria sugerida e o destino recomendado.

### 5. Mapear e selecionar unidades

Classifique aproximadamente o código em componentes, pages, layouts, hooks, providers, contexts, utils, services, repositories, route handlers e itens triviais. Crie scaffolding apenas quando houver comportamento significativo capaz de quebrar.

Priorize:

- componentes com props relevantes, estado, branches, formulários, listas, dialogs, callbacks, permissões ou integrações;
- loading, empty, error e success states;
- custom hooks com estado, efeitos, cleanup, parâmetros, funções retornadas ou async;
- providers e contexts com inicialização, regras, métodos, storage, auth ou APIs;
- utils com branches, validação, parsing, normalização, cálculos ou regras de negócio;
- services e repositories com contratos, transformação, erros e edge cases;
- acessibilidade observável de controles interativos quando houver risco real.

Ignore por padrão, salvo lógica relevante:

- interfaces, aliases, `.d.ts`, enums e constantes simples;
- barrel exports e `index` apenas com exports;
- estilos, CSS modules, código gerado e configurações;
- wrappers triviais e componentes puramente estáticos;
- arquivos sem comportamento relevante.

Não crie testes por obrigação para `next.config`, `vite.config`, ESLint, PostCSS ou Tailwind. Não busque cobertura artificial de 100%.

### 6. Tratar React e Next.js de acordo com o runtime

Para Client Components, considere interações, hooks, estado, eventos, browser APIs, contexts, router, forms e efeitos.

Para Server Components, considere data fetching, parâmetros, auth, regras condicionais, redirects, erros e estados vazios. Não recomende `userEvent` sem interação no browser e não crie cenários triviais como "should return JSX".

No Next.js App Router, reconheça `page`, `layout`, `loading`, `error`, `not-found`, `template` e `route`, além de `next/navigation`, `next/headers`, cookies, `redirect`, `notFound`, route/search params e handlers. Teste contratos e regras da aplicação, não detalhes internos do framework.

### 7. Planejar cenários

Antes de criar cada cenário, confirme:

1. qual comportamento observável será validado;
2. que ele existe no código atual;
3. que tem relevância suficiente;
4. que não há cenário semanticamente equivalente;
5. quais dependências realmente precisam ser isoladas;
6. quais identificadores reais ajudam a implementação futura;
7. que o cenário não testa detalhes internos.

Prefira poucos cenários distintos e úteis. Evite variações redundantes de "should render", cenários baseados em funções privadas, número de `useState`, estrutura interna do JSX ou classes CSS sem impacto funcional.

Use inglês em nomes de `describe`, nomes de `it`/`test`, código, identificadores e termos técnicos. Use português do Brasil para objetivos, orientação, explicação de mocks, referências e sugestões.

Use `async` no callback vazio somente quando o comportamento futuro for assíncrono. Organize `describe` pela unidade sob teste; use nesting apenas quando trouxer clareza.

### 8. Criar skeletons

Não use `it.todo()`. Todo cenário novo deve ter esta forma sem lógica executável:

```ts
it("should display an error when the request fails", async () => {
  /*
   * Deve validar que uma mensagem de erro adequada é apresentada
   * quando a operação falhar.
   *
   * Mockar:
   * - friendRepository.getAll
   * - toast.error
   *
   * Variáveis e referências relevantes:
   * - error
   * - friends
   */

  // TODO: implementar teste
});
```

O comentário multilinha deve ser a primeira informação relevante do corpo e explicar, conforme aplicável:

- condição inicial;
- comportamento a exercitar;
- resultado esperado;
- mocks específicos necessários;
- props, funções, variáveis e referências reais do código.

O marcador `// TODO: implementar teste`, combinado com a ausência de lógica real, identifica skeletons desta skill. Não classifique um teste como vazio apenas porque contém um TODO esquecido.

### 9. Sugerir mocks e preparar imports

Documente somente mocks necessários ao cenário, como repositories, services, `fetch`, Axios, SDKs, hooks, contexts, stores, timers, datas, `next/navigation`, `next/headers` e browser APIs (`localStorage`, `matchMedia`, observers, clipboard etc.). Não gere `jest.mock`, `vi.mock`, spies ou funções mock.

Em unit tests, normalmente isole dependências externas à unidade. Em integration tests, mantenha reais os colaboradores internos cuja interação é o objeto do cenário e sugira mocks apenas para limites externos. Por exemplo, em `FriendForm -> FriendsProvider -> friendRepository -> Firebase`, mantenha `FriendForm` e `FriendsProvider` reais e considere isolar `friendRepository` ou Firebase conforme a arquitetura. Não recomende mocks que destruam a colaboração que o cenário pretende validar.

Quando a mesma dependência servir a vários cenários, coloque uma única seção `Mocks compartilhados sugeridos:` no início do `describe`; mantenha nos cenários somente mocks específicos.

Prepare o import da unidade sob teste e apenas os imports auxiliares que provavelmente serão usados na implementação futura, sempre de acordo com as dependências instaladas e o runtime da unidade. Não presuma React Testing Library se ela não estiver instalada. Se globals estiverem ativos, não importe APIs globais do runner; caso contrário, prepare os imports seguindo o padrão existente.

Para imports intencionalmente ainda não usados, siga a configuração ESLint do projeto e use a supressão mais localizada possível, por exemplo:

```ts
// eslint-disable-next-line @typescript-eslint/no-unused-vars
import { render, screen } from "@testing-library/react";
```

Não desabilite ESLint no arquivo inteiro nem altere configuração global.

### 10. Atualizar incrementalmente

Antes de adicionar algo:

- preserve a implementação e o setup existentes;
- compare o significado dos cenários, não apenas seus títulos;
- não duplique imports, comentários, skeletons ou sugestões;
- acrescente apenas comportamentos relevantes ainda não cobertos;
- mantenha a localização e o estilo do projeto;
- não formate ou reorganize arquivos fora do trecho necessário.

Considere um teste implementado quando houver evidências contextuais como assertions, `expect`, `render`, `renderHook`, interações, setup, mocks funcionais, chamadas relevantes ou outra lógica real.

Nunca altere assertions, mocks, fixtures, setup, dados ou corpo de um teste implementado. Não refatore testes existentes e não converta Jest para Vitest ou vice-versa.

Quando um teste implementado tiver uma lacuna clara, adicione como primeira informação do corpo um comentário específico, sem tocar na implementação:

```ts
it("should submit the form", async () => {
  /*
   * SUGESTÃO:
   * Este cenário deveria também validar que o botão permanece
   * desabilitado enquanto `isSubmitting` for true.
   *
   * Referências relevantes:
   * - isSubmitting
   * - handleSubmit
   * - submitButton
   */

  // implementação existente permanece intacta
});
```

Não adicione sugestão genérica. Se já existir sugestão semanticamente equivalente, não a repita; atualize-a somente quando estiver claramente incorreta diante do código atual.

### 11. Tratar obsolescência com segurança

Remova automaticamente um cenário apenas com alta confiança de que:

- é um skeleton ainda vazio;
- corresponde a comportamento que deixou de existir;
- contém apenas documentação e o marcador, sem qualquer implementação real.

Em caso ambíguo, preserve e sinalize.

Nunca exclua automaticamente um teste implementado, mesmo que a unidade ou comportamento pareça removido. Liste os testes potencialmente órfãos, explique a evidência e peça aprovação explícita antes da remoção. Não prossiga com a exclusão sem essa aprovação.

Não apague arquivos de produção, não altere código de produção para facilitar testes e não invente componentes, hooks ou arquitetura futura. Reorganize diretórios de testes somente nas condições de preservação e aprovação definidas acima.

### 12. Verificar idempotência

Revise as alterações para garantir que uma segunda execução sem mudanças relevantes resulte em nenhuma ou praticamente nenhuma alteração. Em especial, não:

- recrie arquivos;
- duplique cenários semanticamente equivalentes;
- duplique imports ou comentários;
- reescreva skeletons estáveis;
- repita sugestões.

A cada execução, redetecte a raiz, a estrutura, a categoria dos testes existentes e oportunidades de organização. Inclua testes possivelmente mal classificados no diagnóstico, mas aplique as regras de aprovação antes de mover qualquer teste implementado.

## Planning Mode para projetos sem código suficiente

Quando não houver unidades reais suficientes para cenários úteis, não crie arquivos fictícios nem `TESTING_PLAN.md`.

Busque contexto nesta ordem:

1. README e documentação;
2. requisitos;
3. planos existentes;
4. descrição do usuário;
5. uma pergunta ao usuário sobre as funcionalidades futuras, se ainda necessário.

Retorne somente uma orientação textual associada a funcionalidades futuras conhecidas, deixando claro que os arquivos deverão ser criados quando o código correspondente existir. Para cada unidade ou fluxo futuro, indique `Recommended: unit test` ou `Recommended: integration test` e liste os cenários futuros sem criar diretórios ou arquivos.

## Resumo final

Relate de forma compacta, usando contagens quando disponíveis:

```text
React Test Scaffolder

Created:
- 6 unit test files
- 2 integration test files
- 29 new test scenarios

Updated:
- 4 existing test files
- 7 new scenarios added

Removed obsolete skeletons:
- 2 scenarios

Suggested changes:
- 3 existing tests

Potentially obsolete implemented tests:
- 1 test

Structure suggestions:
- 1 implemented test may be better classified as integration

Skipped:
- 19 files without meaningful behavior to test
```

Adapte ou omita seções vazias. Separe as contagens de arquivos unitários e de integração. Em sugestões estruturais, informe o caminho atual e o destino recomendado sem mover o teste implementado. Quando útil, resuma motivos dos itens ignorados, como arquivos somente de tipos, exports, configurações, constantes estáticas ou componentes sem comportamento. Não liste cada arquivo ignorado quando isso tornar a resposta excessiva.

## Limites permanentes

- Não instalar dependências.
- Não configurar ou migrar o test runner.
- Não modificar código de produção.
- Não implementar mocks ou testes.
- Não excluir testes implementados sem aprovação explícita.
- Não mover ou reclassificar testes implementados sem aprovação explícita.
- Não criar testes E2E.
- Não criar diretórios vazios.
- Não alterar ESLint globalmente.
- Não impor uma arquitetura de testes diferente da existente.
