🧀 Cheese Trail – Unity 개발 방향 요약 (바이브 코딩 전제)
🎯 1. 개발 목표 요약

Unity(WebGL + PC + 스팀 배포 가능)

타일 기반 턴제 멀티플레이 게임

룰은 루미큐브 기반이지만 테마는 고양이/생쥐/치즈/생선

타일 그래픽 중 “고양이·생쥐·치즈·생선” 아이콘만 네가 제작
→ 바탕 타일, 숫자, 색상 포인트는 AI 생성

UI/UX는 간단하고 가볍게

멀티플레이는 Photon 또는 WebSocket 기반

🎮 2. 타일 구조 설계

Unity ScriptableObject 또는 단순 C# 데이터로 구성.

Tile Data
public enum TileType { Cat, Mouse, Cheese, Fish }
public enum TileColor { Red, Green, White, BlueFish, WhiteFish }

public class Tile {
    public TileType Type;
    public TileColor Color;
    public int Number; // 1~12 (Fish는 0)
}


🔍 참고

Fish는 조커 → Number = 0

Color는 생선만 Blue/White 사용

플레이어당 초기 12장

뽑기 시 3장씩

🎮 3. 룰 핵심 요약
✔ 최초 등록

Cat 또는 Mouse 단일 종류로

합 12 이상

등록 전에는 기존 세트 편집 불가

✔ 고양이 vs 생쥐

같은 그룹(세트/런)에 절대 함께 못 놓음

둘 다 Cheese와는 조합 가능

✔ 생선(Fish) 조커

Fish가 끼면 모든 종족 혼합 가능

숫자나 색 제한은 그대로 유지

✔ 승리 조건

손패 0장 → 우승

🎛️ 4. 멀티플레이 구조

Photon Fusion(또는 PUN2) 추천
→ 룸 생성 → 코드 공유 → 친구 입장

또는
Node.js WebSocket 서버 + Unity WebGL
→ 룸 코드 기반 중계 서버 형태

둘 중편한 걸로 가도 OK.

🕒 5. 턴 관리

기본 40초

로비에서 “턴 시간 옵션: 20/30/40/60/무제한” 선택

호스트가 설정

Unity Timer로 구현 → Photon RPC로 전체 싱크

✨ 6. 그래픽 제작 흐름

타일 기본 프레임·숫자·포인트 컬러: AI 이미지 생성
→ 예: Stable Diffusion, ChatGPT Image, Bing

고양이 / 생쥐 / 치즈 / 생선 아이콘은 네가 직접 제작
(PNG 투명 배경)

게임 내에서 조합 방식
→ 타일 배경 + 숫자 텍스트 + 아이콘 스프라이트
→ 하나의 TilePrefab 안에서 조립

TilePrefab 구조 예시
TilePrefab
  ├─ BackgroundSprite (AI 생성)
  ├─ IconSprite (네가 제작)
  ├─ NumberText (TMP)
  ├─ Highlight/Selected Frame

🧩 7. UI & 시스템 요약
✔ 드래그 & 드롭 시스템

손패 → 필드

필드 → 필드

세트 재조립 가능

최초 등록 전에는 제한 있음

✔ 필드 검증 시스템

그룹 단위로 검증

Cat+Mouse 충돌 확인

Fish 포함 시 예외 처리

숫자 규칙(run/set) 체크

✔ 게임 시작 흐름

호스트가 방 생성

친구 입장 (방 번호 또는 링크)

턴 시간·타일 수 등 옵션 설정

게임 시작 버튼

타일 분배(12장)

첫 플레이어 시작

📦 8. 빌드 방향성 요약
초기 개발

PC(Windows) 빌드

WebGL 빌드로 공유 플레이 확인

네트워크 디버그

최종 배포

WebGL 버전 → 친구들이 쉽게 접속하도록

PC 버전 → 스팀 업로드(후반 목표)

Steamworks.NET 패키지 필요

업적/리더보드 선택 가능

🧭 9. 전체 개발 흐름(로드맵)
1단계 – 기본 시스템 구현

타일 데이터

타일 생성

드래그 드롭

UI 구성

2단계 – 룰 검증

최초 등록 규칙

종족 조합 제한

조커 규칙

숫자/색상 규칙

3단계 – 멀티플레이

Photon 또는 WebSocket 적용

턴 동기화

동시 편집 방지

4단계 – 디자인 삽입

네 제작 아이콘 추가

AI 생성 타일 프레임 교체

전체 UI 통일감

5단계 – 배포

WebGL 테스트

Windows 빌드

스팀 업로드



251212 PM 04:42 .add 

