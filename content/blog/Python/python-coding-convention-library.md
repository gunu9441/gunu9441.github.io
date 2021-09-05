---
title: Python Coding Convention Library
date: 2021-09-05 23:09:43
category: Python
thumbnail: { thumbnailSrc }
draft: false
---

&nbsp; &nbsp; 이번 시간에는 **python의 coding convention**을 **맞춰주는 3가지 library**에 대해서 알아보도록 하겠습니다!😊 오픈 소스 환경에서, 협업을 하다보면 **coding convention이 중요**합니다. 다수의 개발자가 모여 협업을 하기 때문에 서로가 코드를 수정하더라도 **일관성 있는 코드를 생산**해 내는 것이 매우 중요합니다. 이에 각 언어 별로 이런 coding convention을 위한 도구가 존재합니다. 그럼 파이썬에서 coding convention을 위한 tool을 알아보도록 하겠습니다. 아래는 이번 포스트의 Category입니다.

## Category

1. What is Lint?
2. black
3. flake8
4. The Attributes of flake8 and Usage
5. isort
6. Git Hook
7. Conclusion
8. Summary
9. Reference

## What is Lint?

&nbsp; &nbsp; **lint**란 무엇일까요? **lint**란 **소스코드를 분석**해서 **스타일 오류, 버그, 프로그램 오류 등등 오류가 의심되는 부분에 표시**(flag)를 하기 위해 사용하는 도구를 가리킵니다. 즉, **오류 등이 발생하지 않도록 검사해주는 역할**을 하게 됩니다. Python도 lint가 존재하는데 바로 **flake 8**이라는 Tool입니다.

## black

&nbsp; &nbsp; **black**은 파이썬 커뮤니티에서 최근 가장 널리쓰고 있는 코드 포멧터라고 합니다. black은 **설정을 할 수 있는 것이 별로 없어서 특정 포멧팅 규칙을 그대로 따르게** 되어 있습니다. 모든 사람들이 똑같은 규칙을 따른다면 그게 무슨 규칙이든 하나의 코드 스타일을 기준으로 일관성 있는 코드를 작성할 수 있겠죠??😉

&nbsp; &nbsp; black을 설치하는 방법은 아래의 command와 같습니다.

```bash
$ pip install black
```

&nbsp; &nbsp; 우리는 black을 이후에 **Git Hook인 pre-commit Hook에 등록하여 사용**할 것입니다. 이 방법은 아래에서 알아보도록 하겠습니다.

## flake8

&nbsp; &nbsp; flake8은 `PEP8`을 기반으로 **coding convention을 검사**합니다. flake8을 설치하는 방법은 아래와 같습니다.

```bash
$ pip install flake8
```

&nbsp; &nbsp; `pip`를 통해 flake8을 설치 할 수 있고 flake8을 사용하는 방법은 아래와 같습니다.

```bash
$ flake8 [option] <file_name|dir_name>
```

## The Attributes of flake8 and Usage

&nbsp; &nbsp; flake8에는 **black과는 달리 다양한 attribute들이 존재**합니다. 이 속성들을 **CLI를 통해 입력**할 수 있습니다. 하지만 **프로젝트 별로 사용**하기 위해 **프로젝트의 상위 디렉토리**에 `setup.cfg` 와 **같은 파일을 활용**하면 됩니다. 기본적으로 `setup.cfg`에서 사용할 때는 다음과 같이 작성합니다.

```python
[flake8]

ignore = E501, E402, E261

exclude = .git, __pycache__

count = True
```

&nbsp; &nbsp; 위에 존재하는 `ignore`, `exclude`, `count`를 CLI로도 입력할 수 있지만 **프로젝트 당 설정**하기 위해선 `setup.cfg`를 설정하여 사용하는 것이 편리합니다. 이렇게 `setup.cfg`를 통해 **attribute를 설정**해주면 `flake8 file_name`을 **해당 프로젝트 경로 어디에서 사용했는지와 상관 없이 적용**됩니다. 이젠 자주 쓰게 될 attribute에 대해 알아보도록 하겠습니다.

&nbsp; &nbsp; 이제 예시코드를 살펴보면서 진행하겠습니다. `practice1.py`는 아래와 같이 구성되어 있습니다.

