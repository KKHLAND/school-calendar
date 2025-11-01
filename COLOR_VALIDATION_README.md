# 달력 키워드 색상 검증 (Calendar Keyword Color Validation)

## 📋 개요 (Overview)

스마트 스쿨 캘린더에 **색상 설정 검증 시스템**이 추가되었습니다. 이 시스템은 달력의 키워드 색상이 올바르게 설정되었는지 자동으로 검증하고, 문제가 있을 경우 개발자 콘솔에 상세한 경고 메시지를 표시합니다.

A **color validation system** has been added to the Smart School Calendar. This system automatically validates that calendar keyword colors are correctly configured and displays detailed warnings in the developer console if issues are found.

---

## 🎯 검증 항목 (Validation Checklist)

### 1. **기본 색상 팔레트 검증** (Base Color Palette Validation)
- ✅ EVENT_COLORS 배열이 정의되어 있고 비어있지 않은지 확인
- ✅ 각 색상 객체에 `bg`와 `text` 속성이 모두 있는지 확인
- ✅ 모든 색상 클래스가 유효한 Tailwind CSS 클래스인지 확인
- ✅ 중복된 색상 조합이 있는지 확인

### 2. **특수 키워드 색상 검증** (Special Keyword Color Validation)
- ✅ 모든 특수 키워드에 색상이 할당되어 있는지 확인
- ✅ 색상 클래스가 유효한 Tailwind CSS 형식인지 확인
- ✅ 동일한 색상을 사용하는 키워드 그룹 표시
- ✅ 접근성 경고 (너무 밝거나 어두운 배경색)

### 3. **Tailwind CSS 클래스 형식 검증** (Tailwind Class Format Validation)
- ✅ 색상 이름이 Tailwind 표준 팔레트에 포함되어 있는지 확인
- ✅ Shade 값이 유효한지 확인 (50, 100, 200, ..., 900, 950)
- ✅ 클래스 형식이 올바른지 확인 (`bg-{color}-{shade}`, `text-{color}-{shade}`)

---

## 🚀 사용법 (Usage)

### 자동 검증 (Automatic Validation)

페이지가 로드될 때 자동으로 색상 검증이 실행됩니다:

```javascript
// index.html의 1433번째 줄
validateColorConfiguration();
```

### 콘솔 출력 예시 (Console Output Example)

정상적인 경우:
```
🎨 달력 키워드 색상 검증 (Calendar Keyword Color Validation)
📋 기본 색상 팔레트 검증 중...
✅ 기본 색상 팔레트: 정상 (8개 색상)
   1. bg-sky-100 + text-sky-800
   2. bg-emerald-100 + text-emerald-800
   3. bg-amber-100 + text-amber-800
   4. bg-violet-100 + text-violet-800
   5. bg-rose-100 + text-rose-800
   6. bg-teal-100 + text-teal-800
   7. bg-indigo-100 + text-indigo-800
   8. bg-orange-100 + text-orange-800

📋 특수 키워드 색상 검증 중...
✅ 특수 키워드 색상: 정상 (4개 고유 색상)
   bg-red-100 font-medium
      - 고사
      - 평가
   bg-red-200 font-bold
      - 수능
   bg-blue-100
      - 방학
      - 개학
   bg-purple-100
      - 졸업
      - 입학

⚠️ 경고 사항:
   ℹ️ 동일한 색상 사용: [고사, 평가] → bg-red-100 font-medium
   ℹ️ 동일한 색상 사용: [방학, 개학] → bg-blue-100
   ℹ️ 동일한 색상 사용: [졸업, 입학] → bg-purple-100

✅ 모든 색상 검증 통과!
```

---

## 🔧 구현 세부 사항 (Implementation Details)

### 파일 위치
- **파일명**: `/home/user/school-calendar/index.html`
- **검증 함수 위치**: Lines 281-531

### 주요 함수 (Key Functions)

#### 1. `validateTailwindColorClass(className)`
**위치**: index.html:290-336

Tailwind CSS 색상 클래스 형식을 검증합니다.

**매개변수**:
- `className` (string): 검증할 클래스명 (예: "bg-red-100 font-medium")

**반환값**:
- `boolean`: 유효한 Tailwind 클래스이면 `true`, 아니면 `false`

