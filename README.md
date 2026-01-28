# Spring AI ZhipuAI Skill

Spring AI 프로젝트에 ZhipuAI와 Swagger를 설정하는 에이전트 스킬입니다.

## 설치

```bash
# Antigravity 에이전트용
npx skills add hwiesung/spring-ai-zhipuai --agent antigravity

# 기본 설치 (에이전트 자동 감지)
npx skills add hwiesung/spring-ai-zhipuai
```

## 기능

- Spring AI ZhipuAI 의존성 설정
- Swagger (SpringDoc OpenAPI) 설정
- Kotlin/Spring Boot 최적화 설정

## 사전 요구사항

- Kotlin 기반 Spring Boot 프로젝트
- Gradle (Kotlin DSL 권장)
- JDK 21 이상
- ZhipuAI API Key

## 호환성

| 구성요소          | 버전  |
| ----------------- | ----- |
| Spring Boot       | 3.3.x |
| Spring AI         | 1.1.2 |
| SpringDoc OpenAPI | 2.5.0 |
| Gradle            | 8.12+ |
| JDK               | 21    |

## 사용 예시

### 기본 사용법

AI 에이전트 (Gemini CLI, Claude 등)에게 아래와 같이 요청하세요:

```
내 Spring Boot 프로젝트에 ZhipuAI와 Swagger를 설정해줘
```

### 구체적인 요청 예시

```
# ZhipuAI 연동 설정
이 프로젝트에 spring-ai-zhipuai 스킬을 적용해서 ZhipuAI 모델을 연동해줘

# 전체 설정
ZhipuAI API 연동과 Swagger UI 테스트 환경을 한번에 설정해줘
```

### 설정 완료 후 확인

```bash
# 애플리케이션 실행
ZHIPUAI_API_KEY=your-api-key ./gradlew bootRun

# Swagger UI 접속
open http://localhost:8080/swagger-ui.html
```

### API 테스트

```bash
curl -X POST http://localhost:8080/api/client/list/parse \
  -H "Content-Type: application/json" \
  -d '{"question": "5가지 프로그래밍 언어를 나열해주세요"}'
```

## License

MIT
