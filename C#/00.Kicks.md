# C# Kicks

### Debug/Release

- .sln 파일이 누락되었을 경우 모드 변경 표시가 안됨

  ```bash
  dotnet new sln -n <solution_name>
  dotnet sln <solution_name>.sln add <project_name>.csproj
  ```

- Release 모드에서 실행 시 컴파일러의 최적화로 인해 예외가 발생할 수 있음(가시성 문제)

  - ~~volatile 키워드로 컴파일러에 최적화를 하지 않도록 지시~~ 사용 x

  ```csharp
  bool isTrue = false;

  while(!isTrue){
    //
  }

  while(true){
    //
  }

  ```

  - Release 모드에서 while문이 최적화되어 무한루프에 빠질 수 있음
  - 스레드 내부에서 isTrue를 변경하지 않을 경우 JIT 컴파일러는 `while(!isTrue)`를 `while(true)`로 캐싱하거나 루프 코드 자체를 변경해버림

### JIT Compiler(Just-In-Time Compiler)

- C# 코드는 빌드 시에 IL(Intermediate Language)로 컴파일
- JIT 컴파일러는 프로그램이 실행될때 IL을 기계어로 변환
- JVM에도 JIT 컴파일러가 있음

#### JIT을 쓰는 이유

- 런타임 최적화
  - 실제 환경에 맞춰 코드를 최적화
- 빠른 실행
  - 필요한 코드만 실행 시점에 컴파일, 초기 속도가 빠름
- 플랫폼 독립성
  - OS 독립적
