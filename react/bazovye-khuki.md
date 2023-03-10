# Базовые хуки

{% hint style="warning" %}
**Самое важное** — хуки могут вызывать повторную визуализацию компонента, к которому они подключены.
{% endhint %}

#### Правила использования хуков

1. Хуки работают только в рамках компонента
2. Лучше разбить функциональность на несколько хуков
3. Хуки следует вызывать только на верхнем уровне
4. Массив зависимостей может сравнивать только примитивы, объекты нужно мемоизоировать.

{% embed url="https://ru.reactjs.org/docs/hooks-reference.html" %}

## • useState

Хранит состояние компонента

{% code lineNumbers="true" %}
```typescript
const [value, setValue] = useState();
```
{% endcode %}

{% hint style="danger" %}
Вызывает повторную визуализацию компонета. Если состояние не изменяется, не нужно его загонять его в **useState**.

Распределение данных о состоянии по слишком большому количеству компонентов затруднит поиск ошибок и внесение изменений в приложение.
{% endhint %}

## • useEffect

Мы используем хук **useEffect**, когда рендеринг должен вызвать побочные эффекты. Побочный эффект (Сайд эффект) — это что-то, что функция делает, но не возвращает.

Первый раз вызывается после отрисовки компонента. Если из него вернуть какую-то функцию, то **useEffect** вызовет ее перед размонтированием компонента.

{% code lineNumbers="true" %}
```tsx
useEffect(() => {

    return () => {};
}, [dependency]);

```
{% endcode %}

{% hint style="info" %}
Разделение функциональности на несколько вызовов **useEffect** обычно полезно.
{% endhint %}

{% hint style="warning" %}
Если не передать массив зависимостей, то хук будет следить за всеми пропсами и стейтами.
{% endhint %}

## • useLayoutEffect

#### Порядок вызова

1. Рендер компонента
2. Вызов **useLayoutEffect**
3. Отрисовка браузером
4. Вызов **useEffect**

{% code lineNumbers="true" %}
```typescript
useLayoutEffect(() => {
    
    return () => {};
}, [dependency]);

```
{% endcode %}

{% hint style="info" %}
В большинстве случаев **useEffect** подходит для работы, но если эффект важен для отрисовки браузера (его внешнего вида или размещения элементов пользовательского интерфейса на экране), вы можете использовать **useLayoutEffect**.
{% endhint %}

## • useRef

Возвращает объект в котором храниться ссылка на **DOM-Element.** Ссылка храниться в свойстве **current** (работает вместо **document.querySelector( )**)

Объект можно использовать для хранения чего угодно, если нужно перебросить это внутреннее состояние через ререндер объекта (**напр. id таймера**)

{% code lineNumbers="true" %}
```typescript
const ref = useRef();
```
{% endcode %}

## • useMemo&#x20;

Оптимизирует вычисление функции. Мы передаем ему функцию, которая используется для вычисления и создания мемоизированного значения. **useMemo** будет пересчитывать это значение только при изменении одной из зависимостей функции.

{% code lineNumbers="true" %}
```typescript
useMemo(() => { ...some code}, [dependency])
```
{% endcode %}

## • React.memo

Cоздает компонент, который будет отображаться только при изменении его **props**

{% code lineNumbers="true" %}
```tsx
const RenderCatOnce = memo(Cat, () => true);
const AlwaysRenderCat = memo(Cat, () => false);
```
{% endcode %}

Второй аргумент, передаваемый в функцию **memo**, — предикат. **Предикат** — это функция, которая возвращает истину или ложь. Эта функция решает, нужно ли повторно отображать кличку кота. Только когда предикат возвращает false, Cat отображается заново. Обычно эта функция используется для проверки фактических значений.

{% code lineNumbers="true" %}
```typescript
const PureCat = memo(Cat, (prevProps, nextProps) => {
  return prevProps.name === nextProps.name);
}
```
{% endcode %}

{% hint style="info" %}
Можно сделать, так что из всех переданных пропсов повторный рендер будет происходить только при изменении одного из них.
{% endhint %}

{% hint style="info" %}
**PureComponent** — то же самое, что **React.memo**, но **PureComponent** используется только для компонентов-классов, а **React.memo** — только для FC. \\

В **React** чистым компонентом называется компонент, который при одних и тех же свойствах всегда отображает один и тот же вывод.
{% endhint %}

## • useCallback

Запоминает ссылку на функцию на момент первого рендера компонента

{% code lineNumbers="true" %}
```typescript
const memoizedCallback = useCallback(() => {
        function(a, b);
    }, [a, b]);
```
{% endcode %}

{% hint style="warning" %}
Частое использование хуков **useCallback** и **useMemo** может снизить производительность приложения. Когда вы проводите рефакторинг для повышения производительности, у него должна быть цель. Возможно, вы хотите, чтобы экран не зависал и не мерцал. Возможно, некоторые трудоемкие функции необоснованно замедляют скорость приложения.
{% endhint %}

## • useContext

Принимает объект контекста (значение, возвращённое из `React.createContext`) и возвращает текущее значение контекста для этого контекста. Текущее значение контекста определяется пропсом `value` ближайшего `<MyContext.Provider>` над вызывающим компонентом в дереве.

{% code lineNumbers="true" %}
```jsx
const value = useContext(MyContext);
```
{% endcode %}

## • useReducer

Принимает функцию редуктор и начальное состояние. Самое простое определение **функции-редуктора** заключается в том, что она принимает текущее состояние и возвращает новое.&#x20;

{% hint style="info" %}
Аналог функции **arr.reduce()**
{% endhint %}

{% code lineNumbers="true" %}
```tsx
function Checkbox() {
    const [checked, toggle] = useReducer((checked) => !checked, false);
   
    return (
        <>
            <input type="checkbox" value={checked} onChange={toggle} />
            {checked ? "checked" : "not checked"}
        </>
    );
}
```
{% endcode %}

{% code lineNumbers="true" %}
```tsx
function User() {
    const [user, setUser] = useReducer((user, newDetails) => 
    ({ ...user, ...newDetails }), firstUser);

    return (
        <>
            <button
                onClick={() => {
                    setUser({ admin: true });
                }}
            >
                Make Admin
            </button>
            ;
        </>
    );
}
```
{% endcode %}

## • useTransition

Разбивает рендер на части, тем самым позволяя реагировать на события пользователя во время отрисовки (**не позволяет зависать на сложных вычислениях**)

## • useDeferredValue

Работает как и **useTransition**. Работает с пропсами и используется, если мы не можем повлиять на вычисление этих пропсов в родительском компоненте.

## • useId <a href="#useid" id="useid"></a>

Это хук для создания уникальных идентификаторов, которые стабильны на сервере и клиенте, избегая при этом несоответствий гидратации.

{% code lineNumbers="true" %}
```jsx
function Checkbox() {
  const id = useId();
  return (
    <>
      <label htmlFor={id}>Do you like React?</label>
      <input id={id} type="checkbox" name="react"/>
    </>
  );
};
```
{% endcode %}

## • useActions

Позволяет получить все экшены, и не импортировать каждый раз **dispatch** в компонент

{% hint style="danger" %}
Не рекомендуется к использованию.
{% endhint %}

##
