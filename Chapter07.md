# 7장 유연한 함수를 만들어라

고차함수, 커링

## TIP 32 테스트하기 쉬운 함수를 작성하라

```js
// 문제
import { getTaxInformation } from './taxService';

function formatPrice(user, { price, location }) {
  const rate = getTaxInformation(location); // <label id="test.external" />
  const taxes = rate ? `plus $${price * rate} in taxes.` : 'plus tax.';

  return `${user} your total is: ${price} ${taxes}`;
}

export { formatPrice };

// Test 영역
import expect from 'expect';

import sinon from 'sinon';
import * as taxService from './taxService';
import { formatPrice } from './problem';

describe('format price', () => {
  let taxStub;

  beforeEach(() => {
    taxStub = sinon.stub(taxService, 'getTaxInformation'); // <label id="test.stub" />
  });

  afterEach(() => {
    taxStub.restore(); // <label id="test.restore" />
  });

  it('should return plus tax if no tax info', () => {
    taxStub.returns(null); // <label id="test.stub2" />
    const item = { price: 30, location: 'Oklahoma' };
    const user = 'Aaron Cometbus';
    const message = formatPrice(user, item);
    const expectedMessage = 'Aaron Cometbus your total is: 30 plus tax.';
    expect(message).toEqual(expectedMessage);
  });

  it('should return plus tax information', () => {
    taxStub.returns(0.1);

    const item = { price: 30, location: 'Oklahoma' };
    const user = 'Aaron Cometbus';
    const message = formatPrice(user, item);
    const expectedMessage = 'Aaron Cometbus your total is: 30 plus $3 in taxes.';
    expect(message).toEqual(expectedMessage);
  });
});

// 해결 방법
// START:final
function formatPrice(user, { price, location }, getTaxInformation) {
  const rate = getTaxInformation(location);
  const taxes = rate ? `plus $${price * rate} in taxes.` : 'plus tax.';
  return `${user} your total is: ${price} ${taxes}`;
}

// 결과
import expect from 'expect';

import { formatPrice } from './test';

describe('format price', () => {
  it('should return plus tax if no tax info', () => {
    const item = { price: 30, location: 'Oklahoma' };
    const user = 'Aaron Cometbus';
    const message = formatPrice(user, item, () => null);
    expect(message).toEqual('Aaron Cometbus your total is: 30 plus tax.');
  });

  it('should return plus tax information', () => {
    const item = { price: 30, location: 'Oklahoma' };
    const user = 'Aaron Cometbus';
    const message = formatPrice(user, item, () => 0.1);
    expect(message).toEqual('Aaron Cometbus your total is: 30 plus $3 in taxes.');
  });
});
```

스텁: 외부 코드를 덮어 명시적인 결과를 반환 (상태)
모의객체: 기대값을 작성함 (행동)
스파이: 코드를 실행하고 어떻게 호출되었는지 확인

## TIP 33 화살표 함수로 복잡도를 낮춰라

```js
// 문제점
// START: name
const name = {
  first: 'Lemmy',
  last: 'Kilmister',
};

function getName({ first, last }) {
  return `${first} ${last}`;
}
// END: name

// START: full
function getFullName({ first, last }) {
  return {
    fullName: `${first} ${last}`,
  };
}
// END: full

// START: high
const discounter = discount => {
  return price => {
    return price * (1 - discount);
  };
};
const tenPercentOff = discounter(0.1);
tenPercentOff(100);
// 90
// END: high

export { getName, getFullName, discounter, name };

// 해결
// START: name
const comic = {
  first: 'Peter',
  last: 'Bagge',
  city: 'Seattle',
  state: 'Washington',
};

const getName = ({ first, last }) => `${first} ${last}`;
getName(comic);
// Peter Bagge
// END: name

// START: return
const getFullName = ({ first, last }) => ({ fullName: `${first} ${last}` });
getFullName(comic);
// { fullName: 'Peter Bagge' }
// END: return

// START:multi
const getNameAndLocation = ({ first, last, city, state }) => ({
  fullName: `${first} ${last}`,
  location: `${city}, ${state}`,
});
getNameAndLocation(comic);
// {
//   fullName: 'Peter Bagge',
//   location: 'Seattle, Washington'
// }
// END:multi

// START: high
const discounter = discount => price => price * (1 - discount);

const tenPercentOff = discounter(0.1);
tenPercentOff(100);
// 90;
// END: high

// START: together
discounter(0.1)(100);
// 90
// END: together

export { comic, discounter, getName, getNameAndLocation, getFullName };
```

