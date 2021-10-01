# **SCSS**
SCSS는 CSS의 전처리 도구이다.  

CSS 문서의 작성을 더 효율적으로 할 수 있게 도와주지만, 실제 적용하기 위해서는 브라우저가 해석할 수 있도록 CSS로 컴파일을 거쳐야한다.

<br/>
<br/>

# **컴파일**
다양한 개발 의존성 패키지 중 parcel-bundler 등의 패키지는 SCSS의 컴파일 기능도 갖추고 있다. SCSS 파일이 감지되면 자동으로 sass를 설치하며 HTML에 직접 link 해도 자동으로 컴파일을 거쳐서 적용된다. 컴파일을 거친 css 파일은 프로젝트의 dist 폴더에 생성된다.

<br/>
<br/>

# **주석**
SCSS의 주석은 기존 CSS에서 사용하는 `/* */` 과 함께 JS에서 지원하는 `//`도 사용할 수 있다.  

`//`를 사용하여 주석처리 할 경우 컴파일 시 제외된다.

<br/>
<br/>

# **중첩**
SCSS의 가장 큰 특징은 선택자의 중첩이다.

중첩된 선택자는 하위 선택자로 컴파일되며,  
자식 선택자로 컴파일을 원한다면 css와 동일하게 `>`를 선택자 앞에 붙이면 된다.
```scss
.container {
  border: 1px solid gray;
  > .item1 {
    background-color: tomato;
    ul {
      li {
        font-size: 30px;
      }
    }
  }
  > .item2 {
    background-color: powderblue;
    ul {
      li {
        font-size: 40px;
      }
    }
  }
}
```

<br/>
<br/>

# **상위 선택자 참조**
선택자의 중첩 시 `&`를 통해 상위 선택자를 참조할 수 있다. 이 때 가리키는 선택자는 `&` 가 포함되어 있는 부모 요소이다.

`&` 가 사용된 선언문은 문법 구조상 `&` 가 가리키는 선택자의 하위 선택자처럼 보이지만  
`&` 가 가리키는 선택자와 동등한 관계(형제)를 갖는다.

```scss
// & = .btn
// .btn{}   
// .btn.active{}
.btn {
  color: red;
  &.active {
    color: blue;
  }
}

// & = li
// .list li:last-child {}
// 구조상으로는 .list li li:last-child {} 처럼 보이지만 위가 맞다.
.list {
  li {
    &:last-child {
      color: gray;
    }
  }
}

// .item-red {}
// .item-blue {}
// .item-green {}
.item {
  &-red {color: red;}
  &-blue {color: blue;}
  &-green {color: green;}
}
```

<br/>
<br/>

# **속성 중첩**
네임스페이스가 동일한 여러 속성을 중첩하여 작성할 수 있다.  

`:`와 `;`를 사용하여 작성한다.
```scss
.container {
  font: {
    size: 40px;
    weight: bold;
  };
  margin: {
    top: 10px;
    bottom: 5px;
  };
}
```

<br/>
<br/>

# **변수**
`$` 을 이용하여 변수를 선언하고 호출할 수 있다.  
`:` 으로 값을 할당한다.  

변수는 유효범위를 갖게 되며, 유효범위는 변수가 선언된 `{}` 범위 내부이다.  
`{}` 밖에서 선언할 경우 전역변수로 문서 전역에서 사용이 가능하다.  

재할당도 가능하지만, 상하위 중첩 관계에 상관 없이 재할당문 이후에 등장하는 모든 호출은 재할당 된 값을 갖게 된다.

```scss
.container {
  $size: 1234px;
  width: $size;
  .item {
    width: $size;
  }
}
```

<br/>
<br/>

# **산술 연산**
산술 연산자를 사용할 수 있다.  

나눗셈의 경우 `()`로 감싸거나 변수를 사용하거나 또는 다른 수식과 함께 사용하여야 한다. 

css 문법상 `/` 가 구분자로 쓰이는 경우가 있기 때문이다.

단위가 동일할 경우에만 가능하다. 단위가 다를 경우 `calc()` 함수를 사용해야 한다.
```scss
div {
  $size: 10px;

  margin: 10px + 10px;
  padding: 10px - 5px;
  width: 5px * 3;
  height: 30px % 7;

  font-size: (10px / 2);
  line-height: $size / 2;
  top: 10px + 10px / 2;
}
```

<br/>
<br/>

# **@mixin (재활용)**
여러 곳에서 반복 등장하는 선언을 저장해두고 간단하게 참조하여 사용할 수 있다.

함수처럼 매개변수를 선언하고 인자를 받아서 사용할 수 있다. 매개변수는 복수의 선언도 가능하며  `:`로 기본값을 지정할 수 있다.

