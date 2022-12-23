# Кастомные хуки

## • useDebounce

Вызывает переданный callback через время задержки. Реализует работу setTimeout.&#x20;

{% code lineNumbers="true" %}
```typescript
import { MutableRefObject, useCallback, useRef } from "react";

/**
 * Реализует работу setTimeout
 * @param callback 
 * @param delay
 * @returns
 */

export function useDebounce(callback: (...args: any[]) => void, delay: number) {
    const timer = useRef() as MutableRefObject<ReturnType<typeof setTimeout>>;

    return useCallback(
        (...args: any[]) => {
            if (timer.current) {
                clearTimeout(timer.current);
            }

            timer.current = setTimeout(() => {
                callback(...args);
            }, delay);
        },
        [callback, delay]
    );
}

```
{% endcode %}

## • useTrottle

Вызывает переданный callback каждые delay. Реализует работу setInterval.

{% code lineNumbers="true" %}
```typescript
import { useCallback, useRef } from "react";

/**
 * Реализует работу setInterval
 * @param callback
 * @param delay
 * @returns
 */

export function useThrottle(callback: (...args: any[]) => void, delay: number) {
    const throttleRef = useRef(false);

    return useCallback(
        (...args: any[]) => {
            if (!throttleRef.current) {
                callback(...args);
                throttleRef.current = true;

                setTimeout(() => {
                    throttleRef.current = false;
                }, delay);
            }
        },
        [callback, delay]
    );
}

```
{% endcode %}

## • useHover

Обрабатывает наведение курсора на элемент

{% code lineNumbers="true" %}
```typescript
import { useCallback, useMemo, useState } from "react";


interface UseHoveBind {
    onMouseEnter: () => void;
    onMouseLeave: () => void;
}

type useHoverResult = [boolean, UseHoveBind];

/**
 * Обрабатывает наведение курсора на элемент
 * @returns кортеж [isHover, onMouseEnter, onMouseLeave]
 */

export function useHover(): useHoverResult {
    const [isHover, setIsHover] = useState(false);

    const onMouseEnter = useCallback(() => {
        setIsHover(true);
    }, []);

    const onMouseLeave = useCallback(() => {
        setIsHover(false);
    }, []);

    return useMemo(
        () => [isHover, { onMouseEnter, onMouseLeave }],
        [isHover, onMouseEnter, onMouseLeave]
    );
}

```
{% endcode %}

## • useInfiniteScroll

Реализует бесконечный scroll ленты

{% code lineNumbers="true" %}
```typescript

import { MutableRefObject, useEffect } from "react";

export interface UseInfiniteScrollOptions {
    callback?: () => void;
    triggerRef: MutableRefObject<HTMLElement>;
    wrapperRef: MutableRefObject<HTMLElement>;
}

/**
 * Вызывает callback когда triggerRef попадает во viewport
 * @param param0 объект типа UseInfiniteScrollOptions
 */

export function useInfiniteScroll({
    callback,
    triggerRef,
    wrapperRef,
}: UseInfiniteScrollOptions) {
    useEffect(() => {
        let observer: IntersectionObserver | null = null;
        const triggerElement = triggerRef.current;
        const wrapperElement = wrapperRef.current;

        if (callback) {
            const options = {
                root: wrapperElement,
                rootMargin: "0px",
                threshold: 1.0,
            };

            observer = new IntersectionObserver(([entry]) => {
                if (entry.isIntersecting) {
                    callback();
                }
            }, options);

            observer.observe(triggerElement);
        }

        return () => {
            if (observer && triggerElement) {
                // eslint-disable-next-line
                observer.unobserve(triggerElement);
            }
        };
    }, [triggerRef, wrapperRef, callback]);
}

```
{% endcode %}

## • useOutsideClick

Закрывает Popup если клик сделан мимо него

{% code lineNumbers="true" %}
```typescript
import React, { MutableRefObject, useCallback, useEffect, useRef } from "react";

/**
 * Закрывает Popup если сделан клик мимо него
 * @param ref - элемент по которому можно кликать
 * @param isOpen - флаг рендера элемента
 * @param setIsOpen - функция изменения флага рендера
 */

export function useOutsideClick(
    ref: React.MutableRefObject<any>,
    isOpen: boolean,
    setIsOpen: (arg1: boolean) => void
) {
    const timerRef = useRef() as MutableRefObject<
        ReturnType<typeof setTimeout>
    >;
    const listener = useCallback(
        (e: MouseEvent | TouchEvent) => {
            const node = ref.current;
            if (!node || node.contains(e.target)) {
                return;
            }

            if (isOpen) {
                setIsOpen(false);
            }
        },
        [ref, setIsOpen, isOpen]
    );

    useEffect(() => {
        if (isOpen) {
            timerRef.current = setTimeout(() => {
                document.addEventListener("click", listener);
            }, 50);
        }

        return () => {
            clearTimeout(timerRef.current);
            document.removeEventListener("click", listener);
        };
    }, [isOpen, listener]);
}

```
{% endcode %}

## •  useComponentDidMount

Возвращает true если элемент монтировался

{% code lineNumbers="true" %}
```typescript
import { useEffect, useRef } from "react";

/**
 * Возвращает true если элемент монтировался
 * @returns флаг boolean
 */
export function useComponentDidMount() {
    const ref = useRef<boolean | null>();

    useEffect(() => {
        ref.current = true;
    }, []);

    return ref.current;
}

```
{% endcode %}

## • useInitialEffect

Выполняет callback только при первой отрисовке компонента

