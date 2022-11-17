---
# try also 'default' to start simple
theme: vuetiful
class: 'text-center'
# emoji : https://emojipedia.org/twitter/
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
altCover: true
---

# TypeScript로 Vue.js 웹 서비스 다시쓰기

달리는 마차의 본체 갈아끼우기

<small>장기효(캡틴판교)</small>


---
layout: 'intro'
---

# 장기효

<div class="leading-8 opacity-80">
네이버 프런트엔드 개발자<br>
<a href="https://www.youtube.com/channel/UCX04UECIFaAjNnsak6GzpZg" target="_blank"><logos-youtube-icon class="inline-block m-1" />캡틴판교의 프런트엔드 개발 상담소 유튜브 채널 운영</a><br>
<a href="https://www.inflearn.com/users/@captain" target="_blank"><img style="height: 1em; margin: 0 0.3em 0.1em 0.3em;" class="inline-block mb-0.8" src="/inflearn.png"/>대학생, 실무자를 위한 인프런 멘토링 프로그램 운영</a><br>
</div>

<div class="my-10 grid grid-cols-[40px,1fr] gap-y-4">
  <ri-github-line class="opacity-50"/>
  <div><a href="https://github.com/joshua1988" target="_blank">CaptainPangyo</a></div>
  <mdi-book-open-outline class="opacity-50" />
  <div>
    <a href="https://joshua1988.github.io/vue-camp/" target="_blank">Cracking Vue.js</a> | 
    <a href="https://joshua1988.github.io/ts/" target="_blank">타입스크립트 핸드북</a>
  </div>
</div>

<img src="/shield.png" class="rounded-full w-40 abs-tr mt-16 mr-12"/>

---
layout: big-points
---

<style>
/* local stylesheet */
.slidev-layout .big-content p, .slidev-layout .big-content .contents-custom li {
  font-size: 1.475rem;
  line-height: 2.55rem;
}
</style>

# 목차

<div class="contents-custom">
  <ul>
    <li>도입을 결정하기까지</li>
    <li>전략 세우기</li>
    <li>컴포넌트 기반 FE 아키텍처 설계</li>
    <li>적용 결과</li>
    <li>회고</li>
  </ul>
</div>

---

# 시작하며

- 이 발표는 JS로 제작된 아래 사이트를 모두 TS로 변환한 과정과 경험을 공유하는 발표입니다.

<img style="height: 270px;" src="/dubbing-12s-intro.gif"/>

