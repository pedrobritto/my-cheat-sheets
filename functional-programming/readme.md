# Introdução à Programação Funcional (com JavaScript)

## Funções Puras

Fuções puras são funções que trabalham apenas com parametros passados para ela. Ou seja, não interagem com variáveis ou propriedades fora de seu escopo.

Essa função, por exemplo, é pura:

```js
let z = 10;

function add(x, y) {
  return x + y;
}
```

Ela é pura porque possui dois parâmetros, `x` e `y`, e realiza sua operação interna com esses parâmetros. Não lê nem escreve nada em `z`, uma variável externa.

Outra característica de funções puras é que elas sempre retornarão um determinado valor se suas entradas foram as mesmas. Por exemplo:

```js
function add(x, y) {
  return x + y;
}

add(1, 2); // Retorna 3
add(1, 2); // Também retorna 3
add(1, 2); // Também retorna 3
```

Isso não seria possível se o valor de uma variável externa e passível de alterações por outras partes do programa (como `z` do outro exemplo) fizesse parte dos cálculos.

Funções puras também não produzem **Efeitos Colaterais**. Ou seja, não apenas trabalham com seus próprios parâmetros, mas também escrevem/afetam outras partes do programa diretamente.

### Regras básicas de pureza:

1.  Funções puras úteis recebem pelo menus um parâmetro;
2.  Funções puras úteis precisam retornar alguma coisa;
3.  Funções puras sempre retornam o mesmo valor dadas as mesmas entradas;
4.  Funções puras não produzem efeitos colateráis.

## Imutabilidade

O princípio da imutabilidade diz que uma vez que variáveis são declaradas elas não podem ser reescritas, apenas lidas. Não existem variáveis no sentido literal da palavra, apenas constantes.

Caso você queira atualizar o valor de uma variável, é preciso atribuir o novo valor a uma nova variável.

Em muitos casos isso não é muito difícil de contornar, pois muitas variáveis existem apenas num escopo local.

Imutabilidade também significa não criar loops com iteração, mas com recursão.

Imutabilidade é importante por evitar problemas com manipulações indevidas de valores e estados, que são problemas dificeis de debugar.

### Regras básicas da imutabilidade:

1.  Variáveis não podem ser ter seus valores alterados;
2.  Não utilize loops iterativos, mas loops recursivos.

## Refatoração

Em programação funcional é importante modularizar o código o máximo possível. Por isso refatoração é importante. Vamos começar com um exemplo:

```js
function validateCPF(cpf) {
  if (/^\d{3}\.\d{3}\.\d{3}-.\d{2}$/.exec(cpf)) console.log("CPF válido");
  else console.log("CPF inválido");
}

function validatePhone(phone) {
  if (/^\(\d{2}\)\d{4}-\d{4}$/.exec(phone)) console.log("Telefone válido");
  else console.log("Telfone inválido");
}
```

Essas duas funções possuem o código praticamente identico, com exceção da regex, nome de parametro e string impressa. Em vez de copiar e colar uma função e depois editar alguns trechos, podemos parametrizar o valor, a Regex e a parte da mensagem impressa.

Podemos refatorar a validação da seguinte forma:

```js
function validateValue(value, regex, type) {
  if (regex.exec(value)) console.log(type + " válido");
  else console.log(type + " inválido");
}
```

Mas em um caso como esse, onde duas funções `parseAddress` e `parseFullName`, são utilizadas para validar o valor dos parâmetros?

```js
function validateAddress(address) {
  if (parseAddress(address)) console.log("Valid Address");
  else console.log("Invalid Address");
}
function validateName(name) {
  if (parseFullName(name)) console.log("Valid Name");
  else console.log("Invalid Name");
}
```

Podemos resolver isso com funções de alta ordem.

## Funções de alta ordem

Nesse caso, no lugar de uma Regex está uma função. Isso não é um problema se a linguagem suporta funções de alta ordem, pois assim funções podem ser passadas como parametros para outra função.

> Funções de primeira ordem recebem funções como parâmetros, retornam funções ou os dois.

Podemos reescrever a função `validateValue` como:

```js
function validateValueWithFunc(value, parseFun, type) {
  if (parseFunc(value)) console.log(type + " válido");
  else console.log(type + " inválido");
}
```

E assim podemos reescrever as quatro funções anteriores da seguinte forma:

```js
validateValueWithFunc(
  "123-456-789-01",
  /^\d{3}\.\d{3}\.\d{3}-.\d{2}$/.exec,
  "SSN"
);
validateValueWithFunc("(12)3456-7890", /^\(\d{2}\)\d{4}-\d{4}$/.exec, "Phone");
validateValueWithFunc("123 Main St.", parseAddress, "Address");
validateValueWithFunc("Joe Mama", parseName, "Name");
```

Mas ainda podemos melhorar. Em vez de passar a Regex diretamente como parâmetros, podemos criar variáveis:

