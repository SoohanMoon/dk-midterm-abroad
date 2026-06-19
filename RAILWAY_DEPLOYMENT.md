# Railway 배포 가이드 — 해외법인 중간점검

동국홀딩스 **해외법인 중간점검** 단독 배포 가이드입니다.

## 사전 준비

- [Railway](https://railway.app) 계정
- GitHub 저장소: `https://github.com/SoohanMoon/dk-midterm-abroad`

## 1단계: Railway 프로젝트 생성

1. [Railway 대시보드](https://railway.app/dashboard) → **New Project**
2. **Deploy from GitHub repo** 선택
3. `SoohanMoon/dk-midterm-abroad` 저장소 연결
4. 배포 브랜치: `main`

## 2단계: 환경변수 설정

Railway 서비스 → **Variables** 탭:

| 변수명 | 값 | 설명 |
|--------|-----|------|
| `SECRET_KEY` | (랜덤 긴 문자열) | Flask 세션 암호화 키 |
| `FLASK_DEBUG` | `False` | 프로덕션 디버그 비활성화 |
| `DATA_DIR` | `/data` | 실적 데이터 영속 저장 경로 |

`SECRET_KEY` 생성 (PowerShell):

```powershell
[Convert]::ToBase64String((1..32 | ForEach-Object { Get-Random -Maximum 256 }))
```

## 3단계: Volume 추가 (권장)

재배포 후에도 실적 데이터를 유지하려면:

1. 서비스 → **Volumes** → **Add Volume**
2. 마운트 경로: `/data`
3. `DATA_DIR=/data` 환경변수 확인

## 4단계: 도메인 생성

1. 서비스 → **Settings** → **Networking** → **Generate Domain**
2. 배포 완료 후 접속:

| 화면 | URL |
|------|-----|
| **로그인** | `https://<도메인>/midterm/` |
| 헬스체크 | `https://<도메인>/health` |

## 배포 후 점검

- [ ] `/health` → `{"status":"ok"}`
- [ ] `/midterm/` 로그인 화면 로드
- [ ] 팀원 계정 실적등록 테스트
- [ ] 법인장 계정 실적조회 테스트
- [ ] 관리자(`11210110` / `8820`) 로그인 테스트
- [ ] 재배포 후 데이터 유지 (Volume 설정 시)

## 테스트 계정

| 역할 | ID | PW |
|------|-----|-----|
| 팀원 | 12140045 | 9200 |
| 법인장 (미국) | 11210196 | 5902 |
| 법인장 (일본) | 11220162 | 1163 |
| 관리자 | 11210110 | 8820 |

## 문제 해결

- **배포 실패**: Deployments → View Logs 확인
- **502 오류**: gunicorn `PORT` 바인딩 및 `/health` 응답 확인
- **데이터 소실**: Volume + `DATA_DIR=/data` 설정 확인
- **로그인 오류**: `중간점검(해외)/backdata_midtermperformance_abroad - 시트1.csv` 포함 여부 확인
