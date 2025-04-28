---
layout: post
title: "python package vs module, module import 동작 방식"
date: 2025-04-28 23:00:00 +0900
categories: [python]
tags: [python, import, module, sys.path]
---

본 문서에서는 Python에서 모듈을 import할 때의 동작 방식을 설명합니다. 또한 import 시 경로 문제와 `package`, `module`, `sub-package`, `sub-module`의 개념 차이도 함께 정리합니다.

## 1. package, module, sub-package, sub-module의 차이

| 구분         | 설명                                                      | 예시                      |
|--------------|------------------------------------------------------------|---------------------------|
| Package      | module들의 집합으로, 폴더 단위로 관리되며 일반적으로 `__init__.py` 파일을 가짐 | `utils/` 폴더             |
| Module       | 하나의 Python 파일로 구성된 코드 단위                         | `utils/a.py`              |
| Sub-package  | 다른 package 내부에 포함된 하위 package                       | `utils/math_utils/` 폴더  |
| Sub-module   | sub-package 내부에 있는 Python 파일                            | `utils/math_utils/linear.py` |

**추가 설명**

- Python 3.2까지는 package와 sub-package는 반드시 `__init__.py` 파일을 가진 디렉토리여야 했습니다.
- Python 3.3부터는 **implicit namespace package**가 도입되어, `__init__.py` 파일이 없어도 디렉토리를 package로 인식할 수 있습니다.

## 2. package 내에 `__init__.py`가 있을 때와 없을 때의 import 차이

| 구분               | 설명                                                                 |
|--------------------|-----------------------------------------------------------------------|
| `__init__.py` 있음 | Python이 이 디렉토리를 **package**로 인식하여 내부 module을 정상적으로 import 가능 |
| `__init__.py` 없음 | Python 3.3 이상에서는 암묵적으로 package로 인식될 수 있으나, 일부 경우 import 오류가 발생할 수 있음 |

**자세한 설명**

- Python 3.3 이전 버전에서는 `__init__.py` 파일이 반드시 존재해야만 해당 디렉토리를 package로 인식할 수 있었습니다.
- Python 3.3 이후부터는 `__init__.py`가 없어도 디렉토리를 package로 취급할 수 있습니다.

## 3. implicit namespace package

Python 3.3부터는 **implicit namespace package** 기능이 도입되었습니다.

- `__init__.py` 파일이 없는 디렉토리도 package로 인식할 수 있습니다.
- 이 경우, 해당 디렉토리는 **명시적으로 module로 작성된 것이 아니라**, 파일 시스템상의 디렉토리 구조만으로 Python이 package로 간주합니다.

**특징**

- 여러 경로에 분산된 디렉토리들을 하나의 package로 취급할 수 있습니다.
- import할 때 별도의 초기화 코드가 실행되지 않습니다. (`__init__.py`가 없기 때문)
- 일반적인 경우에는 기존의 명시적 package와 똑같이 사용할 수 있습니다.

**예시**

```
/home/user/project/
├── animals/
│   ├── mammals/
│   │   └── dog.py
│   └── birds/
│       └── eagle.py
```

- `animals/`, `mammals/`, `birds/` 디렉토리에는 `__init__.py` 파일이 없습니다.
- 그러나 Python 3.3 이상에서는 다음과 같이 정상적으로 import할 수 있습니다.

```python
from animals.mammals import dog
from animals.birds import eagle
```

**주의할 점**

- implicit namespace package는 초기화 코드를 삽입할 수 없습니다.
- 패키지별로 초기 설정을 하고 싶다면 여전히 `__init__.py` 파일을 만들어야 합니다.

## 4. `import` 문의 동작

`import` 문은 외부 module을 현재 스크립트로 가져오는 역할을 합니다. 동작 순서는 다음과 같습니다.

1. **module 이름을 해석**합니다.
2. **module을 찾기 위해 `sys.path` 목록을 순서대로 탐색**합니다.
3. **module 파일을 찾으면 로딩**합니다.
   - `.py` 파일, `.pyc` 파일, package 디렉토리 등 다양한 형태를 지원합니다.
