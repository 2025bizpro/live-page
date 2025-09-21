# 무료 라이브 사전등록 시스템

모바일 퍼스트 랜딩페이지와 Google Apps Script를 활용한 사전등록 시스템입니다.

## 📁 프로젝트 구조

```
/landing/
  index.html          # 메인 랜딩페이지 (폼 입력)
  success.html        # 완료 페이지 (이미지 1장만)
  images/            # 이미지 폴더
    hero-image.svg    # 메인 히어로 이미지
    success-image.svg # 완료 페이지 이미지
  README.md          # 배포 가이드

/gas/
  Code.gs            # Google Apps Script 백엔드
  appsscript.json    # GAS 설정 파일
```

## 🚀 배포 가이드

### 0단계: 강사별 라이브 페이지 생성

#### **강사별 디렉토리 구조**
```
live-page/                    # 기본 템플릿
├── landing/
│   ├── index.html           # 강사별로 수정 필요
│   ├── success.html
│   └── images/              # 강사별 이미지
├── gas/
│   ├── Code.gs             # 공통 사용
│   └── appsscript.json     # 공통 사용
└── README.md

캐시루트_라이브/              # 강사별 복사본
├── landing/
│   ├── index.html           # INSTRUCTOR_NAME, INSTRUCTOR_ID 수정
│   └── images/              # 캐시루트 전용 이미지
└── gas/                     # 공통 GAS 코드

사뚜_라이브/                  # 강사별 복사본
├── landing/
│   ├── index.html           # INSTRUCTOR_NAME, INSTRUCTOR_ID 수정
│   └── images/              # 사뚜 전용 이미지
└── gas/                     # 공통 GAS 코드
```

#### **강사별 설정 방법**

1. **디렉토리 복사**
   ```bash
   cp -r live-page 캐시루트_라이브
   cp -r live-page 사뚜_라이브
   cp -r live-page 김메타강사_라이브
   ```

2. **각 강사별 index.html 수정**
   ```javascript
   // 캐시루트_라이브/landing/index.html
   const INSTRUCTOR_NAME = "캐시루트";
   const INSTRUCTOR_ID = "cashroot";
   
   // 사뚜_라이브/landing/index.html
   const INSTRUCTOR_NAME = "사뚜";
   const INSTRUCTOR_ID = "sattu";
   
   // 김메타강사_라이브/landing/index.html
   const INSTRUCTOR_NAME = "김메타강사";
   const INSTRUCTOR_ID = "kimmeta";
   ```

3. **강사별 이미지 교체**
   - 각 디렉토리의 `images/` 폴더에 강사별 이미지 추가
   - 001.png, 002.png, 003.png... 순서로 배치

### 1단계: Google Spreadsheet 준비

1. **새 스프레드시트 생성**
   - Google Drive에서 새 스프레드시트 생성
   - 시트명을 `LiveRSVP`로 변경
   - 스프레드시트 ID 복사 (URL에서 `/d/` 다음 부분)

2. **헤더 설정** (자동 생성되지만 미리 설정 가능)
   ```
   A1: timestamp
   B1: name  
   C1: phone
   D1: consent
   E1: instructorName
   F1: instructorId
   G1: status
   H1: msgId
   I1: error
   ```

3. **강사별 시트 자동 생성**
   - 각 강사별로 별도 시트가 자동 생성됩니다
   - 예: `캐시루트_LiveRSVP`, `사뚜_LiveRSVP`, `김메타강사_LiveRSVP`

### 2단계: Google Apps Script 배포

