# TP1 da disciplina PDS2 do semestre 2021/2

Aluno: Diego Barros da Silveira

## Exemplos de testes

O sistema de qual foram pegos os seguintes testes é o [vuejs/vue]()

Os testes desse sistema são feitas na linguagem `Node`, por meio do framework de testes [Jasmine](https://jasmine.github.io/).

1. O seguinte teste se encontra [neste link](https://github.com/vuejs/vue/blob/dev/test/unit/modules/util/error.spec.js):

```js
import Vue from 'vue'
import { invokeWithErrorHandling } from 'core/util/error'


describe('invokeWithErrorHandling', () => {
  if (typeof Promise !== 'undefined') {
    it('should errorHandler call once when nested calls return rejected promise', done => {
      const originalHandler = Vue.config.errorHandler
      const handler = Vue.config.errorHandler = jasmine.createSpy()
      const userCatch = jasmine.createSpy()
      const err = new Error('fake error')

      invokeWithErrorHandling(() => {
        return invokeWithErrorHandling(() => {
          return Promise.reject(err)
        })
      }).catch(userCatch).then(() => {
        Vue.config.errorHandler = originalHandler
        expect(handler.calls.count()).toBe(1)
        expect(userCatch).toHaveBeenCalledWith(err)
        done()
      })
    })
  }
})
```

Podemos perceber que o propósito deste teste é justamente testar o funcionamento do Error Handler do vuejs com chamadas aninhadas de tal ferramenta _core_. Por meio do handle implementado e do handler mockado que é um `spy`, e de chamadas aninhadas com `Promises` que retornam erros falsos (spies), vemos que a chamada aninhada de `invokeWithErrorHandling` é esperada ter sido chamada somente uma vez e pegar o erro lançado.

2. O seguinte teste se encontra [neste link](https://github.com/vuejs/vue/blob/dev/test/unit/features/filter/filter.spec.js):

```js
  it('chained usage', () => {
    const vm = new Vue({
      template: '<div>{{ msg | upper | reverse }}</div>',
      data: {
        msg: 'hi'
      },
      filters: {
        upper: v => v.toUpperCase(),
        reverse: v => v.split('').reverse().join('')
      }
    }).$mount()
    expect(vm.$el.textContent).toBe('IH')
  })
```

Neste teste está sendo testado o uso aninhado de filtros em componentes básicos do vuejs. O filtro é uma propriedade (prop) de um componente Vue, e o teste garante que o uso dos filtros no conteúdo do componente faça o esperado e funcione com o uso encadeado (um após o outro).

3. O seguinte teste se encontra [neste link](https://github.com/vuejs/vue/blob/dev/test/unit/modules/sfc/sfc-parser.spec.js):

```js
  it('deindent content', () => {
    const content = `
      <template>
        <div></div>
      </template>
      <script>
        export default {}
      </script>
      <style>
        h1 { color: red }
      </style>
    `
    const deindentDefault = parseComponent(content.trim(), { pad: false })
    const deindentEnabled = parseComponent(content.trim(), { pad: false, deindent: true })
    const deindentDisabled = parseComponent(content.trim(), { pad: false, deindent: false })

    expect(deindentDefault.template.content).toBe('\n<div></div>\n')
    expect(deindentDefault.script.content).toBe('\nexport default {}\n')
    expect(deindentDefault.styles[0].content).toBe('\nh1 { color: red }\n')
    expect(deindentEnabled.template.content).toBe('\n<div></div>\n')
    expect(deindentEnabled.script.content).toBe('\nexport default {}\n')
    expect(deindentEnabled.styles[0].content).toBe('\nh1 { color: red }\n')
    expect(deindentDisabled.template.content).toBe('\n        <div></div>\n      ')
    expect(deindentDisabled.script.content).toBe('\n        export default {}\n      ')
    expect(deindentDisabled.styles[0].content).toBe('\n        h1 { color: red }\n      ')
  })
```

Neste arquivo são testadas as funcionalidades da implementação de um parser SFC (Single File Component) e neste teste específico são testadas 'desindentações' com base em diferentes configurações (com ou sem _padding_ e a própria 'desindentação' explícita). O teste cria um componente básico, com estrutura e conteúdo simples e testa as diferentes configurações de 'desindentação' para os diferentes constituintes do componente.