- [Vue.js](https://vuejs.org/)와 [TypeScript](https://www.typescriptlang.org/)의 기본 개념과 문법은 설명하지 않습니다.
- React, Vue 등 컴포넌트 기반 프런트엔드 설계와 타입스크립트 도입에 관심 있으신 분들께 적합한 발표입니다.

---
layout: section
---

# 도입을 결정하기까지

<!-- 첫 번째로, 운영 중인 서비스에 TS 도입을 결정하기 까지의 과정에 대해서 말씀 드리겠습니다. 도입을 결정하던 시점의 팀 상황이나 서비스 구조, 도입을 했을 때 기대 효과 등에 대해서 공유하려고 합니다. "도입을 결정하기까지의 고민과 의사 결정 과정" -->

---

# 대상 서비스

- 동영상 웹 편집기. TTS(Text to Speech) 저작 도구. 비디오, 오디오 등의 미디어 제어와 타임라인 편집

<img style="height: 360px;" src="/dubbing-intro.gif"/>

--- 

<style>
/* local stylesheet */
.grid-item {
  border: black solid 1px;
  padding: 1rem;
  border-radius: 1rem;
}
</style>

# 문제 상황 - 왜 타입스크립트를 도입했나?

<v-clicks>

적은 인원으로 빠르게 서비스를 오픈하면서 기술 부채가 심하게 쌓였던 상황.

</v-clicks>

<div class="grid grid-cols-3 gap-x-4 mt-8">

<div v-click class="grid-item">

### 컴포넌트 설계

컴포넌트 1개가 4천 줄이 넘고 1, 2천 줄이 넘는 컴포넌트가 여러 개 존재

</div><div v-click class="grid-item">

### 서비스 특징

위치 ↔ 시간 단위 변환 코드가 많음

</div><div v-click class="grid-item">

### 데이터 흐름

컴포넌트 ↔ API 함수까지 7단계의 레이어. 특정 위치의 코드가 어떤 값을 들고 있는지 알기 어려움

</div></div>

<div class="grid grid-cols-3 gap-x-4 mt-4">

<div v-click class="grid-item">

### API 문서

REST API 문서가 최신화 되어 있지 않아 오히려 FE 코드가 최신 문서 역할

</div><div v-click class="grid-item">

### 개발 환경

ESLint, TS 등 빌드 과정에서의 사전 에러 검출 과정 부재

</div><div v-click class="grid-item">

### 기타

그외 n개로 분산된 액시오스 인터셉터 설정, 권한 관리 체계 부재 등..

</div></div>

---

<style>
/* local stylesheet */
.grid-item {
  border: black solid 1px;
  padding: 1rem;
  border-radius: 1rem;
}
.grid-item.highlight {
  border: black solid 3px;
}
</style>

# 문제 상황 - 왜 타입스크립트를 도입했나?

적은 인원으로 빠르게 서비스를 오픈하면서 기술 부채가 심하게 쌓였던 상황.

<div class="grid grid-cols-3 gap-x-4 mt-8">

<div class="grid-item">

### 컴포넌트 설계

컴포넌트 1개가 4천 줄이 넘고 1, 2천 줄이 넘는 컴포넌트가 여러 개 존재

</div><div class="grid-item highlight">

### 서비스 특징 <Badge type="success">적합</Badge>

위치 ↔ 시간 단위 변환 코드가 많음

</div><div class="grid-item">

### 데이터 흐름

컴포넌트 ↔ API 함수까지 7단계의 레이어. 특정 위치의 코드가 어떤 값을 들고 있는지 알기 어려움

</div></div>

<div class="grid grid-cols-3 gap-x-4 mt-4">

<div class="grid-item highlight">

### API 문서 <Badge type="success">적합</Badge>

REST API 문서가 최신화 되어 있지 않아 오히려 FE 코드가 최신 문서 역할

</div><div class="grid-item highlight">

### 개발 환경 <Badge type="success">적합</Badge>

ESLint, TS 등 빌드 과정에서의 사전 에러 검출 과정 부재

</div><div class="grid-item">

### 기타

그외 n개로 분산된 액시오스 인터셉터 설정, 권한 관리 체계 부재 등..

</div></div>

---
layout: big-points
---

# 하지만 현실은..

<style>
/* local stylesheet */
.slidev-layout .big-content p, .slidev-layout .big-content li {
  font-size: 1.475rem;
  line-height: 2.55rem;
}
</style>

<div v-click class="mt-8 min-h-1 text-2xl font-bold">
안정성
</div>

<div v-after class="mt-2">

- 잘 돌아가는 서비스는 건드리지 않는게 좋다
- 오랜 기간을 거쳐 안정성을 확보한 서비스와 기능을 건드리는 건 위험하다

</div>

<div v-click class="mt-4 min-h-1 text-2xl font-bold">
학습 비용
</div>

<div v-after class="mt-2">

- TS를 아는 사람은 나 혼자
- 팀 전체의 학습 비용이 큰 상황

</div>

<div v-click class="mt-4 min-h-1 text-2xl font-bold">
테스팅
</div>

<div v-after class="mt-2">

- 기능을 보장할 수 있는 테스트 코드는 없었다

</div>

---

# 코드 생산 비용

<!-- NOTE: 그래프에 영어가 좀 많은데 하나도 신경 안쓰셔도 됩니다. 제가 설명해 드릴게요 라고 안내하고 시작하기 -->

<img src="/code-quality.png"/>

<div class="text-right">
<a href="https://martinfowler.com/articles/is-quality-worth-cost.html" target="_blank">“Is High Quality Software Worth the Cost?” - 마틴 파울러</a>
</div>


---
layout: big-points
title: 지속가능성
titleRow: true
---

<style>
/* local stylesheet */
.quote {
  line-height: 3rem !important;
}
.reference {
  margin-bottom: 5rem !important;
}
</style>

<div class="min-h-1 text-3xl font-bold quote">
"코드베이스의 수명이 다할 때까지 직면하는 변화가 <br>
몰고 오는 모든 변경을 안전하게 처리할 수 있다면 <br>
그 코드 베이스는 지속 가능하다."
</div>
<p class="text-right reference">구글 엔지니어는 이렇게 일한다. <small>톰 맨쉬렉 저</small></p>

---
layout: big-points
title: 지속가능성
titleRow: true
---

<style>
/* local stylesheet */
.quote {
  line-height: 3rem !important;
}
.reference {
  margin-bottom: 5rem !important;
}
</style>

<div class="min-h-1 text-3xl font-bold quote">
"서비스의 변경 사항을 안전하게 처리할 수 있다면 <br>
그 코드는 지속 가능하다."
</div>
<p class="text-right reference">구글 엔지니어는 이렇게 일한다. <small>톰 맨쉬렉 저</small></p>

<!-- 이후에 발생할 변화에 대해서 기민하게 대응하기 어려운 구조와 코드 품질이었고 무엇보다도 안전하게 변경이 가능해 보이지 않았습니다. 종종 발생하는 버그의 정확한 원인 분석조차 되질 않았습니다. 위 정의에 따르면 지속 가능한 코드 베이스가 아니었던거죠 -->

---
layout: big-points
titleRow: true
---

# <Badge type="warn" style="font-size: 0.9em;line-height: 1.1em;">결론</Badge>
# 현재의 상황과 이후의 유지보수 비용을 고려하면
# TS 기반 코드 재작성이 필요하다고 판단

<br>
<br>
<br>

---
layout: section
---

# 전략 세우기

---

# 프로젝트 진행 절차

<v-clicks>

1) TS 기반 별도의 신규 Vue.js 프로젝트 환경 구성
   - 기존 소스 코드의 ESLint 에러가 수천개 ➡ 기존 프로젝트에서 점진적 TS 적용 불가능