**검증 규칙**:
- 유효한 색상 이름: slate, gray, zinc, neutral, stone, red, orange, amber, yellow, lime, green, emerald, teal, cyan, sky, blue, indigo, violet, purple, fuchsia, pink, rose
- 유효한 shade: 50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950
- 형식: `bg-{color}-{shade}` 또는 `text-{color}-{shade}`
- 추가 유틸리티 클래스 허용: `font-medium`, `font-bold` 등

**예시**:
```javascript
validateTailwindColorClass('bg-red-100');               // true
validateTailwindColorClass('bg-red-100 font-medium');   // true
validateTailwindColorClass('bg-invalid-100');           // false
validateTailwindColorClass('bg-red-999');               // false
```

---

#### 2. `validateEventColors()`
**위치**: index.html:344-384

기본 색상 팔레트(`EVENT_COLORS`)를 검증합니다.

**반환값**:
```javascript
{
  isValid: boolean,
  errors: string[]
}
```

**검증 항목**:
1. EVENT_COLORS 배열이 존재하고 비어있지 않은지
2. 각 색상 객체에 `bg`와 `text` 속성이 있는지
3. 모든 색상 클래스가 유효한 Tailwind 클래스인지
4. 중복된 색상 조합이 있는지

**예시 에러**:
```
❌ EVENT_COLORS[3]: bg 또는 text 속성이 누락되었습니다.
❌ EVENT_COLORS[5].bg: 유효하지 않은 Tailwind 클래스입니다. (bg-invalid-100)
⚠️ EVENT_COLORS[2]: 중복된 색상 조합입니다. (bg-amber-100, text-amber-800)
```

---

#### 3. `validateSpecialKeywordColors()`
**위치**: index.html:392-463

특수 키워드의 색상 매핑을 검증합니다.

**반환값**:
```javascript
{
  isValid: boolean,
  errors: string[],
  warnings: string[],
  colorGroups: Object
}
```

**검증 항목**:
1. 모든 키워드에 색상이 할당되어 있는지
2. 색상 클래스가 유효한 Tailwind CSS 형식인지
3. 동일한 색상을 사용하는 키워드 그룹 식별
4. 접근성 경고 (shade 50, 900, 950 사용 시)

**특수 키워드 목록**:
- `고사` (Exam) → `bg-red-100 font-medium`
- `평가` (Evaluation) → `bg-red-100 font-medium`
- `수능` (College Entrance) → `bg-red-200 font-bold`
- `방학` (Vacation) → `bg-blue-100`
- `개학` (School Opening) → `bg-blue-100`
- `졸업` (Graduation) → `bg-purple-100`
- `입학` (Enrollment) → `bg-purple-100`

**예시 출력**:
```javascript
{
  isValid: true,
  errors: [],
  warnings: [
    'ℹ️ 동일한 색상 사용: [고사, 평가] → bg-red-100 font-medium',
    'ℹ️ 동일한 색상 사용: [방학, 개학] → bg-blue-100'
  ],
  colorGroups: {
    'bg-red-100 font-medium': ['고사', '평가'],
    'bg-red-200 font-bold': ['수능'],
    'bg-blue-100': ['방학', '개학'],
    'bg-purple-100': ['졸업', '입학']
  }
}
```

---

#### 4. `validateColorConfiguration()`
**위치**: index.html:469-531

전체 색상 설정을 검증하고 결과를 콘솔에 출력합니다.

**반환값**:
- `boolean`: 모든 검증을 통과하면 `true`, 에러가 있으면 `false`

**동작**:
1. 기본 색상 팔레트 검증 (`validateEventColors()`)
2. 특수 키워드 색상 검증 (`validateSpecialKeywordColors()`)
3. 검증 결과를 콘솔에 그룹화하여 표시
4. 에러, 경고 메시지 출력

**호출 위치**: index.html:1433 (페이지 로드 시)

---

## 📊 검증 결과 해석 (Interpreting Validation Results)

### ✅ 정상 (Success)
```
✅ 기본 색상 팔레트: 정상 (8개 색상)
✅ 특수 키워드 색상: 정상 (4개 고유 색상)
✅ 모든 색상 검증 통과!
```

모든 색상 설정이 올바르게 구성되었습니다.

### ⚠️ 경고 (Warnings)
```
⚠️ 경고 사항:
   ℹ️ 동일한 색상 사용: [고사, 평가] → bg-red-100 font-medium
   ⚠️ 키워드 '방학': 배경색이 너무 밝을 수 있습니다. (bg-blue-50)
```

색상이 작동하지만 개선이 필요할 수 있습니다.

