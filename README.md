
## 소개

저의 Github 블로그로 개발, 알고리즘, 취업, 기타 등등 글을 올리는 곳입니다. 혹시나 제 블로그 참고하여 fork뜰려면 아래의 글들을 꼭 참고해주시길 바랍니다. 그리고 이 블로그도 <a href=" https://github.com/theorydb/theorydb.github.io ">TheoryDB님 블로그</a>를 참고해 만들었습니다. TheoryDB님 감사합니다 :)



### 폴더별 규칙

- _data: authors.yml을 개인정보에 맞게 수정해주시길 바랍니다.

- _featured_catagories: 메뉴의 대분류입니다. 아래의 구조로 이루어져 있을 것입니다.

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

- _featured_tags:  메뉴의 소분류입니다. 아래의 구조로 이루어져 있을 것입니다. 파일명은 카테고리-태그.md로 이루어져 있습니다.

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

- _posts: 글들이 담겨져있는 폴더입니다. 아래의 구조로 이루어져 있습니다. 파일명은 YYYY-MM-DD-카테고리-이름.md로 이루어져 있습니다. 이 형식은 꼭 지켜주셔야 합니다.

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