2) 페이지, UI 영역별 코드 재작성
   - 기존 JS 코드의 버그 수정을 위해 신규로 작성된 TS 코드를 역으로 JS 코드에 병합 ➡ 병합시 타입만 제거
3) 코드 재작성 중 추가되는 신규 비즈니스 기능은 JS 소스 그대로 TS 프로젝트에 포함
   - `allowJS` 옵션을 이용하여 컴포넌트, 모듈, 함수 등을 그대로 임포트

</v-clicks>

<!-- TODO: 왼쪽 화살표, 오른쪽 화살표 투 트랙으로 나뉘고 왼쪽에서 오른쪽으로 병합되는 모습 그림으로 그려보자  -->

---
layout: big-points
title: 개발 전략
titleRow: true
---

<style>
/* local stylesheet */
.slidev-layout .big-content p, .slidev-layout .big-content li {
  font-size: 1.675rem;
  line-height: 3rem;
}
</style>


- JSDoc 작성
- 컴포넌트 부분 부분 갈아끼우기
- 가성비 안나오면 그냥 allowJS

<br>
<br>
<br>

---

# 첫 번째 전략 - JSDoc 적용

<v-clicks>

- JSDoc이란 일정한 형식으로 코드에 설명을 추가하는 주석

</v-clicks>

<div class="grid grid-cols-2 gap-x-4 mt-2">

<div v-click>

