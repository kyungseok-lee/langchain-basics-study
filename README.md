# LangChain 기본기 학습 노트

## 📚 강의 정보
- **강의명**: 한시간으로 끝내는 LangChain 기본기

## 📖 학습 목차
### LLM을 활용해서 답변을 생성하는 방법
ollama 홈페이지에서 app 다운로드 또는 brew로 설치

```
brew install ollama

ollama pull gemma3:1b
ollama serve
```

ollama 사용법 참고
```
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


### 랭체인 스타일로 프롬프트 작성하는 방법

### 답변의 형식을 컨트롤하는 방법

### LCEL을 활용한 랭"체인" 생성하는 방법

### 지금까지 배운 내용 총정리 (feat. 프롬프트 꿀팁 살짝)

### 랭체인을 활용해서 할 수 있는 것들
