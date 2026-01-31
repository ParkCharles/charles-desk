# 📚 Charles' Desk

> *"내 책상에서 본 AI와 기술 이야기를 여러분과 나눕니다"*

AI와 기술 트렌드에 대한 개인적인 관찰, 번역, 분석을 공유하는 개인 큐레이션 프로젝트입니다.

**📍 Website:** https://parkcharles.github.io/charles-desk/

---

## 📝 글 작성 & 업로드 (매번 참고하세요!)

### 3단계로 글을 자동 게시합니다

#### **Step 1️⃣: 글 작성**

`_templates/post-template.md`를 참고하여 글을 작성합니다.

```markdown
---
layout: post
title: "글의 제목"
date: 2025-02-15
categories: [Reports]
toc: true
---

# 글의 제목

내용을 여기에 작성합니다...
```

#### **Step 2️⃣: 파일 저장**

파일명을 `YYYY-MM-DD-제목.md` 형식으로 저장합니다.

```
_posts/2025-02-15-my-article.md
```

**파일명 규칙:**
- 영문 소문자와 하이픈만 사용
- 공백과 한글 사용 금지
- 예: `2025-02-15-ai-trends.md` ✅ | `2025-02-15-AI트렌드.md` ❌

#### **Step 3️⃣: Git 커밋 & 푸시**

```bash
git add _posts/
git commit -m "Add: 글의 제목"
git push origin master
```

**완료!** 자동으로 사이트에 반영됩니다. (1-2분 소요)

---

## ✅ 글 작성 체크리스트

새 글을 `_posts/`에 넣기 전에 확인하세요:

- [ ] 파일명이 `YYYY-MM-DD-제목.md` 형식인가?
- [ ] Front Matter가 있는가? (layout, title, date, categories)
- [ ] `#` 제목이 하나만 있는가?
- [ ] 문단이 3~4줄을 넘지 않는가?
- [ ] 요약 섹션이 있는가?

**작성 팁:**
- 한 문단은 3~4줄 이내로 유지
- 하나의 문단에 하나의 핵심만 포함
- 리스트로 구조화하여 읽기 쉽게
- 요약과 다음 단계 섹션은 필수

---

## 📂 폴더 구조

```
README.md                         # 이 파일 (글 작성 가이드 포함)

_posts/                           # ← 블로그 글을 여기에 저장합니다!
├── 2025-02-01-getting-started.md
└── 2025-02-15-my-article.md

_templates/
└── post-template.md             # 글 작성 템플릿 (복사해서 사용)

_layouts/
├── default.html                 # 기본 레이아웃
└── post.html                    # 블로그 글 레이아웃

_includes/
├── navigation.html              # 네비게이션 메뉴
└── footer.html                  # 푸터

_config.yml                       # 사이트 설정 (수정 불필요)
```

---

## 🎯 카테고리 선택

글을 작성할 때 아래 중 하나를 선택합니다:

```yaml
categories: [Reports]       # 기업 AI 도입, 시장 분석, 종합 보고서
categories: [Technical]     # AI 에이전트, 아키텍처, 기술 스택
categories: [Curations]     # 주간 큐레이션, 개인 관찰, 인사이트
categories: [Analysis]      # 심층 분석
categories: [Translation]   # 번역 자료
```

---

## ⚙️ 자동화 기능

`_posts/` 폴더에 글을 추가하면 다음이 자동으로 처리됩니다:

✅ **레이아웃 자동 적용** - `_layouts/post.html` 사용
✅ **메타정보 표시** - 날짜, 카테고리 자동 추가
✅ **RSS 피드** - 피드에 자동 포함
✅ **사이트맵** - 검색 엔진 최적화
✅ **SEO 태그** - 소셜 공유 최적화

---

## 🌐 웹사이트

### 주요 페이지

| 페이지 | 설명 |
|--------|------|
| [Home](index.md) | 메인 홈페이지 |
| [Reports](reports.md) | 📊 기업 AI 도입 현황, 시장 분석 |
| [Technical](technical-analysis.md) | 🛠️ AI 에이전트, 아키텍처 분석 |
| [Curations](curations.md) | 📌 주간 큐레이션, 인사이트 |
| [About](about.md) | 🤝 프로젝트 및 큐레이터 정보 |

### 주요 콘텐츠

- [2025 비즈니스 AI 현황 보고서](State_of_AI_in_Business_2025_Report.md) - MIT Project NANDA
- [Swarm Orchestration Skill](Swarm_Orchestration_Skill.md) - AI 에이전트 조율 기술

---

## 📚 글 작성 참고

**이 README.md만 봐도 충분합니다!**
- 글 작성 방법: 위의 "글 작성 & 업로드" 섹션 참고
- 글 템플릿: `_templates/post-template.md` 복사 후 사용
- 체크리스트: 위의 "글 작성 체크리스트" 확인

---

## 🛠️ 로컬 개발 (선택사항)

로컬에서 사이트를 테스트하려면:

```bash
# Jekyll 설치
gem install bundler jekyll

# 로컬 서버 실행
bundle exec jekyll serve
# 또는
jekyll serve

# http://localhost:4000 에서 확인
```

---

## 💡 팁

### 글이 빨리 반영되지 않으면?
1. 브라우저 캐시 삭제: `Ctrl+Shift+Delete`
2. 몇 분 기다리기 (GitHub Pages 업데이트에 1-2분 소요)
3. [GitHub Actions](https://github.com/ParkCharles/charles-desk/actions) 확인

### 글을 수정하고 싶으면?
1. 파일 수정
2. `git add _posts/` → `git commit` → `git push`
3. 자동 업데이트됨

### 글을 임시로 숨기고 싶으면?
Front Matter에 추가:
```yaml
published: false
```

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

**Curated by Charles** | 마지막 업데이트: 2025년 2월 1일
