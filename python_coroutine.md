see https://docs.python.org/ko/3/library/asyncio-task.html

# Python coroutine

## Terminology

- routine: 일련의 명령, 코드의 흐름
- main routine: 프로그램 main 코드의 흐름 (`if __name__ == "__main__":`아래에서 실행되는 코드 흐름)
- sub routine: 하나의 진입점과 하나의 탈출점이 있는 우리가 알고 있는 함수나 메소드 같은 것들.

다 연결시켜 생각해보면,
프로그램이 실행되면 sub routine은 별도 scope에 해당 로직들을 모아 놓고 있다. sub routine이 호출될 때, 해당하는 로직들의 scope로 이동 했다가 return을 통해 원래 호출 시점인 main routine으로 돌아온다.

```python
import time

def delivery(name, mealtime):
    # sub routine
    print(f"{name}에게 배달 완료")
    time.sleep(mealtime)
    print(f"{mealtime}시간 소요되었고, {name} 식사 완료. ")
    print(f"{name} 그릇 수거 완료")

def main():
    # sub routine
    delivery("A", 9)
    delivery("B", 3)
    delivery("C", 6)

if __name__ == "__main__":
    # main routine
    start = time.time()
    main()
    end = time.time()
    print(f"총 소요 시간: {end - start}")

```

- coroutine: 다양한 진입점과 다양한 탈출점이 있는 루틴. (vs. sub routine: 하나의 진입점, 하나틔 탈출점)

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

## Question

1. 그럼 asyncio는 뭐냐?
   > python에서 coroutine 함수의 실행을 가능하도록 도와주는 패키지.
2. 그럼 await은 async를 사용한 coroutine 함수에는 무조건 아무렇게나 사용하면 되냐? 예를 들어 `async hello(): await print("hello world")` 처럼!
   > 아니다. 만약 저 코드를 실행시켜보면 `TypeError: object NoneType cant' be used in 'await' expression` 에러가 뜰 것이다. await는 awaitable 객체에서만 사용할 수 있다.  
   > see https://docs.python.org/ko/3/library/asyncio-task.html#asyncio-awaitables

## Usage

### 1. without coroutine

```python'
# see https://2.python-requests.org/en/master/user/advanced/#id1
# pip install requests

import requests
import time


def fetcher(session, url):
    with session.get(url) as response:
        return response.text


def main():
    urls = ["https://naver.com", "https://google.com", "https://instagram.com"] * 10

    with requests.Session() as session:
        result = [fetcher(session, url) for url in urls] # blocking이 발생함.
        print(result)


if __name__ == "__main__":
    start = time.time()
    main()
    end = time.time()
    print(end - start)  # 12s
```

### 2. with coroutine

```python
# see https://docs.aiohttp.org/en/stable/
# pip install aiohttp~=3.7.3


import aiohttp
import time
import asyncio


async def fetcher(session, url):
    async with session.get(url) as response:
        return await response.text()


async def main():
    urls = ["https://naver.com", "https://google.com", "https://instagram.com"] * 10

    async with aiohttp.ClientSession() as session:
        result = await asyncio.gather(*[fetcher(session, url) for url in urls])
        print(result)


if __name__ == "__main__":
    start = time.time()
    asyncio.run(main())
    end = time.time()
    print(end - start)  # 4.8s
```

## Use multiprocessing, multithreading

multiprocessing과 multithreading을 사용해서 동기적 코드(without coroutine)을 속도를 높이는 방법에 대해.. Next step