```js
// app.js

/**
 * @typedef {Object} User
 * @property {string} name 사용자 이름. ex) capt
 * @property {number} age 사용자 나이. ex) 100
 *
 * @param {number} id 사용자 아이디
 * @return {Promise<User>} 사용자 정보
 **/
function fetchUserById(id) {
  const url = `https://infcon.day/users/${id}`;
  return fetch(url).then(res => res.json());
    // { name: 'capt', age: 100 }
}
```

</div><div v-click>

<img src="/jsdoc-effect.png"/>

</div></div>

<v-clicks>

<Badge type="info">TIP!</Badge> API 함수와 주요 비즈니스 로직이 담긴 함수에 먼저 적용

</v-clicks>


---

# 두 번째 전략 - 부분 부분 갈아끼우기

<v-clicks>

- 컴포넌트 간 결합도가 낮은 단위 컴포넌트 위주로 먼저 포팅 작업

<img style="height: 58%;" src="/component-tree-1.jpeg"/>

</v-clicks>

---

# 두 번째 전략 - 부분 부분 갈아끼우기

- 컴포넌트 간 결합도가 낮은 단위 컴포넌트 위주로 먼저 포팅 작업

<img style="height: 58%;" src="/component-tree-2.jpeg"/>

<!-- NOTE: TS를 적용할 떄에는 가급적 로직 변경보다는 타입 정의만 하는 것을 추천. TS에 익숙하지 않은 사람이라면 기능 보장이 더 어려울 수 있음 -->

---

# 세 번째 전략 - allowJs로 포용하기

<v-clicks>

- TS로 변환했을 때 타입 에러가 많다면? `allowJs` 옵션을 이용해 그냥 JS로 돌리자.

<div class="grid grid-cols-2 gap-x-4 mt-2">

<div>

### app.js

<img src="/js-before.png">

</div><div>

### app.ts

<img src="/ts-after.png">


</div></div>

<Badge type="info">TIP!</Badge> 중요도가 떨어지고 손만 많이 가는 컴포넌트가 있다면 과감히 TS 적용 포기

</v-clicks>

---
layout: section
---

# 컴포넌트 기반 FE 아키텍처 설계

---

# 설계 원칙 5가지

<div v-click>

- 컴포넌트 ↔ API 레이어의 데이터 흐름 단순화
- Reactivity는 필요한 곳에만
- 덩어리가 큰 모듈은 컴포넌트보다 클래스로
- 불필요한 전역 상태는 줄이고 state는 화면과 가장 가깝게 배치
- 제약이 없는 믹스인은 죄악이다

</div>

---

# 설계 원칙 #1 - 데이터 흐름 단순화

<v-clicks>

- 데이터를 받아와 화면에 뿌리기까지의 레이어 단순화

<img style="height: 68%" src="/component-structure.png"/>

</v-clicks>

---

# 설계 원칙 #2 - Reactivity는 필요한 곳에만

<v-clicks>

- `<template/>`에 표시되어야 하는 속성인가?

```html
<template>
  <input type="file" @change="uploadImage" ref="selectedFile">이미지 업로드</input>
  <p v-if="uploading">업로드 중입니다</p>
</template>

<script>
const allowedFileTypes = ['jpg','png','webp'];

export default {
  data() {
    return { uploading: false, allowedFileTypes: [...] }
  },
  methods: {
    uploadImage() {
      this.uploading = true;
      const file = this.$refs.selectedFile.files[0];
      allowedFileTypes.some(allowedType => file.type.includes(allowedType));
    }
  }
}
</script>
```

</v-clicks>

---

# 설계 원칙 #3 - 덩어리가 큰 모듈은 컴포넌트보다 클래스로

<v-clicks>

- 화면단 코드와 직접적인 연관이 있는 코드인가?

</v-clicks>

<div class="grid grid-cols-2 gap-x-4 mt-4">

<div v-click>

```js
// VideoInput.vue
export default {
  methods: {
    validateCodec() {
      // ..
    },
    validateVideoResolution() {
      // ..
    },
    validateFileSize() {
      // ..
    }
  }
}
```

</div><div v-click>

```js
class VideoFileValidator {
  // ...
}

