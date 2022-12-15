# 군집 주행

> 밑의 사진처럼 두 대 이상의 트럭이 하나로 연결된 것처럼 운행하는 기술로 자율주행 기술과 함께 차량 연결 기술이 복합적으로 적용된 주행을 목표로 한다.
이 프로젝트는 완전 자율 주행의 전 단계인 제일 앞 트럭은 사람이 운전하고 뒤 트럭이 따라가는 시나리오다.

- 이 프로젝트로 연비 증가, 배출 가스 감소, 도로 정체 해소, 운전 인력 대체 효과가 있을 것으로 보인다.

![8499_12973_3652](https://user-images.githubusercontent.com/81899557/206140450-86746584-f7c0-4044-9902-0d6c30f2c605.jpg)

## 팀원

- 20197120 이병규
- 20197118 강지훈
- 20197124 하상욱


## 조립 및 설치 방법

공식 사이트 : http://www.yahboom.net/study/JETBOT  


사용하는 기기는 jetson nano 4GB이다.  

![image](https://user-images.githubusercontent.com/64821752/207927716-ed039317-af13-46b7-85fb-510156338190.png)

First Trial V1.0에서 Case-A에 해당하는 경우이다.  

1) SD카드를 리더기에 삽입후 컴퓨터에 연결한다.  

2) 인식된 SD카드를 포맷  

3) 이미지 버너 설치  
https://www.balena.io/etcher/  

4) Jetbot image를 다운로드 후 SD카드에 Jetbot이미지를 굽는다.  
맞지 않는 image를 선택한 경우 SD카드의 용량을 다 쓰지 못하거나 동작및 앱 실행시 문제가 생기는 것을 확인하였다.  
ex) 64GB중 32GB만 쓸 수 있다.  
 
5) Yahboom_jetbot_64G_20200520를 사용한다.

6) 초기 비밀번호는 yahboom으로 Jupyter lab을 설치하기 위해 pip install jupyterlab을 실행한다.  


## 직진 및 정지

![KakaoTalk_20221207_172759268](https://user-images.githubusercontent.com/81899557/206127508-f0c9ba91-8f95-4d52-8535-e49d1224c590.gif)

## 커브

![KakaoTalk_20221207_173438398](https://user-images.githubusercontent.com/81899557/206128837-08821e96-3982-45dc-8cb3-6ab592df5983.gif)

## 코드 사용, 업데이트 내용

- 사용할 모델 불러오기

```python
from jetbot import ObjectDetector

model = ObjectDetector('ssd_mobilenet_v2_coco.engine')
```

- 자동차 label로 변경, speed와 turn_gain값을 변경해서 객체 탐지 후 따라가게 설정

```python
from jetbot import bgr8_to_jpeg

blocked_widget = widgets.FloatSlider(min=0.0, max=1.0, value=0.0, description='blocked')
image_widget = widgets.Image(format='jpeg', width=300, height=300)
label_widget = widgets.IntText(value=1, description='tracked label')
speed_widget = widgets.FloatSlider(value=0.4, min=0.0, max=1.0, description='speed')
turn_gain_widget = widgets.FloatSlider(value=0.8, min=0.0, max=2.0, description='turn gain')

display(widgets.VBox([
    widgets.HBox([image_widget, blocked_widget]),
    label_widget,
    speed_widget,
    turn_gain_widget
]))

```

![KakaoTalk_20221207_175717784](https://user-images.githubusercontent.com/81899557/206133960-1353716f-4e31-457a-a563-69e842c2f4c1.gif)

###### - 초록색 바운딩 박스 탐지 시 속도 업데이트
###### - 탐지 실패 속도 0으로 업데이트

- forward 0으로 수정해 자동차 label이 아닌 객체가 탐지되면 스피드를 0으로 바뀌며 멈춤

```python
# otherwise go forward if no target detected
    if det is None:
        robot.forward(0)
```
<img src= "https://user-images.githubusercontent.com/64821752/207931416-5a71f861-f52f-4408-b46a-7c642937667e.png" width="600" height="540"/>
