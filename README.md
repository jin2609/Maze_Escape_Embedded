# Maze_Escape_Embedded
- 충남대학교 컴퓨터공학과 김진섭
- 2019년도 봄학기 임베디드 소프트웨어
- Embedded Challenge 2등
---------------------------------------------------------------------  
## 대회 소개
- ubrain을 활용하여 랜덤하게 만든 미로의 벽에 부딪히지 않고 통과하는 자율주행 Embedded 구현 대회
- 순위는 빠르게 통과하는 순서로 매김

----------------------------------------------------------------------
## 설계
1. 주행전략  
- 안전성  
(초음파 센서)     
초음파 센서는 ubrain의 좌 우 그리고 앞에 있습니다. 따라서, 초음파 센서를 활용하여 이동하며 전방에 장애물 이 있는지 탐지하고 장애물이 있다면 좌측 혹은 우측으로 방향을 전환하여 장애물을 피해갑니다.     
(IR 센서)  
Calibration을 정확히 해 주어도 바퀴가 틈에 걸리는 경우, uBrain의 배터리의 잔량 등의 예외상황 때문에 정확 히 장애물들을 피해 이동하기가 어렵습니다. 초음파 센서는 ubrain의 전방과 좌 우 방향의 장애물만 탐지가 가 능하기 때문에 대각선 방향으로 점차 가까워지는 경우에 물체 감지를 할 수 없습니다. (대각선 방향으로 가까워 지는 경우는 정확히 90도 회전을 못하거나, 직선으로 이동을 하지 못하는 경우입니다.) 따라서 저희는 좌측 우측 45도 방향에 있는 적외선 센서를 활용하여 1자로 이동을 하지 못하고, 약간 우측 혹은 좌측으로 치우친 이동을 해서 장애물과 부딪히게 되는 것을 방지하기 위하여 적외선 센서를 활용하여 약간의 방향 조정을 해주는 방법으로 구현을 하였습니다.  
- 목표 성취를 위한 주행 전략   
(uBrain의 방위)   
uBrain은 자신이 지금 어디로 움직이고 있는지 인지를 하지 못합니다. 즉 시작을 해서 좌측 90도 돌아서 움직여 도 ‘앞으로 간다’고 생각하지, ‘시작방향의 좌측으로 이동하고 있다.’ 라고 생각하지 않습니다. 따라서 저희는 좌 측 혹은 우측으로 가는 경우를 나누어 uBrain이 지금 어느 방위로 이동을 하고 있는지 판단하도록 구현하였습니 다. uBrain이 시작되는 지점의 전방이 북쪽 후방이 남쪽 우측이 동쪽 좌측이 서쪽으로 판단하여 현재 어느 방향 으로 uBrain이 이동하고 있는지 알도록 구현하여, 상황에 따른 방향 수정을 할 수 있도록 구현하였습니다.  
(주행 전략)  
위에서 언급하였듯이 uBrain이 현재 어느 방향으로 이동하고 있는지(방위)를 알 수 있도록 구현을 하였습니다. 따라서, 방위에 따라 uBrain이 어느 방향으로 회전을 하는 것이 바람직한지를 알 수 있습니다. 저희는 uBrain을 최대한 빨리 완주를 할 수 있도록, 무조건 북쪽으로(목적지)로 갈 수 있도록 알고리즘을 개발하 였습니다. 서쪽으로 동쪽으로 이동을 하는 경우에는 북쪽으로 가능 방향의 초음파 센서를 확인하여 북쪽으로 갈 수 있는 길이 있다면, 무조건 빠르게 그 길을 갈 수 있도록 코드를 구현하였습니다. 즉, 서쪽으로 가는데, 오른쪽 초음파 센서를 확인 결과 북쪽으로 가는 길이 있다면 바로 그 길을 선택하여 이동 을 하고, 동쪽으로 가는데 왼쪽 초음파 센서를 확인 결과 북쪽으로 가는 길이 있다면 그 길을 선택해 이동하는 것입니다. 또한 장애물을 만나 이동을 할 때도 북쪽에 장애물이 없다면 무조건 북쪽으로 회전하여 이동을 하도 록 구현을 하였습니다. 요약을 하면, 저희의 주행 전략은 빠른 목표 도달이었으며, 그 방법을 위해 uBrain의 방위를 주고 그 방위 중 목 표지점이 있는 곳(북쪽)으로 최대한 다가가려고 노력하는 알고리즘을 구현하였습니다.  
(정확한 Calibration)   
Calibration을 정확히 해 주지 못하면 90도로 직각으로 회전을 하지 못하고, 직선방향으로 이동이 어렵습니다. 그렇게 되면 장애물을 만나게되고 장애물을 만나면 IR센서에 의해 약간의 거리를 보정해야하며 그 거리를 보정 하기 위해 잠시 멈추어야하는 상황이 됩니다. 저희의 목표인 ‘최대한 빨리 목표에 도달’을 달성하기 위해서는 이 렇게 보정을 위해 잠시 멈추는 상황을 최대한 적게 할 필요가 있다고 생각하여 정확한 Calibration을 해주려 노 력하였습니다. 정확히 직선방향으로 이동하기 위하여 Palse값을 조절 하였고, 회전되는 회전율도 계속 변경하여 최대한 90도가 될 수 있도록 수 백번 계속 uBrain에 코드를 로드하고 테스트하는 것을 노력하였습니다.  
2. 전략의 우수성  
- 안전성   
저희의 전략은 초음파 센서를 활용하여 전방에 있는 장애물을 감지하고 추가로 적외선 센서를 활용하여 대각의 장애물들이 점차 가까워지는 것을 확인하고 방향 조정으로 부딪히는 것을 방지하여 최대한 안전하게 움직이도 록 노력하였습니다. 요약 – 초음파 센서와 적외선 센서로 장애물을 감지하여 안전을 우선시하는 전략을 구사하였습니다.  
- 최대한 빠르게 목표에 도달하기
저희의 두 번째 전략은 최대한 빨리 목표지점에 도달하기 위해서 ubrain이 해야 하는 노력에 대하여 생각하였습 니다. 따라서, 무조건 벽이 앞에 보이면 고개를 돌리고 가는 것 보다, 목표지점으로 갈 수 있는 길이 있다면 그 곳으로 가는 것으로 약간의 시간 단축을 하려고 노력하였습니다. 두 번째로 항상 방위를 주어 갈림길에 놓여있 는 상황일 때 어디로 가는 것이 빠를지 판단하여 회전을 하도록 만들었습니다. 마지막으로 Calibration을 정확하 게 해 주어 약간 비스듬하게 가서, 적외선 센서에 의해 방향 조정을 하는 시간을 단축시키고자 노력하였습니다. 요약 – 목표지점으로 가는 길이 있다면 그 길을 바로 활용하고, 갈림길에서도 방위를 알고 목표지점을 찾아서 이동하며, Calibration을 최대한 정확히 해서 방향 조정을 최소화하는 전략으로 최대한 빠르게 트랙을 통과하는 전략을 구사하였습니다.  
3. 테스크 구성 및 설계 
- 테스크 구성   
총 두 개의 테스크를 구성하였습니다. 장애물을 탐지하여 방향을 설정하는 테스크, 모터를 제어하는 테 스크 이렇게 두 개의 테스크로 나누어 구성하였습니다. 장애물 탐지 및 방향을 설정하는 테스크에서 장애물을 탐지하면 현재 ubrain의 상황(방위, 장애물 위치)에 따라 움직여야하는 것을 flag로 제시하면 모터 제어 테스크 에서 이를 통해 움직임을 제어하여주는 방식으로 구성하였습니다. 테스크를 두 개만 구성하여 최대한 시스템의 불안전성을 막으려고 노력하였습니다.  
- 장애물 탐지 및 방향 설정 테스크(테스크1)  
장애물을 탐지하고 방향을 설정하는 테스크는 현재 ubrain이 어느 방위로 움직이는지, 주변의 장애물이 어디에 위치하는지에 따라 방향을 제시하게 됩니다. 테스크에 대하여 간단히 설명을 드리도록 하겠습니다. - 장애물이 앞에 있는 경우(초음파 센서로 바로 앞에 장애물이 탐지된 경우) 이 경우에는 왼쪽, 오른쪽 초음파 센서를 활용해 좌우측에 벽이 있는지 감지합니다. 그리고 좌우측에 벽이 있다 면 최대한 그 벽을 피해가는 방향으로 회전을 하며 벽이 좌우측에 모두 없는 경우(갈림길)는 방위를 통해 나아 갈 목표지점을 찾아가기 위한 flag를 세팅하여주고, 벽이 있다면 그 벽을 피해 가기 위해 flag세팅을 합니다. - 장애물이 앞에 없는 경우(초음파 센서로 바로 앞에 장애물이 탐지되지 않은 경우) 장애물이 앞에 없더라도, ubrain이 움직이는 방위에 따라서, 어느 방향으로 움직일지 계산합니다. 만약 동쪽으로 움직이고 있다면 초음파 센서의 좌측을 탐지하여 좌측에 통로가 생기면 바로 좌측 길을 활용하고, 서쪽으로 움 직이고 있다면 초음파 센서의 우측을 탐지해 우측 통로가 생기면 바로 우측길을 이용합니다. - IR센서로 장애물이 가까움을 감지한 경우(대각선의 장애물이 가까워지는 경우) IR센서로 장애물이 가까워짐을 탐지하면 약간만 회전을 해주어 장애물을 피해가도록 합니다. 그 외의 경우는 모터를 항상 앞으로 갈 수 있도록 FRONT로 설정하여줍니다.   
- 모터 제어 테스크(테스크2)   
모터를 제어하는 테스크는 장애물 탐지 및 방향 설정 테스크에서 세팅된 값을 확인하여 그 설정에 맞추어 움직 임을 제공합니다. Motor를 제어하기 위해서 왼쪽 오른쪽으로 90도 회전하는 것과 약간만 회전하는 함수를 설계 하였습니다. 이 때 90도 회전시에는 방위를 설정하는 역할까지 수행하도록 합니다.   
 ----------------------------------------------------------------------