## TIP 34 부분 적용 함수로 단일 책임 매개변수를 관리하라

```js
// 문제
import { building, manager, program, exhibit } from './partial';

// START:func
function mergeProgramInformation(building, manager, program) {
  const { hours, address } = building;
  const { name, phone } = manager;
  const defaults = {
    hours,
    address,
    contact: name,
    phone,
  };

  return { ...defaults, ...program };
}
// END:func

// START:implement
const programInfo = mergeProgramInformation(building, manager, program);

const exhibitInfo = mergeProgramInformation(building, manager, exhibit);
// END:implement

export { mergeProgramInformation };

// 해결
// START:func
function mergeProgramInformation(building, manager) {
  const { hours, address } = building;
  const { name, phone } = manager;
  const defaults = {
    hours,
    address,
    contact: name,
    phone,
  };

  return program => {
    return { ...defaults, ...program };
  };
}
// END:func
// START:invoke
const programInfo = mergeProgramInformation(building, manager)(program);
// {
//    name: 'Presenting Research',
//    room: '415',
//    hours: '3 - 6',
//    address: 'Jayhawk Blvd',
//    contact: 'Augusto',
//    phone: '555-555-5555'
//  }

const exhibitInfo = mergeProgramInformation(building, manager)(exhibit);
// {
//    name: 'Emerging Scholarship',
//    contact: 'Dyan'
//    hours: '8 a.m. - 8 p.m.',
//    address: 'Jayhawk Blvd'
//    phone: '555-555-5555'
//  }
// END:invoke
```

## TIP 35 커링과 배열 메서드를 조합한 부분 적용 함수를 사용하라

```js
// 문제
import { dogs } from './curry';
// START: func
function getDogNames(dogs, filter) {
  const [key, value] = filter;
  return dogs
    .filter(dog => dog[key] === value)
    .map(dog => dog.name);
}

getDogNames(dogs, ['color', 'black']);
// ['max', 'don']
// END: func

export { getDogNames };

// 해결
/* eslint-disable */
// START:dogs
const dogs = [
  {
    name: 'max',
    weight: 10,
    breed: 'boston terrier',
    state: 'wisconsin',
    color: 'black',
  },
  {
    name: 'don',
    weight: 90,
    breed: 'labrador',
    state: 'kansas',
    color: 'black',
  },
  {
    name: 'shadow',
    weight: 40,
    breed: 'labrador',
    state: 'wisconsin',
    color: 'chocolate',
  },
];
// END:dogs

// START: func
function getDogNames(dogs, filterFunc) {
  return dogs
  .filter(filterFunc)
  .map(dog => dog.name)
}

getDogNames(dogs, dog => dog.weight < 20);
// ['max']
// END: func

// START:curry
const weightCheck = weight => dog => dog.weight < weight;

getDogNames(dogs, weightCheck(20));
// ['max']

getDogNames(dogs, weightCheck(50));
// ['max', 'shadow']
// END:curry

// START:identity
const identity = field => value => dog => dog[field] === value;
const colorCheck = identity('color');
const stateCheck = identity('state');

getDogNames(dogs, colorCheck('chocolate'));
// ['shadow']

getDogNames(dogs, stateCheck('kansas'));
// ['don']
// END:identity

// START:all
function allFilters(dogs, ...checks) {
  return dogs
  .filter(dog => checks.every(check => check(dog)))
  .map(dog => dog.name);
}
allFilters(dogs, colorCheck('black'), stateCheck('kansas'));
// ['Don']

function anyFilters(dogs, ...checks) {
  return dogs
  .filter(dog => checks.some(check => check(dog)))
  .map(dog => dog.name);
}

anyFilters(dogs, weightCheck(20), colorCheck('chocolate'));
// ['max', 'shadow']
// END:all

export { anyFilters, dogs, getDogNames, colorCheck, weightCheck, stateCheck, allFilters }
```

## TIP 36 화살표 함수로 문맥 혼동을 피하라

- this

### 모르는 단어



### 코딩연습


