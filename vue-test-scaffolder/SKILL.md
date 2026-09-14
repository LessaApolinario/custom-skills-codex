---
name: vue-test-scaffolder
description: Analise projetos Vue e crie, atualize, reorganize ou revise scaffolding de testes unitários e de integração com a infraestrutura existente. Use em Vue + Vite, Quasar CLI, Vue com o plugin Vite do Quasar, Nuxt e outros setups Vue identificáveis; não use para E2E, migração de ferramentas ou implementação de testes.
---

# Vue Test Scaffolder

Produza uma suíte de testes planejada, organizada e pronta para implementação posterior. Analise o que merece teste, crie a estrutura e descreva os comportamentos esperados, mas nunca implemente os testes.

## Regra inviolável

Nesta skill, pedidos como “crie os testes”, “adicione os testes que faltam” e “revise os testes” autorizam somente scaffolding.

Não adicione assertions, mocks funcionais, spies, fixtures executáveis, mounts, renders, wrappers, interações, setup de framework ou lógica real em cenários novos. Não converta skeletons em testes implementados. Se o usuário pedir uma suíte funcional completa, explique que esta skill cobre apenas scaffolding.

Seja agressivo ao analisar e conservador ao modificar.

## Prioridades

Resolva conflitos nesta ordem:

1. preservar testes implementados e código de produção;
2. seguir as convenções reais do projeto;
3. representar o comportamento presente no código atual;
4. produzir cenários distintos e relevantes;
5. aplicar os padrões default desta skill.

Reanalise toda a aplicação relevante em cada execução. Não limite a análise ao diff, aos arquivos alterados no Git nem a resultados anteriores. Não crie nem dependa de `TESTING_PLAN.md`.

## Fluxo

### 1. Detectar o ecossistema Vue

Antes de decidir sobre testes, inspecione em conjunto:

- `package.json`, dependencies, devDependencies e scripts;
- lockfiles e package manager;
- configurações de framework, build e testes;
- plugins declarados e como são configurados;
- estrutura e convenções da aplicação;
- imports e APIs usados pelo código.

Classifique o projeto somente com evidências convergentes. Priorize dependências, configuração do framework, scripts, configuração de testes e convenções da aplicação, nessa ordem.

Reconheça pelo menos:

- **Vue + Vite:** `vue`, `vite`, `@vitejs/plugin-vue` e `vite.config.*` coerentes;
- **Quasar CLI:** `quasar`, `@quasar/app-vite`, `quasar.config.*`, boot files e convenções do CLI;
- **Vue + Quasar Vite Plugin:** `quasar`, `@quasar/vite-plugin` e configuração correspondente no Vite; não classifique como Quasar CLI;
- **Nuxt:** `nuxt`, pacotes `@nuxt/*`, `nuxt.config.*` e APIs/convenções do Nuxt; a presença interna de Vite não torna o projeto Vue + Vite puro;
- **Outro setup Vue:** somente quando puder ser identificado com segurança.

Identifique versões de Vue, Nuxt, Quasar, Vite, runner e test utilities quando elas mudarem convenções ou runtime. Não atualize dependências.

Depois da detecção local, consulte somente documentação oficial de Vue, Vue Test Utils, Vitest, Vite, Quasar, ferramentas oficiais de testes do Quasar ou Nuxt quando uma ambiguidade de versão, configuração ou runtime puder mudar a análise. Não pesquise quando o projeto já fornecer evidência suficiente.

Se não houver evidência suficiente para identificar um projeto Vue suportado, não crie arquivos. Relate os sinais encontrados e a ambiguidade.

### 2. Detectar a infraestrutura de testes

Antes de modificar arquivos, identifique:

- Vitest, Jest ou outro runner configurado;
- Vue Test Utils, Vue Testing Library, `@nuxt/test-utils` e utilities do Quasar;
- `jsdom`, `happy-dom` ou outro ambiente;
- setup files e configuração do runner;
- uso global ou importado de `describe`, `it`/`test` e hooks;
- raiz de testes, colocação próxima ao código e separação atual entre unit e integration;
- convenção predominante `.test` ou `.spec`;
- JavaScript ou TypeScript, extensão dos testes, estilo e aliases.

