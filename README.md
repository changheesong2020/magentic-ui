<div align="center">

<img src="frontend/src/assets/logo.svg" alt="Magentic-UI Logo" height="100">

# Magentic-UI

_사용자가 제어권을 유지하면서 웹 작업을 자동화하세요_

[![image](https://img.shields.io/pypi/v/magentic_ui.svg)](https://pypi.python.org/pypi/uv)
[![image](https://img.shields.io/pypi/l/magentic_ui.svg)](https://pypi.python.org/pypi/magentic_ui)
![Python Versions](https://img.shields.io/badge/python-3.10%20%7C%203.11%20%7C%203.12%20%7C%203.13-blue)

</div>

<!-- <img src="./docs/landing.gif" loop=infinite> -->

<!-- 스크린샷 -->
<p align="center">
  <img src="docs/magui-landing.png" alt="Magentic-UI Landing" width="45%" style="margin:10px;">
  <img src="docs/magui-coplanning.png" alt="Co-Planning UI" width="45%" style="margin:10px;">
  <img src="docs/magui-cotasking.png" alt="Co-Tasking UI" width="45%" style="margin:10px;">
  <img src="docs/magui-actionguard.png" alt="Action Guard UI" width="45%" style="margin:10px;">
</p>

Magentic-UI는 웹을 탐색하고 동작을 수행하며 코드를 생성·실행하고 파일을 분석할 수 있는 다중 에이전트 시스템 기반 **연구용 프로토타입**입니다. 검색 엔진에 나타나지 않는 웹사이트 탐색이나 웹 상호작용과 코드 실행이 모두 필요한 작업 등에 유용합니다.

Magentic-UI는 투명하고 사용자가 제어할 수 있는 인터페이스를 제공하여 사람이 직접 개입하기 쉽습니다. [AutoGen](https://github.com/microsoft/autogen)을 기반으로 하며, 사람-에이전트 상호작용 연구와 웹 에이전트 실험을 위한 플랫폼을 제공합니다. 주요 기능은 다음과 같습니다:

- 🧑‍🤝‍🧑 **공동 계획(Co-Planning)**: 채팅과 계획 편집기를 사용하여 단계별 계획을 함께 만들고 승인합니다.
- 🤝 **공동 실행(Co-Tasking)**: 웹 브라우저나 채팅을 통해 작업 실행을 중단하거나 안내합니다. 필요한 경우 Magentic-UI가 추가 정보를 요청할 수 있습니다.
- 🛡️ **행동 보호(Action Guards)**: 민감한 행동은 명시적인 사용자 승인을 받아서만 실행됩니다.
- 🧠 **계획 학습 및 검색**: 이전 실행 결과를 학습하여 향후 자동화를 개선하고 계획 갤러리에 저장합니다. 저장된 계획을 자동 또는 수동으로 불러올 수 있습니다.
- 🔀 **병렬 작업 실행**: 여러 작업을 동시에 실행할 수 있으며, 세션 상태 표시기로 Magentic-UI가 입력을 기다리는지, 작업이 완료되었는지 확인할 수 있습니다.

시작 방법은 다음과 같습니다. 설치와 실행이 간단하며 원한다면 소스에서 빌드할 수도 있습니다.

> **참고**: 설치 전에 [사전 요구 사항](#prerequisites)을 읽어 주세요. Magentic-UI는 Docker가 필요하며 Windows 사용자는 WSL2가 필요합니다. Mac이나 Linux 사용자는 WSL2를 건너뛰어도 됩니다.

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install magentic-ui
# export OPENAI_API_KEY=<YOUR API KEY>
magentic ui --port 8081
```

Azure 모델이나 Ollama를 사용하려면 선택적 종속 항목을 설치합니다:
```bash
# Azure 사용 시
pip install magentic-ui[azure]
# Ollama 사용 시
pip install magentic-ui[ollama]
```


## 목차

- [Magentic-UI 소개](#magentic-ui-소개)
- [동작 원리](#동작-원리)
- [사용 방법](#사용-방법)
- [사용자 정의 클라이언트 설정](#사용자-정의-클라이언트-설정)
- [소스에서 빌드하기](#소스에서-magentic-ui-빌드하기)
- [기여 방법](#기여-방법)
- [법적 고지](#법적-고지)

## Magentic-UI 소개
Magentic-UI는 강력한 다중 에이전트 시스템과 상호 작용할 수 있는 웹 애플리케이션으로, 실시간 웹을 탐색하고 동작을 수행하며 코드를 생성·실행하고 파일을 분석할 수 있습니다.

<p align="center">
  <img src="./docs/magenticui_running.png" alt="Magentic-UI" height="400">
</p>

위 스크린샷은 Magentic-UI의 인터페이스를 보여 줍니다. 왼쪽 패널은 세션 탐색기로 새 세션을 만들고 전환하며 상태 표시기(🔴 입력 필요, ✅ 완료, ↺ 진행 중)를 확인할 수 있습니다.

오른쪽 패널은 선택한 세션 화면으로, 질문 입력과 텍스트·이미지 첨부, 진행 상황 확인, 에이전트와의 상호 작용을 할 수 있습니다. 화면은 두 부분으로 나뉘어 왼쪽은 계획·진행 상황·행동 승인 요청을, 오른쪽은 브라우저 뷰를 보여 주며 상단의 진행률 막대가 작업 진행도를 표시합니다.

### 동작 원리

<p align="center">
  <img src="./docs/magenticui.jpg" alt="Magentic-UI" height="400">
</p>

Magentic-UI의 내부 시스템은 AutoGen의 Magentic-One 시스템에서 발전한 전문화된 에이전트 팀으로 구성됩니다.

- **Orchestrator**: LLM 기반 주요 에이전트로, 사용자와 공동으로 계획을 세우고 피드백을 요청할 시점을 결정하며 나머지 에이전트에게 하위 작업을 분담합니다.
- **WebSurfer**: 웹 브라우저를 제어하는 LLM 에이전트로, Orchestrator의 요청에 따라 클릭, 입력, 스크롤 등의 동작을 여러 번 반복하며 요청을 수행합니다.
- **Coder**: Docker 코드 실행 컨테이너를 가진 LLM 에이전트로, Python과 셸 명령을 작성·실행하고 결과를 Orchestrator에게 전달합니다.
- **FileSurfer**: Docker 컨테이너와 파일 변환 도구를 갖춘 LLM 에이전트로, Magentic-UI가 관리하는 디렉터리에서 파일을 찾아 마크다운으로 변환하고 질문에 답변합니다.
- **UserProxy**: 사용자를 대표하는 에이전트로, Orchestrator가 다른 에이전트 대신 사용자에게 작업을 위임할 수 있습니다.

사용자는 텍스트 메시지를 입력하고 이미지를 첨부하여 Magentic-UI와 상호작용합니다. Magentic-UI는 자연어 단계별 계획을 생성하며, 사용자는 계획 편집기를 통해 단계를 추가·삭제·수정하거나 다시 생성할 수 있습니다. 계획을 수정하면 초기에는 시간이 들 수 있지만 실행 시 시간을 절약하고 성공률을 높일 수 있습니다.

계획은 Orchestrator에 저장되어 작업 실행에 사용됩니다. 각 단계마다 Orchestrator는 어떤 에이전트나 사용자가 수행할지 결정하고 요청을 보낸 뒤 응답을 기다립니다. 응답이 오면 단계 완료 여부를 판단하고, 완료되면 다음 단계로 진행합니다.

모든 단계가 완료되면 Orchestrator는 최종 답변을 생성하여 사용자에게 제공합니다. 실행 중 계획이 부적절하다고 판단되면 사용자 허가를 받아 새 계획을 세우고 다시 실행할 수 있습니다.

진행 상황은 모두 명확하게 표시되며, 사용자는 실행을 일시 중지하고 추가 요청이나 피드백을 보낼 수 있습니다. 또한 인터페이스에서 에이전트 행동(예: 버튼 클릭)에 대한 승인 여부를 설정할 수 있습니다.

## 사용 방법

### 사전 요구 사항

1. Magentic-UI는 정상 작동을 위해 Docker가 필요합니다. Windows나 Mac에서는 [Docker Desktop](https://www.docker.com/products/docker-desktop/)을 사용할 수 있습니다.
2. Windows에서 사용하려면 WSL2가 필요합니다. [설치 방법](https://docs.microsoft.com/en-us/windows/wsl/install)을 참고하고 Docker Desktop에서 WSL2를 사용하도록 설정하세요(Settings > Resources > WSL Integration). 자세한 내용은 [여기](https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-containers)에서 확인할 수 있습니다.
3. 환경 변수 `OPENAI_API_KEY`에 OpenAI API 키를 설정하거나 아래 [사용자 정의 클라이언트 설정](#사용자-정의-클라이언트-설정) 절을 참고하세요.
4. Python 3.10 이상이 필요합니다.

### PyPI 설치

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install magentic-ui
```

`uv` 사용 시:

```bash
uv venv --python=3.12 .venv
. .venv/bin/activate
uv pip install magentic-ui
```

### Magentic-UI 실행

```bash
magentic ui --port 8081
```

처음 실행 시 Docker 이미지를 빌드하느라 시간이 걸릴 수 있습니다. 다음 실행부터는 더 빠릅니다.

실행 후 <http://localhost:8081> 에 접속하면 UI를 사용할 수 있습니다.

### 사용자 정의 클라이언트 설정

다른 OpenAI 키를 사용하거나 Azure OpenAI/Ollama를 활용하려면 `config.yaml`을 만들어 아래 형식으로 설정하고 실행 시 경로를 지정합니다:

```bash
magentic ui --config path/to/config.yaml
```

OpenAI 예시:

```yaml
model_config: &client
  provider: autogen_ext.models.openai.OpenAIChatCompletionClient
  config:
    model: gpt-4o
    api_key: <YOUR API KEY>
    max_retries: 10
orchestrator_client: *client
coder_client: *client
web_surfer_client: *client
file_surfer_client: *client
action_guard_client: *client
```

Azure OpenAI 예시:

```yaml
model_config: &client
  provider: AzureOpenAIChatCompletionClient
  config:
    model: gpt-4o
    azure_endpoint: "<YOUR ENDPOINT>"
    azure_deployment: "<YOUR DEPLOYMENT>"
    api_version: "2024-10-21"
    azure_ad_token_provider:
      provider: autogen_ext.auth.azure.AzureTokenProvider
      config:
        provider_kind: DefaultAzureCredential
        scopes:
          - https://cognitiveservices.azure.com/.default
    max_retries: 10
orchestrator_client: *client
coder_client: *client
web_surfer_client: *client
file_surfer_client: *client
action_guard_client: *client
```

### 소스에서 Magentic-UI 빌드하기

1. 위 사전 요구 사항을 갖추고 Docker가 실행 중인지 확인합니다.
2. 저장소를 클론합니다:

```bash
git clone https://github.com/microsoft/magentic-ui.git
cd magentic-ui
```

SSH 사용 시:

```bash
git clone git@github.com:microsoft/magentic-ui.git
cd magentic-ui
```

3. `uv`로 의존성을 설치합니다:

```bash
uv venv --python=3.12 .venv
uv sync --all-extras
source .venv/bin/activate
```

4. 프런트엔드를 빌드합니다:

```bash
# nvm 설치 후 node 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
nvm install node

cd frontend
npm install -g gatsby-cli
npm install --global yarn
yarn install
yarn build
```

5. Magentic-UI 실행:

```bash
magentic ui --port 8081
```

### 소스에서 UI 실행

개발 중에는 프런트엔드를 개발 모드로 실행하여 변경 사항을 즉시 반영할 수 있습니다.

```bash
cd frontend
cp .env.default .env.development
npm run start
```

이후 `magentic ui --port 8081` 명령으로 UI를 실행하세요. 프런트엔드 개발 서버는 <http://localhost:8000>, 빌드된 버전은 <http://localhost:8081> 에서 확인할 수 있습니다.

## 기여 방법

이 프로젝트는 기여와 제안을 환영합니다. 대부분의 기여는 Contributor License Agreement(CLA)에 동의해야 합니다. 자세한 내용은 https://cla.opensource.microsoft.com 를 참조하세요.

PR을 제출하면 CLA 봇이 자동으로 확인하여 안내합니다. 이는 한 번만 진행하면 됩니다.

이 프로젝트는 [Microsoft 오픈 소스 행동 강령](https://opensource.microsoft.com/codeofconduct/)을 따릅니다. 궁금한 점은 [행동 강령 FAQ](https://opensource.microsoft.com/codeofconduct/faq/)를 확인하거나 [opencode@microsoft.com](mailto:opencode@microsoft.com)으로 문의하세요.

### 기여하는 방법

이슈를 확인하거나 PR 리뷰를 도와 기여할 수 있습니다. 일부 항목은 'open for contribution' 또는 'open for reviewing' 라벨이 붙어
있습니다.

<div align="center">

|            | 전체 링크                                                     | 커뮤니티의 도움이 특히 필요한 항목                                                         |
| ---------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| **Issues** | [모든 이슈](https://github.com/microsoft/magentic-ui/issues) | [기여 가능한 이슈](https://github.com/microsoft/magentic-ui/issues?q=is%3Aissue+is%3Aopen+label%3A%22open+for+contribution%22) |
| **PRs**    | [모든 PR](https://github.com/microsoft/magentic-ui/pulls)     | [검토가 필요한 PR](https://github.com/microsoft/magentic-ui/pulls?q=is%3Apr+is%3Aopen+label%3A%22open+for+reviewing%22)              |

</div>

새 기능을 추가하는 PR에는 새로운 테스트가 포함되어야 합니다. 기존 테스트는 `tests` 디렉터리에서 확인할 수 있습니다.

### 테스트 및 체크 실행

```sh
poe check
```

## 법적 고지

Microsoft 및 모든 기여자는 [MIT 라이선스](https://opensource.org/licenses/MIT)에 따라 이 리포지토리의 코드 사용 권한을 부여합니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.

Microsoft, Windows, Microsoft Azure 등 문서에서 언급된 Microsoft 제품과 서비스는 미국 및 기타 국가에서 Microsoft의 상표이거나 등록 상표일 수 있습니다. 이 프로젝트의 라이선스는 이러한 이름이나 로고, 상표 사용 권한을 부여하지 않습니다. Microsoft 상표 지침은 <http://go.microsoft.com/fwlink/?LinkID=254653> 에서 확인할 수 있습니다.

타사 상표나 로고 사용은 해당 제삼자의 정책을 따릅니다.

개인정보 보호 정보는 <https://go.microsoft.com/fwlink/?LinkId=521839> 에서 확인할 수 있습니다.

Microsoft 및 모든 기여자는 기타 모든 권리를 보유합니다. 이는 저작권, 특허, 상표에 대한 권리를 묵시적 또는 기타 방식으로 부여하지 않습니다.
