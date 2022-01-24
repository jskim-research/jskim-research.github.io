---
layout: single
title: "눈으로 보는 깃허브 페이지 (github page) 코드 분석: From code to HTML"

toc: true
toc_label: INDEX
toc_sticky: true

categories:
  - GithubPage
---

# Introduction

깃허브 페이지를 처음 사용해보는 유저의 입장에서 코드를 분석하고자 한다.  
~~(실제로 작성자가 초보다.)~~

여타 유투브 영상등을 통해 Minimal mistake와 같은 template을 fork해서 사이트를 만들었지만 코드 내용은 모르는 사람들을 타겟으로 한다.

![Figure 1](/images/github_page_code.png "Figure 1"){: width="50%" height="50%" .align-center}
*Figure 1. Code structure*

![Figure 2](/images/index_html_render.png "Figure 2"){: width="100%" height="100%" .align-center}
*Figure 2. Output HTML*

우선 template을 fork하고 나면 Figure 1과 같은 code structure을 가진다. 

그렇다면 Figure 1의 code가 Figure 2의 HTML로 어떻게 변환되는 것인가?

이에 대한 궁금증을 풀어나가도록 하겠다. 그리고 이것을 이해하면 다른 공식 문서들을 이해하는 것도 한결 쉬워질 것이다.


# Structure

본 글의 코드는 깃허브 페이지와 코드 ([[1], [2]])을 참고하여 진행한다.

## /index.html

Github page를 만들고 가장 처음 접근하는 주소는 "https://user-name.github.io/"이다.

"https://user-name.github.io/"을 주소창에 치면 Figure 1의 code structure에서 /index.html 이 호출된다.

![Figure 3](/images/index_html_code.png "Figure 3"){: width="100%" height="100%" .align-center}
*Figure 3. index.html*

Figure 3의 /index.html을 보니 원하던 html 코드는 없고 생뚱맞은 내용과 이를 감싸는 `---` 들로 구성되어 있다.

`---` 들로 감싼 부분을 머리말(front-matter)이라고 한다. 공식 문서[[3]]을 참고하면 layout, sidebar_main, author_profile 들이 값을 저장하는 변수라는 것을 알 수 있다.

**나중에 언급하겠지만 Liquid 라는 언어의 if, for 문 등을 이용해 최종 HTML 파일을 만든다. 변수들도 이러한 로직을 짤 때 사용된다.**

여기서는 layout 변수에 저장된 home에 집중하자. 

공식 문서[[3]]에서도 layout이라는 사전 정의된 전역 변수에 대해 간략히 소개하고 있다. layout에 저장하는 이름을 layout_name이라고 했을 때 /&#95;layouts/layout_name.html 파일을 이용하여 최종 html 파일을 만들겠다는 것이다.

여기서는 /&#95;layouts/home.html 을 이용하겠다는 것이 된다. 실제로 code structure에서 찾아보면 home.html이 이미 있을 것이다.

## /&#95;layouts/home.html

![Figure 4](/images/home_html.png "Figure 4"){: width="100%" height="100%" .align-center}
*Figure 4. home.html*

일단 Figure 4의 home.html을 보니까 뭔가 `<h3 class>` 나 `<div>` 등의 HTML code가 있다. 이게 실제로 어떻게 반영되는지 눈으로 확인해보자.

![Figure 5](/images/home_html_render.png "Figure 5"){: width="100%" height="100%" .align-center}
*Figure 5. home.html to HTML*

참고로 Figure 5처럼 특정 부분의 HTML 소스코드를 보려면 특정 부분에 마우스를 대고 오른쪽 클릭 후 "검사"를 누르면 된다.

Figure 5의 HTML 소스코드중 회색으로 변한 부분을 보도록 하자. 

회색 부분에 해당하는 HTML 소스코드인 `<h3 class="archive__subtitle">Recent posts</h3>`는 Figure 4의 home.html 8번 째 줄에 나온 부분과 같다.  
(정확히 똑같진 않은데 중괄호로 감싼 부분은 Liquid 문법이니 그저 뭔가의 로직이 실행됐다고만 알고 지나가자.)

