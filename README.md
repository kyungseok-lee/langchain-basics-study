# LangChain 기본기 학습 노트

## 📚 강의 정보
- **강의명**: 한시간으로 끝내는 LangChain 기본기

## 📖 학습 목차
### LLM을 활용해서 답변을 생성하는 방법

ollama 홈페이지에서 app 다운로드 또는 brew로 설치
```bash
brew install ollama
ollama pull gemma3:1b
ollama serve
```

ollama 사용법 참고
```bash
Usage:
  ollama [flags]
  ollama [command]

Available Commands:
  serve       Start ollama
  create      Create a model
  show        Show information for a model
  run         Run a model
  stop        Stop a running model
  pull        Pull a model from a registry
  push        Push a model to a registry
  list        List models
  ps          List running models
  cp          Copy a model
  rm          Remove a model
  help        Help about any command

Flags:
  -h, --help      help for ollama
  -v, --version   Show version information

Use "ollama [command] --help" for more information about a command.
```

#### python 가상 환경 설정
강의에선 pyenv를 사용했으나 uv로 대체함
```bash
uv venv --python 3.11.9 .venv
source .venv/bin/activate
uv pip install --upgrade pip setuptools wheel

python -V                     # Python 3.11.9
python -m pip --version
```

#### 1. Ollama를 활용한 로컬 LLM 사용

```python
# langchain-ollama 패키지 설치
%pip install -q langchain-ollama

# ChatOllama을 사용한 로컬 모델 호출
from langchain_ollama import ChatOllama

llm = ChatOllama(model="gemma3:1b")
response = llm.invoke("너는 누구냐?")

# 응답 예시:
# AIMessage(content='저는 Google DeepMind에서 훈련한 대규모 언어 모델입니다.', 
#           additional_kwargs={}, 
#           response_metadata={'model': 'gemma3:1b', 'created_at': '2025-08-30T22:03:57.432788Z', 'done': True, 'done_reason': 'stop', 'total_duration': 237999125, 'load_duration': 91485041, 'prompt_eval_count': 14, 'prompt_eval_duration': 31124917, 'eval_count': 16, 'eval_duration': 114957250, 'model_name': 'gemma3:1b'}, 
#           id='run--669fd310-5541-4378-838a-60c3e57033e6-0', 
#           usage_metadata={'input_tokens': 14, 'output_tokens': 16, 'total_tokens': 30})
```

#### 2. OpenAI API를 활용한 클라우드 LLM 사용

```python
# 필요한 패키지 설치
%pip install -q langchain-openai python-dotenv

# 환경변수 로드
from dotenv import load_dotenv
load_dotenv()

# ChatOpenAI을 사용한 OpenAI 모델 호출
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")  # OPENAI_API_KEY 환경변수 필요
response = llm.invoke("너는 누구냐?")

# 응답 예시:
# AIMessage(content='저는 OpenAI에서 개발한 인공지능 언어 모델입니다. 질문에 답하거나 정보를 제공하는 데 도움을 드릴 수 있습니다. 무엇을 도와드릴까요?', 
#           additional_kwargs={'refusal': None}, 
#           response_metadata={'token_usage': {'completion_tokens': 38, 'prompt_tokens': 12, 'total_tokens': 50, 'completion_tokens_details': {'accepted_prediction_tokens': 0, 'audio_tokens': 0, 'reasoning_tokens': 0, 'rejected_prediction_tokens': 0}, 'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens': 0}}, 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_560af6e559', 'id': 'chatcmpl-CANy3k9BvSD6ik1TSsYgfSnCx78oB', 'service_tier': 'default', 'finish_reason': 'stop', 'logprobs': None}, 
#           id='run--9e87536c-a51a-490b-8fd9-36efe9c51f3f-0', 
#           usage_metadata={'input_tokens': 12, 'output_tokens': 38, 'total_tokens': 50, 'input_token_details': {'audio': 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}})
```

#### 3. Azure OpenAI 사용 (주의사항)

```python
# Azure OpenAI 사용 시 올바른 설정 필요
from langchain_openai import AzureOpenAI

# 올바른 설정 예시:
llm = AzureOpenAI(
    azure_deployment="your-deployment-name",  # Azure 배포 이름
    openai_api_version="2024-02-15-preview",  # API 버전
    azure_endpoint="https://your-resource.openai.azure.com/"  # Azure 엔드포인트
)

# 잘못된 설정으로 인한 오류:
# APIConnectionError: Connection error.
# UnsupportedProtocol: Request URL is missing an 'http://' or 'https://' protocol.
```

#### 4. 주요 차이점 및 특징

| 구분 | Ollama (로컬) | OpenAI (클라우드) | Azure OpenAI |
|------|----------------|-------------------|--------------|
| **설치** | 로컬 설치 필요 | API 키만 필요 | Azure 리소스 필요 |
| **비용** | 무료 | 토큰당 과금 | Azure 사용량 과금 |
| **성능** | 하드웨어 의존 | 고성능 | 고성능 |
| **프라이버시** | 완전 로컬 | OpenAI 서버 | Azure 서버 |
| **모델** | 제한적 | 다양한 모델 | OpenAI 모델 |

#### 5. 환경 설정 체크리스트

- [ ] Python 3.11.9 이상 설치 (uv 사용 권장)
- [ ] 가상환경 생성 및 활성화 (`uv venv --python 3.11.9 .venv`)
- [ ] 필요한 패키지 설치 (langchain-ollama, langchain-openai, python-dotenv)
- [ ] 환경변수 설정 (.env 파일)
- [ ] Ollama 서비스 실행 (로컬 모델 사용 시)
- [ ] API 키 설정 (클라우드 모델 사용 시)