Use dependências, scripts, configurações e testes existentes como evidência conjunta. Preserve uma stack válida; não troque Jest por Vitest ou vice-versa.

Se faltar uma dependência essencial para criar scaffolding válido:

1. não instale, configure nem modifique arquivos;
2. identifique a dependência ausente e por que ela é necessária;
3. indique o comando de instalação correspondente ao package manager do projeto;
4. encerre a execução para que a skill seja repetida depois da instalação.

Não invente uma raiz de testes ou convenção de nome quando o projeto não oferecer evidência. Solicite a escolha necessária antes de criar arquivos. Na ausência de evidência apenas para `it` versus `test`, prefira `it`.

### 3. Mapear aplicação e testes existentes

Mapeie a aplicação inteira, incluindo quando existirem:

- components, composables e stores;
- pages, views e layouts;
- services, repositories, utils e validators;
- router, middleware, plugins e boot files;
- server code;
- todos os testes existentes.

Ignore dependências, build, coverage, artefatos gerados e diretórios excluídos pelo projeto. Leia integralmente cada teste antes de alterá-lo. Relacione testes ao código atual e classifique internamente cada item como `KEEP`, `UPDATE`, `MOVE`, `CREATE` ou `OBSOLETE`.

Considere implementado qualquer teste com assertions, `expect`, mounts/renders, interações, setup, mocks funcionais, chamadas relevantes ou outra lógica executável. Um `TODO` isolado não prova que o teste é um skeleton.

### 4. Selecionar comportamentos relevantes

Crie scaffolding apenas para comportamento significativo que possa quebrar, como:

- branches, validação e transformação de dados;
- estado inicial, computed state e transições;
- props, eventos, callbacks e condições de renderização;
- loading, empty, success e error states;
- permissões, navegação e parâmetros de rota;
- side effects, cleanup e contratos assíncronos;
- regras de negócio e colaboração real entre módulos.

Ignore por padrão, salvo lógica relevante: tipos, interfaces, aliases, declarações, constantes triviais, reexports, código gerado, estilos, configurações, wrappers sem comportamento e components puramente estáticos.

Nem todo arquivo precisa de teste. Não busque cobertura artificial de 100%.

Antes de criar cada cenário, confirme:

1. qual comportamento observável será validado;
2. que ele existe no código atual e é relevante;
3. que não está implementado ou scaffolded de forma equivalente;
4. quais colaboradores devem permanecer reais;
5. quais limites externos poderão precisar de mock;
6. quais identificadores reais ajudarão a futura implementação.

### 5. Classificar como unit ou integration

Classifique pelo comportamento, nunca pelo tamanho do arquivo ou pela mera presença de imports.

Use **unit** quando houver uma unidade principal, o cenário for localizado e dependências externas puderem ser isoladas. Isso pode incluir:

- component isolado;
- composable com estado, effects, callbacks, cleanup ou branches;
- store com actions, getters/computed state, transitions, reset ou erros;
- utility, formatter, normalizer, validator ou parser;
- service, repository, helper ou pequena regra de negócio.

Pergunta de decisão: “Consigo validar o comportamento relevante desta unidade isoladamente, substituindo limites externos?”

Use **integration** quando o comportamento emergir da colaboração entre duas ou mais unidades reais, por exemplo:

- component + composable ou store;
- parent + child behavior;
- page/view + components, store ou router;
- form + store;
- layout + navigation;
- fluxo de autenticação ou criação, edição e remoção de entidade;
- comportamento Quasar ou Nuxt ligado a partes reais da aplicação.

Pergunta de decisão: “O comportamento importante só faz sentido quando essas partes reais colaboram?”

Um component importar Button, Icon, utility ou outro component não torna o teste automaticamente integração. Flows continuam sendo integration dentro do runner instalado; não os transforme em E2E.

Unit e integration devem se complementar. Não replique em integration o mesmo comportamento isolado já coberto em unit; valide uma colaboração mais ampla. Não invente integration tests quando nenhuma colaboração significativa existir.