그리고 바로 다음 이어지는 HTML 소스코드들이 바로 Figure 4의 다음 문장을 실행시킨 것이다.

{% raw %}
```html
<div class="entries-{{ entries_layout }}">
  {% for post in posts %}
    {% include archive-single.html type=entries_layout %}
  {% endfor %}
</div>
```
{% endraw %}

for문이 반복문의 개념이고, `archive-sigle.html`이 특정 post를 list로 나열해주는 함수처럼 생각하면 해석은 간단하다.

모든 post들을 담은 전역변수, posts에서 post를 하나씩 꺼내 페이지 list에 나열해주는 코드와 같다. (즉, Figure 5의 Recent posts 밑 부분처럼 post를 나열한다. 지금은 post가 한 개라서 하나만 나타남)

그런데 home.html에 의해 생성된 HTML 코드들은 일부분의 코드일뿐 아직 전체 HTML 코드가 완성되지 않았다. 나머지 코드는 어디서 완성하는걸까?

Figure 4. home.html의 머리말 (front-matter)을 다시 보면 layout: archive가 또 정의되어 있다.

index.html에서 home.html로 넘어왔듯, 다시 한번 archive.html로 HTML 코드 생성의 역할이 넘어가게 된다.

공식 문서[[4]]에서는 이를 layout 상속이라고 표현하는데 개인적인 생각으론 wrapper 개념에 가까운 듯 하다. 그 이유는 다음 sub-section에서 설명하겠다.

## /&#95;layouts/archive.html

![Figure 6](/images/archive_html.png "Figure 6"){: width="100%" height="100%" .align-center}
*Figure 6. archive.html*

결론적으로 말하자면 home.html의 모든 코드가 archive.html의 {% raw %}`{{ content }}`{% endraw %}부분에 들어가게 된다.

이를 확인하기 위해 Figure 6의 코드들중 파란색으로 감싼 부분들에 초점을 맞춰보자.

가장 첫 줄인 `<div class="archive">`는 Figure 5의 HTML 코드에서도 나온다. 회색으로 줄 친 부분 윗윗 줄이다. 그리고 {% raw %}`{{ content }}`{% endraw %} 부분은 home.html의 코드로 대체되어 Figure 5의 회색 줄 부분이 시작되게 된다.

마치 archive.html이 home.html을 감싸는 듯한 느낌이기에 상속보다는 wrapper가 더 좋은 묘사가 아닌가 싶다.

하지만 archive.html도 머리말(front-matter)을 보면 default.html이라는 최상위 html에 의해 감싸지는 것을 확인할 수 있다.

## /&#95;layouts/default.html

![Figure 7](/images/default_html.png "Figure 7"){: width="100%" height="100%" .align-center}
*Figure 7. default.html*

드디어 Figure 2의 최종 HTML 파일을 완전히 구성하는 태초의 HTML을 발견했다.

HTML 소스코드의 시작과 끝은 <html> ~ </html>이고 Figure 7에서 확인할 수 있다.

물론, 여전히 이해가 안되는 중괄호 내의 Liquid 문법등이 남긴 했지만 이는 다음 글로 넘기도록 하겠다.

# Summary

본 글에서 알아가야할 것은 다음과 같다.

* "https://user-name.github.io/"을 주소창에 쳤을 때 처음으로 찾는 index.html 부터 최종적인 HTML 파일을 만들기까지의 코드 흐름

하지만 아직은 전체적인 그림만 본 것이기 때문에 바로 응용에 들어가기란 쉽지 않다.

다른 사람들이 잘 만들어둔 깃허브 페이지와 코드[[1], [2]]들을 잘 참고하면서 원하는 기능들을 하나씩 구현하다 보면 응용의 단계까지 금방 도달할 수 있으리라 생각한다.

추가로 공식 문서[[3], [4], [5]]들도 잘 참고하도록 하자.

# References

[1]: https://ansohxxn.github.io/  
[2]: https://github.com/ansohxxn/ansohxxn.github.io
[3]: https://jekyllrb-ko.github.io/docs/front-matter/  
[4]: https://jekyllrb-ko.github.io/docs/layouts/
[5]: https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/    


