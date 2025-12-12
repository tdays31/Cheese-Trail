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