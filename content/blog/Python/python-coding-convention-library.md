---
title: Python Coding Convention Library
date: 2021-09-05 23:09:43
category: Python
thumbnail: { thumbnailSrc }
draft: false
---

이번 시간에는 python의 coding convention을 맞춰주는 2가지 tool에 대해서 알아보도록 하겠습니다!😊 오픈 소스 환경에서, 협업을 하다보면 coding convention이 중요합니다. 다수의 개발자가 모여 협업을 하기 때문에 서로가 코드를 수정하더라도 일관성 있는 코드를 생산해 내는 것이 매우 중요합니다. 이에 각 언어 별로 이런 coding convention을 위한 도구가 존재합니다. 그럼 파이썬에서 coding convention을 위한 tool을 알아보도록 하겠습니다. 아래는 이번 포스트의 Category입니다.

## Category

1. What is Lint?
2. flake8
3. The Attributes of flake8 and Usage
4. Git Hook
5. Isort
6. Summary
7. Reference

## What is Lint?

lint란 무엇일까요? 린트란 소스코드를 분석해서 스타일 오류, 버그, 프로그램 오류 등등 오류가 의심되는 부분에 표시(flag)를 하기 위해 사용하는 도구를 가리킵니다. 즉, 오류 등이 발생하지 않도록 검사해주는 역할을 하게 됩니다. Python도 lint가 존재하는데 바로 flake 8이라는 Tool입니다.

## flake8

flake8은 `PEP8`을 기반으로 coding convention을 검사합니다. flake8을 설치하는 방법은 아래와 같습니다.

```bash
$ pip install flake8
```

`pip`를 통해 flake8을 설치 할 수 있고 flake8을 사용하는 방법은 아래와 같습니다.

```bash
$ flake8 [option] <file_name|dir_name>
```

## The Attributes of flake8 and Usage

flake8에는 **다양한 attribute들이 존재**합니다. 이 속성들을 **CLI를 통해 입력**할 수 있습니다. 하지만 **프로젝트 별로 사용**하기 위해 **프로젝트의 상위 디렉토리**에 `setup.cfg` 와 **같은 파일을 활용**하면 됩니다. 기본적으로 `setup.cfg`에서 사용할 때는 다음과 같이 작성합니다.

```python
[flake8]

ignore = E501, E402, E261

exclude = .git, __pycache__

count = True
```

위에 존재하는 `ignore`, `exclude`, `count`를 CLI로도 입력할 수 있지만 **프로젝트 당 설정**하기 위해선 `setup.cfg`를 설정하여 사용하는 것이 편리합니다. 이렇게 `setup.cfg`를 통해 **attribute를 설정**해주면 `flake8 file_name`을 **해당 프로젝트 경로 어디에서 사용했는지와 상관 없이 적용**됩니다. 이젠 자주 쓰게 될 attribute에 대해 알아보도록 하겠습니다.

이제 예시코드를 살펴보면서 진행하겠습니다. `practice1.py`는 아래와 같이 구성되어 있습니다.

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

① ignore

무시할 error code를 명시할 때 사용합니다. 무시할 error code를 적을 땐, comma 단위로 분리하며 기본적으로 무시하는 에러가 존재합니다. `E121,E123,E126,E226,E24,E704,W503,W504`를 기본적으로 무시하게 됩니다. 하지만 명시적으로 `ignore`에 `ignore = E501, E402, E261`과 같이 명시하게 되면 기본적으로 무시하게 되는 에러 코드(`E121`~ `W504`)는 overriding되어 없어지게 됩니다.

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

위의 코드와 명령 프롬프트를 보게되면 default ignore component들을 제외한 나머지 error들이 뜨는 것을 확인할 수 있습니다. 이제 ignore attribute를 사용하게 되면 어떻게 결과가 바뀌는지 확인해보겠습니다.

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

ignore를 사용하지 않았을 때와 위의 결과를 비교해보면 `E402`는 없어진 것을 확인할 수 있지만 `W504`는 생긴걸 확인해볼 수 있습니다. 이는 setup.cfg에서 ignore attribute를 명시적으로 사용해주었기 때문에 `W504`가 overiding되었기 때문에 무시된 것입니다.

이에 기존의 default ignore component들을 유지한채로 추가시키고 싶다면 아래의 예제처럼 `extend-ignore`을 사용하면 됩니다.

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

위의 그림을 보게되면 `extend-ignore`을 사용하여 default ignore component였던 `W504`가 ignore된 채로 다른 error도 ignore된 것을 확인할 수 있습니다.

## Git Hook

Git의 Hook 기능을 사용하게 되면 위에서 배운 flake8을 더 편하게 사용할 수 있습니다. 그렇다면 Git의 Hook은 무엇을 말하는 걸까요?

Git Hook은 어떤 이벤트(상황)에 특정 스크립트를 실행하는 기능입니다. Git은 10가지의 특정 이벤트(상황)에서의 특정 스크립트를 실행할 수 있는데 10개 중 하나가 바로 commit전에 수행하는 pre-commit 입니다. 이 pre-commit에서 우리가 배웠던 Lint인 flake8을 실행할 수 있습니다.

## Reference

1. Flake 8:[https://flake8.pycqa.org/en/latest/user/options.html#cmdoption-flake8-config](https://flake8.pycqa.org/en/latest/user/options.html#cmdoption-flake8-config)
2. Flake 8 description: [https://tech.songyunseop.com/post/2017/05/lint-with-flake8/](https://tech.songyunseop.com/post/2017/05/lint-with-flake8/)
3. isort: [https://pypi.org/project/isort/](https://pypi.org/project/isort/)
4. what is E203 error? `whitespace`: [https://itholic.github.io/python-flake8-list/](https://itholic.github.io/python-flake8-list/)
5. what is w503? `line break before binary operator`: [https://jonnung.dev/python/2019/11/10/python-black-uncompromising-code-formatter/](https://jonnung.dev/python/2019/11/10/python-black-uncompromising-code-formatter/)
6. Git Hook: [https://techblog.woowahan.com/2530/](https://techblog.woowahan.com/2530/)
