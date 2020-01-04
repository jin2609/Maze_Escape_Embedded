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
1. 설계- 주행전략  
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
 