### ❌ 에러 (Errors)
```
❌ 기본 색상 팔레트 검증 실패:
   ❌ EVENT_COLORS[2].bg: 유효하지 않은 Tailwind 클래스입니다. (bg-invalid-100)
❌ 색상 검증 실패: 위의 오류를 수정해주세요.
```

색상 설정에 문제가 있으며 수정이 필요합니다.

---

## 🛠️ 색상 설정 수정 방법 (How to Modify Color Settings)

### 기본 색상 팔레트 수정

`EVENT_COLORS` 배열을 수정합니다 (index.html:238-247):

```javascript
const EVENT_COLORS = [
    { bg: 'bg-sky-100', text: 'text-sky-800' },
    { bg: 'bg-emerald-100', text: 'text-emerald-800' },
    // ... 색상 추가/수정
];
```

### 특수 키워드 색상 수정

`getEventColor()` 함수 내의 `specialKeywords` 객체를 수정합니다 (index.html:260-268):

```javascript
const specialKeywords = {
    '고사': 'bg-red-100 font-medium',
    '평가': 'bg-red-100 font-medium',
    '수능': 'bg-red-200 font-bold',
    // ... 키워드 추가/수정
};
```

**주의사항**:
1. Tailwind CSS 표준 색상만 사용
2. 유효한 shade 값 사용 (50, 100, 200, ..., 900, 950)
3. `bg-`와 `text-` 접두사 필수
4. `validateSpecialKeywordColors()` 함수의 동일한 객체도 함께 수정 필요 (index.html:397-405)

---

## 🎨 Tailwind CSS 색상 참고 (Tailwind Color Reference)

### 사용 가능한 색상 (Available Colors)
```
slate, gray, zinc, neutral, stone, red, orange, amber, yellow,
lime, green, emerald, teal, cyan, sky, blue, indigo, violet,
purple, fuchsia, pink, rose
```

### 사용 가능한 Shade (Available Shades)
```
50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950
```

### 접근성 권장사항 (Accessibility Recommendations)
- **배경색**: 100-300 권장 (밝은 배경)
- **텍스트 색**: 700-900 권장 (어두운 텍스트로 대비 확보)
- **피해야 할 조합**: bg-50 (너무 밝음), bg-900/950 (너무 어두움)

---

## 🔍 디버깅 (Debugging)

### 콘솔에서 수동 검증 실행

브라우저 개발자 콘솔에서 다음 명령어를 실행할 수 있습니다:

```javascript
// 전체 검증 실행
validateColorConfiguration();

// 개별 함수 테스트
validateTailwindColorClass('bg-red-100');
validateEventColors();
validateSpecialKeywordColors();
```

### 특정 색상 클래스 테스트

```javascript
// 유효성 테스트
console.log(validateTailwindColorClass('bg-red-100'));        // true
console.log(validateTailwindColorClass('bg-invalid-100'));   // false

// 특수 키워드 색상 그룹 확인
const result = validateSpecialKeywordColors();
console.log(result.colorGroups);
```

---

## 📝 변경 이력 (Change Log)

### v1.0.0 (2025-11-01)
- ✨ 초기 색상 검증 시스템 구현
- ✅ Tailwind CSS 클래스 형식 검증 추가
- ✅ 기본 색상 팔레트 검증 추가
- ✅ 특수 키워드 색상 검증 추가
- ✅ 자동 검증 실행 추가 (페이지 로드 시)
- 📊 콘솔 출력 형식 개선

---

## 🤝 기여 (Contributing)

색상 검증 시스템 개선을 위한 제안이나 버그 리포트를 환영합니다.

### 개선 아이디어
- [ ] 색상 대비(contrast) 자동 계산 및 WCAG 준수 확인
- [ ] 사용자 정의 색상 팔레트 지원
- [ ] UI에서 검증 결과 표시
- [ ] 색상 테마 프리셋 기능
- [ ] 다크 모드 색상 검증

---

## 📚 참고 자료 (References)

- [Tailwind CSS Colors](https://tailwindcss.com/docs/customizing-colors)
- [WCAG Color Contrast Guidelines](https://www.w3.org/WAI/WCAG21/Understanding/contrast-minimum.html)
- [React Documentation](https://react.dev/)

---

**작성자**: Claude Code Agent
**날짜**: 2025-11-01
**프로젝트**: Smart School Calendar (스마트 스쿨 캘린더)