### 6. Escolher a organização

Decida a localização nesta ordem:

1. convenção de testes existente;
2. arquitetura ou organização por feature/domain;
3. separação `unit`/`integration`;
4. categoria semântica;
5. padrão default desta skill.

Preserve uma organização clara já existente. Não espelhe cegamente toda a árvore de produção e não crie uma estrutura híbrida sem consentimento.

Quando não houver convenção mais forte e as escolhas necessárias estiverem confirmadas, use somente as categorias que receberão arquivos:

```text
<test-root>/
  unit/
    components/
    composables/
    stores/
    utils/
    services/
    repositories/
  integration/
    components/
    pages/
    views/
    layouts/
    router/
    middleware/
    plugins/
    boot/
    flows/
```

Essa árvore é um repertório, não uma estrutura obrigatória. Preserve categorias de domínio como `friends`, `auth` ou `profile` quando comunicarem melhor a arquitetura. Não use `.unit.test` ou `.integration.test` quando o diretório já representar a categoria. Preserve `.test` ou `.spec` conforme o projeto.

Se a suíte estiver plana ou organizada sem `unit`/`integration`, continue usando a estrutura atual para novos skeletons até que uma migração seja aprovada. Sugira melhorias no resumo quando houver benefício claro.

### 7. Respeitar o runtime identificado

Em **Vue + Vite**, reconheça components, composables, stores, router, views, services, repositories e utils, mas não implemente Vue Test Utils.

Em **Quasar**, considere pages, layouts, boot files, stores, `useQuasar`, `$q`, Notify, Dialog, Loading, LocalStorage e SessionStorage. Documente limites relevantes nos cenários, mas não configure Quasar nos testes.

Em **Nuxt**, considere pages, layouts, middleware, plugins, server code, auto-imports, `useState`, `useFetch`, `useAsyncData`, `useRuntimeConfig` e `navigateTo`. Não configure `@nuxt/test-utils` nem o runtime do Nuxt nos skeletons.

Teste contratos e comportamento da aplicação, não detalhes internos do framework.

### 8. Criar skeletons

Use inglês em nomes de `describe`, `it`/`test`, código, identificadores e termos técnicos. Use português do Brasil em objetivos, orientações, mocks e referências.

Não use `it.todo()`. Cada cenário novo deve conter somente documentação e o marcador:

```ts
it("should display an error notification when saving the profile fails", async () => {
  /*
   * Deve validar que uma notificação de erro seja exibida quando
   * a atualização do perfil falhar.
   *
   * Mockar:
   * - profileRepository
   * - $q.notify
   *
   * Variáveis e referências relevantes:
   * - saveProfile
   * - error
   * - $q.notify
   */

  // TODO: implementar teste
});
```

Use `async` somente quando o comportamento futuro for assíncrono. Organize `describe` pela unidade ou fluxo; use nesting apenas quando trouxer clareza.

O comentário deve ser a primeira informação relevante do corpo e registrar, conforme aplicável: condição inicial, comportamento a exercitar, resultado esperado, mocks específicos e variáveis, funções, props ou referências reais do código.

Não adicione imports especulativos. Inclua somente imports exigidos para que o skeleton estrutural siga a convenção já usada no projeto. Não adicione `mount`, `shallowMount`, `render`, `createTestingPinia`, `createRouter`, `flushPromises`, `vi.mock`, `jest.mock` ou equivalentes apenas porque poderão ser úteis depois.

Não implemente:

- `expect` ou qualquer assertion;
- mounts, renders ou wrappers;
- `beforeEach`, `afterEach` ou outros setups;
- Pinia, Vue Router, Quasar plugins ou runtime Nuxt;
- mocks, spies ou fake implementations;
- HTTP mocking, factories ou fixtures.

### 9. Documentar boundaries e mocks

Distinga colaboradores internos de limites externos. Em um integration skeleton para:

```text
FriendForm -> useFriends -> FriendsStore -> friendRepository -> Firebase
```

