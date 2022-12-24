# 6장 매개변수와 return 문을 정리하라

매개변수(input) return(output)
함수에 대한 이야기


## TIP 28 매개변수 기본값을 생성하라

```js
function convertWeight(weight, ounces, roundTo) {
  const oz = ounces / 16 || 0;
  const total = weight + oz;
  const conversion = total / 2.2;
  const round = roundTo === undefined ? 2: roundTo;
  return roundToDecimalPlace(conversion, round);
}

// 매개변수 기본값 적용
function convertWeight(weight, ounces = 0, roundTo = 2) {
  const total = weight + (ounces / 16);
  const conversion = total / 2.2;
  return roundToDecimalPlace(conversion, round);
}
```

## TIP 29 해체 할당으로 객체 속성에 접근하라

```js
// As-Is
function displayPhoto(photo) {
  const title = photo.title;
  const photographer = photo.photographer || 'Anonymous';
  const location = photo.location;
  const url = photo.src;

  const copy = { ...photo };
  delete copy.title;
  delete copy.photographer;
  delete copy.location;
  delete copy.src;

  const additional = Object.keys(copy).map(key => `${key}: ${copy[key]}`);

  return (`
    <img alt="Photo of ${title} by ${photographer}" src="${url}" />
    <div>${title}</div>
    <div>${photographer}</div>
    <div>Latitude: ${location[0]} </div>
    <div>Longitude: ${location[1]} </div>
    <div>${additional.join(' <br/> ')}</div>
  `);
}

// To-Be No.1
function displayPhoto(photo) {
  const {
    title,
    photographer = 'Anonymous',
    location: [latitude, longitude],
    src: url,
    ...other
  } = photo;
  const additional = Object.keys(other).map(key => `${key}: ${other[key]}`);
  return (`
    <img alt="Photo of ${title} by ${photographer}" src="${url}" />
    <div>${title}</div>
    <div>${photographer}</div>
    <div>Latitude: ${latitude} </div>
    <div>Longitude: ${longitude} </div>
    <div>${additional.join(' <br/> ')}</div>
  `);
}

// To-Be No.2
function displayPhoto({
  title,
  photographer = 'Anonymous',
  location: [latitude, longitude],
  src: url,
  ...other
}) {
  const additional = Object.keys(other).map(key => `${key}: ${other[key]}`);
  return (`
    <img alt="Photo of ${title} by ${photographer}" src="${url}" />
    <div>${title}</div>
    <div>${photographer}</div>
    <div>Latitude: ${latitude} </div>
    <div>Longitude: ${longitude} </div>
    <div>${additional.join(' <br/> ')}</div>
  `);
}
```

## TIP 30 키-값 할당을 단순화하라

```js
// START:landscape
const landscape = {
  title: 'Landscape',
  photographer: 'Nathan',
  location: [32.7122222, -103.1405556],
};
// END:landscape

const update =
// START:update
{
  title: 'Landscape',
  photographer: 'Nathan',
  city: 'Hobbs',
  state: 'NM',
};
// END:update

function determineCityAndState([latitude, longitude]) {
  // Geo lookup would happen here
  // START:region
  const region = {
    city: 'Hobbs',
    county: 'Lea',
    state: {
      name: 'New Mexico',
      abbreviation: 'NM',
    },
  };
  // END:region
  return region;
}
// START:handle
function getCityAndState({ location }) {
  const { city, state } = determineCityAndState(location);
  return {
    city,
    state: state.abbreviation,
  };
  // {
  //   city: 'Hobbs',
  //   state: 'NM'
  // }
}
// END:handle

// START:set
function setRegion({ location, ...details }) {
  const { city, state } = determineCityAndState(location);
  return {
    city,
    state: state.abbreviation,
    ...details,
  };
}
// END:set
```

## TIP 31 나머지 매개변수로 여러 개의 인수를 변수로 전달하라

사용하지 마세요!

### 모르는 단어



### 코딩연습


