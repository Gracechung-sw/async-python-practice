# python multiprocessing

## computer architecture, Operating System

- CPU: 연산
- 메모리
- 주메모리: 작업에 필요한 프로그램과 데이터를 저장하는 장치 (RAM)
- 보조메모리: 저장장치라고 불리며 데이터를 일시적, 영구적으로 저장하는 장치
- 입출력장치
- 시스템 버스

프로그램과 프로세스

- program: 저장장치에 저장된 정적인 상태
- process: 실행을 위해 주메모리에 올라온 동적인 상태
- thread:
  프로세스가 생성되면 CPU는 프로세스가 해야할 작업을 수행하고,  
  이때 CPU가 처리하는 작업의 단위가 바로 thread이다.  
  다시 말해 thread란 process내에서 실행되는 여러 작업의 단위.
  - multi thread: 여러개의 thread가 동작하면(?) 동작한다는게 좀 추상적임..
    - 다수의 thread까리 메모리 공유와 통신이 가능
      - 장점: 자원의 낭비를 막고, 효율성 향상
      - 단점: 한 스레드에 문제가 생기면 전체 프로세스에 영향을 미친다.
  - single thread

## 동시성(Concurrency) vs 병렬성(Parallelism)

### 동시성(Concurrency)

switching을 하며 한 번에 여러 작업을 `동시에` 처리하는 것

ex. 로봇 자비스의 주요 임무는 주문받은 음식을 요리하는 것
이 때 라면, 파스타, 케이크 음식 주문이 한 번에 3개가 온다면?

see img/concurrency.png

아래처럼 비동기로 구현하면 동시성 프로그래밍이 가능해진다. 즉, single thread에서 동시성 프로그래밍을 한 것이다.

```python
import asyncio
import time


async def delivery(name, mealtime): # 2) 이쪽으로 진입하여 실행됨. (진입점 1)
    print(f"{name}에게 배달 완료")
    await asyncio.sleep(mealtime) # 3) 여기까지 실행되고, 여기서 탈출하여 (탈출구 1)
    print(f"{mealtime}시간 소요되었고, {name} 식사 완료. ") # 5) 그리고 9초가 지난 뒤 '9시간 소요되었고, A 식사 완료' 가 실행될 때는 여기로 진입됨 (진입점 2)
    print(f"{name} 그릇 수거 완료")
    return # 6) 그리고 여기서 탈출함 (탈출구 2)

# 이런식으로 async, await을 활용하여 진입점, 탈출구가 하나씩 만 있는게 아니라 다양하게 활용하게 되는 routine을, 이걸 coroutine이라고 함.


async def main():
    await asyncio.gather( # asyncio.gather: see https://docs.python.org/ko/3/library/asyncio-task.html#running-tasks-concurrently
        delivery("A", 9), # 1) 이게 실행되면
        delivery("B", 3), # 4) 바로 이게 실행됨.
        delivery("C", 6)
    )


if __name__ == "__main__":
    start = time.time()
    asyncio.run(main())
    end = time.time()
    print(f"총 소요 시간: {end - start}")

```

이처럼 실글 코어에서 한 코어가 동시성을 사용할 수도 있지만,
멀티 코어에서도 각각의 코어가 동시성을 사용할 수 있다.
그래서 동시성은 논리적 개념으로 멀티 스레딩에서도 동시성 프로그래밍이 가능하기도 하고, 싱글 스레드에서도 동시성 프로그래밍이 가능하기도 하다.

### 병렬성(Parallelism)

여러 worker들(=cpu core라고 생각하면 multi processing, thread라고 생각하면 multi threading)을 사용하며 한 번에 여러 작업을 `병렬적으로 at the same time에` 처리하는 것

ex. 로봇 자비스와 프라이데이와 캐런의 주요 임무는 주문받은 음식을 요리하는 것
이 때 라면, 파스타, 케이크 음식 주문이 한 번에 3개가 온다면?

see img/parallelism.png

이처럼 명령을 수행해야 하는 process나 thread가 있어야 하기 때문에 물리적 개념이 된다.