```python
# pratice1.py

def hello():
    print('hello')

import os
import torch
import numpy

hello()

a = (
    1 +
    2 +
    3
)
```

**① ignore**

&nbsp; &nbsp; **무시할 error code를 명시**할 때 사용합니다. 무시할 error code를 적을 땐, comma 단위로 분리하며 기본적으로 무시하는 에러가 존재합니다. `E121,E123,E126,E226,E24,E704,W503,W504`를 기본적으로 무시하게 됩니다. 하지만 명시적으로 `ignore`에 `ignore = E501, E402, E261`과 같이 명시하게 되면 기본적으로 무시하게 되는 에러 코드(`E121`~ `W504`)는 overriding되어 없어지게 됩니다.

```python

#setup.cfg

[flake8]

exclude = .git, __pycache__

count = True

#CLI

$ flake8 practice1.py
practice1.py:4:1: E305 expected 2 blank lines after class or function definition, found 1
practice1.py:4:1: E402 module level import not at top of file
practice1.py:4:1: F401 'os' imported but unused
practice1.py:5:1: E402 module level import not at top of file
practice1.py:5:1: F401 'torch' imported but unused
practice1.py:6:1: E402 module level import not at top of file
practice1.py:6:1: F401 'numpy' imported but unused
practice1.py:12:8: W291 trailing whitespace
8
```

&nbsp; &nbsp; 위의 코드와 명령 프롬프트를 보게되면 default ignore component들을 제외한 나머지 error들이 뜨는 것을 확인할 수 있습니다. 이제 ignore attribute를 사용하게 되면 어떻게 결과가 바뀌는지 확인해보겠습니다.

```python
# setup.cfg
[flake8]

ignore = E501, E402, E261

exclude = .git, __pycache__

count = True

# CLI
$ flake8 practice1.py
practice1.py:4:1: E305 expected 2 blank lines after class or function definition, found 1
practice1.py:4:1: F401 'os' imported but unused
practice1.py:5:1: F401 'torch' imported but unused
practice1.py:6:1: F401 'numpy' imported but unused
practice1.py:11:7: W504 line break after binary operator
practice1.py:12:7: W504 line break after binary operator
practice1.py:12:8: W291 trailing whitespace
7
```

&nbsp; &nbsp; **ignore를 사용하지 않았을 때와 위의 결과를 비교**해보면 `E402`는 없어진 것을 확인할 수 있지만 `W504`는 생긴걸 확인해볼 수 있습니다. 이는 `setup.cfg`에서 **ignore attribute를 명시적으로 사용**해주어 `W504`가 **overiding되었기 때문에 무시**된 것입니다.

&nbsp; &nbsp; 이에 기존의 **default ignore component들을 유지한채로 추가시키고 싶다**면 아래의 예제처럼 `extend-ignore`을 사용하면 됩니다.

```python
# setup.cfg
[flake8]

extend-ignore = E501, E402, E261

exclude = .git, __pycache__

count = True

# CLI
$ flake8 practice1.py
practice1.py:4:1: E305 expected 2 blank lines after class or function definition, found 1
practice1.py:4:1: F401 'os' imported but unused
practice1.py:5:1: F401 'torch' imported but unused
practice1.py:6:1: F401 'numpy' imported but unused
practice1.py:12:8: W291 trailing whitespace
5
```

&nbsp; &nbsp; 위의 그림을 보게되면 `extend-ignore`을 사용하여 default ignore component였던 `W504`가 **ignore된 채로 다른 error도 ignore된 것을 확인**할 수 있습니다.

## isort

&nbsp; &nbsp; isort란 import한 라이브러리들을 **알파벳 순서대로 정렬**하거나 **종류에 따라서 분류**하는 라이브러리 입니다.

```python
pip install isort
```

isort를 설치하기 위해선 위와 같은 command를 입력하면 됩니다. Git Hook에 적용하는 방법은 아래에서 알아보도록 하겠습니다.

## Git Hook

&nbsp; &nbsp; Git의 Hook 기능을 사용하게 되면 위에서 배운 `black`, `flake8`, `isort`를 더 편하게 사용할 수 있습니다. 그렇다면 Git의 Hook은 무엇을 말하는 걸까요?

