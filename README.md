# 📚 Charles' Desk

> *"내 책상에서 본 AI와 기술 이야기를 여러분과 나눕니다"*

AI와 기술 트렌드에 대한 개인적인 관찰, 번역, 분석을 공유하는 개인 큐레이션 프로젝트입니다.

## 🌐 Visit the Website

**📍 Website:** https://[username].github.io/charles-desk

_(GitHub 저장소를 연결하고 Pages를 활성화하면 위 URL에서 사이트에 접속할 수 있습니다)_

---

## 📦 Project Structure

```
charles-desk/
├── index.md                                 # 메인 홈페이지
├── reports.md                               # 📊 Reports & Analysis
├── technical-analysis.md                    # 🛠️ Technical Deep Dives
├── curations.md                             # 📌 Curations & Insights
├── about.md                                 # 🤝 About
├── State_of_AI_in_Business_2025_Report.md  # Featured Report
├── Swarm_Orchestration_Skill.md            # Technical Article
├── Ocean_Business.md                        # Industry Analysis
├── _config.yml                              # Jekyll 설정
├── _includes/                               # 공통 컴포넌트
│   ├── navigation.html
│   └── footer.html
├── _layouts/                                # 레이아웃 템플릿
│   └── default.html
└── README.md                                # 이 파일
```

## 🚀 GitHub Pages 활성화 (5분)

이 프로젝트는 **Jekyll**을 사용하여 자동으로 정적 사이트로 빌드됩니다.

### Step 1: GitHub에 푸시
```bash
git add .
git commit -m "Update: Site structure and configuration"
git push origin main
```

### Step 2: GitHub Pages 설정
1. GitHub 저장소의 **Settings** 탭 이동
2. 왼쪽 메뉴에서 **Pages** 선택
3. **Source** 섹션에서:
   - Branch: `main` 선택
   - Folder: `/ (root)` 선택
4. **Save** 클릭

### Step 3: 배포 확인
- GitHub Actions가 자동으로 빌드를 시작합니다 (1-2분)
- "Actions" 탭에서 배포 상태를 확인할 수 있습니다
- 배포 완료 후 `https://[username].github.io/charles-desk` 에서 사이트 확인

---

## 📚 Content Categories

| 섹션 | 설명 |
|------|------|
| **📊 Reports** | 기업 AI 도입 현황, 시장 분석, 종합 보고서 |
| **🛠️ Technical** | AI 에이전트, 아키텍처, 기술 스택 분석 |
| **📌 Curations** | 주간 큐레이션, 개인 관찰, 인사이트 |
| **🤝 About** | 프로젝트 및 큐레이터 정보 |

## 📝 Featured Content

### 🔥 [2025 비즈니스 AI 현황 보고서](State_of_AI_in_Business_2025_Report.md)
- **출처:** MIT Project NANDA
- **핵심:** 300-400억 달러 투자에도 불구하고 95% 조직이 제로 수익
- **대상:** 기업의 생성형 AI 도입 현황 분석

### 🤖 [Swarm Orchestration Skill](Swarm_Orchestration_Skill.md)
- 여러 AI 에이전트 조율 기술 심층 가이드

---

## 🛠️ Local Development

### 로컬에서 사이트 테스트하기 (선택사항)

```bash
# Jekyll 설치 (Mac/Linux)
gem install bundler jekyll

# 로컬 서버 실행
bundle exec jekyll serve
# 또는
jekyll serve

# http://localhost:4000 에서 확인
```

---

## 🎯 Configuration

### 소셜 미디어 링크 추가
`_config.yml` 파일에서 다음을 수정하세요:

```yaml
author: Charles
twitter_username: your_twitter_handle
github_username: your_github_username
email: your_email@example.com
```

### 사이트 URL 업데이트
```yaml
url: "https://your-github-username.github.io/charles-desk"
repository: "your-github-username/charles-desk"
```

---

## 💡 Contributing

이 프로젝트는 개인 큐레이션 프로젝트이지만, 다음과 같은 기여를 환영합니다:

- 🔍 **오탈자 신고** - Issues 또는 Pull Request
- 💬 **개선 제안** - Issues를 통해 피드백
- 🔗 **링크 수정** - 사용 중단된 링크 업데이트

### 기여하기
1. Fork this repository
2. Create your feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -m 'Add improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## 📖 Recommended Reading Order

신규 방문자를 위한 추천 읽기 순서:

1. **[Home Page](index.md)** - 프로젝트 개요
2. **[About](about.md)** - 큐레이터와 프로젝트 정보
3. **[Featured Report](State_of_AI_in_Business_2025_Report.md)** - 메인 콘텐츠
4. **[Reports](reports.md)** - 더 많은 분석 자료
5. **[Technical Deep Dives](technical-analysis.md)** - 기술 상세 분석

---

## 📄 License

이 저장소의 모든 콘텐츠는 개인 큐레이션 작업입니다.

- **원문 저작권:** 원본 저자 및 출판사
- **번역 & 큐레이션:** Charles (CC BY 4.0)

---

## 🙏 Thanks

- MIT Project NANDA - 기업 AI 도입 연구
- 모든 원문 저자 및 연구자들
- 이 공간을 방문하는 모든 분들

---

**Curated by Charles** | Updated: 2025년 1월 31일
