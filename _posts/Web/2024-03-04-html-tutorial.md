---
title:  "[HTML5] HTML 총정리" 
excerpt: ""

categories:
  - Web
tags:
  - [HTML5]

toc: true
toc_sticky: true
 
date: 2024-03-04
last_modified_at: 2024-03-04
---

---

💡 이 글은 <a href="https://wikidocs.net/book/7596" target="_blank">입문자를 위한 HTML5</a>를 참고하여 공부하면서 핵심만 짧게 요약한 문서입니다.
{: .notice--info}

## 1. 기초 문법

* 태그(Tag)
    ```html
    <tag>content</tag>
    <tag> or <tag/>
    ```
* 속성(Attribute)
    ```html
    <tag attribute="value">content</tag>
    <tag attribute="value">
    ```
* 주석(Comment)
    ```html
    <!-- Any Comments -->
    ```

## 2. 문서의 기본 구조

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="utf-8">
    <title>Title</title>
</head>
<body>
    Contents
</body>
</html>
```

- `<!DOCTYPE html>`: Markup language type
- `<html lang='ko'>` & `</html>`: Start & End
- `<head>`: Informations (encoding, title, etc)
- `<body>`: Contents

## 3. 텍스트(Text)

### 블록 레벨 요소(Block Level Element)

> 태그를 사용해 요소를 삽입했을 때 혼자 한 줄을 차지하는 요소

* 제목(Head): `<h1> ~ <h6>`
* 문단(Paragraph): `<p>`
* 인용구(Quote): `<blockquote>` \| Attributes: `cite`
* 수평선(Horizon): `<hr>` \| Attributes: `color`, `size`
* 코드(Code): `<pre>`

### 인라인 레벨 요소(Inline Level Element)

> 태그에 포함된 콘텐츠 크기만큼만 공간을 차지하는 요소

* 개행(Line Breaking): `<br>`
* 볼드, 기울임꼴, 형광펜: `<strong>`, `<em>`, `<mark>`
* 취소선, 밑줄: `<del>`, `<u>`
* 엔티티 코드(Entity Code)
    - \[ \] (Blank Space): `&nbsp;`
    - \[&\]: `&amp;`, \[<\]: `&lt;`, \[>\]: `&gt;`
    - Reference: <https://entitycode.com/>

### 컨테이너와 전역 속성

* 컨테이너(Container): 다른 요소 여럿을 묶어 관리하는 태그
    - 블록 레벨: `<div>`
    - 인라인 레벨: `<span>`
* 전역 속성(Global Attributes): 모든 HTML 태그에서 공통적으로 사용할 수 있는 속성
    - 고유 식별자: `id`
    - 그룹화 식별자: `class`
    - 툴팁(tooptip): `title`

## 4. 이미지(Image)

### `<img>` 태그

> `<img>` 태그는 인라인 레벨 요소이다.

* 이미지 경로 또는 URL: `src`
* 대체 텍스트: `alt`
* 너비와 높이: `width`, `height`
  
### `<figure>` 및 `<picture>` 태그

* 이미지, 오디오, 비디오 등에 자막 삽입: `<figure>`
    ```html
    <body>
        <figure>
            <img src=[URLorPATH]>
            <figcaption>caption</figcaption>
        </figure>
    </body>
    ```
* 화면 크기에 따라 다른 이미지 표시: `<picture>`
    ```html
    <body>
        <picture>
            <source media="(max-width: 375px)" srcset="image1.png">
            <source media="(max-width: 768px)" srcset="image2.png">
            <source media="(max-width: 1024px)" srcset="image3.png">
            <img src=[URLorPATH] alt="source의 모든 조건이 맞지 않거나 브라우저 호환 문제가 있을 때">
        </picture>
    </body>
    ```

## 5. 링크(Link)

### `<a>` 태그

> `<a>` 태그는 인라인 레벨 요소이다.

* 하이퍼텍스트(Hypertext Reference): `href`
    ```html
    <a href="https://www.naver.com/">링크로 이동하기</a>
    <a href="tel:010-1234-5678">전화 걸기</a>
    <a href="mailto:eansdrawer@naver.com">메일 보내기</a>
    ```
* 새 탭 설정 속성: `target`
    - 현재 탭에서 열기(Default): `target="_self"`
    - 새 탭에서 열기: `target="_blank"`

## 6. 목록(List)

### 순서 없는 목록(Unordered List)

* 태그: `<ul>`
* 기호: `type` - disc, circle, square, none
  
```html
<ul>
    <li>Item 1</li>
    <li>Item 2</li>
