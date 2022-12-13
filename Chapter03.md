# 3장 특수한 컬렉션을 이용해 코드 명료성을 극대화하라

## TIP 10 객체를 이용해 정적인 키-값을 탐색하라

- 객체는 정적인 정보에 적합하다.

## TIP 11 Object.assign( )으로 조작 없이 객체를 생성하라

- `Object.assign`을 이용하면 복잡한 코드 작성 없이 새로운 객체를 생성할 수 있다.
- `Object.assign`을 사용할 때 기본값 객체를 수정하지 않기 위해 첫번째 매개변수는 `{}`(빈 객체)를 넣는다.
- 중첩된 객체가 있는 경우는 내부도 `Object.assign`를 사용하면 된다.
- 깊은 복사를 위해서는 `lodash`의 [clonedeep()](https://lodash.com/docs/4.17.15#cloneDeep) 메서드를 사용할 수 있다.
- 혹은 객체 펼침 연산자가 있다.

## TIP 12 객체 펼침 연산자로 정보를 갱신하라

- 객체 펼침 연산자는 시각적으로 보기 편하다는 장점이 있다.

## TIP 13 맵으로 명확하게 키-값 데이터를 갱신하라

- 맵(Map)을 이용하면 체이닝을 이용하여 데이터를 추가할 수 있다.
- 정보를 자주 변경하고 키-값을 가지는 경우에 유리하다.

## TIP 14 맵과 펼침 연산자로 키-값 데이터를 순회하라

- 맵(Map)은 순서대로 데이터를 저장한다.
- 하지만 정렬 메서드가 내장되어 있지 않다.

## TIP 15 맵 생성 시 부수 효과를 피하라

- 맵(Map)을 사본으로 복사하기 위해 펼침 연산자를 사용한다.

## TIP 16 세트를 이용해 고윳값을 관리하라

- 세트(Set)을 이용하면 고유값(Unique)을 가질 수 있다.

### 모르는 단어

- 컬렉션: 
- 위크맵:
- 위크셋:

### 코딩 연습

```js
// Deep Copy
const tester = {
  isLoading: false,
  answerPaper: [
    {
      answer: 'Britain',
      isCorrect: false
    },
    {
      answer: 'Syria',
      isCorrect: false
    },
    {
      answer: 'Hydrogen & Helium',
      isCorrect: true
    },
    {
      answer: 'Mana Duels',
      isCorrect: false
    },
    {
      answer: 'Pan troglodytes',
      isCorrect: true
    },
  ],
  reportPaper: {
    beginTime: 1670836746234,
    finishTime: 1670836816237,
  },
};
```
