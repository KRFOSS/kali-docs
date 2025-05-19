# 칼리 리눅스 문서 번역 가이드라인

아래 내용을 따르지 않았을 시 PR이 거절될 수 있어요.

1. OS 명칭의 경우 영문 대신 한글(ex. 칼리 리눅스)로 표기해 주세요.

2. 상단에 있는 `title, description, author, 번역` 헤더들은 임의로 수정해서는 안되며 `번역` 속성을 추가하려는 경우에는 기존 틀을 유지하여야 합니다.
   
    *올바른 예시:*
    ```md
    ---
    title: "문서 제목"
    description: "문서 설명"
    author: ["문서 작성자"]
    ---
    ```

    *틀린 예시:*
    ```md
    ---
    title:
      - "문서 제목"
    description:
      - "문서 설명"
    author:
      - 문서 작성자
    ---
    ```

3. 사람들이 이해하기 어려운 내용의 경우 아래와 같은 박스를 생성하여 작성해 주세요.

    ```plaintext
    {{% notice info %}}
    
    내용
    
    {{% /notice %}}
    ```

4. 가능하면 GitHub 내 에디터 대신 [VSCode](https://code.visualstudio.com/)를 이용하여 작업 후 PR을 보내주세요.