// VideoInput.vue
export default {
  methods: {
    validateVideo() {
      const validator = new VideoFileValidator(file);
      this.isVideoValid = validator.check();
    }
  }
}
```

</div></div>

---

<!-- # 설계 원칙 #4 - state는 화면과 가장 가깝게 배치 -->
# 설계 원칙 #4 - state는 줄이고 data는 화면과 가깝게 배치

- 스토어에 꼭 들어가야 하는 상태인가?

<div class="grid grid-cols-2 gap-x-4">

<div v-click>

<img style="height: 260px;" src="/this-is-data.png">

</div><div v-click>

<img src="/this-must-be-state.png">

</div></div>

---

# 설계 원칙 #4 - state는 줄이고 data는 화면과 가깝게 배치

<v-clicks>

- `data`는 항상 사용하는 곳과 가장 가깝게 배치

<img src="/put-state-in-place-1.png">

</v-clicks>

---

# 설계 원칙 #4 - state는 줄이고 data는 화면과 가깝게 배치

- `data`는 항상 사용하는 곳과 가장 가깝게 배치

<img src="/put-state-in-place-2.png">

---


# 설계 원칙 #5 - 제약이 없는 믹스인은 죄악이다

- 제한된 규칙으로 사용해야 효과가 증대되는 [믹스인(mixins)](https://v2.vuejs.org/v2/guide/mixins.html?redirect=true#Basics)

<div class="grid grid-cols-2 gap-x-4 mt-4">

<div v-click>

### 믹스인을 잘못 사용한 사례

```js
// mixins/audioAdd.js
export default {
  mixins: [getAudioIndex],
  data() {
    return { audioAddCheck: {valid: false} }
  },
  methods: {
    isAudioBetween() {
      this.audioIndex = this.getAudioIndex();
    }
  }
}
```

</div><div v-click>

### 믹스인 중첩은 지양해야함

```js
// mixins/getAudioIndex.js
export default {
  data() {
    return { audioIndex: -1 }
  },
  methods: {
    getAudioIndex() {
      // ...
    }
  }
}
```

</div></div>

<v-clicks>

<Badge type="warn">주의!</Badge> 믹스인은 컴포넌트에 병합되어 들어가는 순서나 로직, 값 덮어쓰기 등 주의할 점이 많음

</v-clicks>

---

# 설계 원칙 #5 - 제약이 없는 믹스인은 죄악이다

<!-- NOTE: 컴포지션 API는 Vue 2와 Vue 3 모두 사용할 수 있다고 언급. FEConf 언급할 필요 X -->

- 믹스인 대신 컴포지션 API(리액트 훅)을 사용

<div class="grid grid-cols-2 gap-x-4 mt-4">

<div v-click>

```js
// composables/useAudio.js
export function useAudio() {
  const valid = ref(false);

  const getAudioIndex = () => { ... }
  const isAudioBetween = () => { ... }

  return { valid, getAudioIndex, isAudioBetween };
}
```

</div><div v-click>

```js
// AudioList.vue
export default {
  setup() {
    const { 
      valid, getAudioIndex, isAudioBetween 
    } = useAudio();

    return { valid, getAudioIndex, isAudioBetween };
  },
  data() { ... }
  methods: { ... }
}
```

</div></div>

<div v-click>

<Badge type="info">TIP!</Badge> data 속성과 강하게 얽혀 있는 재사용 대상 로직을 모두 컴포지션으로 옮겨도 정상 동작함

</div>

---
layout: section
---

# 적용 결과

<!-- NOTE: 그렇게 진행한 TS 적용 작업의 결과를 살펴보겠습니다. TS의 장점을 다시 한번 확인해 볼 수 있을 겁니다.-->

---

# 적용 결과 #1 - 코드 가독성 향상

<!-- NOTE: 타입스크립트를 써서 시간 관련 코드의 타입도 정의되기 때문에 변환에 대한 고민을 하지 않아도 된다 -->

- 코드의 역할을 더 구체적으로 파악할 수 있음 ➡ 신규 입사자 온보딩과 디버깅에 도움

<div class="grid grid-cols-2 gap-x-4 mt-4">

<div v-click>

### api/audio.js

```js
// 오디오 변경 API 함수
function editAudio({ projectMediaId, time }) {
  return axios.put(`${projectMediaId}`, { time });
}
```

</div><div v-click>

### api/audio.ts

```ts
interface Params {
  projectMediaId: number;
  time: ms;
}

interface ApiResponse {
  id: string;
  url: string;
}

// 오디오 변경 API 함수
function editAudio({
  projectMediaId,
  time,
}: Params): AxiosPromise<ApiResponse> {
  return axios.put(`${projectMediaId}`, { time });
}
```

</div></div>

---

# 적용 결과 #2 - 에러의 사전 검출

- 로컬 개발 환경에서의 린트 + TS 컴파일로 사전 에러 검출 가능

<div class="grid grid-cols-2 gap-x-4 mt-2">

<div v-click>

```js
import Vuex from 'vuex'; 
import { i18n } from '../plugins/i18n';
import { encodeBase64 } from '../mixins/encodeBase64';
import IndexPage from './components/IndexPage.vue';
import { sum } from './utils/sum';