```js
const parseCPF = /^\d{3}\.\d{3}\.\d{3}-.\d{2}$/.exec;
const parsePhone = /^\(\d{2}\)\d{4}-\d{4}$/.exec;

validateValueWithFunc("123-456-789-01", parseCPF, "SSN");
validateValueWithFunc("(12)3456-7890", parsePhone, "Phone");
validateValueWithFunc("123 Main St.", parseAddress, "Address");
validateValueWithFunc("Joe Mama", parseName, "Name");
```

Ainda há outro problema. É preciso adicionar `.exec` no final de cada uma das Regexes e isso é fácil de esquecer.

Podemos criar mais uma simples função que cuide disso pra gente:

```js
function makeRegexParser(regex) {
  return regex.exec;
}

const parseCPF = makeRegexParser(/^\d{3}\.\d{3}\.\d{3}-.\d{2}$/);
const parsePhone = makeRegexParser(/^\(\d{2}\)\d{4}-\d{4}$/);
```

O resultado produzido é o mesmo.

Funções de alta ordem também retornam funções. Isso é muito útil e conhecido como **closure**, mas não vou entrar em detalhes agora.

## Composição de funções

Composição facilita a reutilização de código quando se deseja unir vaŕias funções em cadeia para conseguir uma outra funcionalidade.

Vamos aos exemplos, dessa vez em ES6:

```js
const add10 = x => x + 10;
const mult5 = x => x * 5;
```

Agora vamos imaginar que queremos uma função que recebe um valor, adiciona 10 e então multiplica o resultado por 5. Podemos escrever assim:

```js
const mult5AfterAdd10 = x => 5 * (x + 10);
```

Isso não é bom porque acabamos criando mais uma função do zero, sendo que temos funções que cobrem a funcionalidade dessa função (somar com 10 e multiplicar port 5).

Vamos utilizar nossas outras funções em vez de criar uma do zero:

```js
const mult5AfterAdd10 = x => mult5(add10(x));
```

Melhorou, mas ainda pode ficar melhor. Mas antes vamos tentar algo diferente. Vamos substituir a função `add10` por `add`, que em vez de 1 precisa de 2 parâmetros.

A função anterior precisará ser reescrita como:

```js
const mult5AfterAdd10 = x => mult5(add(10, x));
```

Como são necessários dois parâmetros para `add`, a situação poderá ficar um tanto complicada ou imprática se quisermos compor o novo `mult5AfterAdd10` com outra função.

Poderiamos resolver esse problema se pudessemos passar o primeiro parâmetro antes e passar o segundo parametro apenas quando a função `mult5AfterAdd10` fosse chamada.

Isso pode ser feito com **Currying**!

## Currying

Para resolver o problema anterior podemos escrever uma função que precise de 2 parâmetros, mas receba um de cada vez.

> Uma função que recebe um parametro de cada vez é chamada Curried Function.

Podemos reescrever a função `add` com uma closure para que isso aconteça:

```js
const add = x => y => x + y;
```

Em resumo, a função `add` recebe um parametro `x` e retorna uma outra função que recebe um parametro `y`, que no final retorna o valor da soma `x + y`.

Podemos então reescrever `mult5AfterAdd10` como:

```js
const mult5AfterAdd10 = x => mult5(add(10));
```

Podemos criar uma função que cuide da composição para que a leitura fique mais fácil:

```js
const compose = (f, g) => x => f(g(x));
const mult5AfterAdd10 = compose(
  mult5,
  add(10)
);
```

É simples: `compose` é uma função que recebe 2 parâmetros e retorna outra função que recebe um parâmetro, que quando chamado irá aplicar `f` depois de `g` em `x`.

No exemplo de `mult5AfterAdd10`, `mult5` é aplicado depois de `add` em `10`.

### Outro exemplo de Currying

Vamos criar uma função que adicionar chaves simples e duplas em volta de uma string.

```js
const bracket = str => "{" + str + "}";
const doubleBracket = str => "{{" + str + "}}";
```

Podemos utiliza-las da seguinte forma:

```js
bracket("Nome");
doubleBracket("Nome");
```

Ótimo, mas podemos escrever essas funções a partir de outra que vamos chamar de `generalBracket`:

```js
const generalBracket = (prefix, str, suffix) => prefix + str + suffix;

const bracket = str => generalBracket("{", str, "}");
const doubleBracket = str => generalBracket("{{", str, "}}");

bracket("Nome");
doubleBracket("Nome");
```

Também podemos tornar generalBracket uma "curried function" utilizando closure:

```js
const generalBracket = (prefix, suffix) => str => prefix + str + suffix;

const bracket = generalBracket("{", "}");
const doubleBracket = generalBracket("{{", "}}");

bracket("Nome");
doubleBracket("Nome");
```

### Fontes

- [So You Want to be a Functional Programmer](https://medium.com/@cscalfani/so-you-want-to-be-a-functional-programmer-part-1-1f15e387e536)
- [A practical introduction to functional programming](https://maryrosecook.com/blog/post/a-practical-introduction-to-functional-programming)
