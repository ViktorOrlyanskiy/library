# Функциональные концепции

## • **Неизменяемость**

Функция должна возвращать новые данные, а не изменять существующие.

## • **Чистые функции**

Это функции, которые возвращают значения, вычисленные на основе ее аргументов (чистые функции пригодны для тестирования)

**Правила написания чистой функции:**

1. Функция должна принимать хотя бы один аргумент.
2. Функция должна возвращать значение или другую функцию.
3. Функция не должна изменять переданный ей аргумент.

## • Преобразование данных

**Функции map и reduce** — главное оружие функционального программирования, в том числе в **JavaScript**. Если вы хотите стать опытным инженером по **JavaScript**, овладейте этими функциями. Способность создавать один набор данных из другого — обязательный навык, который полезен для любой парадигмы программирования.



**Функциональный способ создания массива компонентов**

{% code lineNumbers="true" %}
```jsx
const createArray = length => [...Array(length)];

function StarRating({ totalStars }) {
  return createArray(totalStars).map((n, i) => <Star key={i} />);
}
```
{% endcode %}

## • Функции высшего порядка

Это функции, которые могут управлять другими функциями: принимать функции в качестве аргументов и/или возвращать функции.

**Каррирование** — это функциональная технология, в которой используются функции высшего порядка. (добавить пример)

{% code lineNumbers="true" %}
```javascript
const userLogs = userName => message =>
  console.log(`${userName} -> ${message}`);
  
const log = userLogs("grandpa23");

log("attempted to load 20 fake members");

getFakeMembers(20).then(
  members => log(`successfully loaded ${members.length} members`),
  error => log("encountered an error loading members")
);
// grandpa23 -> попытка загрузки 20 фиктивных сотрудников
// grandpa23 -> успешно загружены 20 сотрудников
// grandpa23 -> попытка загрузки 20 фиктивных сотрудников
// grandpa23 -> при загрузке сотрудников возникла ошибка
```
{% endcode %}

## • Рекурсия

Это технология, подразумевающая создание функций, которые вызывают сами себя.

**Рекурсия** — это паттерн, который особенно хорошо работает с асинхронными процессами. Функции могут восстанавливать себя по готовности, например, когда становятся доступными данные или когда таймер заканчивает работу.

{% code lineNumbers="true" %}
```javascript
const countdown = (value, fn) => {
  fn(value);
  return value > 0 ? countdown(value — 1, fn) : value;
};
countdown(10, value => console.log(value));
```
{% endcode %}

**Рекурсия** — хороший метод поиска структур данных. Вы можете использовать рекурсию для перебора вложенных папок до тех пор, пока не будет определена папка, содержащая только файлы.

{% code lineNumbers="true" %}
```javascript
const deepPick = (fields, object = {}) => {
  const [first, ...remaining] = fields.split(".");
  return remaining.length
    ? deepPick(remaining.join("."), object[first])
    : object[first];
};


const dan = {
  type: "person",
  data: {Функциональные концепции    67
    gender: "male",
    info: {
      id: 22,
      fullname: {
        first: "Dan",
        last: "Deacon"
      }
    }
  }
};
deepPick("type", dan);                       // "person"
deepPick("data.info.fullname.first", dan);   // "Dan"
```
{% endcode %}

## • Композиция

В функциональных программах логика разбита на небольшие чистые функции, выполняющие отдельные задачи. Эти мелкие функции нужно собрать вместе: объединить их, вызвать их последовательно или параллельно или объединить их в более крупные функции, пока не сформируется готовое приложение.

{% code lineNumbers="true" %}
```typescript
const template = "hh:mm:ss tt";
const clockTime = template
  .replace("hh", "03")
  .replace("mm", "33")
  .replace("ss", "33")
  .replace("tt", "PM");
console.log(clockTime);
```
{% endcode %}
