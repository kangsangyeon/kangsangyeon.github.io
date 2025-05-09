---
layout: post
title: "Python 패키지 관리의 진화: Poetry 도입 배경과 사용법"
date: 2025-05-10 02:00:00 +0900
categories: [python]
tags: [poetry, python, dependency-management, 패키지관리]
---

Python 생태계에서 패키지 설정 관리란, 프로젝트의 의존성과 배포 관련 정보를 정의하고 유지하는 일련의 작업을 의미합니다. 여기에는 외부 패키지 버전 지정, 가상 환경 관리, 빌드와 배포 설정, 메타데이터 작성 등이 포함됩니다.

Poetry가 등장하기 이전에는 `pip`, `virtualenv`, `requirements.txt`, `setup.py` 등을 함께 사용했습니다. 각각의 도구는 용도가 달라, 패키지 설치, 가상 환경 관리, 의존성 고정, 배포 설정 등 여러 작업을 따로 처리해야 했습니다. 설정 파일은 분산되고, 도구마다 방식이 달라 혼란이 많았습니다. 표준이 없다 보니 각 프로젝트마다 방식이 달랐고, 이를 해결하려는 다양한 서드파티 도구들이 오히려 생태계를 더 복잡하게 만들기도 했습니다.

이러한 문제를 해결하고자 등장한 도구가 `Poetry`입니다. 이 글에서는 기존 방식의 한계와 Poetry가 이를 어떻게 극복하는지, 다른 유사 도구들과의 비교, 그리고 Poetry의 실사용 방법까지 순서대로 정리합니다.

## 기존 Python 패키지 관리 방식의 한계

Python에서 패키지를 관리할 때 전통적으로 사용되던 방식은 다음과 같습니다:

- 의존성 관리: `pip`, `requirements.txt`
- 가상 환경: `virtualenv`, `venv`
- 패키지 배포 설정: `setup.py`, `setup.cfg`

이러한 조합을 사용하는 과정에서 다음과 같은 불편함이 자주 발생합니다:

1. **의존성 불일치 및 충돌**
   `requirements.txt`에 명시된 패키지 버전은 하위 의존성까지 통제하지 않으며, 프로젝트 간 환경을 일관되게 유지하기 어렵습니다.

2. **가상 환경 관리의 번거로움**
   `venv`는 별도 디렉토리에서 생성되며, 수동으로 활성화하거나 `.gitignore`를 따로 구성해야 합니다.

3. **중복된 설정 파일 관리**
   `setup.py`, `requirements.txt`, `MANIFEST.in` 등 설정 정보가 여러 파일에 흩어져 있어 유지 보수가 어렵습니다.

4. **배포 프로세스의 복잡성**
   실제 PyPI에 패키지를 올리려면, 빌드 도구 설정, `twine`을 통한 업로드 등을 별도로 처리해야 합니다.

## Poetry 소개