mantenha `FriendForm`, `useFriends` e `FriendsStore` reais quando sua interação for o objeto do cenário. Sugira mock somente para `friendRepository`, Firebase ou outro limite externo adequado à arquitetura.

Em unit skeletons, documente dependências externas que provavelmente precisarão ser isoladas. Em integration skeletons, não sugira mocks que eliminem a colaboração que o cenário pretende validar. Nunca implemente o mock.

### 10. Atualizar incrementalmente

Antes de adicionar ou editar algo:

- preserve toda implementação e setup existentes;
- compare o significado dos cenários, não apenas títulos;
- não duplique cenários, imports, comentários ou sugestões;
- acrescente somente comportamentos relevantes ainda não cobertos;
- mantenha localização, estilo e formatação predominantes;
- não altere trechos fora do necessário.

Nunca remova assertions, mocks, fixtures, setup ou lógica de um teste implementado. Não refatore testes existentes e não converta runners.

Se um teste implementado tiver uma lacuna clara, acrescente apenas uma orientação específica como primeira informação do corpo, sem tocar na implementação:

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
   */

  // implementação existente permanece intacta
});
```

Não adicione sugestões genéricas ou semanticamente duplicadas.

### 11. Preservar estrutura e tratar obsolescência

Nunca mova ou reclassifique automaticamente um teste implementado. Movimentos podem quebrar imports relativos, snapshots, aliases, paths, scripts e CI. Informe no resumo o caminho atual, a categoria sugerida e o destino recomendado; mova somente após aprovação explícita.

Skeletons comprovadamente vazios podem ser reorganizados automaticamente quando a categoria correta estiver clara e a mudança for segura. Em caso de dúvida, preserve e sinalize.

Remova automaticamente um cenário somente com alta confiança de que ele é um skeleton vazio, corresponde a comportamento inexistente ou duplicado e contém apenas documentação e o marcador, sem lógica real.

Nunca exclua silenciosamente um teste implementado. Liste testes possivelmente obsoletos, explique a evidência e peça aprovação explícita antes de remover. Não altere código de produção e não invente unidades ou arquitetura futura.

### 12. Verificar idempotência

Garanta que uma segunda execução sem mudanças relevantes produza nenhuma ou praticamente nenhuma alteração. Não recrie arquivos, duplique cenários ou sugestões, nem reescreva skeletons estáveis.

## Planning Mode para código insuficiente

Quando a aplicação ou uma unidade futura ainda não existir, não crie arquivos fictícios, diretórios vazios nem `TESTING_PLAN.md`.

Use documentação e requisitos existentes para retornar somente orientação textual. Para cada unidade ou fluxo futuro conhecido, indique `Recommended: unit test` ou `Recommended: integration test` e liste cenários futuros. Deixe claro que os arquivos devem ser criados quando o código correspondente existir.

## Resumo final

Relate o ecossistema, runner e alterações de forma compacta, separando unit e integration. Adapte ou omita seções vazias:

```text
Vue Test Scaffolder

Ecosystem:
- Quasar CLI

Test runner:
- Vitest

Created:
- 6 unit test files
- 2 integration test files
- 27 new test scenarios

Updated:
- 3 existing skeletons
- 6 new scenarios

Removed obsolete skeletons:
- 2 scenarios

Suggested structural changes:
- 2 implemented tests

Potentially obsolete implemented tests:
- 1 test

Skipped:
- 18 files without meaningful behavior to test
```

Em sugestões estruturais, informe caminho atual e destino recomendado sem mover o teste implementado. Quando útil, resuma por que arquivos foram ignorados sem listar cada item.

## Limites permanentes

- Não instalar ou atualizar dependências.
- Não configurar, substituir ou migrar o test runner.
- Não modificar código de produção.
- Não implementar assertions, mocks, setup ou testes.
- Não excluir, mover ou reclassificar testes implementados sem aprovação explícita.
- Não criar testes E2E com Playwright, Cypress, Selenium ou equivalentes.
- Não criar categorias ou diretórios vazios.
- Não impor uma arquitetura diferente da existente.
