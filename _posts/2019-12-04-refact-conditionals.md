---
layout: post
title: JavaScript：如何重构你的条件语句
summary: 几个小技巧，让你的条件语句更优雅更易于阅读
featured-img: javascript
categories: Guide
---

### 1.利用`Array.includes`

不好的写法：

```javascript
const func = name => {
  if (name === 'Jerry' || name === 'Tom' || name === 'Speike') {
    // do somathing
  }
}
```

利用`Array.includes`重构：

```javascript
const names = [
  'Jerry',
  'Tom',
  'Speike'
];
const func = name => {
  if (names.includes(name)) {
    // do somathing
  }
}
```

### 2.使用对象代替多个`if`或`switch`

不好的写法：

```javascript
const getRace = name => {
  switch(name) {
    case 'Jerry':
      return 'mouse';
      breake;
    case 'Tom':
      return 'cat';
      breake;
    case 'Speike':
      return 'dog';
      breake;
    default:
      return 'animal';
  }
}
```

使用对象重构：

```javascript
const raceObj = {
  Jerry: 'mouse',
  Tom: 'cat',
  Speike: 'dog'
}
const getRace = name => {
  return raceObj[name] || 'animal';
}
```

### 3.提前`return`，而不是嵌套判断

不好的写法：

```javascript
const getAnimalInfo = animal => {
  let animalInfo;
  if (animal) {
    if (animal.name) {
      if (animal.age) {
        animalInfo = `Name: ${animal.name},age: ${animal.age}`;
      } else {
        animalInfo = 'No age';
      }
    } else {
      animalInfo = 'No name';
    }
  } else {
    animalInfo = 'No animal';
  }

  return animalInfo;
}
```

重构：

```javascript
const getAnimalInfo = animal => {
  if (!animal) return 'No animal';
  if (!animal.name) return 'No name';
  if (!animal.age) return 'No age';
  return `Name: ${animal.name},age: ${animal.age}`;
}
```