## 구현 알고리즘
1. 장애물 탐지 및 방향 설정 테스크 알고리즘  
![flow chart](flowchart.PNG)  
초음파 센서를 활용하여 앞에 물체가 있는지 확인합니다.  
- 앞에 물체가 있는 경우   
좌 우측에 벽이 있는지 확인을 합니다.  
(1) 좌 우측에 벽이 모두 없는 경우 방위에 따라 움직일 방향을 정해줍니다. 방위가 동쪽이면 좌측으로 그 외에 경우는 모두 우측으로 설정합니다.    (2) 좌 우측에 벽이 하나 혹은 2개 다 있는 경우 오른쪽에 벽이 있다면, 오른쪽 벽을 피해 왼쪽으로 그 외에 경우 모두 오른쪽으로 설정하여줍니다.      
- 앞에 물체가 없는 경우   
IR센서가 감지하였는지 확인하여 줍니다.   
(1) IR 센서가 감지한 경우 감지된 센서의 반대방향으로 약간의 보정을 해 올바르게 길을 갈 수 있게 설정합니다.   
(2) IR 센서가 감지하지 않은 경우 방위를 보고 만약 방위가 북쪽이나 남쪽이면 계속 직진을 수행합니다. 동쪽이나 서쪽일 경우는 빠르게 나아가기 위하여 계속 좌 우측 초음파 센서 값을 확인합니다.   
■ 동쪽으로 가고 있는 경우   
왼쪽의 초음파 센서값을 읽어서 왼쪽에 길이 생기면 왼쪽으로 바로 이동해 북쪽으로 가도록 합니다.   
■ 서쪽으로 가고 있는 경우  
오른쪽의 초음파 센서값을 읽어서 오른쪽에 길이 생기면 오른쪽으로 바로 이동해 북쪽으로 가도록 합니다. 그 외에 경우에는 계속 직진을 할 수 있도록 합니다.