### 랭체인 스타일로 프롬프트 작성하는 방법

#### 1. 기본 개념 및 구조

LangChain은 LLM과의 상호작용을 체계화하고 재사용 가능한 컴포넌트로 만드는 프레임워크입니다.

**핵심 구성 요소:**
- **LLM (Large Language Model)**: 실제 AI 모델 (Ollama, OpenAI, Azure 등)
- **Prompt**: LLM에게 전달하는 입력 텍스트
- **Chain**: 여러 컴포넌트를 연결하여 복잡한 워크플로우 구성
- **Output Parser**: LLM의 응답을 구조화된 형태로 변환

#### 2. 기본 LLM 호출 패턴

```python
# 1. 모델 초기화
from langchain_ollama import ChatOllama
from langchain_openai import ChatOpenAI

# 로컬 모델 (Ollama)
llm_local = ChatOllama(model="gemma3:1b")

# 클라우드 모델 (OpenAI)
llm_cloud = ChatOpenAI(model="gpt-4o-mini")

# 2. 기본 호출
response = llm.invoke("너는 누구냐?")
print(response.content)  # 응답 내용만 추출
```

#### 3. 프롬프트 템플릿 활용

```python
from langchain_core.prompts import ChatPromptTemplate

# 프롬프트 템플릿 생성
prompt = ChatPromptTemplate.from_template(
    "당신은 {role}입니다. {task}에 대해 {style}로 답변해주세요."
)

# 템플릿에 값 채우기
formatted_prompt = prompt.format_messages(
    role="전문 프로그래머",
    task="Python 가상환경 설정",
    style="단계별로 자세하게"
)

# LLM에 전달
response = llm.invoke(formatted_prompt)
```

#### 4. 체인(Chain) 구성

```python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough

# 간단한 체인 구성
chain = (
    {"question": RunnablePassthrough()} 
    | prompt 
    | llm 
    | StrOutputParser()
)

# 체인 실행
result = chain.invoke("Python 가상환경을 설정하는 방법을 알려주세요")
```

#### 5. 출력 파싱 및 구조화

```python
from langchain_core.output_parsers import JsonOutputParser
from langchain_core.pydantic_v1 import BaseModel, Field
from typing import List

# 출력 스키마 정의
class CodeExample(BaseModel):
    language: str = Field(description="프로그래밍 언어")
    code: str = Field(description="실제 코드")
    explanation: str = Field(description="코드 설명")

class CodeResponse(BaseModel):
    examples: List[CodeExample] = Field(description="코드 예시들")
    summary: str = Field(description="전체 요약")

# JSON 파서 설정
parser = JsonOutputParser(pydantic_object=CodeResponse)

# 체인에 파서 추가
chain_with_parser = (
    {"question": RunnablePassthrough()} 
    | prompt 
    | llm 
    | parser
)

# 구조화된 응답 받기
structured_result = chain_with_parser.invoke("Python 가상환경 설정 코드 예시를 보여주세요")
```

#### 6. 실무 활용 패턴

**A. 단계별 작업 분해**
```python
# 1단계: 요구사항 분석
analysis_prompt = ChatPromptTemplate.from_template(
    "다음 요구사항을 분석하여 필요한 단계들을 나열해주세요: {requirement}"
)

# 2단계: 각 단계별 상세 설명
detail_prompt = ChatPromptTemplate.from_template(
    "다음 단계에 대해 자세한 설명과 코드 예시를 제공해주세요: {step}"
)

# 3단계: 최종 검증
validation_prompt = ChatPromptTemplate.from_template(
    "다음 해결방안이 올바른지 검증하고 개선점을 제시해주세요: {solution}"
)
```

**B. 에러 처리 및 재시도**
```python
from langchain_core.runnables import RunnableRetry

# 재시도 로직이 포함된 체인
robust_chain = (
    {"question": RunnablePassthrough()} 
    | prompt 
    | llm 
    | RunnableRetry(
        stop_after_attempt=3,
        wait=1
    )
    | parser
)
```

#### 7. 성능 최적화 팁

1. **배치 처리**: 여러 요청을 한 번에 처리
2. **캐싱**: 동일한 프롬프트 결과 재사용
3. **스트리밍**: 긴 응답을 실시간으로 받기
4. **프롬프트 압축**: 불필요한 정보 제거하여 토큰 절약

#### 8. 디버깅 및 모니터링

```python
from langchain_core.callbacks import ConsoleCallbackHandler

# 콜백을 통한 실행 과정 모니터링
with ConsoleCallbackHandler() as handler:
    result = chain.invoke("테스트 질문", config={"callbacks": [handler]})
```

#### 9. 환경별 설정 관리

```python
import os
from dotenv import load_dotenv

load_dotenv()

# 환경별 모델 선택
if os.getenv("ENVIRONMENT") == "production":
    llm = ChatOpenAI(model="gpt-4", temperature=0.1)
else:
    llm = ChatOllama(model="gemma3:1b", temperature=0.7)
```

#### 10. 체크리스트

- [ ] 적절한 LLM 모델 선택 (로컬 vs 클라우드)
- [ ] 프롬프트 템플릿 설계 및 검증
- [ ] 출력 파서 설정 및 테스트
- [ ] 에러 처리 및 재시도 로직 구현
- [ ] 성능 모니터링 및 최적화
- [ ] 환경별 설정 분리
- [ ] 보안 및 API 키 관리

### 답변의 형식을 컨트롤하는 방법

### LCEL을 활용한 랭"체인" 생성하는 방법

### 지금까지 배운 내용 총정리 (feat. 프롬프트 꿀팁 살짝)

### 랭체인을 활용해서 할 수 있는 것들
