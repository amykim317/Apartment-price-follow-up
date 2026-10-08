# 전세 실거래 모니터 설정 가이드

매주 월요일 아침(KST)에 국토부 실거래 API에서 관심 단지의 전세 거래를 가져와 `data/trades.json`에 누적하고, `index.html`이 이를 보여줍니다.

## 폴더 구조
```
config.json                 # 단지/면적 설정
index.html                  # 결과 페이지 (GitHub Pages)
scripts/fetch_trades.py     # 수집 스크립트
.github/workflows/weekly.yml# 주간 자동 실행 설정
data/trades.json            # (자동 생성)
```

## 1. API 키 받기
1. https://www.data.go.kr 가입 → 로그인
2. "국토교통부_아파트 전월세 자료" 검색 → **활용신청** (오픈API)
3. 마이페이지 > 개발계정에서 **일반 인증키** 복사 (Encoding/Decoding 어느 쪽이든 OK)
   - 승인 직후에는 키가 동작하기까지 최대 1~2시간 걸릴 수 있어요.

## 2. GitHub 저장소 만들기
1. https://github.com 에서 새 저장소(Repository) 생성 (예: `jeonse-monitor`, Public 권장 — 무료 Pages는 Public이 필요해요)
2. 이 폴더의 파일들을 올리기 (Add file > Upload files)
   - `.github` 폴더는 숨김 폴더라 올리기 어려울 수 있어요. 그땐 **Add file > Create new file** 에서 파일명에
     `.github/workflows/weekly.yml` 을 입력하고 내용을 붙여넣으세요.

## 3. 키 등록
저장소 **Settings > Secrets and variables > Actions > New repository secret**
- Name: `DATA_GO_KR_KEY`
- Secret: 복사한 인증키

## 4. 처음 한 번 실행 (과거 데이터 채우기)
**Actions 탭 > weekly-trades > Run workflow** → months 에 `12` 입력 → 실행.
로그에서 단지별 "조회된 전세 N건"을 확인하세요. 0건이면 안내되는 단지명을 보고 `config.json`의 `aliases`/`umd`/`lawd`를 수정한 뒤 다시 실행하세요.

## 5. 페이지 공개
**Settings > Pages > Branch: main / (root) > Save**
잠시 후 `https://<내아이디>.github.io/<저장소명>/` 에서 확인.

> 저장소가 Public이면 데이터도 공개돼요. 실거래가는 원래 공개 정보지만 어떤 단지를 보는지가 신경 쓰이면 Private + 로컬로만 쓰는 방법도 있어요.

## 참고
- `lawd` 는 시군구 법정동코드 5자리입니다. 구로구 11530, 서대문구 11410, 부천시 소사구 41194, 원미구 41192 (부천은 코드 체계가 바뀐 적이 있어 41190도 같이 조회하게 해뒀어요).
- 면적 범위는 `min_area`/`max_area`(전용㎡)로 조정하세요. 기본 55~85㎡.
- 이 방식은 **실거래(체결된 전세)만** 자동 수집합니다. 현재 나와 있는 매물(호가)은 네이버/호갱노노에서 확인하세요.