4. **module을 메모리에 로딩한 뒤, `sys.modules`에 등록**합니다.
   - 이미 `sys.modules`에 있다면 다시 로딩하지 않고 캐시된 객체를 사용합니다.

### sys.modules 동작

- `sys.modules`는 **Python 인터프리터 전체에 대해 하나만 존재하는 전역(global) 딕셔너리**입니다.
- import된 **각 module(.py 파일)** 이 독립적으로 `sys.modules`에 등록됩니다.
- package 단위로 묶어서 관리하지 않고, **package도 단순히 하나의 module로서 등록**합니다.

**예시**

| Key                  | Value (module 객체) |
|----------------------|---------------------|
| `package`            | `<module 'package' ...>` |
| `package.module_a`   | `<module 'package.module_a' ...>` |
| `package.module_b`   | `<module 'package.module_b' ...>` |

- `package`, `package.module_a`, `package.module_b`가 각각 독립적으로 등록됩니다.
- 동일한 module을 여러 번 import해도 항상 같은 객체(instance)를 사용합니다.

```python
import package.module_a
import package.module_a

print(id(package.module_a))  # 항상 같은 id
```

## 5. `from ~ import ~` 문의 동작

`from` 문은 module의 특정 객체를 직접 가져옵니다. 동작 순서는 다음과 같습니다.

1. `import` 문처럼 **module을 찾고 로딩**합니다.
2. module이 로딩된 후, **module 내에서 지정한 이름을 찾아 현재 네임스페이스로 가져옵니다**.

**예시**

```python
from math import sqrt

# 현재 네임스페이스에 'sqrt'가 추가되었는지 확인
print('sqrt' in globals())  # True
print(globals()['sqrt'])    # <built-in function sqrt>

# 가져온 sqrt를 실제로 사용
print(sqrt(9))  # 3.0
```

- 먼저 `import math`를 수행합니다.
- 그 후 `math` module 안에서 `sqrt` 이름을 찾아 **현재 네임스페이스**에 등록합니다.
- 따라서 이후 코드에서는 `sqrt(9)`처럼 `math.sqrt`를 다시 명시할 필요 없이 바로 사용할 수 있습니다.

## 6. import할 module의 경로

이 문서에서는 다음과 같은 예시 프로젝트 구조를 기준으로 설명합니다.

```
/home/user/project/
├── main.py
├── app.py
├── utils/
│   ├── __init__.py
│   ├── math_utils.py
│   └── task_utils.py
```

Python은 module을 import할 때 `sys.path` 목록을 순서대로 탐색하여 module을 찾습니다.

`sys.path` 구성 순서:

1. **python 명령어로 실행한 진입점 스크립트가 포함된 디렉토리의 절대경로**
   - python main.py로 실행할 때, 진입점 스크립트(main.py)가 포함된 디렉토리의 절대경로를 sys.path[0]에 추가합니다.
   - 예를 들어 현재 작업 디렉토리가 `/home/user/project`이고, `python main.py`로 실행하면 `sys.path[0]`은 `/home/user/project`가 됩니다.
2. **`PYTHONPATH` 환경 변수에 설정된 디렉토리들**
3. **표준 라이브러리 디렉토리**
4. **site-packages 디렉토리**

### 예시 (`sys.path` 출력 결과 예시)

```python
import sys
for path in sys.path:
    print(path)
```

출력 예시:

```
/home/user/project
/usr/local/lib/python3.11
/usr/local/lib/python3.11/site-packages
```

## Reference

- [Python 공식 문서 - The import system](https://docs.python.org/3/reference/import.html)
- [Python 공식 문서 - sys.path 설명](https://docs.python.org/3/library/sys.html#sys.path)
- [Python 공식 문서 - PEP 420 (Implicit Namespace Packages)](https://peps.python.org/pep-0420/)
- [Real Python - Python Modules and Packages – An Introduction](https://realpython.com/python-modules-packages/)