Git Hook은 **어떤 이벤트(상황)에 특정 스크립트를 실행하는 기능**입니다. Git은 여러가지 특정 이벤트(상황)에서 특정 스크립트를 실행할 수 있는데 10개의 hook중 하나가 **바로 commit전에 특정 스크립트를 수행하는 pre-commit hook**입니다.

이 Git의 pre-commit hook을 편리하게 사용할 수 있도록 도와주는 `pre-commit`이라는 도구에 대해서 알아보도록 하겠습니다.

① pre-commit 설치

```bash
$ pip install pre-commit
```

&nbsp; &nbsp; 위의 command를 통해 pre-commit tool을 설치해줍니다.

② `.pre-commit-config.yaml` 생성

&nbsp; &nbsp; pre-commit은 `.pre-commit-config.yaml` 설정 파일을 필요로합니다.

```bash
$ pre-commit sample-config > .pre-commit-config.yaml
```

&nbsp; &nbsp; 위의 command를 사용하면 pre-commit에서 제공하는 기본 작업이 적혀있는 채로 파일이 작성됩니다.

③ 설정한 설정 파일을 .git/hooks/pre-commit에 반영

```bash
$ pre-commmit install
```

&nbsp; &nbsp; 위의 command를 사용하게 되면`.pre-commit-config.yaml`에 저장되어 있는 특정 작업들을 **.git/hooks/pre-commit을 생성한 후 저장**합니다. 이에 `commit`할 때마다 특정 작업들을 자동으로 실행합니다.

④ 초기 셋업 단계에서 직접 `pre-commit`을 실행하여 설정이 잘 되어 있는지 확인(optional)

```bash
$ pre-commit run
```

&nbsp; &nbsp; `.pre-commit-config.yaml`에 저장되어 있는대로 실행이 잘 되는지 확인해 볼 필요가 있기 때문에 위와 같은 command를 사용합니다.

⑤ 새로운 hook을 추가했을 때, 기존의 파일 검사(optional)

```bash
$ pre-commit run --all-files
```

&nbsp; &nbsp; 새로운 hook을 추가했을 때, 기존의 존재하는 모든 파일을 검사할 필요가 있습니다. 이에 위와 같은 command를 실행시켜주면 검사를 진행하게 됩니다.

&nbsp; &nbsp; 이제 위에서 배운 `black`, `flake8`, `isort`을 어떻게 `pre-commit-config.yaml`에 적용할 수 있는지 알아보겠습니다.

```python
repos:
-   repo: https://github.com/psf/black
    rev: 20.8b1
    hooks:
    - id: black
-   repo: https://gitlab.com/pycqa/flake8
    rev: 3.8.4
    hooks:
    - id: flake8
-   repo: https://github.com/timothycrosley/isort
    rev: 5.7.0
    hooks:
    -   id: isort
```

&nbsp; &nbsp; 위에 코드를 yaml파일에 추가시키면 됩니다. 만약 `black`과 `isort`에 **argument를 넣고 싶다**면 아래의 그림과 같이 `setup.cfg`에 추가시키면 됩니다.

```python
# setup.cfg
[flake8]
extend-ignore = E501, E402, E261
exclude = .git, __pycache__
count = True
```

## Conclusion

&nbsp; &nbsp; 대부분의 project에서 아래와 같이 `setup.cfg`와 `.pre-commit-config.yaml`을 설정할 예정입니다:)

```python
# setup.cfg
[flake8]
max-line-length = 88
extend-ignore = E203, W503

[isort]
multi_line_output = 3
include_trailing_comma = True
force_grid_wrap = 0
use_parentheses = True
ensure_newline_before_comments = True
line_length = 88
known_third_party = stepper
profile = "black"

## .pre-commit-config.yaml
repos:
  - repo: https://github.com/psf/black
    rev: 20.8b1
    hooks:
      - id: black
  - repo: https://gitlab.com/pycqa/flake8
    rev: 3.8.4
    hooks:
      - id: flake8
  - repo: https://github.com/PyCQA/isort
    rev: 5.7.0
    hooks:
      - id: isort
		#or args: ["--profile", "black"]
```

