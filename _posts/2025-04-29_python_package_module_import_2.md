---
layout: post
title: "Python module import best practices"
date: 2025-04-29 04:23:00 +0900
categories: [python]
tags: [import, best-practices, package-structure]
---

Python 프로젝트를 구성할 때 module과 package를 import하는 방법은 코드의 유지보수성과 안정성에 직결됩니다.
이 글에서는 Python 프로젝트 개발 시 따라야 할 module import best practices만을 정리합니다.

- import 방식 선택 기준
- 프로젝트 루트 파일 간 import 방법
- 루트 파일에서 하위 package를 import하는 방법
- package 내 module에서 다른 module을 import하는 방법

## 예시 프로젝트 구조

이 글에서는 다음과 같은 프로젝트 구조를 가정합니다.

```plaintext
project/
├── main.py
├── app.py
├── utils/
│   ├── __init__.py
│   ├── math_utils.py
│   ├── async_utils.py
│   └── file_system_utils.py
├── shared/
│   ├── __init__.py
│   ├── structs.py
│   └── assets.py
```

## import module vs from module import name vs from module import * 선택 기준

module을 import할 때는 다음 기준에 따라 방식을 선택합니다.

- **from module import name**을 기본으로 사용합니다.
  - 필요한 이름만 명시적으로 가져와 코드가 간결해집니다.
  - 현대 Python 코드 스타일(예: 유명 오픈소스, PEP 8 권장 흐름)에서도 널리 사용되고 있습니다.
  - 예시: `from utils.math_utils import calculate_sum`

- **import module**은 다음 경우에 사용합니다.
  - 모듈 전체를 명시적으로 네임스페이스로 구분해야 할 때
  - 충돌 가능성이 있는 경우 (예: 동일한 이름이 다른 모듈에도 존재할 때)
  - 예시: `import utils.math_utils`

- **from module import \*** 방식은 사용을 지양합니다.
  - 어떤 이름이 import되는지 명확하지 않아 코드 가독성이 저하될 수 있습니다.
  - 네임스페이스 오염(namespace pollution) 문제를 일으킬 수 있습니다.
  - 유지보수성과 디버깅이 어려워질 수 있습니다.

### import할 name이 많은 경우 정리하는 방법

import할 이름이 많아지는 경우, 다음과 같이 줄바꿈하여 작성합니다.

```python
from utils.math_utils import (
    calculate_sum,
    calculate_average,
    calculate_max,
)
```

- 괄호(`(`, `)`)를 사용하여 여러 이름을 묶습니다.
- 각 이름은 새 줄에 하나씩 작성합니다.
- 마지막 항목에도 쉼표(,)를 붙이는 것이 권장됩니다.
  (추후 diff 관리가 용이하고, 오류를 방지할 수 있습니다.)

### 판단 기준 요약

| 상황 | 추천 방식 | 이유 |
|:----|:----------|:-----|
| 대부분의 경우 | `from module import name` | 코드 간결성, 현대적인 스타일 |
| 충돌 가능성이 있는 경우 | `import module` | 네임스페이스 명확화, 안전성 확보 |
| 사용을 지양해야 하는 경우 | `from module import *` | 네임스페이스 오염 위험, 유지보수성 저하 |

**주의**:

- `from module import *` 방식은 사용을 지양합니다.
- 필요한 이름만 명확하게 import하여 코드 가독성과 명확성을 유지합니다.

## 1. main.py에서 app.py module을 사용할 때의 best practice

- **절대 경로**를 사용하여 import합니다.

```python
# main.py
import app

# 또는 필요한 경우
from app import SomeClassOrFunction
```

## 2. main.py에서 utils package 내 module을 사용할 때의 best practice

- **절대 경로**를 사용하여 import합니다.

```python
# main.py
from utils import math_utils
from utils import async_utils
from utils import file_system_utils

# 또는 필요한 경우
from utils.math_utils import calculate_sum
```

## 3. package 내 module에서 다른 module을 사용할 때의 best practice

- **절대 경로**를 사용하여 import합니다.
- **동일한 package** 또는 **다른 package**에 속한 module만 참조해야 합니다.
- **프로젝트 루트에 위치한 모듈**(예: `main.py`, `app.py`)은 참조하지 않아야 합니다.

### 동일한 package 내 module 참조 예시

```python
# utils/async_utils.py
from utils import math_utils

# 또는 필요한 경우
from utils.math_utils import calculate_sum
```

### 다른 package module 참조 예시

```python
# utils/file_system_utils.py
import shared.assets

# 또는 필요한 경우
from shared.assets import load_asset
```

### 절대 경로 import를 사용하기 위한 전제 조건

- **프로젝트 루트** 바로 아래에 package 디렉터리를 위치시켜야 합니다.
- 또는 **package를 PyPI에 배포하고**, pip 등 패키지 관리자로 설치해야 합니다.

Python은 프로그램 실행 시 현재 작업 디렉터리를 `sys.path`에 자동으로 추가하므로,
루트 바로 아래에 package가 있으면 절대 경로 import가 가능합니다.

### 주의사항

- package 내부 module 간에는 항상 package 단위로 import합니다.
- 프로젝트 루트에 위치한 파일(예: `main.py`, `app.py`)을 직접 참조하면,
  - 순환 참조(circular import) 위험이 증가할 수 있으며,
  - 코드의 의존성 관리가 어려워지고,
  - 프로젝트 구조의 일관성이 무너질 수 있습니다.

따라서, 항상 **package → package** 간의 명확한 참조 관계를 유지하는 것이 중요합니다.

## 절대 경로 import와 상대 경로 import 비교

| 항목 | 절대 경로 import | 상대 경로 import |
|:----|:----------------|:----------------|
| 사용 위치 | 루트 디렉터리 기준 전체 module | 동일한 package 내부 module 간 |
| 가독성 | 경로가 명확하여 초심자에게 유리 | 상대적으로 복잡할 수 있음 |
| 이동성 | 프로젝트 구조 변경에 강함 | package 이동 시 상대 경로 유지 필요 |
| 예시 | `from utils import math_utils` | `from . import math_utils` |

## Reference

- [Python 공식 문서 - Modules](https://docs.python.org/3/tutorial/modules.html)
- [Python 공식 문서 - Packages](https://docs.python.org/3/tutorial/modules.html#packages)
- [PEP 8 - Imports](https://peps.python.org/pep-0008/#imports)
