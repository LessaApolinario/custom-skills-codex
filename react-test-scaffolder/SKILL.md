---
name: react-test-scaffolder
description: Analise projetos React ou Next.js e crie, atualize ou revise scaffolding de testes Jest/Vitest sem implementar testes funcionais. Use para planejar casos, criar skeletons vazios, complementar suites existentes, apontar mocks e sugerir melhorias; não use para escrever assertions ou implementar suites completas.
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
- organização geral do código e dos testes.

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

Se o runner estiver configurado, mas não houver nenhum arquivo de teste, pergunte ao usuário se prefere `.test` ou `.spec` antes de criar arquivos. Não decida arbitrariamente. Na ausência de evidência somente para `it` versus `test`, prefira `it`.

### 4. Mapear e selecionar unidades

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

### 5. Tratar React e Next.js de acordo com o runtime

Para Client Components, considere interações, hooks, estado, eventos, browser APIs, contexts, router, forms e efeitos.

Para Server Components, considere data fetching, parâmetros, auth, regras condicionais, redirects, erros e estados vazios. Não recomende `userEvent` sem interação no browser e não crie cenários triviais como "should return JSX".

No Next.js App Router, reconheça `page`, `layout`, `loading`, `error`, `not-found`, `template` e `route`, além de `next/navigation`, `next/headers`, cookies, `redirect`, `notFound`, route/search params e handlers. Teste contratos e regras da aplicação, não detalhes internos do framework.

### 6. Planejar cenários

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

### 7. Criar skeletons

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

### 8. Sugerir mocks e preparar imports

Documente somente mocks necessários ao cenário, como repositories, services, `fetch`, Axios, SDKs, hooks, contexts, stores, timers, datas, `next/navigation`, `next/headers` e browser APIs (`localStorage`, `matchMedia`, observers, clipboard etc.). Não gere `jest.mock`, `vi.mock`, spies ou funções mock.

Quando a mesma dependência servir a vários cenários, coloque uma única seção `Mocks compartilhados sugeridos:` no início do `describe`; mantenha nos cenários somente mocks específicos.

Prepare o import da unidade sob teste e apenas os imports auxiliares que provavelmente serão usados na implementação futura, sempre de acordo com as dependências instaladas e o runtime da unidade. Não presuma React Testing Library se ela não estiver instalada. Se globals estiverem ativos, não importe APIs globais do runner; caso contrário, prepare os imports seguindo o padrão existente.

Para imports intencionalmente ainda não usados, siga a configuração ESLint do projeto e use a supressão mais localizada possível, por exemplo:

```ts
// eslint-disable-next-line @typescript-eslint/no-unused-vars
import { render, screen } from "@testing-library/react";
```

Não desabilite ESLint no arquivo inteiro nem altere configuração global.

### 9. Atualizar incrementalmente

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

### 10. Tratar obsolescência com segurança

Remova automaticamente um cenário apenas com alta confiança de que:

- é um skeleton ainda vazio;
- corresponde a comportamento que deixou de existir;
- contém apenas documentação e o marcador, sem qualquer implementação real.

Em caso ambíguo, preserve e sinalize.

Nunca exclua automaticamente um teste implementado, mesmo que a unidade ou comportamento pareça removido. Liste os testes potencialmente órfãos, explique a evidência e peça aprovação explícita antes da remoção. Não prossiga com a exclusão sem essa aprovação.

Não apague arquivos de produção, não altere código de produção para facilitar testes, não reorganize diretórios e não invente componentes, hooks ou arquitetura futura.

### 11. Verificar idempotência

Revise as alterações para garantir que uma segunda execução sem mudanças relevantes resulte em nenhuma ou praticamente nenhuma alteração. Em especial, não:

- recrie arquivos;
- duplique cenários semanticamente equivalentes;
- duplique imports ou comentários;
- reescreva skeletons estáveis;
- repita sugestões.

## Planning Mode para projetos sem código suficiente

Quando não houver unidades reais suficientes para cenários úteis, não crie arquivos fictícios nem `TESTING_PLAN.md`.

Busque contexto nesta ordem:

1. README e documentação;
2. requisitos;
3. planos existentes;
4. descrição do usuário;
5. uma pergunta ao usuário sobre as funcionalidades futuras, se ainda necessário.

Retorne somente uma orientação textual associada a funcionalidades futuras conhecidas, deixando claro que os arquivos deverão ser criados quando o código correspondente existir.

## Resumo final

Relate de forma compacta, usando contagens quando disponíveis:

```text
React Test Scaffolder

Created:
- 5 test files
- 21 new test scenarios

Updated:
- 4 existing test files
- 8 new scenarios added

Removed obsolete skeletons:
- 3 scenarios

Suggested changes:
- 4 existing tests

Potentially obsolete implemented tests:
- 2 tests

Skipped:
- 17 files without meaningful behavior to test
```

Adapte ou omita seções vazias. Quando útil, resuma motivos dos itens ignorados, como arquivos somente de tipos, exports, configurações, constantes estáticas ou componentes sem comportamento. Não liste cada arquivo ignorado quando isso tornar a resposta excessiva.

## Limites permanentes

- Não instalar dependências.
- Não configurar ou migrar o test runner.
- Não modificar código de produção.
- Não implementar mocks ou testes.
- Não excluir testes implementados sem aprovação explícita.
- Não alterar ESLint globalmente.
- Não impor uma arquitetura de testes diferente da existente.
