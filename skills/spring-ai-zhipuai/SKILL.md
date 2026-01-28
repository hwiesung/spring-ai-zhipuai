---
name: spring-ai-zhipuai
description: Spring AI 프로젝트에 ZhipuAI 설정, Swagger 적용 및 테스트 실행
---

# Spring AI ZhipuAI & Swagger 설정 스킬

이 스킬은 Spring AI 프로젝트를 ZhipuAI로 마이그레이션하고 Swagger를 설정하는 작업을 자동화합니다.

## 사전 요구사항

- Kotlin 기반 Spring Boot 프로젝트
- Gradle (Kotlin DSL 권장)
- JDK 21 이상
- ZhipuAI API Key ([ZhipuAI 플랫폼](https://open.bigmodel.cn/)에서 발급)

## 작업 단계

### 1. Gradle Wrapper 업데이트

```bash
# 프로젝트 디렉토리에서 실행
gradle wrapper --gradle-version=8.12
```

### 2. build.gradle.kts 수정

**JDK 21 설정:**

```kotlin
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(21)
    }
}
```

**Spring AI ZhipuAI 의존성 추가:**

```kotlin
dependencies {
    // Spring AI ZhipuAI (기존 Ollama/OpenAI 의존성 교체)
    implementation("org.springframework.ai:spring-ai-starter-model-zhipuai:1.1.2")

    // Swagger (SpringDoc OpenAPI) - Spring Boot 3.3.x 호환 버전
    implementation("org.springdoc:springdoc-openapi-starter-webmvc-ui:2.5.0")
}
```

**Kotlin JVM Target 설정:**

```kotlin
tasks.withType<KotlinCompile> {
    kotlinOptions {
        freeCompilerArgs = listOf("-Xjsr305=strict")
        jvmTarget = "21"
    }
}
```

### 3. application.yml 설정

```yaml
spring:
  ai:
    zhipuai:
      api-key: ${ZHIPUAI_API_KEY} # 또는 직접 입력
      chat:
        options:
          model: glm-4.7-flash # 또는 glm-4-air, glm-4.5, glm-4.6
          temperature: 0.7

# SpringDoc OpenAPI (Swagger)
springdoc:
  api-docs:
    path: /api-docs
  swagger-ui:
    path: /swagger-ui.html
    tags-sorter: alpha
    operations-sorter: alpha
```

### 4. Swagger 어노테이션 추가

**Model 클래스에 @Schema 추가:**

```kotlin
import io.swagger.v3.oas.annotations.media.Schema

@Schema(description = "AI 파싱 요청")
data class ParseRequest(
    @Schema(
        description = "AI에게 질문할 내용",
        example = "5가지 프로그래밍 언어를 나열해주세요",
        required = true
    )
    val question: String
)
```

**Controller에 @Tag, @Operation 추가:**

```kotlin
import io.swagger.v3.oas.annotations.Operation
import io.swagger.v3.oas.annotations.tags.Tag

@RestController
@RequestMapping("/api/example")
@Tag(name = "Example API", description = "예제 API 설명")
class ExampleController {

    @Operation(
        summary = "기능 요약",
        description = "상세 설명"
    )
    @PostMapping("/endpoint")
    fun example(@RequestBody request: ParseRequest): Map<String, Any> {
        // ...
    }
}
```

### 5. 빌드 및 테스트

```bash
# 빌드 테스트
./gradlew clean build -x test

# 단위 테스트 실행
./gradlew test

# 애플리케이션 실행
./gradlew bootRun
# 또는 환경변수로 API Key 전달
ZHIPUAI_API_KEY=your-api-key ./gradlew bootRun
```

### 6. 검증

- **Swagger UI**: http://localhost:8080/swagger-ui.html
- **API Docs (JSON)**: http://localhost:8080/api-docs

**HTTP 테스트:**

```bash
curl -X POST http://localhost:8080/api/client/list/parse \
  -H "Content-Type: application/json" \
  -d '{"question": "5가지 프로그래밍 언어를 나열해주세요"}'
```

## 버전 호환성

| 구성요소          | 권장 버전 |
| ----------------- | --------- |
| Spring Boot       | 3.3.x     |
| Spring AI         | 1.1.2     |
| SpringDoc OpenAPI | 2.5.0     |
| Gradle            | 8.12+     |
| JDK               | 21        |

## ZhipuAI 모델 옵션

| 모델명          | 설명                 |
| --------------- | -------------------- |
| `glm-4.7-flash` | 빠른 응답, 일반 용도 |
| `glm-4-air`     | 경량 모델            |
| `glm-4.5`       | 표준 성능            |
| `glm-4.6`       | 향상된 성능          |

## 트러블슈팅

### Port Already in Use

```bash
lsof -ti:8080 | xargs kill -9
```

### SpringDoc 호환성 오류

- Spring Boot 3.3.x에서는 `springdoc-openapi-starter-webmvc-ui:2.5.0` 사용
- 2.8.x 버전은 호환성 문제 발생

### Gradle Wrapper 누락

```bash
gradle wrapper --gradle-version=8.12
```