1. **새 Apps Script 프로젝트 생성**
   - [script.google.com](https://script.google.com) 접속
   - "새 프로젝트" 클릭

2. **코드 업로드**
   - `Code.gs` 파일 내용을 복사하여 붙여넣기
   - `appsscript.json` 파일 내용을 복사하여 붙여넣기

3. **스크립트 속성 설정**
   - 좌측 메뉴 "프로젝트 설정" → "스크립트 속성" 탭
   - 다음 속성들을 추가:

   | 속성명 | 값 | 설명 |
   |--------|-----|------|
   | `SHEET_NAME` | `LiveRSVP` | 스프레드시트 시트명 |
   | `SPREADSHEET_ID` | `YOUR_SPREADSHEET_ID` | 스프레드시트 ID |
   | `SHARED_SECRET` | `frontend_backend_shared_secret` | 프론트/백엔드 공유 비밀키 |
   | `ALLOWED_ORIGIN` | `https://yourdomain.vercel.app` | 허용 도메인 (쉼표로 구분) |
   | `ALIMTALK_PROVIDER` | `SOLAPI` | 알림톡 대행사 (SOLAPI/NAVER/TOAST) |
   | `PROVIDER_API_KEY` | `your_api_key` | 대행사 API 키 |
   | `PROVIDER_API_SECRET` | `your_api_secret` | 대행사 API 시크릿 |
   | `SENDER_KEY` | `your_sender_key` | 카카오 발신프로필 키 |
   | `TEMPLATE_CODE` | `LIVE_REG_CONFIRM_V1` | 알림톡 템플릿 코드 |
   | `LIVE_DATE_KST` | `2025-10-05 20:00` | 라이브 일시 |
   | `LIVE_URL` | `https://youtube.com/live/XXXX` | 라이브 시청 링크 |

4. **웹 앱 배포**
   - 우측 상단 "배포" → "새 배포"
   - 유형: "웹 앱" 선택
   - 실행 사용자: "나"
   - 액세스 권한: "모든 사용자"
   - "배포" 클릭
   - **웹 앱 URL 복사** (나중에 프론트엔드에서 사용)

### 3단계: 프론트엔드 배포

1. **환경 변수 수정**
   - `landing/index.html` 파일 열기
   - 상단 스크립트 섹션에서 다음 값 수정:
   ```javascript
   const WEB_APP_URL = "https://script.google.com/macros/s/XXXX/exec"; // 실제 GAS URL
   const SHARED_SECRET = "frontend_backend_shared_secret"; // 백엔드와 동일한 값
   ```

2. **이미지 교체 (선택사항)**
   - `landing/images/hero-image.svg`: 메인 히어로 이미지 교체
   - `landing/images/success-image.svg`: 완료 페이지 이미지 교체
   - 또는 HTML 파일에서 이미지 경로를 다른 이미지로 변경

3. **정적 호스팅 배포**
   - Vercel, Netlify, GitHub Pages 등에 `/landing` 폴더 배포
   - 배포된 도메인을 GAS의 `ALLOWED_ORIGIN`에 추가

## 📱 카카오 알림톡 템플릿 등록

### 1. 카카오 비즈니스 계정 설정
1. [카카오 비즈니스](https://business.kakao.com) 접속
2. 발신프로필 등록 및 승인
3. 알림톡 템플릿 등록

### 2. 템플릿 내용 예시
```
#{name}님, 무료 라이브 사전등록이 완료되었습니다.
📅 일시: #{date} (KST)
▶ 시청 링크: #{url}
```

### 3. 치환 변수 설정
- `#{name}`: 사용자 이름
- `#{date}`: 라이브 일시 (KST)
- `#{url}`: 라이브 시청 링크

### 4. 버튼 설정 (선택사항)
- **버튼 타입**: WL (웹링크)
- **버튼명**: "바로 시청하기"
- **모바일 링크**: 라이브 URL
- **PC 링크**: 라이브 URL

### 5. Solapi 대행사 설정
1. [Solapi](https://solapi.com) 계정 생성
2. API 키 발급
3. 카카오 알림톡 서비스 신청
4. 발신프로필과 템플릿 연동

## 🧪 테스트 체크리스트

### ✅ 기본 기능 테스트
- [ ] 이름 입력 (2-30자, 한글/영문/공백)
- [ ] 전화번호 입력 (010-XXXX-XXXX 형식)
- [ ] 개인정보 동의 체크박스
- [ ] 하단 고정 CTA 버튼 표시
- [ ] 모바일 반응형 레이아웃

### ✅ 유효성 검사 테스트
- [ ] 이름 미입력 시 오류 메시지
- [ ] 잘못된 전화번호 형식 시 오류 메시지
- [ ] 개인정보 미동의 시 제출 불가
- [ ] 허니팟 필드 채워진 경우 스팸 처리

### ✅ 백엔드 연동 테스트
- [ ] 성공 시 스프레드시트에 데이터 저장
- [ ] 알림톡 발송 성공 시 `status=sent`
- [ ] 알림톡 발송 실패 시 `status=failed`
- [ ] 잘못된 secret 값 거부
- [ ] 허용되지 않은 도메인 CORS 차단

### ✅ 완료 페이지 테스트
- [ ] 성공 시 `success.html`로 이동
- [ ] 완료 페이지에서 이미지 1장만 표시
- [ ] 모바일에서 전체 화면 이미지 표시

### ✅ 보안 테스트
- [ ] CORS 정책 적용 확인
- [ ] 공유 비밀키 인증 확인
- [ ] SMS 대체 발송 비활성화 확인 (`disableSms: true`)

## 🔧 문제 해결

### 자주 발생하는 오류

1. **CORS 오류**
   - GAS의 `ALLOWED_ORIGIN`에 정확한 도메인 추가
   - 프로토콜(https) 포함하여 입력

2. **알림톡 발송 실패**
   - Solapi API 키/시크릿 확인
   - 발신프로필 승인 상태 확인
   - 템플릿 승인 상태 확인

3. **스프레드시트 접근 오류**
   - 스프레드시트 ID 정확성 확인
   - GAS 계정의 스프레드시트 접근 권한 확인

4. **폼 제출 실패**
   - GAS 웹 앱 URL 정확성 확인
   - `SHARED_SECRET` 값 일치 확인

### 디버깅 방법

1. **GAS 로그 확인**
   - Apps Script 에디터에서 "실행" → "로그" 확인

2. **브라우저 개발자 도구**
   - Network 탭에서 API 요청/응답 확인
   - Console 탭에서 JavaScript 오류 확인

3. **테스트 함수 실행**
   - GAS에서 `testDoPost()`, `testAlimtalk()` 함수 직접 실행

## 📞 지원

문제가 발생하면 다음을 확인해주세요:
1. 모든 환경 변수가 올바르게 설정되었는지
2. 카카오 알림톡 템플릿이 승인되었는지
3. GAS 웹 앱이 올바르게 배포되었는지
4. CORS 설정이 정확한지

---

**주의사항**: 
- SMS 대체 발송은 `disableSms: true` 설정으로 비활성화되어 있습니다.
- 모든 개인정보는 라이브 알림 목적으로만 사용됩니다.
- 프로덕션 배포 전 충분한 테스트를 진행해주세요.
