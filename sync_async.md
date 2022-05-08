# 동기 vs 비동기

## 동기(Sync)

코드가 동기적으로 동작한다는 것은 코드가 반드시 작성된 순서 그대로 실행된다는 것.

```python
import time

def delivery(name, mealtime):
    print(f"{name}에게 배달 완료")
    time.sleep(mealtime)
    print(f"{mealtime}시간 소요되었고, {name} 식사 완료. ")
    print(f"{name} 그릇 수거 완료")

def main():
    delivery("A", 9)
    delivery("B", 3)
    delivery("C", 6)

if __name__ == "__main__":
    start = time.time()
    main()
    end = time.time()
    print(f"총 소요 시간: {end - start}")

```

실행 결과

```
A에게 배달 완료
9시간 소요되었고, A 식사 완료.
A 그릇 수거 완료
B에게 배달 완료
3시간 소요되었고, B 식사 완료.
B 그릇 수거 완료
C에게 배달 완료
6시간 소요되었고, C 식사 완료.
C 그릇 수거 완료
총 소요 시간: 18.01293110847473
```

## 비동기(Async)

코드가 반드시 작성된 순서 그대로 실행되는 것이 아니다.

```python
import asyncio
import time


async def delivery(name, mealtime): # 비동기 함수 ( = 코루틴 함수를 사용해서 비동기적으로 동작하는 비동기 함수 작성이 가능하다. 그리고 비동기 함수를 가지꼬 동시성 프로그래밍이 가능해진다. )
    print(f"{name}에게 배달 완료")
    await asyncio.sleep(mealtime)
    print(f"{mealtime}시간 소요되었고, {name} 식사 완료. ")
    print(f"{name} 그릇 수거 완료")


async def main():
    await asyncio.gather(
        delivery("A", 9),
        delivery("B", 3),
        delivery("C", 6)
    )


if __name__ == "__main__":
    start = time.time()
    asyncio.run(main())
    end = time.time()
    print(f"총 소요 시간: {end - start}")

```

실행 결과

```
A에게 배달 완료
B에게 배달 완료
C에게 배달 완료
3시간 소요되었고, B 식사 완료.
B 그릇 수거 완료
6시간 소요되었고, C 식사 완료.
C 그릇 수거 완료
9시간 소요되었고, A 식사 완료.
A 그릇 수거 완료
총 소요 시간: 9.00208592414856
```

이 동기/비동기를 bound/blocking이랑 연관시켜 생각해보면

- see img/sync_io.png
- see img/async_io.png
