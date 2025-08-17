# 메모리 베리어

- 하드웨어의 최적화 방지
 
 ```csharp
 static volatile int x = 0;
 static volatile int y = 0;
 static volatile int r1 = 0;
 static volatile int r2 = 0;
 
static void Thread_1(){
    y = 1;
    r1 = x;
}

static void Thread_2(){
    x = 1;
    r2 = y;
}


static void Main(){

    int count = 0;
    
    while(true){
        count++;

        x = y = r1 = r2 = 0;

        Task t1 = new Task(Thread_1);
        Task t2 = new Task(Thread_2);

        t1.Start();
        t2.Start();

        Task.WaitAll(t1, t2);

        if(r1 == 0 && r2 == 0){
            break;
        }
    }

    Console.WriteLine($"{count}번 만에 탈출");
}
 ```
 - 위의 코드는 탈출 조건을 만족한다.
 - CPU는 스레드의 각 라인이 서로 영향이 없다고 판단하면 호출 순서를 변경해 실행
 - `r1 == 0 && r2 == 0` 조건이 만족하게됨.
 - `volatile` 키워드를 써도 안먹힘

 `Thread.MemoryBarrier()`를 사용한다.

 ```csharp
static void Thread_1(){
    y = 1;

    Thread.MemoryBarrier();

    r1 = x;
}
 ```

 - CPU나 컴파일러의 명령어 재배치 방지


 ### 메모리 가시성

- CPU는 성능 최적화를 위해 값을 레지스터나 L1 캐시 등에 저장하고, 메인 메모리와 즉시 동기화하지 않음
- 컴파일러도 명령 순서 재배치 가능
- 따라서 스레드 간 동기화 없이는 변경된 값이 '보장되지 않음'

`volatile` 키워드 -> 해당 변수는 항상 메인 메모리에서 읽고 씀 (가시성 보장)