2. 모터 제어 테스크 알고리즘  
MoveStatus를 확인하여 적절한 움직임을 제공하여주기만 하면 됩니다. 장애물 탐지 및 방향 설정 테스크에서 어떻게 움직여야할지 방향을 제시하면 그에 대한 모터 제어를 수행합니다. 좌측 그리고 우측의 90도 방향 회전 이 이루어질 때, 방위를 변경하여주게 됩니다.   
방위 계산은 북쪽을 0 동쪽을 1 남쪽을 2 서쪽을 3으로 하여 우측 90도 회전시 +1 후 %연산으로 방위 계산을 좌측 90도 회전시 + 3 후 % 연산으로 방위를 계산하도록 하였습니다.  

 ----------------------------------------------------------------------
## 대회 영상 및 성능 분석
1. 1차 주행  
- 주행 영상  
[![1차주행](https://img.youtube.com/vi/eUQFtpxet1kw/0.jpg)](https://www.youtube.com/watch?v=eUQFtpxet1k&feature=youtu.be)   
- 성능 분석  
1차 주행시에 가장 불안하였던 성능은, 동쪽 혹은 서쪽으로 이동하는 경우에 북쪽으로 갈 수 있는 길이 있다면 가게 되는데, 그것의 딜레이 값이 조금 길어서 약간 원하는 경로로 가지 못하는 경우가 생기는 문제점이 있었 다. 하지만 IR센서를 활용하여 약간의 보정을 해주는 등의 주행 중 오류를 보정하는 것을 통하여 안정적으로 완 주를 할 수 있게 되었다. 또한 정확하게 Calibration을 수행해 주어 거의 직선으로 이동하였고, 회전도 바퀴가 틈 에 끼지 않는 이상 거의 90도를 회전하는 모습을 보여 굉장히 빠른 시간(59초)에 통과할 수 있었다. 목표하였던 시간 단축을 위해 수행해주었던 작업들이 모두 성공적으로 동작함을 알 수 있었다.
