# Firebase 설정 가이드

## 1. Firebase 프로젝트 생성

1. https://console.firebase.google.com 접속
2. Google 계정으로 로그인
3. **"프로젝트 추가"** 클릭
4. 프로젝트 이름 입력 (예: `prayer-share`)
5. Google Analytics는 **사용 안함** 선택 (선택사항)
6. **"프로젝트 만들기"** 클릭

## 2. Realtime Database 생성

1. 왼쪽 메뉴에서 **빌드 > Realtime Database** 클릭
2. **"데이터베이스 만들기"** 클릭
3. 위치 선택: **싱가포르 (asia-southeast1)** 권장
4. 보안 규칙: **테스트 모드에서 시작** 선택 후 **"사용 설정"**

## 3. 보안 규칙 설정

Realtime Database > **규칙** 탭에서 아래 내용으로 교체:

```json
{
  "rules": {
    "prayers": {
      ".read": true,
      "$prayerId": {
        ".write": true,
        "prayCount": {
          ".write": true
        }
      }
    }
  }
}
```

> 이 규칙은 누구나 읽을 수 있고, 기도 카운트 업데이트와 새 기도제목 추가가 가능합니다.

## 4. 웹 앱 등록 & Config 값 복사

1. 프로젝트 개요 페이지에서 **웹 아이콘 (`</>`)** 클릭
2. 앱 닉네임 입력 (예: `prayer-share-web`)
3. **Firebase Hosting**은 체크 안 해도 됨
4. **"앱 등록"** 클릭
5. 화면에 나오는 `firebaseConfig` 값을 복사

```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "prayer-share-xxxxx.firebaseapp.com",
  databaseURL: "https://prayer-share-xxxxx-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "prayer-share-xxxxx",
  storageBucket: "prayer-share-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

## 5. Config 값 붙여넣기

아래 두 파일에서 `firebaseConfig` 부분을 복사한 값으로 교체:

- **`index.html`** — 일반 사용자 페이지
- **`admin.html`** — 관리자 페이지

## 6. 관리자 비밀번호 변경

`admin.html`에서 아래 줄을 찾아 원하는 비밀번호로 변경:

```javascript
const ADMIN_PASSWORD = 'prayer1234';  // ← 여기를 변경
```

## 7. 완료! 테스트

1. `index.html`을 브라우저에서 열기 — 기도제목 목록 확인
2. `admin.html`을 브라우저에서 열기 — 비밀번호 입력 후 기도제목 등록
3. admin에서 등록하면 index.html에 **실시간으로** 반영되는지 확인

## 파일 구조

```
prayer-share/
├── index.html          ← 일반 사용자용 (기도제목 보기 + 기도 버튼)
├── admin.html          ← 관리자용 (기도제목 등록/삭제)
└── FIREBASE_SETUP.md   ← 이 가이드
```

## 호스팅 (선택사항)

실제 배포하려면 Firebase Hosting을 사용할 수 있습니다:

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
# public 디렉토리: . (현재 폴더)
# SPA: No
firebase deploy
```

배포 후 `https://your-project.web.app`으로 접속 가능합니다.
