# 🚀 배포 가이드

## GitHub 저장소 생성 및 연결

### 1. GitHub에서 새 저장소 생성
1. [GitHub](https://github.com)에 로그인
2. **New repository** 클릭
3. Repository name: `business-tool-hub` 
4. Description: `소상공인을 위한 비즈니스 도구 허브`
5. **Public** 선택 (무료 Vercel 배포를 위해)
6. **Create repository** 클릭

### 2. 로컬 저장소와 GitHub 연결
```bash
# GitHub 저장소 URL로 원격 저장소 추가
git remote add origin https://github.com/YOUR_USERNAME/business-tool-hub.git

# 코드를 GitHub에 푸시
git push -u origin main
```

## Vercel 배포

### 1. Vercel 계정 연결
1. [Vercel](https://vercel.com)에 GitHub 계정으로 로그인
2. **New Project** 클릭
3. GitHub에서 `business-tool-hub` 저장소 Import
4. **Deploy** 클릭

### 2. 자동 배포 설정
- `main` 브랜치에 push할 때마다 자동 배포됨
- Pull Request 생성시 Preview 배포 생성
- 빌드 명령어: `npm run build`
- 출력 디렉토리: `dist`

### 3. 환경 변수 설정 (선택사항)
Vercel 프로젝트 설정에서 다음 환경 변수들을 추가할 수 있습니다:

```
VITE_APP_TITLE=BusinessToolHub
VITE_APP_DESCRIPTION=필요한 비즈니스 도구를 편의점처럼 간편하게
```

## GitHub Actions (선택사항)

자동 CI/CD를 위해 GitHub Secrets에 다음 값들을 추가하세요:

1. **Settings** → **Secrets and variables** → **Actions**
2. 다음 secrets 추가:
   - `VERCEL_TOKEN`: Vercel 계정 토큰
   - `ORG_ID`: Vercel 조직 ID  
   - `PROJECT_ID`: Vercel 프로젝트 ID

## 배포 확인

### 로컬 빌드 테스트
```bash
# 프로덕션 빌드 생성
npm run build

# 빌드 결과 미리보기
npm run preview
```

### 배포 URL
- **Production**: `https://business-tool-hub.vercel.app`
- **Custom Domain**: Vercel에서 사용자 정의 도메인 설정 가능

## 배포 후 설정

### 1. 도메인 설정
- Vercel 대시보드에서 Custom Domain 추가
- DNS 설정으로 사용자 정의 도메인 연결

### 2. 성능 최적화
- Vercel Analytics 활성화
- Web Vitals 모니터링 설정

### 3. SEO 설정
- 메타 태그 최적화
- OpenGraph 태그 추가
- Sitemap.xml 생성

## 문제 해결

### 빌드 실패시
```bash
# 로컬에서 빌드 에러 확인
npm run build

# 의존성 재설치
rm -rf node_modules package-lock.json
npm install
```

### 배포 에러시
1. Vercel 대시보드에서 빌드 로그 확인
2. Node.js 버전 호환성 확인 (18.x 권장)
3. 환경 변수 설정 확인

## 지속적 개발

### 브랜치 전략
- `main`: 프로덕션 브랜치
- `develop`: 개발 브랜치  
- `feature/*`: 기능 개발 브랜치

### 코드 품질
```bash
# 코드 포맷팅
npm run format

# 린팅 실행
npm run lint

# 타입 체크 (향후 TypeScript 도입시)
npm run type-check
```

## 모니터링

### Vercel Analytics
- 페이지 뷰 추적
- 사용자 행동 분석
- 성능 메트릭 확인

### 에러 모니터링
- Sentry 연동 (선택사항)
- 에러 로깅 및 알림 설정

---

**🎉 배포 완료 후 소상공인들에게 유용한 도구들을 제공하세요!** 