</ul>
```

### 순서 있는 목록(Ordered List)

* 태그: `<ol>`
* 시작 번호: `start` - 자연수 지정 (default=1)
* 기호: `type` - A, a, 1, i, I

```html
<!-- c,d로 출력 -->
<ol start="3" type="a">
    <li>유체역학</li>
    <li>전자회로</li>
</ol>
```

## 7. 표(Table)

### 표 만들기

* `<table>`: 하나의 표를 나타내는 태그 \| Attributes: `border`
* `<tr>`: 하나의 행을 나타내는 태그
* `<th>`: 행 안에서 제목(Table Head)에 해당하는 셀을 나타내는 태그
* `<td>`: 행 안에서 항목(Table Data)에 해당하는 셀을 나타내는 태그

```html
<body>
  <table border="1">
    <tr>
      <th>한국 선수</th>
      <th>일본 선수</th>
      <th>미국 선수</th>
    </tr>
    <tr>
      <td>김철수</td>
      <td>오오다</td>
      <td>제임스</td>
    </tr>
  </table>
</body>
```

<img src="https://wikidocs.net/images/page/163980/05-2-2.png">

### 제목(자막) 추가하기

* `<caption>`: 가운데 정렬 제목 (Table 내부)
    ```html
    <body>
        <table border="1">
            <caption>
            <strong>국가대표 선수 명단</strong>
            <br>
            남자 마라톤
            </caption>
            <tr>
            <th>한국 선수</th>
            <th>일본 선수</th>
            <th>미국 선수</th>
            </tr>
            <tr>
            <td>김철수</td>
            <td>오오다</td>
            <td>제임스</td>
            </tr>
        </table>
    </body>
    ```
    <img src="https://wikidocs.net/images/page/163980/05-2-3.png">

* `<figure>`: 왼쪽 정렬 + 여백 제목 (Table 외부, 독립)
    ```html
    <body>
        <figure>
            <figcaption>
            <strong>국가대표 선수 명단</strong>
            </figcaption>
            <table border="1">
            <tr>
                <th>한국 선수</th>
                <th>일본 선수</th>
                <th>미국 선수</th>
            </tr>
            <tr>
                <td>김철수</td>
                <td>오오다</td>
                <td>제임스</td>
            </tr>
            </table>
        </figure>
    </body>
    ```
    <img src="https://wikidocs.net/images/page/163980/05-2-4.png">

### 표 구조 태그
> 아래 태그들을 사용했을 때와 그렇지 않을 때의 차이는 존재하지 않는다.
> 대신, 코드 유지 및 보수가 편리해지고 웹 접근성을 향상시킬 수 있다.

* `<thead>` : 표의 제목을 나타내는 영역
* `<tbody>` : 표의 본문을 나타내는 영역
* `<tfoot>` : 표의 요약 부분을 나타내는 영역

```html
<body>
  <table border="1">
    <thead>
      <tr>
        <th>한국 선수</th>
        <th>일본 선수</th>
        <th>미국 선수</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>김철수</td>
        <td>오오다</td>
        <td>제임스</td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <td>세계 랭킹 5위</td>
        <td>세계 랭킹 8위</td>
        <td>세계 랭킹 2위</td>
      </tr>
    </tfoot>
  </table>