📌 Cheese Trail – 모바일(안드로이드) 출시 기준 프로젝트 구조/기획 정리
1️⃣ 데이터 저장 방식 (유저 통계 / 기록)

모바일 출시엔 반드시 필요함.

✔ 어떤 데이터를 저장할까?

플레이 횟수

승리/패배 기록

평균 턴 시간

최고 연속 승리

최근 매칭 플레이 로그

로컬 방 비밀번호 / 방 기록

설정 데이터(사운드, 색맹 모드, 언어 등)

✔ 저장 방식 두 가지
A. 로컬 저장 (플레이스토어 단독)

간단한 개인용 통계면 이걸로 충분.

Unity PlayerPrefs

작은 설정 값 (음량, 언어 등)

로컬 JSON 저장

게임 기록/통계 저장에 적합

암호화(AES) 적용 권장

기록 조작 방지

B. 온라인 저장(멀티플레이 도입 시)

스팀/온라인 경쟁전 고려한다면 나중에 추가로 필요.

Firebase Firestore

Firebase Realtime DB

Supabase / PocketBase (서버리스)

직접 Node.js 서버

✔ 현재 네 계획 "로컬 네트워크 플레이"라면
A 방식만으로 충분.

2️⃣ 에러 처리 / 예외 상황

모바일은 네트워크 변동이 매우 자주 발생해서 필요함.

✔ 네트워크 끊김

호스트(방장)가 종료되면 → 자동 방 폭파 → 참가자에게 팝업 표시

유저가 잠시 인터넷 끊기면 → 10초 재연결 시도

재연결 실패 → AI 대체? or 패배 처리 선택

✔ 플레이어 이탈

중간 이탈 시:

해당 유저 턴을 스킵하거나

AI가 이어받도록 설정 가능하게

✔ 싱크 불일치 (State Mismatch)

방장 authoritative 구조 추천

모든 타일 이동/제출 판정은 “호스트가 최종 결정”

클라이언트는 단순 요청

3️⃣ 사운드 시스템 (BGM / SFX)
✔ BGM

로비 BGM

게임 시작 BGM

승리/패배 짧은 사운드

✔ SFX (필수)

타일 잡기 / 놓기

타일 조합 완성될 때

에러(불가능한 조합)

타이머 5초 남았을 때 ‘딸깍’

버튼/메뉴 효과

➡ 아주 간단한 2D SFX로 충분.
직접 만드는 대신 무료 SFX 패키지 활용 추천.

4️⃣ 접근성 (Accessibility)
✔ 색맹 모드 (약간 중요)

빨강/녹색/흰색이라 적록색맹 문제 발생함.

"색맹 모드 ON" 시

타일 색을 파랑/노랑/회색 같은 구별 쉬운 색으로 변경

또는 아이콘으로 색상 구분(테두리 형태 변경)

✔ 초보자 튜토리얼 (필수)

루미큐브와 비슷하지만 룰이 고양이/쥐/치즈/생선 때문에 달라서
튜토리얼은 선택이 아니라 필수.

형태:

첫 실행 시 모달 튜토리얼

메뉴에 튜토리얼 다시보기 추가

5️⃣ 버전 관리 (프로젝트 관리용)
✔ Unity 버전 예시

Unity 2022.3 LTS (안드로이드 안정용)

✔ 패키지 버전 명시

Netcode for GameObjects (NGO) – 1.8.x

Unity Transport – 2.x

Input System – 1.7.x

TextMeshPro – 최신

Addressables – 1.21.x (리소스 관리 시)

✔ GitHub 저장 시 규칙

/Assets/ → 전체 커밋

/ProjectSettings/ → 커밋해야 버전 동기됨

/Packages/manifest.json → 커밋

Unity 버전 명시하는 README.md 필수

6️⃣ 모바일 출시까지의 개발 플로우
① 프로토타입

타일 드래그/드롭

기본 룰 적용

AI 없이 1P 테스트

UI 간략히

② 로컬 네트워크 멀티플레이

Unity Relay or LAN 방식

호스트/클라이언트 구조 완성

③ 전체 UI 디자인

메인화면

방 만들기 / 방 참가

옵션 (색맹모드, 사운드)

타일 인벤토리

④ 인증/데이터 관리

로컬 기록 저장

통계 기능 추가

⑤ 사운드 적용
⑥ 퍼포먼스 최적화

모바일 기준:

텍스처 압축

Tile Render 최적화

GC 최소화

⑦ 구글 플레이 등록

앱 아이콘

Google Play Console 계정(25$ 1회)

AAB 빌드 업로드

스크린샷/설명 작성