---  
layout: post  
title: "Blog Rules - 01"  
subtitle: "How to manage my blog post?"  
categories: think  
tags: [think]   
comments: true  
# header-img: img/review/2019-11-22-review-book-pycharm-1.jpg  
---  
  
## 개요  
2019년에 잠깐 만들었다가 방치한 블로그를 5년이 지나서야 다시 관리합니다.  
블로그 관리 방법을 정리한 다음에 다시 취준으로 돌아간 8월부터 공부했던 것들을 정리하고 올립니다.  
일단은 어떻게 올릴지에 대해서 다시 연구해봅니다...

## 관리 방법
![구성요소](https://zzangkkmin.github.io/assets/img/postImages/2024-08-02-think-blogRule-01.png)  
github 블로그 답게 git과 연동하여 작성하고 올리면 되며, 올릴 컨텐츠는 크게 ```_featured_catagories```, ```_featured_tags```, 그리고 ```posts```에 올립니다.  
블로그 작성 시 카테고리 먼저 정하고, 그 다음 태그를 정한 다음에, 본문을 작성하면 됩니다. 각각의 작성 요소는 아래에 설명합니다.
그 외 필요한 이미지나, 첨부파일은 ```posts```에 올립니다.

## 작성 요소
- 메뉴_카테고리   
  블로그 내부 폴더 중 ```_featured_catagories``` 에 파일들이 있습니다. 이 파일들은 ```메뉴_카테고리 slug이름```으로 되어있으며, 아래와 같이 구성이 되어 있습니다.
  - ```markdown
    ---
    layout: list
    title: Algorithm
    slug: algo
    menu: true
    submenu: true
    order: 3
    description: >
      알고리즘 지식에 관한 글  
    ---
    
    ```  
  - ```layout: list``` = 메뉴_카테고리상에서 layout 형태는 list로 유지
  - ```title``` = 제목
  - ```slug``` = 태그로 관리할 때 먼저 붙이는 선제이름
  - ```menu: true``` = 메뉴 표현 True
  - ```submenu``` = 하위 메뉴 표현 여부
  - ```order``` = 메뉴 순서
  - ```description: >``` = 설명은 위 표현 아래에 표기

- 소메뉴_태그  
  블로그 내부 폴더 중 ```_featured_tags``` 에 파일들이 있습니다. 이 파일들은 ```메뉴_카테고리_slug이름-소메뉴_태그_slug이름```으로 되어있으며, 아래와 같이 구성이 되어 있습니다.
  - ```markdown
    ---
    layout: tag-blog
    title: Basic
    slug: basic
    category: algo
    menu: false
    order: 1
    ---
    
    ```
  - ```layout: tag-blog``` = 소메뉴_태그상에서 layout 형태는 tag-blog로 유지
  - ```title``` = 제목
  - ```slug``` = 태그로 관리할 때 붙이는 이름 (파일명은 ```메뉴이름-해당 태그 이름```으로 구성됨)
  - ```category``` = 카테고리 태그 이름
  - ```menu``` = 메뉴 표현 여부
  - ```order``` = 메뉴 순서

- 포스트  
  블로그 내부 폴더 중 ```_posts``` 에 파일들이 있습니다. 파일명은 ```YYYY-MM-DD-카테고리-이름```으로 되어있어야 합니다. 이 파일들은 아래와 같이 구성이 되어 있습니다.
  - ```markdown
    ---
    layout: post  
    title: "alpha Test"  
    subtitle: "Test"  
    categories: think  
    tags: [think]   
    comments: true  
    # header-img: img/review/2019-11-22-review-book-pycharm-1.jpg  
    ---
    
    [글 내용]
    
    ```
  - ```layout: post``` = 포스트상에서 layout 형태는 post로 유지
  - ```title``` = 제목
  - ```subtitle``` = 부제목
  - ```categories``` = 단일 카테고리 설정 
  - ```tags``` = 여러 태그 설정
  - ```comments``` = 댓글 여부
  - ```header-img``` = 제목 이미지
  - 이후 글 내용은 md마크다운 형식으로 작성