1. **단일 구성 파일: `pyproject.toml`**
   의존성, 패키징, 메타데이터 모두를 한 파일에 통합하여 관리합니다. 또한, `pyproject.toml`은 [PEP 518](https://peps.python.org/pep-0518/)에서 정의된 Python 표준 빌드 시스템 설정 파일로, Poetry는 이를 기반으로 빌드 시스템과 의존성 관리를 통합합니다.

2. **가상 환경 자동 생성 및 관리**
   프로젝트 루트 기준으로 독립적인 가상 환경을 생성하고, 내부적으로 `venv`를 이용하되 CLI에서 자동으로 관리합니다.

3. **정확한 의존성 잠금: `poetry.lock`**
   `poetry.lock` 파일은 실제 머신에 설치되는 패키지 버전을 고정합니다. 이를 통해 팀원의 로컬 개발 환경과 실제 운영 환경에서 발생할 수 있는 패키지 버전 차이로 인한 오류나 버그를 예방할 수 있습니다. 이 파일은 `poetry add`, `poetry update` 등의 명령을 실행할 때 자동으로 생성되거나 갱신됩니다. 사용자가 수동으로 작성할 필요가 없으며, `pyproject.toml`을 기반으로 전체 의존성 트리를 해석한 결과가 이 파일에 저장됩니다.

4. **빌드 및 배포까지 포괄하는 CLI**
   Poetry는 `build`, `publish`, `version` 명령을 통해 패키지의 배포 과정을 자동화합니다.
   - `build`: 프로젝트를 PyPI 업로드에 적합한 `.tar.gz`, `.whl` 형식의 아카이브로 빌드합니다.
   - `publish`: 빌드된 패키지를 Python 패키지 저장소(Python Package Index, PyPI)나 기타 지정한 저장소에 업로드합니다.
   - `version`: `pyproject.toml`의 버전 번호를 자동으로 증가시키며, 버전 관리 전략(예: 패치, 마이너, 메이저)에 따라 조절할 수 있습니다.

5. **간결하고 직관적인 CLI**
   `poetry add`, `poetry install`, `poetry update` 등의 명령으로 대부분의 작업을 수행할 수 있습니다.

## Poetry 설치 방법

다음 명령어 중 하나를 사용하여 Poetry를 설치할 수 있습니다.

```bash
# pipx를 사용하여 설치 (권장)
pipx install poetry

# curl을 사용하여 설치 (Linux/macOS)
curl -sSL https://install.python-poetry.org | python3 -

# PowerShell을 사용하여 설치 (Windows)
(Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | py -
```

설치 후, `poetry --version` 명령어로 정상적으로 설치되었는지 확인할 수 있습니다.

## Poetry 사용 방법

### 프로젝트 생성

Poetry는 기존 프로젝트에 적용하거나 새로운 프로젝트의 골격을 생성할 수 있는 명령어를 제공합니다. 다음은 자주 사용하는 옵션입니다:

- `--name`: 프로젝트 이름을 지정합니다. (예: `poetry new --name example_project`)
- `--src`: `src/` 디렉토리 기반 프로젝트로 생성합니다. (예: `poetry new --src my_project`)

```bash
# 기존 프로젝트에 Poetry를 초기화합니다.
poetry init

# 새 프로젝트를 생성합니다.
poetry new my_project
```

### 의존성 관리

Poetry는 의존성 관리를 자동화하며, 개발용과 배포용 의존성을 명확히 구분해 추가할 수 있도록 합니다. 아래 옵션들은 모두 `poetry add` 명령어에서 사용되는 대표적인 옵션들입니다:

- `--dev`: 개발 의존성으로 추가합니다. (예: `poetry add --dev pytest`)
  테스트, 코드 정적 분석, 포매터 등 런타임에 사용되지 않는 도구를 분리해 관리할 수 있습니다.

- `--optional`: 선택적(optional) 의존성으로 추가합니다. (예: `poetry add some-package --optional`)
  꼭 필요한 것은 아니지만, 일부 환경에서만 필요한 패키지를 명시할 때 사용합니다.
  사용자는 `extras` 옵션을 통해 선택적으로 설치할 수 있습니다.

- `--group`: 특정 의존성 그룹에 추가합니다. (예: `poetry add some-lib --group test`)
  개발, 테스트, 문서화 등 목적별로 의존성을 논리적으로 구분해 관리할 수 있으며, `poetry install --with test` 와 같이 필요한 그룹만 선택해 설치할 수 있습니다.

```bash
# 의존성 추가
# poetry add 명령어는 외부 패키지를 프로젝트에 추가하는 데 사용됩니다.
# 기본적으로 [tool.poetry.dependencies]에 기록되며,
# --dev 옵션을 사용하면 [tool.poetry.dev-dependencies]에 기록됩니다.
poetry add requests
poetry add --dev black

# 의존성 설치
# poetry.lock 파일에 명시된 의존성 버전을 기준으로 실제 패키지를 설치합니다.
# --no-root: 현재 프로젝트를 설치하지 않고 의존성만 설치합니다.
# --only <group>: 특정 그룹 의존성만 설치합니다. (예: --only dev)
# --with <group>: 기본 그룹 외에 추가 그룹까지 함께 설치합니다. (예: --with test)
poetry install

# 잠금 파일 갱신
# poetry.lock 파일을 최신 버전으로 갱신하고 다시 잠급니다.
# --dry-run: 실제 변경 없이 어떤 패키지가 변경될지를 시뮬레이션합니다.
# --lock: 실제 패키지를 설치하지 않고 poetry.lock 파일만 갱신합니다.
poetry update
```

### 가상 환경

Poetry는 프로젝트별로 자동으로 가상 환경을 생성하고 이를 CLI 명령어로 쉽게 확인하거나 진입할 수 있도록 지원합니다.

```bash
# 현재 가상 환경 정보를 확인합니다.
poetry env info

# 가상 환경에 진입합니다.
poetry shell

# main.py를 Poetry가 생성한 프로젝트 가상 환경 내에서 실행합니다.
poetry run python main.py
```

### 패키지 빌드 및 배포

Poetry는 패키지를 PyPI에 배포할 수 있도록 빌드와 업로드 과정을 모두 CLI로 제공합니다. 주요 옵션은 다음과 같습니다:

- `poetry build -f wheel`: `.whl` 포맷만 빌드합니다.
- `poetry publish --dry-run`: 실제 업로드 없이 테스트 실행합니다.
- `poetry publish -r <repository>`: 기본이 아닌 다른 저장소로 업로드합니다.

Poetry에서는 `version` 명령어를 통해 `pyproject.toml` 파일 내의 버전 정보를 쉽게 갱신할 수 있습니다. 이 명령어는 주로 릴리스를 준비할 때 사용되며, 다음과 같이 동작합니다:

- `poetry version patch`: 패치 버전(예: 0.1.0 → 0.1.1)을 증가시킵니다.
- `poetry version minor`: 마이너 버전(예: 0.1.0 → 0.2.0)을 증가시킵니다.
- `poetry version major`: 메이저 버전(예: 0.1.0 → 1.0.0)을 증가시킵니다.

버전 변경은 `pyproject.toml` 파일의 `[tool.poetry]` 섹션 내 `version` 필드에 직접 반영됩니다.

```bash
# 패키지를 빌드합니다.
poetry build

# PyPI에 업로드합니다.
poetry publish --username=__token__ --password=YOUR_TOKEN
```

### `pyproject.toml` 구성 예시

```toml
[tool.poetry]
name = "my_project"
version = "0.1.0"
description = "Poetry example project"
authors = ["Your Name <you@example.com>"]

[tool.poetry.dependencies]
python = "^3.10"
requests = "^2.31.0"

[tool.poetry.dev-dependencies]
black = "^24.3.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

## 정리

Poetry는 Python의 기존 패키지 관리 생태계가 가진 여러 단점을 효과적으로 개선하는 현대적인 도구입니다. 의존성 관리, 가상 환경, 패키징, 배포를 하나의 워크플로우로 통합하여 생산성을 크게 높여줍니다. 특히 팀 프로젝트나 배포가 필요한 프로젝트에서 그 진가를 발휘합니다. 복잡한 구성을 단순화하고, 유지 보수를 쉽게 하고자 한다면 Poetry는 매우 강력한 선택지입니다.

## Reference

- [Poetry 공식 문서](https://python-poetry.org/docs/)
- [PEP 518 – pyproject.toml](https://peps.python.org/pep-0518/)
- [Comparing Python Packaging Tools](https://packaging.python.org/en/latest/)