{% code lineNumbers="true" %}
```typescript
import { useEffect } from "react";

/**
 * Выполняет callback только при первой отрисовке компонента
 * @param callback
 */
export function useInitialEffect(callback: () => void) {
    useEffect(() => {
        if (__PROJECT__ !== "storybook" && __PROJECT__ !== "jest") {
            callback();
        }
        // eslint-disable-next-line
    }, []);
}

```
{% endcode %}

## • useMatchMedia

Определяет тип устройства пользователя по ширине экрана

{% code lineNumbers="true" %}
```typescript
import { useState, useLayoutEffect } from 'react';

interface ReturnType {
    isMobile: boolean;
    isTablet: boolean;
    isDesktop: boolean;
}

// кастомизировать под проект
const queries = [
    '(max-width: 767px)',
    '(min-width: 766px) and (max-width: 1199px)',
    '(min-width: 1200px)',
];

/**
 * Определяет тип устройства пользователя
 * @returns объект типа {isMobile: boolean; isTablet: boolean; isDesktop: boolean;}
 */

export function useMatchMedia(): ReturnType {
    const mediaQueryLists = queries.map((query) => matchMedia(query));

    const getValues = () => mediaQueryLists.map((list) => list.matches);

    const [values, setValues] = useState(getValues);

    useLayoutEffect(() => {
        const handler = () => setValues(getValues);

        mediaQueryLists.forEach((list) =>
            list.addEventListener('change', handler)
        );

        return () =>
            mediaQueryLists.forEach((list) =>
                list.removeEventListener('change', handler)
            );
        // eslint-disable-next-line react-hooks/exhaustive-deps
    }, []);

    return ['isMobile', 'isTablet', 'isDesktop'].reduce(
        (acc, screen, index) => ({
            ...acc,
            [screen]: values[index],
        }),
        {} as ReturnType
    );
}

```
{% endcode %}

## • useInput

Обрабатывает ввод данных в управляемый input

{% code lineNumbers="true" %}
```typescript
import React, { useState } from "react";

/**
 * Обрабатывает ввод данных в управляемый input
 * @param initialValue
 * @returns кортеж [{value, onChange}, resetValue]
 */

export function useInput(initialValue: string) {
    const [value, setValue] = useState<string>(initialValue);

    const onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
        setValue(e.target.value);
    };

    return [{ value, onChange }, () => setValue(initialValue)];
}

```
{% endcode %}

## • useLocalStorage

Получает и сохраняет данные в localStorage

{% code lineNumbers="true" %}
```typescript
import { useState, useEffect } from "react";

/**
 * Получает и сохраняет данные в localStorage
 * @param initialValue
 * @param key
 * @returns кортеж [value, setValue]
 */

export function useLocalStorage(initialValue: any, key: string) {
    const getValue = () => {
        const storage = localStorage.getItem(key); // string | null

        if (storage) {
            return JSON.parse(storage);
        }

        return initialValue;
    };

    const [value, setValue] = useState<any>(getValue);

    useEffect(() => {
        localStorage.setItem(key, JSON.stringify(value));
    }, [value, key]);

    return [value, setValue];
}

```
{% endcode %}

## • useMountedRef

Показывает монтировался компонент или нет

{% code lineNumbers="true" %}
```typescript
import { useEffect, useRef } from "react";

/**
 * Определяет монтировался компонент или нет
 * @returns флаг boolean
 */

export function useMountedRef() {
    const mounted = useRef(false);

    useEffect(() => {
        mounted.current = true;

        return () => {
            mounted.current = false;
        };
    });

    return mounted;
}
```
{% endcode %}

## • useIterator

Index используется для идентификации текущего элемента в массиве. Этот хук возвращает текущий элемент, item\[i] и функции для итерации по массиву: prev и next. Эти функции либо уменьшают, либо увеличивают значение i, вызывая setIndex и повторный рендеринг хука с новым индексом.

{% code lineNumbers="true" %}
```typescript
import { useCallback, useMemo, useState } from "react";

/**
 * Итерируется по переданному массиву
 * @param items массив элементов для перебора
 * @param initialIndex
 * @returns кортеж [item || items[0], prev, next]
 */

export function useIterator(items = [], initialIndex = 0) {
    const [i, setIndex] = useState(initialIndex);

    const prev = useCallback(() => {
        if (i === 0) {
            setIndex(items.length - 1);
            return;
        }
        setIndex(i - 1);
        // eslint-disable-next-line react-hooks/exhaustive-deps
    }, [i]);

    const next = useCallback(() => {
        if (i === items.length - 1) {
            setIndex(0);
            return;
        }
        setIndex(i + 1);
        // eslint-disable-next-line react-hooks/exhaustive-deps
    }, [i]);

    const item = useMemo(
        () => items[i],
        // eslint-disable-next-line react-hooks/exhaustive-deps
        [i]
    );

    return [item || items[0], prev, next];
}

```
{% endcode %}

## • useRequest

Принимает запрос на сервер. Например function fetch() { return axios.get(url) } и возвращает состояние обработки запроса.

{% code lineNumbers="true" %}
```typescript
import { useEffect, useState } from "react";

/**
 * Обрабатывает состояния отправки запроса на сервер
 * @param request запрос на сервер
 * @returns кортеж [data, loading, error]
 */
export function useRequest(request: any) {
    const [data, setData] = useState<any>(null);
    const [loading, setLoading] = useState<boolean>(false);
    const [error, setError] = useState<any>("");

    useEffect(() => {
        setLoading(true);
        request()
            .then((response: any) => {
                setData(response.data);
            })
            .catch((error: any) => {
                setError(error);
            })
            .finally(() => {
                setLoading(false);
            });
    }, [request]);

    return [data, loading, error];
}

```
{% endcode %}

