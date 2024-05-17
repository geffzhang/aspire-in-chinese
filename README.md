# aspire-in-chinese
## 概述
这是一个使用.NET Aspire来编排Java、React、Python等开发语言及框架的示例。

## demo source 目录说明
| 目录名| 说明 | 备注 |
| --------- | ---- | --- |
| AspireJavaScript.React | react front-end sample | 在MS Sample中进行修改，直接运行。 |
| cmd | Windows批处理文件执行示例  | 直接运行 |
| demo.ApiService | webapi backend asp.net core | aspire starter application |
| demo.AppHost | aspire app host application | aspire starter application |
| demo.ServiceDefaults | aspire service defaults 实现项目 | aspire starter application |
| demo.Web | blazor interactive server front-end | aspire starter application |
| fastapi | python fast api 示例 | 使用Docker镜像创建 |
| sbweb | java, springboot 示例 | 使用Docker镜像创建 |

## AspireJavaScript.React
- 在上述文件夹中执行以下命令（使用以下命令通过npm安装React所需的库代码）
  ```
  npm install
  ```
- aspire app host에서 `services__apiservice__http__0` 通过环境变量传递后端的endpoint，请参考该目录下的.env文件

- 参考microsof的示例 [readme](https://github.com/dotnet/aspire-samples/tree/main/samples/AspireWithJavaScript/AspireJavaScript.React) 

参考资料
- [JavaScript OpenTelemetry Getting Started](https://opentelemetry.io/docs/languages/js/getting-started/)
- [.NET Aspire with Angular, React, and Vue](https://learn.microsoft.com/en-us/samples/dotnet/aspire-samples/aspire-angular-react-vue/)
- [Integrating Angular, React, and Vue with .NET Aspire](https://github.com/dotnet/aspire-samples/tree/main/samples/AspireWithJavaScript)

## cmd
- 可以在DOS窗口中执行命令而创建的示例
- 执行一个名为` test.bat`的文件，该文件接受时间（分钟）作为参数，并简单地等待

## aspire starter application
- demo.ApiService, demo.AppHost, demo.ServiceDefaults, demo.Web
- 默认创建的4个项目
- ApiService
  - 在React中，这个项目尝试获取天气数据时遇到了CORS（跨源资源共享）问题，因此需要添加相关的设置来解决此问题。
    ```
    builder.Services.AddCors();
    ```
    ```
    app.UseCors(static builder =>
      builder.AllowAnyMethod()
          .AllowAnyHeader()
          .AllowAnyOrigin());
    ```
  - 因为是示例，所以全部允许。
- App Host
  - 为了进行编排而添加的代码。
  - 为了在HTTP上运行，从预览版5开始在`launchSettings.json`中需要以下设置：
    ```
    "ASPIRE_ALLOW_UNSECURED_TRANSPORT": "true"
    ```

## fastapi
- 使用Python创建Web API框架FastAPI的Docker镜像的示例。
- 利用[fastapi 示例](https://fastapi.tiangolo.com/ko/deployment/docker/)
- 在上述文件夹中使用以下命令创建docker镜像。
  ``` 
  docker build -t aspire-fastapi:0.0.1 .
  ```
- 在Dockerfile中设置ENV很重要。
  ```
  ENV OTEL_EXPORTER_OTLP_PROTOCOL=grpc
  ENV OTEL_SERVICE_NAME=OTEL_SERVICE_NAME
  ENV OTEL_EXPORTER_OTLP_ENDPOINT=OTEL_EXPORTER_OTLP_ENDPOINT
  ENV OTEL_TRACES_EXPORTER="otlp"
  ENV OTEL_METRICS_EXPORTER="otlp"

  ENV OTEL_BLRP_SCHEDULE_DELAY=OTEL_BLRP_SCHEDULE_DELAY 
  ENV OTEL_BSP_SCHEDULE_DELAY=OTEL_BSP_SCHEDULE_DELAY
  ```
  - 目前只支持grpc协议。
  - 根据aspire的版本，设置值似乎会不断变化，因此在正式发布（GA）时需要确认。
  - [OpenTelemetry的Python手册。](https://opentelemetry.io/docs/languages/python/automatic/)

## sbweb
- 使用Spring Boot创建的Java示例
- `RollController.java`文件中有一个控制器
- 尽管没有处理，但为了从React前端连接，需要处理CORS
- [OpenTelemetry的Java手册](https://opentelemetry.io/docs/languages/java/automatic/)
- 在Dockerfile中设置ENV很重要，并且使用JVM的javaagent很方便
  ```
  ENV OTEL_EXPORTER_OTLP_PROTOCOL=grpc \
      OTEL_EXPORTER_OTLP_ENDPOINT=OTEL_EXPORTER_OTLP_ENDPOINT \
      OTEL_SERVICE_NAME=OTEL_SERVICE_NAME \
      OTEL_BLRP_SCHEDULE_DELAY=OTEL_BLRP_SCHEDULE_DELAY \
      OTEL_BSP_SCHEDULE_DELAY=OTEL_BSP_SCHEDULE_DELAY \
      OTEL_DOTNET_EXPERIMENTAL_OTLP_EMIT_EVENT_LOG_ATTRIBUTES=OTEL_DOTNET_EXPERIMENTAL_OTLP_EMIT_EVENT_LOG_ATTRIBUTES \
      OTEL_DOTNET_EXPERIMENTAL_OTLP_EMIT_EXCEPTION_LOG_ATTRIBUTES=OTEL_DOTNET_EXPERIMENTAL_OTLP_EMIT_EXCEPTION_LOG_ATTRIBUTES
  ```
- `opentelemetry-javaagent.jar` 下载文件并作为Java代理使用。

# 参考资料
- [.NET Aspire telemetry](https://learn.microsoft.com/en-us/dotnet/aspire/fundamentals/telemetry)
- [OpenTelemetry Getting Started](https://opentelemetry.io/docs/getting-started/)
- [OTLP Exporter Configuration](https://opentelemetry.io/docs/languages/sdk-configuration/otlp-exporter)