매개변수가 복수일 경우 인자도 그 순서에 맞게 전달하여야 하는데, 하나의 인자만 전달하고 나머지는 기본값으로 사용하고 싶다면 매개변수명을 명시하여 같이 전달하면 된다. (키워드 인자)
```scss
@mixin center {
  display: flex;
  align-items: center;
  justify-content: center;
}

.container {
  @include center;
  .item {
    @include center;
  }
}

// 매개변수 사용
@mixin square($size: 100px, $color: red) {
  width: $size;
  height: $size;
  color: $color;
}

// 모두 기본값
.box-100 {
  @include square;
}

// 크기 200, 색상 기본값
.box-200 {
  @include square(200px)
}

// 크기 기본값, 색상 파랑 
// 매개변수명 없이 전달하면 $size가 blue로 전달된다.
.box-blue {
  @include square($color: blue)
}
```

<br/>
<br/>

# **반복문**
scss에서는 반복문을 사용할 수 있다.

`@for` 키워드로 반복문을 선언할 수 있다.  

반복문의 횟수를 카운트 할 변수 `i`를 선언하고  
`from through`를 통해 변수 `i` 가 몇부터 시작하여 몇까지 반복할지 선언한다.

JS와는 다르게 0이 아닌 1부터 카운트가 시작된다.

변수 `i` 를 적극적으로 활용하면 반복문을 더 유용하게 사용할 수 있다. 변수 `i`는 보간법을 통해 활용이 가능하다.

SCSS의 보간법은 `#{}` 으로 작성한다.

```scss
@for $i from 1 through 10 {
  .item:nth-child(#{$i}) {
    width: 100px;
  }
}
```

<br/>
<br/>

# **함수**
위에 나온 mixin은 함수와 유사한 구조를 갖고 있지만, SCSS에는 JS의 함수와 동일한 구조를 갖는 기능이 존재한다.  
```scss
@function ratio($width) {
  @return $width * 9/16
}
```

<br/>
<br/>

# **색상 내장 함수**
- `mix(color, color)`  
인자로 전달한 두 가지 색상을 섞는다.

- `lighten(color, %)`  
전달한 백분율만큼 색상이 밝아진다.

- `darken(color, %)`  
전달한 백분율만큼 색상이 어두워진다.

- `saturate(color, %)`  
전달한 백분율만큼 채도가 올라간다.  

- `desaturate(color, %)`  
전달한 백분율만큼 채도가 내려간다.  

- `grayscale(color)`  
회색으로 변경한다.

- `invert(color)`  
색반전  

- `rgba(color, 0.0~1.0)`  
색상의 투명도를 지정한다.

<br/>
<br/>

# **import**
`@import url()` 을 통해 css 파일 내에서 다른 css 파일을 불러올 수 있다.

`url()` 함수의 인자로 불러올 css의 경로를 입력하는데, SCSS에서는 `url()` 함수를 생략할 수 있다.  

확장자도 생략이 가능하며, 쉼표 구분으로 복수의 파일을 불러올 수 있다.

```scss
@import "./sub;", "./subsub";
```

<br/>
<br/>

# **데이터 종류**
- **number**  
  123, 0.5, 20px 등

- **string**  
  "./images/1.jpg", center 등  

- **color**  
red, #fff00, rgba() 등  

- **boolean**  
true, false  

- **null**  
  null

- **list**  
  array와 비슷하다.  
  ```scss
  $list: red, blue, green;
  ```

- **map**  
객체와 비슷하다.  
  ```scss
  $map: (
    r: red,
    b: blue,
    g: green
  )
  ```

  <br/>
  <br/>

# **@each**
list 혹은 map 데이터를 다루는데 사용한다. `for in` 문과 비슷하다.
```scss
$list: red, blue, green;
$map: (
  r: red,
  b: blue,
  g: green
);


// .box {color: red;}
// .box {color: blue;}
// .box {color: green;}
@each $c in $list {
  .box {
    color: $c;
  }
}

// .box-r {color: red;}
// .box-b {color: blue;}
// .box-g {color: green;}
@each $k, $v in $map {
  .box-#{$k} {
    color: $v;
  }
}
```

<br/>
<br/>

# **@content**
`@mixin` 으로 내용을 저장할 때 내용에 `@content;` 를 추가하면 `@incluede` 할 때 별도의 내용을 추가할 수 있다.
```scss
@mixin center {
  display: flex;
  justify-content: center;
  align-items: center;
  @content;
}

.box {
  @include center {
    width: 100px;   // 별도 내용 추가
  }
}
```