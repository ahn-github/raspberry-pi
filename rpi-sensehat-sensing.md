---
layout: page
title: xwMOOC 라즈베리 파이
subtitle: 감각모자로 주변 감지
---

> ### 학습 목표 {.objectives}
>
> * 감각모자(Sense HAT)를 이해한다.
> * 파이썬 프로그래밍 환경 IDEL을 사용해서 감각모자로 의사소통하는 방법을 학습한다. 
> * 감각모자 입출력을 프로그램하는 방법을 학습한다.
> * 감각모자 라이브러리를 사용해서 메시지와 이미지를 화면에 출력하고, 방향을 제어하고, 센서 데이터를 수집하고, 움직임에 반응한다.
> * 변수를 사용해서 센서 데이터를 수집하는 방법을 학습한다.
> * 특정 동작을 반복하는데 루프를 사용하는 방법도 학습한다.

[Astro Pi](https://astro-pi.org/) 프로젝트 일환으로 감지센서에 대한 교재가 많이 개발되었다.

|      감지 센서          |          입출력         |
|:-----------------------:|:-----------------------:|
|        온도             |         조이스틱        |
|        습도             |        LED 전광판       |
|        압력             |           버튼          |
|        움직임           |                         |

### 1. 온도 

감각 모자에는 다양한 센서가 포함되어 있는데, 온도센서도 그중 하나다.

<img src="fig/rpi-thermometer.jpg" alt="온도 센서" width="50%" />

상기 이미지에 몸에 온도를 측정하는 체온계가 보인다. 아파서 병원에 가면
입에 물고 체온을 측정한 경험이 모두 있을 것이다.
체온계는 35도에서 시작되어, 몸의 체온을 측정하는데만 사용된다.
감각모자에 매립된 온도센서는 -40도에서 영상 120도까지 측정이 가능해서
의료용 체온계보다 다양한 용도에 사용될 수 있다.

감각모자는 온도센서가 두개 있는데, 하나는 습도센서에 달려있고, 또다른 하나는
압력센서에 달려있다. 어느 것을 사용할지 골라 사용하거나, 둘 모두 측정하고 평균을 
내서 사용할 수 있다.

#### 1.1. 온도 측정 시작

1. 다음 명령어를 루트 권한 `sudo` 으로 **파이썬3(Python 3)** 를 터미널 윈도우에서 연다.

~~~ {.python}
sudo idle3 &
~~~

2. `File > New Window` 로 새로운 창을 열고 다음 코드를 입력한다:

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()
sense.clear()

temp = sense.get_temperature()
print(temp)
~~~

3. `File > Save` 메뉴를 선택하고 상기 파이썬 프로그램에 대한 파일명을 지정하고 저장한다. 

4. `Run > Run module` 선택해서 파이썬 프로그램을 실행시킨다.

5. `Humidity Init Failed, please run as root / use sudo` 오류 메시지(파이썬 코드 실행 마지막 줄에 붉은색 글씨)가 뜨게 되면, 상기 절차를 충실히 따르지 않는 것으로 볼 수 있다. 모든 것을 닫고, 첫번째 단계부터 다시 시작한다.

6. 결과가 다음과 같이 나오면 성공이다.

~~~ {.output}
Humidity sensor Init Succeeded
28.6293258667
~~~

7. `print(temp)` 바로 앞에 다음 명령줄을 추가한다. 소수점이 너무 길어, 소수점 아래 첫번째 자리에서 반올림한다는 것이다.

~~~ {.python}
temp = round(temp, 1)
~~~

8. 다음과 같이 보이면 성공이다. 소수점 아래 첫번째 숫자를 제외하고 반올림해서 모두 사라졌다.

~~~ {.output}
Humidity sensor Init Succeeded
28.6
~~~

9. `get_temperature` 함수 대신에 다음 함수를 사용한다.
    - `get_temperature_from_humidity`, 여기서 습도센서를 사용한다는 것으로 축약해서 `get_temperature`을 사용한다.
    - `get_temperature_from_pressure`, 여기서 압력센서를 사용한다.

예를 들어, 현재 습도센서에서 압력센서로 바꿀려면 다음과 같이 파이썬 코드를 변경한다.

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()
sense.clear()

temp = sense.get_temperature_from_pressure()
temp = round(temp, 1)
print(temp)
~~~

작성한 코드는 압력센서에서 온도정보를 받아 소수점아래 첫번째 자리 반올림하고 결과를 출력하고 종료된다.

#### 1.2. 온도 모니터링

1. 온도변화를 모니터링하는 것이 필요해서 `while` 루프에 위에서 작성한 코드를 넣고, 재실행한다.

~~~ {.python}
while True:
  temp = sense.get_temperature()
  temp = round(temp, 1)
  print(temp)
~~~

파이썬 코드를 실행시며면, 가장 최근에 측정한 온도가 바닥에 출력되며 스크롤이 쭉 올라간다.

2. 온도센서 위에 한동안 엄지를 올려놓는다. 온도가 상승되는 것이 보일 것이다.

3. 입김을 불거나, 공기를 불어 먼지를 털어내는 먼지청소기에서 바람을 센서에 불게 한다. 온도가 떨어지는 것이 보일 것이다.

4. `Ctrl + C` 키를 눌러 프로그램을 종료시킨다.

#### 1.3. LED 전광판에 온도 출력

온도정보를 LED 전광판에 표시하는 방법을 생각해본다.
`show_message` 함수를 사용하는 것도 좋은 선택지다.
하지만, 더 좋은 방법이 있다. 예를 들어, 다음과 같이 실행한다.

* `clear` 함수를 사용하여 측정한 온도가 속하는 범위에 미리 정의된 색상을 표현한다. 예를 들어 0 ~ 5 도는 푸른색.
* `clear` 함수를 사용하여 단일 색상을 표현하지만, 측정된 온도에 따라 붉은색 휘도(0 ~ 255)를 변경시킨다.
* `set_pixel` 함수를 사용하여 온도계와 유사하게 막대를 위로 아래로 표시한다.

다음에 나온 코드는 온도막대를 표현한 코드다. 코드에 8도 온도범위를 막대로 표시한다. (LED 가로행마다 1도를 표현)
표시할 수 있는 최대온도는 `31` (하드 코딩되어 있지만, 자유로이 편집하라), 최소 온도는 `31-8` 즉, `23`도다.
만약 측정된 온도가 범위 밖에 떨어지면 오류가 발생된다. 필요하면 측정된 온도정보도 소중하니, 오류 발생을 방지하는 코드를 추가하라.

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()
sense.clear()

tmax = 31
tmin = tmax - 8

while True:
    temp = sense.get_temperature()
    print(temp)
    temp = int(temp) - tmin
    for x in range(0, 8):
        for y in range(0, temp):
            sense.set_pixel(x, y, 255, 0, 0)
        for y in range(temp, 8):
            sense.set_pixel(x, y, 0, 0, 0)
~~~

측정된 값에서 최소값을 빼서 0 ~ 8 사이 온도값을 산출한다.
중첩된 `for` 루프를 두개 사용한다. 외곽 루프는 `x` 축에 대한 것이고, 내부 2개 루프는 `y` 축에 대한 것이다.
여기서 `y`축에 대한 루프를 두개 사용하는 이유는 측정된 온도 아래 모든 LED를 `set_pixel` 함수로 켜고, 아래 모든 LED는 끈다. 측정된 온도에 따라 막대가 `y` 축을 따라 위로 아래로 변화하는 것처럼 보인다.

`sense.clear()` 다음에 바로 `sense.set_rotation(n)` 함수(여기서 `n`은 0, 90, 180, 270)를 사용해서 막대 방향을 바꿀 수도 있다. 

### 2. 습도(Humidity)

* 습도는 공기중에 함유된 수증기 양이다.
* 수증기는 물이 기화된 상태다.


공기중에 떠있는 수증기 양은 온도에 달려있다.

* 온도가 높으면 높을수록, 공기중에 더 많은 수증기가 떠있게 된다.
* 온도가 낮으면 낮을수록, 공기중에 더 적은 수증기가 떠있게 된다.

<img src="fig/rpi-condensation.jpg" alt="수증기" width="50%" />

냉장고에서 PT병을 빼면, 물방울이 맺치는 것을 볼 수 있다. 이유는 차가운 PT병이 주변 공기를 식혀서 
공기에 함유된 수증기가 떠다니지 못하게 만들기 때문이다.
따라서, 떠있는 수증기를 액체상태 물로 변하게 만든다. 
이것을 기체의 **응결(condensation)** 이라고 부른다. 이런 기본지식을 바탕으로,
습도를 축정하는 두가지 방식이 있다는 것을 이해할 필요가 있다.

* **절대 습도(Absolute humidity)** : 해당 공기 중에 떠다니는 수증기 전체 질량. 온도는 이런 경우 고려하지 않는다.
공기 세제곱미터 당 그램으로 보통 표시한다.
* **상대 습도(relative humidity)** : 백분율로 표시한다. 공기온도가 주어지면, 함유될 수 있는 최대 수증기량이 된다.
상대습도는 최대가능한 양과 대비하여, 실제 수증기량을 백분율로 표현한 것이 상대습도다.

공기중 온도에 따라 함유할 수 있는 수증기량이 달라지기 때문에, 수증기 양이 서로 다른 상대습도 측정값으로 표현된다. 그렇기 때문에, 낮은 공기 온도는 상대적으로 높은 습도를 주는데 이유는 낮은 공기상태에서는 많은 수증기를 함유할 수 없기 때문이다. 공기중 온도를 높이면서 동일한 수증기양을 유지시키면 상대적으로 습도측정치를 떨어뜨리게 된다. 이유는 공기중에 함유할 수 있는 최대 수증기량이 증가되기 때문이다.

감각모자는 상대습도로 습도측정 정보를 제공한다. 이런 이유로 인해서 습도센서는 온도센서가 함께 내장된다.

#### 2.1. 현재 습도 측정 시작

1. 다음 명령어를 루트 권한 `sudo` 으로 **파이썬3(Python 3)** 를 터미널 윈도우에서 연다.

~~~ {.python}
sudo idle3 &
~~~

2. `File > New Window` 로 새로운 창을 열고 다음 코드를 입력한다:

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()
sense.clear()

humidity = sense.get_humidity()
print(humidity)
~~~

3. `File > Save` 메뉴를 선택하고 상기 파이썬 프로그램에 대한 파일명을 지정하고 저장한다. 

4. `Run > Run module` 선택해서 파이썬 프로그램을 실행시킨다.

5. `Humidity Init Failed, please run as root / use sudo` 오류 메시지(파이썬 코드 실행 마지막 줄에 붉은색 글씨)가 뜨게 되면, 상기 절차를 충실히 따르지 않는 것으로 볼 수 있다. 모든 것을 닫고, 첫번째 단계부터 다시 시작한다.

6. 결과가 다음과 같이 나오면 성공이다.

~~~ {.output}
Humidity sensor Init Succeeded
34.6234588623
~~~

7. `print(humidity)` 바로 앞에 다음 명령줄을 추가한다. 소수점이 너무 길어, 소수점 아래 첫번째 자리에서 반올림한다는 것이다.

~~~ {.python}
humidity = round(humidity, 1)
~~~

8. 다음과 같이 보이면 성공이다. 소수점 아래 첫번째 숫자를 제외하고 반올림해서 모두 사라졌다.

~~~ {.output}
Humidity sensor Init Succeeded
34.6
~~~

#### 2.2. 습도 모니터링

1. 습도변화를 모니터링하는 것이 필요해서 `while` 루프에 위에서 작성한 코드를 넣고, 재실행한다.

~~~ {.python}
while True:
    humidity = sense.get_humidity()
    humidity = round(humidity, 1)
    print(humidity)
~~~

파이썬 코드를 실행시며면, 가장 최근에 측정한 온도가 바닥에 출력되며 스크롤이 쭉 올라간다.

2. 센서에 천천히 숨을 불어 넣는다. 숨속에 함유된 수증기가 습도측정치를 올린다.

3. 숨을 불어 넣고 계속해서 주시하고 나면 습도측정치가 현재 방안 습도값으로 천천히 돌아가는 것이 보인다.

4. `Ctrl + C` 키를 눌러 프로그램을 종료시킨다.

#### 2.3. LED 전광판에 습도 출력

습도정보를 LED 전광판에 표시하는 방법을 생각해본다.
한방법은 64(LED 전광판 갯수)를 100으로 나누고 나서, 상대습도 백분율을 곱하면 
켜야되는 LED 전광판 갯수가 산출도니다. 예를 들어, 상대습도가 100%라면 64개 LED 모두를 켜게 된다.
이렇게 만들려면, 켜야되는 픽셀 갯수와 꺼야되는 픽셀 갯수를 리스트로 만들고 나서,
`set_pixels` 함수를 호출한다.

다음에 예제 코드가 나와 있다. 습도측정값을 100으로 고정시킨 것에 주의한다.

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()
sense.clear()

on_pixel = [255, 0, 0]
off_pixel = [0, 0, 0]

while True:
    humidity = sense.get_humidity()
    humidity = round(humidity, 1)

    if humidity > 100:
        humidity = 100.0

    pixels = []
    on_count = int((64 / 100.0) * humidity)
    off_count = 64 - on_count

    pixels.extend([on_pixel] * on_count)
    pixels.extend([off_pixel] * off_count)

    sense.set_pixels(pixels)
~~~

**습도센서에서 100보다 큰 값을 갖는 것도 가능하다는 것에 주의한다.**

### 3. 압력 

통상, 압력(pressure)은 단위 면적위에 가해지는 힘이다. 식탁위에 놓인 벽돌을 상상해보자.
식탁 한쪽면을 통해 벽돌무게의 힘이 가해진다. 
벽돌을 중심을 잡아 바늘위에 놓게 되면, 벽돌무게의 힘이 바늘끝에 모아지게 되서, 
식탁에 가해지는 압력이 훨씬더 높아지게 된다.

기압(대기압이라고도 함)은 지구 대기에 공기의 무게로 가해지는 압력이다.
공기압은 항상 우리 몸 전체로 가해진다. 몸속 내부 압력이 외부와 동일해서, 보통 아무것도 느끼지 못한다.
측정단위는 기압 단위로 바아(bar) 혹은 밀리바(millibar)가 된다.
1 바아는 해수면 기준 대기압과 같고, 1000 밀리바가 된다.

#### 3.1. 압력에 관한 사실

<img src="fig/rpi-bottle.jpg" alt="PT병" width="50%" />

* 대기압은 지구표면(해수면)에서 우주로 나가면서 점차 떨어진다.
* 상기 PT병을 해발고도 4300 미터에서 두껑을 막아 밀폐했다. 2700 미터에서 대기압이 증가해서 PT병이 쭈글어 들었고, 해수면 300미터근방에서 거의 쪼글아 든 것이 확연히 보인다.
* 동식물은 생존에 적정한 대기압을 필요로 한다. 고도 8000 미터 이상 공기는 사람에게 위험해서 등반가들 사이에 [죽음의 지역](http://simple.wikipedia.org/wiki/Death_zone)으로 불린다. 에버레스트 정상이 죽음의 지역에 포함된다.
* [암스트롱 경계(Armstrong line)](http://en.wikipedia.org/wiki/Armstrong_limit)은 사람이 절대로 살수 없는 고도가 된다. 18900 에서 19350 사이로 사람체온에서 물이 끓는다. 
* 우주정거장(ISS)은 고도 약 400 KM(400,000 미터) 궤도를 유지한다. 암스트롱 경계선을 훨씬 뛰어넘어 공기가 전혀 없는 (0 밀리바) 우주 공간을 돌고 있다. 하지만, 우주정거장 내부 공기는 1013 미리바를 유지하여 승무원에게 최적의 환경을 제공한다.

#### 3.1. 압력 측정 시작

1. 다음 명령어를 루트 권한 `sudo` 으로 **파이썬3(Python 3)** 를 터미널 윈도우에서 연다.

~~~ {.python}
sudo idle3 &
~~~

2. 파이썬 쉘 윈도우가 나타난다. 터미널 윈도우를 닫아도 된다.

3. `File > New Window` 로 새로운 창을 열고 나서,

4. 다음 코드를 입력한다:

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()
sense.clear()

pressure = sense.get_pressure()
print(pressure)
~~~

5. `File > Save` 메뉴를 선택하고 상기 파이썬 프로그램에 대한 파일명을 지정하고 저장한다. 

6. `Run > Run module` 선택해서 파이썬 프로그램을 실행시킨다.

7. `Pressure Init Failed, please run as root / use sudo` 오류 메시지(파이썬 코드 실행 마지막 줄에 붉은색 글씨)가 뜨게 되면, 상기 절차를 충실히 따르지 않는 것으로 볼 수 있다. 모든 것을 닫고, 첫번째 단계부터 다시 시작한다.

8. 결과가 다음과 같이 나오면 성공이다.

~~~ {.output}
Pressure sensor Init Succeeded
1013.40380859
~~~

`0`이 출력되면 다시 파이썬 프로그램을 재실행한다.
처음 압력센서를 사용할 때 종종 이런 경우가 발생된다.

9. `print(pressure)` 바로 앞에 다음 명령줄을 추가한다. 소수점이 너무 길어, 소수점 아래 첫번째 자리에서 반올림한다는 것이다.

~~~ {.python}
pressure = round(pressure, 1)
~~~

#### 3.2. 압력 모니터링

1. 압력변화를 모니터링하는 것이 필요해서 `while` 루프에 위에서 작성한 코드를 넣고, 재실행한다.

~~~ {.python}
while True:
    pressure = sense.get_pressure()
    pressure = round(pressure, 1)
    print(pressure)
~~~

2. 불행히도, 센서에 손가락을 올려 놓거나 숨을 부는 것처럼 단순히 압력변화를 만들어 내기가 쉽지 않아서 작성한 파이썬 코드 테스트를 위해 아래와 같이 PT병 실험을 진행한다.

#### 3.3. PT병 실험

PT병 내부에 핸드폰 보조 배터리로 전원이 공급되는 라즈베리파이에 감각모자를 씌워서 밀봉하고 나서,
공기압을 증가시키는데 공기를 불어 넣는다. 폐활량이 좋은 성인이 공기를 불어 넣으면 PT병 내부 공기압을 약 1100 밀리바까지 수월히 증가시킬 수 있다. 먼저 시각적으로 LED 전광판에 표시되도록 프로그램을 작성한다.

1. PT병 실험에 대한 [Dave Honess 동영상](https://www.youtube.com/watch?v=CHUukiKF3ew)을 감상한다.

2. 2리터 PT 병을 준비해서 뚜껑은 버리고, 다음과 같이 PT병 중간을 반으로 자른다.

<img src="fig/rpi-Astro_Pi_Diagrams-01.png" alt="PT병 실험" width="50%" />

휴지나 마른 행주, 수건 등으로 PT병 내부를 건조시킨다. PT병을 변형시키는 헤어드라이어 기기나 기타 열기구 사용은 삼가한다.

3. 체결 결합 실험. 중간을 반으로 가른 PT병에서 위쪽 부분을 아래쪽 부분에 결합하여 체결시킨다.
PT병 내부에 공기를 불어 넣을 때, 공기가 다시 주입구를 통해 나오는지 확인한다; 나중에 테이프로 PT병 두 조각을 단단히 체결하여 밀봉시킨다.

4. 라즈베리파이를 끄고 나서, 모든 주변장치는 연결된 상태로 둔다.

5. 전원을 분리하고, 휴대폰 보조 배터리로 전원공급장치를 교체한다.

6. 평상시와 다름없이 라즈베리파이를 부팅시켜, 파이썬 코드가 정상적으로 실행되도록 둔다.

7. 코드가 정상적으로 돌기 시작하면, 배터리를 제외한 모든 주변장치를 분리한다. 그리고 PT병속에 넣는다.
분리한 PT병 위쪽 반쪽이 아래쪽 반쪽에 들어가는지 확인한다. 이는 공기를 입구로 불어넣은 공기가 다시 입구로 나오는 것을 보고 확인한다.

<img src="fig/rpi-Astro_Pi_Diagrams-02.png" alt="PT병 실험" width="50%" />

8. 테이프를 사용해서 PT병 위쪽과 아래쪽음 단단히 체결하여 밀봉한다.

<img src="fig/rpi-Astro_Pi_Diagrams-04.png" alt="PT병 실험" width="50%" />

9. 공기를 PT병 내부로 불어 넣는다. 만약 공기가 새는 곳이 발견되면 테이프를 사용해서 새는 곳을 매꿔서 공기를 불어 넣고 나면 공기가 바로 불어넣은 곳으로 나오는 것이 확인되어야 한다.

10. 숨속에 포함된 습기 때문에 PT병 내부에 김이 서리게 되는 것에 주의한다. 라즈베리파이에 습기가 차기 되기 전에 잠시 멈춘다.

11. 프로그램 코드를 변경할 필요가 있는 경우 테이프를 제거하고 PT병을 분리한다. 주변장치를 다시 연결해서 재빨리 편집작업을 수행한다.

#### 3.4. LED 전광판에 압력정보 출력

아래 코드가 비디오에 사용된 바로 그 코드다.
1000에서 1100 밀리바까지 측정할 수 있고 100 밀리바가 중간단위가 된다.
LED 전광판 색상이 0 에서 255까지 범위를 갖게 되어,
대기압 범위와 색상 범위를 매핑하는 것이 첫번째 작업이 된다.
대기압에 대한 색상정보를 출력하는데 측정된 대기압을 비율로 곱한다.
이를 위해서  측정된 대기압에서 1000을 빼고 비율대로 0에서 100 사이 숫자를 곱한다.
그리고 나서 색상을 최대 255까지로 고정시키다. 이유는 1100 밀리바보다 더 큰 폐활량을 갖는 사람이 매우 
강하게 숨을 불어 넣을 수 있는 경우도 있기 때문이다.

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()
sense.clear()

ratio = 255 / 100.0

while True:
    pressure = sense.get_pressure()
    pressure = round(pressure, 1) - 1000
    blue = int(ratio * pressure)
    if blue > 255:
        blue = 255
    sense.clear(0, 0, blue)
~~~    

### 4. 움직임

#### 4.1. 관성측정장비

관성측정장비(IMU, Inertial Measurement Unit)는 내부에 센서가 3개 구비되어 있다:

* 자이로스코프 (운동량과 회전을 측정)
* 가속도 센서 (가속도를 측정하고 중력 방향을 찾는데 사용)
* 지자계 센서 (지구 자기장을 측정, 나침반과 유사)

<img src="fig/rpi-apollo_imu.jpg" alt="아폴로 우주선 관성측정장비" width="50%" />

운동 센서가 그런데 왜 중요할까? 우주공간에 떠 있을 때, 항상 정답을 알고 있어야 되는 정말 중요한 질문이 있다: **내가 어느 방향을 가리키고 있는가?**

방향을 알지 못하면, 정말 큰 문제에 봉착하게 된다. 위와 같은 IMU 센서는 유인, 무인 우주선에 사용되어 이동한 것을 추적하고 방향을 이해하는데 사용된다. 심지어 초기 우주선에도 장착되어 있다. 할아버지 할머니에게 [아폴로 임무](http://en.wikipedia.org/wiki/Apollo_program)을 기억하는지 물어보라. 아폴로 임무는 인간을 달표면에 착륙시키는 것이다.

아폴로 사령선에 장착된 IMU 센서 그림이 위에 나와 있다. 감각모자에 작착된 아주 작은 검은색 사각형 반도체와 비교할 때 얼마나 작은지 금방 크기가 비교된다; 1975년과 2016년 기술 격차를 보여주고 있다. 크기는 그렇지만, 감각모자에 장착된 IMU는 아폴로 우주선에 장착된 IMU보다 정확도가 떨어질 수 있지만, 백만배나 더 값이 저렴하다.

#### 4.2. 방향 표시 방법

지구가 남극과 북극을 축으로 자전하는 것은 알고 있다. 우주든 지구든 모든 물체는 회전하는데 축이 **3개** 필요하다. 각 축을 따라 얼마나 회전했는지 알고 있다면, 물체가 어느 방향을 가르키고 있는지도 알게 된다.

축 3개는 다음과 같다:

* 피치(Pitch) : 비행기가 이륙하는 것과 유사
* 롤(Roll) : 비행기가 승리를 축하하는 회전 비행과 유사
* 요(Yaw) : 자동차처럼 비행기를 조정하는 것과 유사

[짧은 동영상](https://www.youtube.com/watch?v=pQ24NtnaLl8)에 비행기와 연관되어 각 축이 어떻게 위치하는지 보여주고 있다. 임의 방향을 가르키고 있는 비행기를 상상해 보자. 비행기를 원하는 위치로 가져오려면, 각 축을 알고 있는 정도만큼 회전시키면 된다.

<img src="fig/rpi-orientation.png" alt="감각모자 방향" width="50%" />

감각모자와 연관해서 해당 축이 어디에 해당되는지 상기 그림이 보여주고 있다.

인터넷에서 프로그램을 다운로드 받아 3D 데모 프로그램을 구현해서 체험을 해 보자.

#### 4.3. 아폴로 소유즈 데모

<img src="fig/rpi-apollo_soyuz.jpg" alt="아폴로 소유즈" width="50%" />

다음 그림은 아폴로 소유즈(Apollo Soyuz)[^soyuz] 우주선을 보여주고 있는데 인간을 1970년대 달 표면에 착륙시키는데 사용되었다. 3D 데모는 동일한 우주선이지만 정교성에서 조금 떨어진다.

[^soyuz]: 구소련의 우주 정류장 조립을 목적으로 발사한 우주선

필요한 소프트웨어를 다운로드 받는데 라즈베리파이와 감각모자가 인터넷에 연결되어야 한다.

터미널 윈도우에 아래 명령어를 타이핑한다.

~~~ {.python}
sudo apt-get install python3-pip
sudo pip-3.2 install pi3d
git clone git://github.com/astro-pi/apollo-soyuz
cd apollo-soyuz
sudo ./soyuz.py
~~~

라즈베리파이 1을 갖는 사용자는 3D 데모 프로그램이 뜨는데 약 3~4분이 걸리고, 
라즈베리파이 2는 약 30초가 걸린다. 우주선이 화면에 나타나면 손에 감각모자가 장착된 라즈베리파이를 
들고 이리저리 움직인다. 주추진로켓이 라즈베리파이 SD카드가 꽂힌 곳이다.

우주선을 갖고 피치, 롤, 요 움직임을 직접 체험한다. 어느 방향이 어느 방향인지 헤깔리면, [짧은 동영상](https://www.youtube.com/watch?v=pQ24NtnaLl8)을 다시 참조한다.

3D 데모 코드는 기본적으로 감각모자 IMU 센서에 접근해서 `get_orientation` 함수를 호출한다. 그러면 IMU 센서가 각 축(피치, 롤, 요)에 대해 0에서 360 사이 값을 각도로 반환한다. 그리고 나면 우주선 모형이 IMU 센서 호출된 각도만큼 회전해서 동일한 방향을 가리키게 된다. 매우 빠르게 반복해서 우주선 모형과 IMU 센서가 전달하는 것과 같은 방향을 유지하게 된다.

`Esc` 키를 눌러 데모를 빠져나온다. 좀더 간단한 코드를 살펴보자.


#### 4.4. 내가 어느 방향을 가리키고 있는가?

1. 다음 명령어를 루트 권한 `sudo` 으로 **파이썬3(Python 3)** 를 터미널 윈도우에서 연다.

~~~ {.python}
sudo idle3 &
~~~

2. `File > New Window` 로 새로운 창을 열고 다음 코드를 입력한다:

~~~ {.python}
from sense_hat import SenseHat
sense = SenseHat()
sense.clear()

o = sense.get_orientation()
pitch = o["pitch"]
roll = o["roll"]
yaw = o["yaw"]
print("pitch %s roll %s yaw %s" % (pitch, roll, yaw))
~~~

3. `File > Save` 메뉴를 선택하고 상기 파이썬 프로그램에 대한 파일명을 지정하고 저장한다. 

4. `Run > Run module` 선택해서 파이썬 프로그램을 실행시킨다.

5. `IMU Init Failed, please run as root / use sudo` 오류 메시지(파이썬 코드 실행 마지막 줄에 붉은색 글씨)가 뜨게 되면, 상기 절차를 충실히 따르지 않는 것으로 볼 수 있다. 모든 것을 닫고, 첫번째 단계부터 다시 시작한다.

6. 결과가 다음과 같이 나오면 성공이다.

~~~ {.output}
IMU Init Succeeded
pitch 356.35723002363454 roll 303.4986602798494 yaw 339.19880231669873
~~~

7. 소수점 아래 모든 숫자는 필요없으니 반올림한다. `print("pitch %s roll %s yaw %s" % (pitch, roll, yaw))` 행 바로 앞에 다음 코드를 추가한다:

~~~ {.python}
pitch = round(pitch, 1)
roll = round(roll, 1)
yaw = round(yaw, 1)
~~~

#### 4.5. 시간에 따른 이동방향 모니터링

1. 운동방향 변화를 모니터링하는 것이 필요해서 `while` 루프에 위에서 작성한 코드를 넣고, 재실행한다.

~~~ {.python}
while True:
    o = sense.get_orientation()
    pitch = o["pitch"]
    roll = o["roll"]
    yaw = o["yaw"]

    pitch = round(pitch, 1)
    roll = round(roll, 1)
    yaw = round(yaw, 1)

    print("pitch %s roll %s yaw %s" % (pitch, roll, yaw))
~~~

2. 손에 라즈베리파이를 들고서 움직이면, 숫자가 바뀌는 것이 보인다. 예를 들어 피치방향으로만 축을 변경시켜본다. 다른 축에도 동일한 작업을 수행한다.


4. `Ctrl + C` 키를 눌러 프로그램을 종료시킨다.

#### 4.5. LED 전광판에 운동방향정보 출력

1. 항상 3차원에 있는 것을 2차원으로 표시하는 것은 쉽지 않다. 특히 전광판 크기가 $8 \times 8$ 픽셀 크기를 갖는 경우는 더욱 그렇다. 이것을 구현하는 한방식은 각축마다 LED 하나를 갖추고 다른 방식으로 이동시키는 것이다. 예를 들어,

* 피치 LED는 위와 아래를 표현한다.
* 롤 LED는 옆으로 표현한다.
* 요 LED는 가장자리를 따라간다.

2. 다음에 조금더 똑똑한 기법이 나와 있다. 가로축을 따라 LED 숫자가 배치된 표가 나와 있다.


| 0  | 1  | 2  | 3  | 4  | 5  | 6  |  7 |
|----|----|----|----|----|----|----|----|
| 8  | 9  | 10 | 11 | 12 | 13 | 14 | 15 |
| 16 | 17 | 18 | 19 | 20 | 21 | 22 | 23 |
| 24 | 25 | 26 | 27 | 28 | 29 | 30 | 31 |
| 32 | 33 | 34 | 35 | 36 | 37 | 38 | 39 |
| 40 | 41 | 42 | 43 | 44 | 45 | 46 | 47 |
| 48 | 49 | 50 | 51 | 52 | 53 | 54 | 55 |
| 56 | 57 | 58 | 59 | 60 | 61 | 62 | 63 |

어떤 숫자도 다음 코드를 통해 $(X, Y)$ 좌표로 전환한다.

~~~ {.python}
y = number // 8
x = number % 8
~~~

어떠한 `y` 값에 대해서도 8로 `//` 나눈다. 정수만 취하고, 나머지는 버린다.
그리고 `x`에 대해서, 8로 `%` 나머지 연산을 수행하고, **나머지만** 취한다.

예를 드어 (가장 마지막 줄에 위치한 60을 사용해보자)

* `60 // 8 = 7`
* `60 % 8 = 4`

3. 파이썬 코드로 돌려본다:

~~~ {.python}
number = 60

y = number // 8
x = number % 8

sense.set_pixel(x, y, 255, 255, 255)
~~~

4. 앞뒤로 이동하는 경로에 LED 숫자를 포함하는 리스트를 생성하는 것도 좋은 전략이다.
그렇게 해서 가장자리를 LED가 따라 돌게 만든다. 숫자는 상기 표 위에서 왼쪽에서 시작해서 오른쪽으로,
그리고 우측 가장자리를 따라 아래쪽으로, 표 하단에서는 역방향으로, 마지막으로 표 왼쪽에서는 위쪽방향으로 돈다.

~~~ {.python}
edge = [0, 1, 2, 3, 4, 5, 6, 7, 15, 23, 31, 39, 47, 55, 63, 62, 61, 60, 59, 58, 57, 56, 48, 40, 32, 24, 16, 8]
~~~

`len` 함수를 사용해서 리스트 길이를 알아낸다.

~~~ {.python}
length = len(edge)
~~~

길이는 `28`이 된다. 28을 360으로 나누면, 요 측정값과 리스트 위치 사이 비율이 된다. 그리고 나서 순차 픽셀 숫자를 얻어, 좌표를 식별하고 나서 LED 스위치를 켠다. 

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()
sense.clear()

edge = [0, 1, 2, 3, 4, 5, 6, 7, 15, 23, 31, 39, 47, 55, 63, 62, 61, 60, 59, 58, 57, 56, 48, 40, 32, 24, 16, 8]
length = len(edge)
ratio = length / 360.0

while True:
    o = sense.get_orientation()
    pitch = o["pitch"]
    roll = o["roll"]
    yaw = o["yaw"]

    yaw_list_position = int(yaw * ratio)

    yaw_pixel_number = edge[yaw_list_position]

    y = yaw_pixel_number // 8
    x = yaw_pixel_number % 8

    sense.set_pixel(x, y, 255, 255, 255)
~~~

5. 상기 코드는 LED를 켜는 것만 있어, LED를 끄는 것을 알아내야 한다.
루프를 돌며 마지막에 이전 `x`, `y`를 기억하는 변수를 준비하고, 새로운 `x`, `y`와 다르면 `set_pixel` 함수를 사용해서 LED를 검정색으로 설정한다.


### 5. 조이스틱

감각모자 조이스틱은 네방향 키보드 방향키와 조이스틱 중간클릭은 엔터키에 매핑된다.
따라서 조이스틱이 키보드 방향키와 엔터키를 누르는 것과 정확하게 동일한 효과를 갖는다.
아래쪽 방향은 HDMI 포트 아래쪽으로 향하는 것을 기억한다.

<img src="fig/rpi-cursor_keys.jpg" alt="조이스틱 키보드 매핑" width="50%" />

#### 5.1. 키보드 매핑


1. 다음 명령어를 루트 권한 `sudo` 으로 **파이썬3(Python 3)** 를 터미널 윈도우에서 연다.

~~~ {.python}
sudo idle3 &
~~~

2. 파이썬 쉘 윈도우가 나타난다. 터미널 윈도우를 닫아도 된다.

3. `File > New Window` 로 새로운 창을 열고 나서,

4. 다음 코드를 입력한다:

~~~ {.python}
import pygame

from pygame.locals import *
from sense_hat import SenseHat

pygame.init()
pygame.display.set_mode((640, 480))
sense = SenseHat()
sense.clear()

running = True

while running:
    for event in pygame.event.get():
        print(event)
        if event.type == QUIT:
            running = False
            print("BYE")
~~~

5. `File > Save` 메뉴를 선택하고 상기 파이썬 프로그램에 대한 파일명을 지정하고 저장한다. 

6. `Run > Run module` 선택해서 파이썬 프로그램을 실행시킨다.

    키보드 키를 눌렀는지는 [pygame](http://www.pygame.org/docs/) 파이썬 모듈를 사용해서 처리했다.

7. 배경이 까만 윈도우가 나타난다. 마우스를 사용해서 스크린 한쪽면으로 이동시켜, 파이썬 쉘 윈도우도 함께 볼 수 있도록 화면을 정리한다.

8. 배경이 까만 윈도우를 선택해서 활성화 시키고 마우스를 이리저리 이동시키고, 키보드 키를 눌렀다가 떼고, 감각모자 조이스틱도 이리저리 움직인다. 한동안 키를 눌르고 잠시 멈추고나서 몇초뒤에 풀어준다. 이벤트 두개가 발생하는 것이 보일 것이다; 키가 눌렀을 때 이벤트와 키를 풀었을 때 이벤트. 상기 프로그램에서는 키를 눌렀을 때 **KEY DOWN** 이벤트만 사용했다.

9. 배경이 검은 pygame 윈도우 모퉁이에 `x` 를 클릭한다. 파이썬 쉘 윈도우에 `BYE` 메시지가 보이지만, 배경이 까만 윈도우는 사라지지 않는다.

`for event in pygame.event.get():` 구문을 사용해서 pygame 이벤트 큐를 사용했다.
모든 키보드 마우스 이벤트에 대한 루프를 돌린다.
루프 내부에 `print(event)`를 사용해서 어떤 이벤트인지 화면에 출력하고 나서 이벤트 유형이 `QUIT`인지도 검사한다.
만약 `QUIT`라면, `running` 상태가 `False`로 되고, `while` 루프가 종료되고 프로그램이 끝나게 된다.
마우스를 이동시키고, 마우스를 클릭하고, 키보드를 누르거나 풀게 되면 파이썬 쉘에 텍스트를 출력하는 것이 위에 작성된 프로그램이다.

#### 5.2. 코드로 조이스틱 이동 감지하기

조이스틱이 어떻게 동작하는지 생각해보자.
LED 전광판을 사용해서 조이스틱 작동방식에 대해 생각해본다.
픽셀을 흰색으로 만들고, 조이스틱을 사용해서 픽셀이 $8 \times 8$ 크기 전광판을 돌아다니게 한다.
이를 구현하는데, 이벤트를 사용해서 키가 눌렸는지 탐지한다. 예를 들어, 키가 아래로 눌린 경우, 픽셀을 이동시키는데 어떤 단계가 필요할까?

> * 현재 `x`, `y` 위치 정보를 사용해서 LED를 OFF 해서 끈다.
> * 아래 `DOWN`이 눌러진 경우 `y`에 1을 더한다.
> * 위 `UP`이 눌러진 경우, `y`에서 1을 뺀다.
> * 우측 `RIGHT`이 눌러진 경우, `x`에 1을 더한다.
> * 좌측 `LEFT`가 눌러진 경우, `x`에서 1을 뺀다.
> * 갱신된 `x`, `y` 위치 정보를 사용해서 LED를 ON 해서 켠다.

1. 아래 DOWN 키에 대해 코드를 추가하면서 코딩을 시작한다. 이전 프로그램에서 사용된 `print(event)` 명령어를 삭제하고, 들여쓰기 수준을 동일하게 만들고 코드를 아래와 같이 추가시킨다:

~~~ {.python}
if event.type == KEYDOWN:
    sense.set_pixel(x, y, 0, 0, 0)  # 검정색 0,0,0 이 OFF를 의미

    if event.key == K_DOWN:
        y = y + 1

        sense.set_pixel(x, y, 255, 255, 255)
~~~


2. 저장하고 코드를 실행시킨다. 키보드 아래 방향키 `DOWN`키 혹은 조이스틱을 사용해서 픽셀을 아래로 이동 시킬 수 있다.
계속하게 `DOWN` 방향키를 누르게 되면, 종국에는 다음과 같은 오류가 발생된다.

~~~ {.error}
ValueError: Y position must be between 0 and 7
~~~

3. `y` 값은 0-7 사이만 가능하다. 그렇지 않는 경우 전광판 가장자리에서 떨어져 나가게 된다.
그런 이유로 인해 오류가 발생된다; 감각모자는 범위 밖에 있는 값은 이해하지 못한다.
방향키 `DOWN`을 누를 때마다 `y`에 1이 계속 추가되는 구조라서, `y`가 7 이상 넘어가게 되면 멈출 필요가 있다.

`and y < 7` 구문(`y`가 7보다 작음)을 추가하면 문제가 해결된다.

~~~ {.python}
if event.key == K_DOWN and y < 7:
    y = y + 1
~~~

4. 저장하고 나서 코드를 다시 실행시킨다; 이번에는 전광판 가장자리로 하얀점이 이동되지 않게 된다. 

5. 이제 정상적으로 동작하기 때문에, 조이스틱에 다른 방향을 추가할 필요가 있다.
코드에 `if event.key == K_DOWN:` 이 추가되면, 다른 것도 정의해서 추가한다:

* `K_UP`
* `K_LEFT`
* `K_RIGHT`
* `K_RETURN`

6. 각 방향에 대해 코드를 추가해서 전체 코드를 완성한다.

~~~ {.python}
import pygame

from pygame.locals import *
from sense_hat import SenseHat

pygame.init()
pygame.display.set_mode(640, 480)

sense = SenseHat()
sense.clear()

running = True

x = 0
y = 0
sense.set_pixel(x, y, 255, 255, 255)

while running:
    for event in pygame.event.get():
        if event.type == KEYDOWN:
            sense.set_pixel(x, y, 0, 0, 0)  # Black 0,0,0 means OFF

            if event.key == K_DOWN and y < 7:
                y = y + 1
            elif event.key == K_UP and y > 0:
                y = y - 1
            elif event.key == K_RIGHT and x < 7:
                x = x + 1
            elif event.key == K_LEFT and x > 0:
                x = x - 1

        sense.set_pixel(x, y, 255, 255, 255)
        if event.type == QUIT:
            running = False
            print("BYE")
~~~

7. 코드를 실행시키면, 이제 LED 전광판 어느 곳이든 흰색 픽셀을 이동시킬 수 있다.


### 6. LED 전광판

감각모자 LED 전광판에는 LED 64개가 박혀있다. LED 64개 각각은 실제로 내부에 더 작은 LED가 세개 들어 있는데, TV, 모니터, 스마트폰 화면과 유사하다.

#### 6.1. 색상 섞기 

<img src="fig/rpi-additive_color_mixing.png" alt="가색 색상 모형" width="50%" />

가색 색상 모형에서는 색상 세가지(적색, 녹색, 청색)를 섞어 사용해서 다른 색을 만들어 낸다.
상기 그림에서 동일한 휘도를 갖는 적색, 녹색, 청색 색상을 스폿라이트로 환하게 비추고 있다.
어떤 색도 없는 경우는 결과가 검정색으로 나타난다. 세가지 색상이 모두 합쳐지게 되면, 결과는 흰색이 된다.
적색과 녹색이 섞이게 되면 결과는 노란색이 된다. 적색과 청색이 결합되면, 결과는 마젠타 자홍색이 된다. 
청색과 녹색이 섞이게 되면 결과는 시안 청록색이 된다. 세가지 원색의 휘도를 바꿔서 더 많은 색상을 만드는 것이 가능하다.


1. 다음 명령어를 루트 권한 `sudo` 으로 **파이썬3(Python 3)** 를 터미널 윈도우에서 연다.

~~~ {.python}
sudo idle3 &
~~~

2. `File > New Window` 로 새로운 창을 열고 다음 코드를 입력한다:

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()

r = 255
g = 255
b = 255

sense.clear((r, g, b))
~~~

3. `File > Save` 메뉴를 선택하고 상기 파이썬 프로그램에 대한 파일명을 지정하고 저장한다. 

4. `Run > Run module` 선택해서 파이썬 프로그램을 실행시킨다.

5. LED 전광판이 흰색으로 변한다.

6. `r`, `g`, `b` 변수는 적색, 녹색, 청색을 나타낸다.
변수가 갖게 되는 숫자는 각 색상이 얼마나 밝은지를 지정한다; 변수가 갖는 범위는 0 에서 255 사이다.
상기 코드에서 각 색상마다 갖을 수 있는 최대값이 사용되었다. 그래서 결과는 흰색이다.

7. 값을 바꿔서 적색은 255, 녹색과 청색은 0 으로 두고 나서, 다시 코드를 실행시킨다.

8. 다른 색상은 어떻게 만들 수 있을까?

#### 6.2. 배경과 전경 색상 바꾸기

이러한 색상섞기 시스템이 라즈베리파이 프로그래밍에 사용된다. 스크롤링 되는 텍스트를 프로그래밍하는데 색상 섞기를 사용한다. 다음 예제에서, 전광판에 나타나는 텍스트 색상을 설정한다.

1. 새로운 파이썬 프로그램 파일을 만들고 다음 코드를 타이핑한다:

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()

sense.show_message("Hello my name is Tim Peake", text_colour=(255, 0, 0))
~~~

2. 다음과 같이 배경색도 변경시킬 수 있다.

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()

sense.show_message("Hello my name is Tim Peake", text_colour=(255, 255, 0), back_colour=(0, 0, 255))
~~~

**콤마는 매우 중요하다. 콤마 찍는 것을 잊지말라!!!**

#### 6.3. 픽셀

<img src="fig/rpi-closeup_of_pixels.jpg" alt="픽셀 가까이서 보기" width="50%" />

상기 이미지는 노트북 컴퓨터 LCD 화면에 픽셀을 보여주고 있다. 픽셀이 켜지고 꺼지면서 문자와 숫자 패턴을 만들어 내는 것이 보인다.

이런 방식으로 모든 컴퓨터와 스마트폰 스크린이 동작한다. 감각모자 LED 전광판에 인식가능한 모양을 만들어 내려면, 여러분도 이런 작업을 수행할 필요가 있다. 감각모자에 포함된 LED 전광판은 $8 \times 8$ 픽셀 해상도를 갖는다. 따라서 형상과 아이콘을 만들게 되면 꽤 거칠게 보인다. 이런 점이 프로그래밍을 하는 것이 도전과제로 다가오게 된다!


1. `File > New Window` 로 새로운 창을 열고 나서,

2. 다음 코드를 입력한다:

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()

sense.clear()

x = 0
y = 0

sense.set_pixel(x, y, 255, 255, 255)
~~~

3. `File > Save` 메뉴를 선택하고 상기 파이썬 프로그램에 대한 파일명을 지정하고 저장한다. 

4. `Run > Run module` 선택해서 파이썬 프로그램을 실행시킨다.

5. 모퉁이에 LED 하나가 흰색으로 켜진다.

6. 필요하면 색상을 바꿀 수도 있다는 것을 기억한다.

#### 6.4. 좌표를 사용해서 픽셀 위치를 지정


`x`, `y` 변수를 사용해서 어떤 개별 LED를 변경시킬지 `set_pixel` 명령어로 제어한다.
**X** 는 수평으로 *왼쪽* 은 `0`, *오른쪽* 은 `7` 범위를 갖는다. 
**Y** 는 수직으로 *윗쪽* 은 `0`, *아래쪽* 은 `7` 범위를 갖는다. 
따라서, `x, y` 좌표 `0, 0`은 *좌측 최상단* 이 되고,
 `x, y` 좌표 `7, 7`은 *우측 최하단* 이 된다.

<img src="fig/rpi-coordinates.png" alt="픽셀 좌표" width="50%" />

LED 전광판 각 모퉁이를 다른 색상으로 채색한다.
다음과 같이 `set_pixel` 명령어를 여러번 사용한다:

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()

sense.clear()
sense.set_pixel(0, 0, 255, 0, 0)
sense.set_pixel(0, 7, 0, 255, 0)
sense.set_pixel(7, 0, 0, 0, 255)
sense.set_pixel(7, 7, 255, 0, 255)
~~~

#### 6.5. LED 전광판에 형상과 패턴을 그리기

코드로 `set_pixel` 명령어를 사용해서 형상과 패턴을 그리고 싶을 것이다. 
`set_pixel` 명령어가 있다. 이 명령어를 가지고 코드 한줄로 전체 64개 LED를 조정할 수 있다.
예를 들어, 마인크래프트 클리퍼 얼굴(Creeper face)을 LED 전광판에 표시한다:

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()

O = (0, 255, 0) # 녹색
X = (0, 0, 0) # 검정색

creeper_pixels = [
    O, O, O, O, O, O, O, O,
    O, O, O, O, O, O, O, O,
    O, X, X, O, O, X, X, O,
    O, X, X, O, O, X, X, O,
    O, O, O, X, X, O, O, O,
    O, O, X, X, X, X, O, O,
    O, O, X, X, X, X, O, O,
    O, O, X, O, O, X, O, O
]

sense.set_pixels(creeper_pixels)
~~~

마인크래프트 스티브 예제처럼 두가지 색깔 이상을 사용할 수도 있다.

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()

B = (102, 51, 0)
b = (0, 0, 255)
S = (205,133,63)
W = (255, 255, 255)

steve_pixels = [
    B, B, B, B, B, B, B, B,
    B, B, B, B, B, B, B, B,
    B, S, S, S, S, S, S, B,
    S, S, S, S, S, S, S, S,
    S, W, b, S, S, b, W, S,
    S, S, S, B, B, S, S, S,
    S, S, B, S, S, B, S, S,
    S, S, B, B, B, B, S, S
]

sense.set_pixels(steve_pixels)
~~~

#### 6.6. 파일에서 이미지 불러오기

LED 전광판을 일일이 설정하는 것 대신에, 파일에서 불러온 이미지를 사용할 수도 있다.
전세계 국기처럼 이미지를 많이 갖는 경우 편리한 기능이 된다.

1. 윈도우, 맥 OSX, 리눅스에서 이미지 편집도구를 사용해서 파일을 생성한다.
라즈베리파이에 $8 \times 8$ 픽셀 크기로 `JPEG` 혹은 `PNG`로 SD카드에 저장만 되면,
LED 전광판에 명령어 하나도 직접 불러올 수 있다.

2. `Menu > Accessories > File Manage`메뉴를 사용해서 라즈베리파이 `파일관리자(File Manager)`를 연다.

3. `astro-pi-hat` 폴더에 `examples` 폴더로 찾아 들어간다. 더블클릭할 수 있는 `space_invader.png` 파일을 폴더에서 찾아낸다.

4. `load_image` 함수를 사용해서 감각모자 LED 전광판에 이미지를 불러온다. 파일을 불러올 때 전체 파일 경로 정보가 필요하다. `space_invader.png` 파일이 있는 전체 경로는 `/home/pi/astro-pi-hat/examples/space_invader.png`이 된다.

다음에 코드가 나와 있다.

~~~ {.python}
from sense_hat import SenseHat

sense = SenseHat()

sense.load_image("/home/pi/astro-pi-hat/examples/space_invader.png")
~~~