# I/O 바운드 & CPU 바운드, 블로킹

## 바운드

### CPU 바운드:

프로그램이 실행될 때 실행 속도가 CPU 속도에 의해 제한됨을 의미
즉, 프로그램이 실행 될 때 CPU에서읙 계산에서 프로그램이 잠시 멈추고 잠시 시간이 걸림.
정말 복잡한 수학 수식을 계산하는 경우에 컴퓨터의 실행 속도가 늦어짐.

```
def cpu_bound_func(number: int):
    total = 1
    arrange = range(1, number + 1)
    for i in arrange:
        for j in arrange:
            for k in arrange:
                total += i * j * k
    return total

if __name__ == "__main__":
    result = cpu_bound_func(100) # 여기서 CPU 연산량이 많으므로 (3중 for문) 이게 실행 되고 끝 날 때 까지 아래 print 문은 실행아 안되고 잠시 멈춰있음.
    # 이를 cpu bound라고 함.
    print(result)
```

### I/O 바운드

CPU 바운드가 CPU 연산에 의해 프로그램 실행이 잠시 멈추는 것이라면,
I/O 바운드란 프로그램이 실행될 때 실행 속도가 I(Input), O(Output)에 의해 제한됨을 의미한다.

```
def io_bound_func():
    print("값을 입력해주세요") # 실행을 하면 이 print문이 실행 된 후 '사용자가 값을 input' 하기 전까지 실행이 멈취있다. 이는 cpu가 연산을 하기 위해 멈춰진게 아니다.
    input_value = input()
    return int(input_value) + 100

if __name__ == "__main__":
    result = io_bound_func()
    print(result)
```

Input(에 대한 응답)을 하는 주체는 사용자, 다른 서버..(이런 경우 Network I/O 라고도 함.) 등이 될 수 있다.
컴퓨터와 컴퓨터끼리 통신을 할 때에도 I/O 바운드가 발생할 수 있다.

```
import requests

def io_bound_func():
    result = requests.get("https://google.com")
    return result

if __name__ == "__main__":
    result = io_bound_func()
    print(result)
```
