# Thread

## Thread Basic

### 쓰레드 생성

```csharp
static void MainThread(Object state)
{
    // C# 스레드는 기본적으로 foreground 실행
    for (int i = 0; i < 10; i++)
    {
        Console.WriteLine("Hello Thread!");
    }
}

static void Main(string[] args)
{
    Thread t = new Thread(MainThread);

    // C# 스레드를 백그라운드
    // Main 스레드가 종료되면 같이 종료된다.
    t.IsBackground = true;
    t.Start();

    // t가 끝날때 까지 대기
    t.Join();

    Console.WriteLine("Hello World!");
}
```

- C# 스레드는 기본적으로 foreground 실행
- isBackground = true로 설정하면 백그라운드 실행
- 쓰레드를 직접 생성하는 것은 비효율적

### ThreadPool

```csharp
// 최대 최소
ThreadPool.SetMinThreads(1,1);
ThreadPool.SetMaxThreads(5,5);

ThreadPool.QueueUserWorkItem(MainThread);
```

- ThreadPool은 가볍고 효율적은 스레드 제공
- 최대 갯수를 넘으면 다음 작업이 실행되지 않음
- 오래 걸리는 작업(무한루프)에 적합하지 않음

### Task

```csharp
// Task 생성
Task t = new Task(() => while(true) { }, TaskCreationOptions.LongRunning);
t.Start();

// Task.Run lambda
Task.Run(() =>
{
    Console.WriteLine("Hello World!");
});

// async/await
async Task AsyncTask()
{
    await Task.Delay(1000);
    Console.WriteLine("Hello World!");
}

await AsyncTask();
```

- 비동기의 고수준 API
- 내부적으로 ThreadPool을 사용
- async/await를 사용하여 비동기 처리
- 오래 걸리는 작업에는 `TaskCreationOptions.LongRunning`을 사용
- 일반 Task는 ThreadPool을 사용
- LongRunning Task는 별도의 스레드를 생성해 오래 걸리는 작업을 처리

## Caching

### Caching 철학

- 시간지역성(Time locality)
  - 최근에 사용된 데이터는 다시 사용될 가능성이 높다.
- 공간지역성(Space locality)
  - 최근에 사용된 데이터의 주변 데이터는 곧 사용될 가능성이 높다

```csharp
static void Main(string[] args)
        {
            int[,] arr = new int[10000, 10000];

            {
                long start = DateTime.Now.Ticks;
                for (int i = 0; i < 10000; i++)
                {
                    for (int j = 0; j < 10000; j++)
                    {
                        arr[i, j] = 1;
                    }
                }
                long end = DateTime.Now.Ticks;
                Console.WriteLine($"eta1: {end - start}");
            }

            {
                long start = DateTime.Now.Ticks;
                for (int i = 0; i < 10000; i++)
                {
                    for (int j = 0; j < 10000; j++)
                    {
                        arr[j, i] = 1;
                    }
                }
                long end = DateTime.Now.Ticks;
                Console.WriteLine($"eta2: {end - start}");
            }
        }

        // eta1: 3431336
        // eta2: 4511872
```

- eta1이 eta2보다 빠름
- eta1의 경우 x 방향으로 진행, 공간지역성에 따라서 인접 값들을 캐시처리
- eta2의 경우 y 방향으로 진행, 캐시처리가 안되어 메모리에 가서 값을 가져옴