&nbsp; &nbsp; 위의 코드에서 **rev는 tag(release)를 의미**하며 **버전**을 뜻합니다. isort의 버전에 대한 정보는 [여기](https://github.com/PyCQA/isort/tags)있습니다.

&nbsp; &nbsp; **pre-commit을 설치**했다면 **매 project마다** `.pre-commit-config.yaml`을 만들어 설정해주고 `pre-commit install`을 사용하여 이것을 **.git/hooks/pre-commit에 반영**해주면 됩니다.

## Summary

1. `pre-commit` command(tool)는 `.pre-commit-config.yaml`에 **저장되어 있는 특정 작업**들을 .git/hooks/pre-commit에 **간편하게 저장하기 위해 사용**합니다. 즉, **pre-commit hook을 간편하게 설정하기 위해 사용**합니다.
2. 프로젝트 단위 당 관리를 할 때, `pre-commit` hook를 활용해도 flake8에 대한 argument를 넣기 위해선 `setup.cfg`를 사용하면 됩니다.
3. flake8에 대해서 setup.cfg를 사용할 때, `W503`을 무시하도록 설정해야합니다. Black은 **이항 연산자 전에 줄 바꿈을 추가**시키는데 `flake8`은 이를 **경고로 발생시키기 때문**입니다.
4. flake8과 isort가 충돌하는 것을 방지하기 위해 `args: ["--profile", "black"]`를 추가시켜야 합니다.
5. **pre-commit을 설치**했다면 **매 project마다** `.pre-commit-config.yaml`을 만들어 설정해주고 `pre-commit install`을 사용하여 이것을 **.git/hooks/pre-commit에 반영**해주면 됩니다.

이것으로 오늘의 포스팅을 마무리하겠습니다.  
다음에 봐요~😎

## Reference

1. Flake 8 documentation: [https://flake8.pycqa.org/en/latest/user/options.html#cmdoption-flake8-config](https://flake8.pycqa.org/en/latest/user/options.html#cmdoption-flake8-config)
2. Flake 8 description: [https://tech.songyunseop.com/post/2017/05/lint-with-flake8/](https://tech.songyunseop.com/post/2017/05/lint-with-flake8/)
3. isort: [https://pypi.org/project/isort/](https://pypi.org/project/isort/)
4. what is E203 error? `whitespace`: [https://itholic.github.io/python-flake8-list/](https://itholic.github.io/python-flake8-list/)
5. what is w503? `line break before binary operator`: [https://jonnung.dev/python/2019/11/10/python-black-uncompromising-code-formatter/](https://jonnung.dev/python/2019/11/10/python-black-uncompromising-code-formatter/)
6. Git Hook: [https://techblog.woowahan.com/2530/](https://techblog.woowahan.com/2530/)
7. Pre-commit is hook: [https://egg-programmer.tistory.com/288](https://egg-programmer.tistory.com/288)
8. Pre-commit 설치 방법 및 실행(**pre-commit install이 중요→ .**git/hook에 pre-commit 생성 후 pre-commit-config.yaml을 보고 특정 작업 저장): [https://www.daleseo.com/pre-commit/](https://www.daleseo.com/pre-commit/)
9. linux '>' 의미: [https://twpower.github.io/114-difference-between-single-and-double-greater-than-sign](https://twpower.github.io/114-difference-between-single-and-double-greater-than-sign)
10. Pre-commit documentation: [https://pre-commit.com/#plugins](https://pre-commit.com/#plugins)
11. Introduce 4 pre-commit plugins(black, flake8, isort, interrogate): [https://towardsdatascience.com/4-pre-commit-plugins-to-automate-code-reviewing-and-formatting-in-python-c80c6d2e9f5](https://towardsdatascience.com/4-pre-commit-plugins-to-automate-code-reviewing-and-formatting-in-python-c80c6d2e9f5)
12. black 소개: [https://www.daleseo.com/python-black/](https://www.daleseo.com/python-black/)
13. black 코드 스타일 소개: [https://jonnung.dev/python/2019/11/10/python-black-uncompromising-code-formatter/](https://jonnung.dev/python/2019/11/10/python-black-uncompromising-code-formatter/)
14. isort documentation: [https://pythonrepo.com/repo/PyCQA-isort-python-general-utilities](https://pythonrepo.com/repo/PyCQA-isort-python-general-utilities)
15. isort와 black 충돌 해결방법: [https://bleepcoder.com/ko/isort/713521288/how-to-make-isort-black-compatible-original-question-isort](https://bleepcoder.com/ko/isort/713521288/how-to-make-isort-black-compatible-original-question-isort)