</body>
```
<img src="https://wikidocs.net/images/page/163980/05-2-5.png">

## 8. 입력 폼(Form)

### `<input>` 태그 

> `<input>` 태그는 인라인 레벨 요소이다.   
> `type`에 따라 `<input>`의 모양은 천차만별이다.


* `type="text"`: 한 줄의 텍스트 (Default)
    - Attributes: `placeholder`, `maxlength`, `size`
* `type="password"`: 입력한 내용이 화면에 표시되지 않음
    - Attributes: `"text"`와 유사
  
    ```html
    <body>
        <h1>type="text" & type="password"</h1>
        <input type="text" placeholder="아이디를 입력하세요">
        <br>
        <input type="password" placeholder="비밀번호를 입력하세요">
    </body>
    ```
    <img src="https://wikidocs.net/images/page/164305/06-1-5.png">
* `type="number"`: 숫자 입력 (증감 버튼 제공)
    - Attributes: `min`, `max`, `step`
    - 그 외 숫자 입력 type: `"data"`, `"time"`, `"range"` 등
* `type="color"`: 색상 입력 (색상표 제공)
* `type="button"`: 클릭할 수 있는 버튼
    - Attributes: `value`
* `type="radio"`: 단일 선택 가능한 선택지
* `type="checkbox"`: 중복 선택 가능한 선택지
    ```html
    <body>
        <p>좋아하는 과목을 하나만 고르세요</p>
        <input type="radio" name="subject">국어 
        <input type="radio" name="subject">영어 
        <input type="radio" name="subject">수학 

        <p>좋아하는 음식을 모두 고르세요</p>
        <input type="checkbox" name="food">제육볶음 
        <input type="checkbox" name="food">돈까스 
        <input type="checkbox" name="food">떡볶이 
    </body>
    ```
    <img src=https://wikidocs.net/images/page/164305/06-1-10.png>
* `<label>` 태그: 입력 요소에 라벨을 붙이는 역할
    1. `<label>` 태그 안에 입력 요소 포함
    2. 입력 요소 `id`를 기반으로 `<label>` 태그와 짝지어주기
   
    ```html
    <body>
        <h1>로그인 해주세요</h1>
        <label>
            아이디
            <input type="text">
        </label>
        <br>
        <label for="pw">비밀번호</label>
        <input id="pw" type="password">
    </body>
    ```
    <img src="https://wikidocs.net/images/page/164305/06-1-11.png">
* ☑️ <a href="https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input" target="_blank">`input` 더 자세히 알아보기</a>
  
### 입력 요소 태그

* `<select>` 와 `<option>`: 드롭다운 메뉴
    - `<select>` Attributes
        + `size`: 화면에 표시될 메뉴 항목 개수 지정, 메뉴 형태 변경
        + `multiple`: 여러 개를 함께 선택하도록 하는 bool 속성, `Ctrl` 누른 상태에서 선택
    
        ```html
        <body>
            <select size="3" multiple>
                <option>커피</option>
                <option>콜라</option>
                <option>쥬스</option>
                <option>생수</option>
                <option>녹차</option>
                <option>맥주</option>
            </select>
        </body>
        ```
        <img src="https://wikidocs.net/images/page/164328/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-04-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_2.03.46.png">

    - `<option>` Attributes
        + `selected`: 기본 선택 항목 지정 (없으면 맨 처음 항목)
        + `value`: 최종적으로 반영될 값 지정

        ```html
        <body>
            <select>
                <option value="coffee">커피</option>
                <option value="coke">콜라</option>
                <option value="juice">쥬스</option>
                <option value="water" selected>생수</option>
                <option value="tea">녹차</option>
                <option value="beer">맥주</option>
            </select>
        </body>
        ```
        <img src="https://wikidocs.net/images/page/164328/06-2-4.png">

* `<textarea>`: 여러 줄 텍스트 입력
    - Attributes: `col`, `row` - 문자 개수 기준
* `<progress>`: 진척도 표시
    - Attributes: `value`, `max="1"`
* `<button>`: 버튼 (`<input>` 활용해도 동일)

### 입력 폼 `<form>`

* `action`: 입력 값을 처리할 서버 프로그램(URL) 지정
* `method`: 입력 값을 서버로 전송하는 방식 지정

    | &nbsp; | 주소창 표시  | 길이 제한 |
    | :----: | :---------: | :-----: |
    | `get`  | 표시됨       | 256~4096바이트 |
    | `post` | 표시되지 않음 | 제한 없음 |

* 입력 요소에 `name` 추가하기: 서버 전송 시 입력 값의 이름으로 사용
    ```html
    <body>
        <form method="post" action="fake_server.php">
            <label for="name">이름</label>
            <input type="text" id="name" name="name">
            <br>
            <label for="age">나이</label>
            <input type="number" id="age" name="age">
            <br>
            <label for="character">성격</label>
            <textarea id="character" name="character"></textarea>
            <br>
            <input type="button" value="전송">
        </form>
    </body>
    ```
    <img src="https://wikidocs.net/images/page/164428/06-3-2.png">

* `submit` 타입 사용하기: 클릭 시 서버로 값 전송, `button`과 화면 상 차이 없음

    ```html
    <input type="submit" value="전송">
    ```

## 9. 미디어(Media)

### 오디오(Audio): `<audio>`

```html
<body>
    <!-- 컨트롤 패널로 재생할 수 있는 오디오 -->
    <audio src="./happydog.mp3" controls></audio>
    <!-- 자동으로 재생되고 반복 재생되는 오디오 -->
    <audio src="./happydog.mp3" autoplay loop></audio>
