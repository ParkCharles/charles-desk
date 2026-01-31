# 📝 자동 게시 가이드

Charles' Desk에 새 글을 게시하는 자동화된 방법입니다.

---

## 🚀 빠른 시작 (3단계)

### Step 1: 글 작성

`_templates/post-template.md`를 복사하여 글을 작성합니다.

**파일명 규칙**: `YYYY-MM-DD-제목.md`
- 예: `2025-02-01-ai-trends.md`
- 영문 소문자와 하이픈만 사용
- 공백과 한글은 사용하지 않음

### Step 2: _posts 폴더에 저장

작성한 파일을 `_posts/` 폴더에 저장합니다.

```
charles-desk/
├── _posts/
│   ├── 2025-02-01-ai-trends.md
│   ├── 2025-02-03-agent-architecture.md
│   └── ...
```

### Step 3: Git 커밋 및 푸시

```bash
git add _posts/
git commit -m "Add: 새 글 제목"
git push origin master
```

완료! 자동으로 GitHub Pages에 반영됩니다.

---

## 📋 글 작성 체크리스트

새 글을 `_posts/`에 넣기 전에 확인하세요:

- [ ] 파일명이 `YYYY-MM-DD-제목.md` 형식인가?
- [ ] Front Matter가 포함되어 있는가?
  ```yaml
  ---
  layout: post
  title: "글 제목"
  date: YYYY-MM-DD
  categories: [카테고리명]
  toc: true
  ---
  ```
- [ ] `#` 제목이 하나만 있는가?
- [ ] 문단이 3~4줄을 넘지 않는가?
- [ ] 강조 표현이 과하지 않은가?
- [ ] 요약 섹션이 있는가?

---

## 📂 폴더 구조

```
_posts/              # 모든 새로운 글이 여기에 저장됨
_templates/          # 글 작성 템플릿
_layouts/post.html   # 블로그 글 레이아웃 (자동 적용)
_config.yml          # 사이트 설정 (자동 반영)
```

---

## 🎯 카테고리 목록

글을 작성할 때 `categories`에 아래 중 하나를 선택합니다:

```yaml
categories: [Reports]        # 기업 AI 도입 현황, 시장 분석
categories: [Technical]      # AI 에이전트, 아키텍처, 기술
categories: [Curations]      # 주간 큐레이션, 인사이트
categories: [Analysis]       # 심층 분석
categories: [Translation]    # 번역 자료
```

---

## 📝 글 작성 템플릿 예시

```markdown
---
layout: post
title: "2025 AI 에이전트 트렌드"
date: 2025-02-01
categories: [Reports]
toc: true
---

# 2025 AI 에이전트 트렌드

에이전트 기술이 빠르게 발전하고 있습니다.

- 주요 트렌드 1
- 주요 트렌드 2
- 주요 트렌드 3

## 첫 번째 주제

핵심 내용을 설명합니다.

### 세부 사항

- 항목 1
- 항목 2

## 요약

- 핵심 요점 1
- 핵심 요점 2

## 다음 단계

- 다음 글 예상 주제
```

---

## 🔍 자동 처리되는 항목

`_posts/` 폴더에 글을 추가하면 다음이 자동으로 처리됩니다:

✅ **레이아웃 자동 적용**
- `layout: post`가 자동으로 `_layouts/post.html` 적용

✅ **메타데이터 자동 표시**
- 글 상단에 작성일, 카테고리 자동 표시
- 목차(toc: true) 자동 생성 지원

✅ **사이트 피드 자동 생성**
- RSS 피드에 자동 추가 (jekyll-feed 플러그인)
- 사이트맵에 자동 포함 (jekyll-sitemap 플러그인)

✅ **검색 최적화**
- 메타 태그 자동 생성 (jekyll-seo-tag 플러그인)
- sitemap.xml 자동 생성

---

## ⚙️ 자동화 설정 (기술 정보)

### _config.yml에 포함된 자동화 설정

```yaml
# 플러그인
plugins:
  - jekyll-feed           # RSS 피드 자동 생성
  - jekyll-sitemap        # 사이트맵 자동 생성
  - jekyll-seo-tag        # SEO 메타 태그 자동 생성

# 피드 설정
feed:
  posts_limit: 10         # 최근 10개 글만 피드에 포함
```

### 자동 생성되는 파일

```
# GitHub Pages 배포 시 자동 생성
/feed.xml               # RSS 피드
/sitemap.xml            # 사이트맵
```

---

## 💡 팁

### 글이 빨리 반영되지 않으면?

1. **브라우저 캐시 삭제**: Ctrl+Shift+Delete
2. **GitHub Actions 확인**:
   - https://github.com/ParkCharles/charles-desk/actions
   - 배포 상태 확인
3. **몇 분 기다리기**: GitHub Pages 업데이트에 1-2분 소요

### 글 수정하고 싶으면?

1. 파일 수정
2. Git 커밋 및 푸시
3. 자동으로 사이트 업데이트

### 글을 비공개로 하고 싶으면?

Front Matter에 다음을 추가:

```yaml
published: false
```

이 글은 사이트에 표시되지 않습니다.

---

## 🎨 고급: 카테고리별 페이지 자동 생성

향후 업데이트에서 카테고리별 아카이브 페이지를 자동 생성할 예정입니다.

```
/reports/          # Reports 카테고리 글 모음
/technical/        # Technical 카테고리 글 모음
/curations/        # Curations 카테고리 글 모음
```

---

## 📞 문제 해결

### Q: 글이 사이트에 보이지 않아요
A: 다음을 확인하세요:
- Front Matter가 올바른가?
- 파일명이 `YYYY-MM-DD-` 형식으로 시작하는가?
- 날짜가 미래 날짜는 아닌가?

### Q: 레이아웃이 이상해요
A: `layout: post` 설정이 있는지 확인하세요. 없으면 기본 레이아웃이 적용됩니다.

### Q: 수정 후 반영 안 되나요
A: 브라우저 캐시를 삭제하고 다시 접속해보세요.

---

**이제 글만 작성해서 `_posts/`에 넣으면 됩니다!** 🚀
