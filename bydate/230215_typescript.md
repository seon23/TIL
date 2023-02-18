# TypeScript

> Detecting errors in code without running it is referred to as _static_ checking. Determining what's an error and what's not based on the kinds of values being operated on is known as static _type_ checking.<br> > _출처:_ [_TypeScript for the New Programmer_](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html#typescript-a-static-type-checker)

> This means that if you move code from JavaScript to TypeScript, it is **guaranteed** to run the same way, even if TypeScript thinks that the code has type errors.<br> > _출처:_ [_TypeScript for the New Programmer_](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html#typescript-a-static-type-checker)

> **타입스크립트는 compile-time type checker가 있는 자바스크립트 실행환경이다.**><br> > _출처:_ [_TypeScript for the New Programmer_](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html#learning-javascript-and-typescript)

평소에는 `interface`사용, 특별한 기능이 필요할 때는 `type`을 사용한다.

중요한 타입스크립트의 타입 중 일부

- T[]: mutable array이며, Array<T>와 같다.
- [T, T]: 길이는 고정되어 있지만 mutable한 튜플이다.

> To get an error when TypeScript produces an `any`, use `"noImplicitAny": true`, or `"strict": true` in `tsconfig.json`.<br>_출처:_ [_TypeScript for Functional Programmers_](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html#gradual-typing)

> Type aliases behave differently from interfaces with respect to recursive definitions and type parameters, however.<br>_출처:_ [_TypeScript for Functional Programmers_](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html#structural-typing)

> `string`, `Array` and `Function` have built-in type predicates, conveniently leaving the object type for the `else` branch. It is possible, however, to generate unions that are difficult to differentiate at runtime. For new code, it’s best to build only discriminated unions.<br>_출처:_ [_TypeScript for Functional Programmers_](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html#unions)

- 여기서 <mark>predicate</mark>이 무엇을 뜻하는지 모르겠다.
- 셋 다 내장 타입 predicate을 가지며 `else` branch에 객체 타입을 남긴다? 하지만 런타임에 분리하기 어려운 union 생성이 가능하다? 새로운 코드에서는 discriminated unions(서로소 집합 개념)만 빌드하는 게 좋다?
- 그리고 추가로 했던 말이, "함수와 배열은 objects at runtime이지만 각자의 predicate을 갖는다."인데, 이게 무슨 의미인지 모르겠다.

```ts
type Combined = { a: number } & { b: string };
type Conflicting = { a: number } & { a: string };
```

> Intersection and union are recursive in case of conflicts, so `Conflicting.a: number & string`.  
> _출처:_ [_TypeScript for Functional Programmers_](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html#intersections)

- Intersection, union 방식 타입 지정은 충돌 상황에서 recursive이다? 재귀적으로 동작한다? 충돌이 있는 상화에서 intersection과 union은 recursive이며, 그래서 Conflicting.a 타입은 number & string라고 했다. `recursive`가 무슨 뜻일까?