</body>
```

* `controls` : 오디오 파일의 컨트롤 패널 표시
* `autoplay` : 오디오 자동 재생
* `muted` : 오디오 음소거 처리
* `loop` : 반복 재생 기능 추가


### 비디오(Video): `<video>`

> 비디오는 오디오와 유사하게 표시할 수 있다.

```html
<body>
    <video muted autoplay>
        <!-- 다양한 형식 중 선택하기 -->
        <source src="my-cat.mp4">
        <source src="my-cat.webm">
        <source src="my-cat.ogv">
    </video>
</body>
```

### 외부 파일: `<object>`, `<embed>`

> PDF 파일, 플래시, 미디어 플레이어 등을 표시할 수 있다.

* `<object>` 사용 예시
    ```html
    <body>
        <object type="application/pdf"
                data="/media/examples/In-CC0.pdf"
                width="250"
                height="200">
        </object>
    </body>
    ```

* `<embed>` 사용 예시
    ```html
    <body>
        <embed type="video/quicktime"
                src="movie.mov"
                width="640"
                height="480">
    </body>
    ```

### 외부 문서: `<iframe>`

* Attributes: `src`, `title`, `width`, `height`

## 10. 시맨틱 태그(Semantic Tag)

> 시맨틱 태그는 문서 요소를 역할 별로 구분하는 데 유용하다.
> 웹 페이지 관리 및 검색 엔진 페이지 탐색에도 도움을 준다.

* `<section>`: 연관 콘텐츠를 묶어 영역 구분

* `<article>`: 독립적으로 사용할 수 있는 연관 콘텐츠 구분
    ```html
    <section>
        <h1>HOT TOPIC</h1>
        <section>
            <p>World</p>
            <article>World news 1</article>
            <article>World news 2</article>
            <article>World news 3</article>
        </section>
        <section>
            <p>Sport</p>
            <article>Sport news 1</article>
            <article>Sport news 2</article>
            <article>Sport news 3</article>
        </section>
    </section>
    ```

* `<aside>`: 보조 정보 (사이드바, 배너 등)

* `<header>`: 머릿말 지정

* `<nav>`: 네비게이션 링크
    ```html
    <body>
        <header>
            <nav>
                <ul>
                    <li><a href="home.html">홈</a></li>
                    <li><a href="news.html">뉴스</a></li>
                    <li><a href="finance.html">증권</a></li>
                    <li><a href="land.html">부동산</a></li>
                </ul>
            </nav>
        </header>
    </body>
    ```
    <img src="https://wikidocs.net/images/page/164661/08-1-1.png">

* `<footer>`: 요약글 지정

> 시맨틱 태그는 `<div>`를 보완한다고 이해하면 편하다.

## 11. 메타데이터(Metadata)

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="utf-8">
  <meta name="author" content="강윤호">
  <meta name="description" content="예시를 위해 만들어 본 페이지입니다.">
  <meta name="keywords" content="메타데이터, meta, HTML, 프론트엔드, 웹 개발">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>메타데이터란 이런 것이다</title>
</head>
<body>
  <h1>표준 메타데이터 이름</h1>
  <p>
    &lt;meta&gt; 태그는 name 속성을 메타데이터의 이름으로, content 속성을 값으로 하여 메타데이터를 추가합니다. HTML 표준 명세를 비롯한 웹 페이지 관련 명세에는 사용 가능한 메타데이터 이름과 값들이 정의되어 있는데, 이를 '표준 메타데이터'라 합니다. 여기에서는 대표적인 표준 메타데이터 몇 가지를 알아보겠습니다.
  </p>
</body>
</html>
```
<img src="https://wikidocs.net/images/page/164662/08-2-2.png">

* `name` & `content`: 메타데이터 유형과 설정 지정
    - `author`: 문서 작성자 정보 추가
    - `description`: 페이지에 대한 짧은 요약 추가
    - `keywords`: 관련 키워드 추가
    - `viewport`: 모바일 기기에서 볼 때의 설정
* `charset`: 문자 집합 선언 지정
* `http-equiv`: 프래그마 지시문 설정

## 12. Tips

* 웹 브라우저 개발자 도구: Chrome `F12`