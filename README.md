# 황주빈 자기소개 웹앱

클라우드 데이터베이스 12주차 실습: 개발환경 구성 및 AWS 정적 웹 배포.

배포 주소: https://dudm4oq85apan.cloudfront.net

## 이번 구현

- 학술 개인 페이지 스타일의 미니멀한 반응형 원페이지 (about · projects · contact)
- 이름 아래 3색 그라데이션 구분선, 소개 문단
- 프로젝트 목록: 분류 라벨(PROJECT · ACTIVITY · STUDY)별 포인트 색(파랑 · 초록 · 주황), 마우스 오버 강조
- 프로젝트 상세 창(DETAIL): 항목별 색, 글머리표 정리, Esc 닫기, 포커스 복귀
- contact 섹션: 이메일 작성 연결, GitHub·블로그 새 탭 열기
- 글꼴: 본문 Pretendard(jsDelivr), 영문 제목 DM Serif Display(Google Fonts)
- 빌드 과정 없이 `index.html` 한 파일로 실행 (JS 라이브러리 없음)

## 개발·배포 흐름

ChatGPT Work/Codex · Claude Code → GitHub 소스 관리 → S3 파일 업로드 → CloudFront HTTPS 배포.

1. `index.html`을 수정하고 GitHub에 커밋합니다.
2. 같은 파일을 S3 버킷 루트에 업로드합니다.
3. CloudFront를 통해 사이트를 확인합니다. 수정 배포 후 필요하면 캐시 무효화를 수행합니다.

S3는 서울 리전, 퍼블릭 액세스 차단 유지, ACL 비활성화, SSE-S3 암호화를 사용합니다.
CloudFront는 S3 REST 원본과 OAC를 사용하며, 해당 배포만 `s3:GetObject`를 허용하도록 버킷 정책을 제한합니다.
기본 루트 객체는 `index.html`입니다.

## 기획서 변경 및 향후 구현

과제 요구사항에 따라 기존 GitHub Pages 프론트엔드 배포 계획을 **Amazon S3 + CloudFront**로 변경합니다.
GitHub는 소스 관리에 사용합니다. 향후 `CloudFront/S3 → API Gateway → Lambda(Python) → RDS PostgreSQL` 구조로 확장합니다.

현재는 1주차 정적 화면이며 DB, API, Lambda는 아직 구현하지 않았습니다. 소개·프로젝트 데이터는 HTML/JavaScript에 포함됩니다.
향후 profile/projects/activities 테이블, 조회 전용 DB 사용자, 비공개 RDS, Lambda 보안 그룹 접근 제한, CloudFront 출처 CORS를 적용합니다.
프로필 사진 영역 없이 텍스트 중심으로 구성했습니다. 확인되지 않은 활동 기간·정량 성과는 기재하지 않았습니다.

## 검증 항목

- CloudFront HTTPS 주소에서 사이트 표시
- S3 객체 직접 주소는 AccessDenied
- S3 Block Public Access 4개 항목 활성화
- CloudFront 원본 OAC 및 배포 ARN 제한 버킷 정책
- 프로젝트 상세 보기(DETAIL), 닫기/Esc, 메뉴 이동, 연락처 링크
- GitHub의 `index.html`과 배포된 파일 내용 일치

## 로컬 확인

`index.html`을 브라우저에서 열어 확인할 수 있습니다. 로컬 웹 서버는 `python -m http.server 8080`으로 실행할 수 있습니다.