const store = new Vuex.Store({
  mutations: {
    // ...
    doSomething() {
      router.push('/home').catch(() => logging(Error));
    }
  }
})
```

</div><div v-click>

```ts {monaco}
import Vuex from 'vuex'; 
import { i18n } from '../plugins/i18n';
import { encodeBase64 } from '../mixins/encodeBase64';
import IndexPage from './components/IndexPage.vue';
import { sum } from './utils/sum';

const store = new Vuex.Store({
  mutations: {
    // ...
    doSomething() {
      router.push('/home').catch(() => logging(Error));
    }
  }
})
```

</div></div>

<!-- TODO: 여기다가 `router`를 찾을 수 없다는 터미널 린트 출력 붙여넣기 -->

---

# 적용 결과 #3 - 기능 향상

- 재생선 동작 개선 및 사용자 제어 정확도 향상

<div class="grid grid-cols-2 gap-x-4">

<div v-click>

<img style="height: 194px;" src="/move-before.gif"/>

</div><div v-click>

<img style="height: 194px;" src="/move-after.gif"/>

</div><div v-click class="mt-1">

<img style="height: 194px;" src="/accuracy-before.gif"/>

</div><div v-click class="mt-1">

<img style="height: 194px;" src="/accuracy-after.gif"/>

</div></div>

<!-- NOTE: TS에 익숙하지 않은 경우 위와 같이 기능적 변경은 하지 않고 타입 변환만 하는 것을 추천. 만약 그래도 두려운 경우 꼭 주요 테스트 코드를 먼저 작성하고 변환할 것 을 권고합니다 -->

요 다음에 빠른 문제 파악 및 디버깅 & 수정 속도 향상 이라는 슬라이드 넣으면 좋을 듯

---
layout: section
---

# 회고

---

# 첫 번째 아쉬웠던 점

- 서비스 전체 구조를 개편하는데 팀 전체가 참여하지 않고 홀로 진행 ➡ 새로운 구조 & 언어에 대한 팀 학습 비용 증가

<img style="height: 340px;" src="/everything.jpeg"/>

<div class="mt-2">
<a href="https://www.meme-arsenal.com/en/create/meme/7393515" target="_blank">https://www.meme-arsenal.com/en/create/meme/7393515</a>
</div>

---

# 두 번째 아쉬웠던 점

- 코드 베이스에 대한 이해가 높았다면 주요 사용자 액션 기준으로 e2e 테스트 코드라도 작성하고 시작했을 듯

<img style="height: 340px;" src="/testing-pyramid.jpeg"/>

<div class="mt-2">
<a href="https://semaphoreci.com/blog/testing-pyramid" target="_blank">https://semaphoreci.com/blog/testing-pyramid</a>
</div>

---
layout: big-points
titleRow: true
---

# 제 경험이 TS 도입을 고민하고 계신 분들께 
# 좋은 참고 자료가 되었으면 좋겠습니다 <twemoji-grinning-face-with-smiling-eyes />

<br>
<br>
<br>

---
layout: big-points
title: TS 적용 가이드
titleRow: true
---

<iframe width="900" height="360" src="https://joshua1988.github.io/ts/etc/convert-js-to-ts.html"></iframe>

<div class="mt-2" style="text-align: right;">
<a href="https://joshua1988.github.io/ts/etc/convert-js-to-ts.html" target="_blank">https://joshua1988.github.io/ts/etc/convert-js-to-ts.html</a>
</div>

<!-- 제 경험기를 들어주셔서 감사합니다. 타입스크립트 도입을 고민하고 계신 분들께 유익했던 시간이었으면 좋겠습니다. 주말 즐겁게 보내시구요~ 감사합니다. -->

---
layout: outro
website: 'https://joshua1988.github.io'
youtubeUrl: https://bit.ly/3mgTeWZ
repository: 'github.com/joshua1988'
---

<div style="color: white;">
  <p style="margin-bottom: 2rem">감사합니다.</p>
  <!-- <p>Q & A</p> -->
</div>