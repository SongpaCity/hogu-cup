# 🔌 호구컵 실시간 저장(Firebase) 켜는 법

`index.html`은 **Firebase 설정 전까지 '로컬 모드'**(혼자만 보이고 저장 안 됨)로 동작한다.
아래 5단계로 **무료** Firebase를 연결하면 **날짜·참가자·우승자·실시간 점수가 전원에게 공유·저장**된다.
(신용카드 불필요, 소규모는 영구 무료 범위)

---

## 1. Firebase 프로젝트 만들기
1. https://console.firebase.google.com 접속 → 구글 로그인
2. **프로젝트 추가** → 이름 `hogu-cup` (아무거나) → 애널리틱스는 꺼도 됨 → 생성

## 2. Firestore 데이터베이스 생성
1. 왼쪽 메뉴 **빌드 → Firestore Database** → **데이터베이스 만들기**
2. 위치는 기본값(또는 asia-northeast3 서울) → **테스트 모드로 시작** 선택 → 사용 설정

## 3. 웹 앱 등록 → 설정키 복사
1. 프로젝트 개요 옆 **⚙️ → 프로젝트 설정**
2. 하단 **내 앱**에서 **웹(</>)** 아이콘 클릭 → 앱 닉네임 입력 → 등록
3. 표시되는 `const firebaseConfig = { ... }` **객체를 복사**

## 4. `index.html`에 붙여넣기
`index.html` 상단 `<script type="module">` 안의 이 부분을 찾아서:
```js
const FIREBASE_CONFIG = null;
```
복사한 설정으로 교체:
```js
const FIREBASE_CONFIG = {
  apiKey: "AIza....",
  authDomain: "hogu-cup.firebaseapp.com",
  projectId: "hogu-cup",
  storageBucket: "hogu-cup.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:...:web:...."
};
```
그리고 바로 아래 `ADMIN_PASS = "hogu"`를 **원하는 관리자 비밀번호**로 바꾼다.

## 5. 보안 규칙 (중요)
Firestore → **규칙** 탭에서 아래로 교체 후 **게시**:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /hogucup/state {
      allow read: if true;     // 누구나 보기
      allow write: if true;    // 친구용: 누구나 쓰기 (아래 주의 참고)
    }
  }
}
```

> ⚠️ `write: if true`는 **링크를 아는 누구나 쓸 수 있다**는 뜻이다. 친구들끼리 쓰는 수준이면 충분하지만,
> 완전히 막으려면 Firebase 인증(Auth) 추가가 필요하다. 필요하면 요청하면 붙여준다.

---

## 커밋·푸시
```bash
cd ~/hogu-cup
git add -A
git commit -m "chore: firebase 설정"
git push
```
약 1분 뒤 https://songpacity.github.io/hogu-cup/ 에서 우상단 배지가 **"실시간 공유 ON"**이면 성공.

## 사용법
- 우상단 **관리자** 버튼 → 비밀번호 입력 → 날짜·참가자·우승자 편집, 스코어보드 +/−.
- 관리자가 바꾸면 **보고 있는 모든 사람 화면이 실시간 갱신**된다.
