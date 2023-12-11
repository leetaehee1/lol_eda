# <div align="center">League Of Lengend 승패 예측 분석</div>

<div align="center">
<img src ="https://github.com/leetaehee1/LOL_EDA/assets/79897716/8891e5d4-410d-4c18-ab37-92ce3d79f0f2" height=400> 
</div>


# 1. 개 요
리그 오브 레전드(이하 LOL)는 전 세계에서 수많은 게이머들에게 사랑받고 있는 대표적인 컴퓨터 게임으로, e-sports 산업에서는 이미 그 영향력을 확인할 수 있는 대표적인 콘텐츠로 자리매김하고 있다. 특히, 2022년 항저우 아시안 게임에서는 LOL이 정식 종목으로 채택되어 게임의 글로벌한 인지도와 영향력을 증명하였다.

게임의 제작사인 Riot Games는 LOL에 대한 플레이어들의 흥미를 더욱 높이기 위해 게임 데이터를 무료로 제공하고 있다. Riot Games API를 통해 LOL 소환사의 개인적인 게임 정보부터 경기 데이터까지 다양한 정보에 접근할 수 있다[1]. 매일 24시간 동안 수집되는 약 20만 건의 경기 데이터는 풍부한 정보를 포함하고 있으며, 소정의 절차를 거치면 이 데이터에 쉽게 접근할 수 있다[2].

이번 프로젝트에서는 LOL 게임의 대규모 데이터셋 중 하루치 20만 건의 데이터를 활용하여 승패를 예측하는 모델을 개발하고자 한다. 이를 통해 게임의 다양한 측면을 분석하고, 어떤 플레이어 행동이 승리에 어떠한 영향을 미치는지에 대한 통찰력을 얻고자 한다.

# 2. 데이터
## 2.1 데이터 소스
이번 프로젝트에 활용할 데이터는 온라인 게임 코칭 전문기업인 더매치랩(The Match LAB)에서 가공한 LOL 게임 데이터를 바탕으로 한다. 데이터는 2023년 8월 25일, 9월 15일, 9월 17일 각각 하루 동안 수집된 LOL 경기에 대한 세부 항목들로 구성되어 있다.

## 2.2 탐색적 데이터 분석
리그 오브 레전드(League of Legends)의 티어 시스템은 플레이어들의 경기 결과를 기반으로 계급을 나누는 시스템이다. 주로 랭크 게임에서 사용되며 각 티어는 언랭크(U), 아이언(I), 브론즈(B), 실버(S), 골드(G), 플래티넘(P), 에메랄드(E), 다이아몬드(D), 마스터(M), 그랜드마스터(G) 그리고 챌린저(C)로 구성되어 있다.

<details>
<summary> 티어에 대한 정보 (이미지)</summary>   
  <img src="https://github.com/leetaehee1/LOL_EDA/assets/79897716/36bfd42c-f00a-4e6d-bacb-3d5d5b30fa2c" width="1000" height="550">

  + 아직 티어가 정해지지않은 **UnRanked(언랭크)** 또는 Unknown도 포함되어 있다.
</details>


데이터는 5대5 솔로 랭크 경기 약 20만 건으로 구성되어 있으며, 포지션별로 데이터가 구분되어 있다. 5가지 포지션에 대한 내용은 다음과 같다.

![롤라인1](https://github.com/leetaehee1/LOL_EDA/assets/79897716/f11495fb-1528-4547-a923-ace72525416f)

| 포지션        | 역할 |
|-----|-------------|
| 탑(TOP)     | 탑 라이너는 상단 라인을 지키며 1대1 전투와 상대 터렛 파괴에 중점을 두며, 팀에서 최전방 탱커 역할과 이니시에이팅을 맡는다. |
| 정글(JUNGLE) | 정글러는 맵 전체를 돌아다니며 중립 몬스터를 처치하고 각 라인에 도움을 주며, 게임의 전략적인 요소를 담당하며 오브젝트 관리(드래곤, 내셔남작 등)와 라인 갱킹, 이니시에이팅을 수행한다. |
| 미드(MID)    | 미드는 팀의 메인 딜러로 모든 싸움에서 높은 영향력을 발휘하며 중앙 라인을 통해 빠른 경험치와 골드를 획득하여 게임을 주도하며, 상대를 제압하고 중립 몬스터에도 적극적으로 참여하여 팀에 유리한 상황을 만든다. |
| 원딜(ADC)    | 원딜은 바텀 라인에서 원거리 딜러로 활약하고, 후방에서 원거리 딜링을 통해 팀의 주요 딜량을 담당한다. |
| 서포터(SPT)    | 서포터는 팀원을 지원하고 보호하며, 다양한 아이템과 스킬을 활용하여 팀의 생존 능력을 높이며, 시야확보, 이니시에이팅 같은 다양한 역할을 수행한다. |   

 *# 거의 대부분은 이러한 역할을 가지고 있지만 아닌 경우도 존재하긴 한다.*

제공된 데이터의 항목은 총 185개로 구성되어 있으며, 전반적으로 다음과 같은 내용으로 정리해볼 수 있다.

| 항목           | 데이터 속성 |
|--------------|--------|
| 플레이어에 대한 데이터 | kills, deaths, assists, cs, kda ...|
| 팀에 대한 데이터    | baron, dragon, turretkills, goldearned ...    |
| 라인전 전후에대한 데이터 | diffdmg, diffgold, dratio, gratio, kratio ... |

여기서 중요하게 고려되는 항목들은 다음과 같다. (10개 이상)

| 데이터 속성         | 데이터의 의미                                    | 중요한 이유 |
|----------------|--------------------------------------------|--------|
| KDA            | Kill/Death/Assist의 비율                      | 전체적인 플레이어 성과를 측정하는 중요한 지표이다. |
| Dealt          | 챔피언에게 넣은 데미지              | 플레이어가 상대에게 가한 데미지를 나타내어, 전투에서의 기여도를 평가하는 중요한 지표이다. |
| Dpm            | 챔피언에게 넣은 데미지 / 게임시간      | 플레어어가 분당 평균적으로 입히는 피해를 나타내며, 높은 DPM는 플레이어의 딜링 능력이 뛰어나다는 것을 의미하며, 팀의 전체 딜량에 기여하여 게임에서 유리한 상황을 만들 수 있다. |
| Dealttaken | 챔피언에게 받은 데미지 | 플레이어가 상대에게 받은 데미지를 나타내어, 생존 능력과 플레이어의 상대에 대한 내구성을 측정하는 지표이다. | 
| Diffdpm        | DPM 격차                       | 전투에서 팀의 강세를 보여주는 중요한 지표이다. |
| Diffgold       | 팀 간의 Gold 격차                                    | 블루, 레드 팀의 경제적 우위를 나타내어 아이템 구매, 경험치, 전투력 등에 영향을 미치며, 큰 차이일수록 전투에서 강세를 보일 수 있다.|
| kill_at14 | 14분 시점의 킬 횟수 | 게임 초기의 플레이어가 상대를 처치한 횟수로, 초기 승부 요인을 나타낼 수 있다. |
| Cs100 / 200 | 블루(100), 레드(200)팀별 총 CS 수 | 팀 내 각 플레이어의 골드 획득에 직결되어 아이템 구매와 전투 능력 강화에 중요한 역할을 한다. 높은 CS는 플레이어가 경제적으로 성장하고, 팀 내에서 강력한 존재가 되는 데 도움을 준다.| 
| Goldearned100 / 200 | 블루(100), 레드(200)팀별 총 획득 골드                                | 블루, 레드 팀의 전체 경제적 성과를 나타내어 팀의 경제 효율성을 평가하는 중요한 지표이다. |
| Dragon100 / 200 | 블루(100), 레드(200)팀별 총 드래곤 처치 수                                   | 드래곤 각 속성은 팀의 전략과 팀워크에 큰 영향을 미치며, 이를 활용하는 것이 게임에서의 승리에 기여하는 중요한 요소이다. |
| Baron100 / 200    | 블루(100), 레드(200)팀별 총 내셔남작 처치 수                                  | 바론 버프는 팀의 골드 및 전투 능력을 향상시켜 게임에서 결정적인 우위를 차지할 수 있는 중요한 요소이다. |
| Tower100 / 200    | 블루(100), 레드(200)팀별 총 타워 처치 수                                | 블루, 레드 팀이 처치한 타워 수를 통해 맵 컨트롤과 길의 개방을 확보하여 승패에 긍정적인 영향을 미치는 전략적인 지표이다. |
| Visionscore100 / 200 | 블루(100), 레드(200)팀별 총 시야점수                                   | 블루 팀의 시야점수로 팀의 지도 및 시야 확보 능력을 측정하는 지표이며, 팀의 전략적인 우위를 위해 중요한 지역을 통제하며, 이는 게임 승패에 긍정적인 영향을 미친다. |

## 2.3 데이터 전처리
20만 건의 경기 데이터는 수준 별로 편차가 어느정도 존재할 것으로 예상된다. 따라서 일정 수준 이상의 데이터로 지표의 상관성을 통해 승패예측 모델을 만드는 것이 합리적일 것이다.

이에 이번 프로젝트에서는 다음과 같이 정의되는 LOL게임의 등급체계(tier)를 바탕으로, 모든 플레이어가 플래티넘 이상인 경기를 추출하여 분석해보고자 한다.


<details>
<summary> 0825 게임 데이터 전체 티어별 분포 (표)</summary>    
  <img src="https://github.com/leetaehee1/LOL_EDA/assets/79897716/ea425786-5fc2-4701-989f-2e80a3783c26" width="900" height="550">
</details>

<details>
<summary> 0825 플래티넘 이상 게임 데이터 티어별 분포 (표)</summary>    
  
  ![0825플래이상](https://github.com/leetaehee1/LOL_EDA/assets/79897716/6dfbbf3b-1377-47f5-9542-68ddb52bae2d)
</details>


<details>
<summary> 0915 게임 데이터 전체 티어별 분포 (표)</summary>    
  <img src="https://github.com/leetaehee1/LOL_EDA/assets/79897716/997596f0-59d4-4b0e-a588-11df7f1e25bc" width="900" height="550">
</details>

<details>
<summary> 0915 플래티넘 이상 게임 데이터 티어별 분포 (표)</summary>    
  
  ![0917플래이상](https://github.com/leetaehee1/LOL_EDA/assets/79897716/5631c187-b40e-4df9-9b46-5187b9680153)
</details>

<details>
<summary> 0917 게임 데이터 전체 티어별 분포 (표)</summary>    
  <img src="https://github.com/leetaehee1/LOL_EDA/assets/79897716/ac6556bb-0a39-4e2a-87d7-54107d80fafe" width="900" height="550">
</details>

<details>
<summary> 0917 플래티넘 이상 게임 데이터 티어별 분포 (표)</summary>    
  
  ![0917플래이상1](https://github.com/leetaehee1/LOL_EDA/assets/79897716/35b89531-5ee3-4ad6-8f24-64c31e2cbcd8)

</details>

도출된 플래티넘 이상의 경기에 대해서, 핵심 데이터 속성으로 kda, dealt, dpm, dealttaken, kill_at14, diffdpm, diffgold, cs100/200, goldearned100/200, visionscore100/200, dragon100/200, baron100/200, tower100/200을 추출했다.     
그 이유는 전반적인 활약도를 나타내는 **kda, dealt, dpm**과 초기에 높은 이점을 가져다줄 수 있는 **kill_at14**, 그리고 그 격차들을 나타내주는 **diffdpm, diffgold**와 경제적 성과와 연결되는 **cs, goldearned**, 시야 확보를 얼마나 햇는가를 나타내는 **visionscore**, 오브젝트를 얼마나 활용햇는가를 나타내는 **dragon, baron**, 마지막으로 맵컨트롤과 길을 얼마나 확보하엿는가를 나타내는 **tower** 모두 승패와 연관있기 때문이다. 그리고 승패를 예측하는 것이기 때문에, 경기 데이터를 기준으로 데이터 속성별 정규화(normalize)를 수행했다. 

## 2.4 데이터 프레임 설계

탐색적 데이터 분석과 데이터 전처리를 통해 예시로 다음과 같은 데이터 프레임을 만들고자 한다.

| Id  | 팀  | 주요지표 | TOP | MID | JUG | SPT | ADC |
|-----|-----|---------|-----|---|---|---|---|
| 고유번호 | 100 | kda  |       1.85714 |     3.0     | 4.6   |  2.75  |    1.5|
|         |     |  dealt    |     27524 |   25655   | 18984  |  18807|    24173|
 |        |     |  dpm     |    786.775 | 733.349  |542.658|  537.599 | 690.986|
 |      |      |   dealttaken |   41685  |  24085  |  50921  |  27731  |  25374|
  |      |     |   kill_at14    |     2     |   3     |   2    |    7   |     3|
  |...|...|...|...|...|...|...|...|
|  |200|  dragon   |         1    |    1   |     1    |    1   |     1|
|    |     |       baron  |           0   |     0    |    0   |     0     |   0|
|      |       |   tower     |        1    |    1     |   1      |  1   |     1|
  

# 3. 승패예측 모델

## 3.1 모델 개요

각 팀의 특징 데이터를 합산하고 정규화한 후, 승패를 예측하는 단순한 규칙 기반 모델이다. 머신러닝 알고리즘 없이 간단한 비교만을 사용하므로 정확도가 한계가 있다. 더 정교한 예측을 위해서는 머신러닝 알고리즘을 활용해야 한다.

## 3.2 성능
0825, 0915, 0917 데이터의 전체 데이터와 한 게임의 플레이어가 모두 플래티넘 이상 게임 데이터의 승패 예측 정확도를 확인해보면 다음과 같다.
- 0825 데이터의 승패 예측
  
전체 데이터에서
예측이 실제 결과와 일치한 횟수 :  194576, 전체 경기의 수 : 201545, 정확도 :  0.9654221141680518로 **정확도가 96.5%가** 나온다.

플래티넘 이상 추출한 데이터에서 
예측이 실제 결과와 일치한 횟수 :  43701, 전체 경기의 수 : 44962, 정확도 :  0.9719540945687469로 **정확도가 97%** 나온다.

- 0915 데이터의 승패 예측
  
전체 데이터에서 
예측이 실제 결과와 일치한 횟수 : 168760, 전체 경기의 수 : 178580, 정확도 : 0.9450106394893045로 **정확도가 94.5%가** 나온다.

플래티넘 이상 추출한 데이터에서 
예측이 실제 결과와 일치한 횟수 : 27141, 전체 경기의 수 : 28209, 정확도 : 0.9621397426353292로 **정확도가 96%가** 나온다.

- 0917 데이터의 승패 예측
  
전체 데이터에서
예측이 실제 결과와 일치한 횟수 :  248498, 전체 경기의 수 : 264395, 정확도 :  0.9398740520811665로 **정확도가 약 94%가** 나온다.

플래티넘 이상 추출한 데이터에서 
예측이 실제 결과와 일치한 횟수 : 23911, 전체 경기의 수 : 25208, 정확도 : 0.9485480799746112로 **정확도가 94.8가** 나온다.

각 날짜별 전체 데이터와 한 게임에서 모든 플레이어가 플래티넘 이상인 경기수의 승패 예측을 시도했더니 최소 94%가 나온걸로 보아 모델의 성능이 좋은것을 볼 수 있다.

## 3.3 소결
* 성능에 대한 의미
  
이 프로젝트에서 성능은 얼마나 정확하게 승패를 예측할 수 있는지를 나타낸다. 모델의 성능은 주어진 특징(feature)과 레이블(label) 사이의 관계를 학습하여 새로운 데이터에 대한 예측 정확도를 평가하는 데 사용되고 정확한 승패 예측 모델은 게임 커뮤니티나 전문 게이머들에게 유용할 뿐만 아니라, e-sports 산업에서도 경기 결과를 예측하는 데 도움이 될 수 있다.
  
* 핵심 데이터 항목에 대한 추정 및 분석

이 프로젝트에서는 위에서 말한 게임 전반적 활약도, 초기 이점을 나타내고, 격차와 경제적 성과, 시야확보, 오브젝트 활용도, 맵 컨트롤 및 시야 장악력 등 승패와 연관된 핵심 데이터 총 19개를 바탕으로 게임에서 발생하는 다양한 상황을 이해하고, 이를 기반으로 승패를 예측할 수 있을 것이다.   
분석적으로는 주요 데이터 간의 상관관계를 확인하고, 특정 데이터가 승패 예측에 미치는 영향을 심층적으로 살펴볼 필요가 있다. 예를 들어, 킬 격차와 승패간의 상관관계를 확인하고, 이것이 승패에 미치는 영향을 분석할 수 있다.    
모델의 예측 성능을 향상시키기 위해서는 중요한 데이터들을 식별하고, 이를 기반으로 모델을 조정하거나 새로운 데이터를 도입하는 등의 전략을 수립할 수 있다. 예를 들어, 킬 격차, cs격차, 골드 격차 등과 같은 데이터들이 승패 예측애 중요한 영향을 미치는 것으로 분석이 된다면, 이러한 데이터들을 보다 강조하는 방식으로 모델을 조정할 수 있다.

# 4. 결론 및 배운점
이번 연구를 통해 다음과 같은 결론을 도출할 수 있다.
* 롤 승패 예측에 영향을 미치는 주요 데이터 항목은 다음과 같다.
  * 게임 전반적인 활약도 : 킬, 어시스트, 데스, cs, 골드, 경험치 등
  * 초기 이점 : 초기 14분간의 나온 킬, 어시스트, 데스 등
  * 격차 : 킬, cs, 골드, 경험치 격차
  * 시야확보 : 시야석 설치 개수, 파괴 개수, 점령 비율, 시야 장악 지수 등
  * 오브젝트 활용도 : 드래곤, 바론, 내셔 남작 등 획득 개수
  * 맵 컨트롤 및 장악력 : 타워 처치수, 맵 컨트롤 지수 등

* 이러한 데이터 항목 간의 상관관계는 다음과 같다.
  * 킬, cs, 골드 격차등과 같은 경제적 성과와 타워 처치 수는 승패 간의 상관 관계가 가장 높다.
  * 초기 이점, 시야확보, 오브젝트 활용도 등과 같은 요소도 승패에 영향을 미친다.
  * 맵 컨트롤 및 시야 장악력은 승패 예측에 있어 상대적으로 덜 중요하다.  

따라서 승패 예측 모델을 개발할때 킬, cs, 골드 격차등과 같은 경제적 성과와 타워 처치 수 등 상관관계가 높은 것들이 중요하다.    
이를 바탕으로 성능 테스트를 해보았을때 한 게임에 모두 플래티넘 이상인 플레이어만 포함된 데이터의 승패 예측을 시도했을때 최소 94.8%가 나왔고, 또한 전체 티어에 대한 게임 데이터로 시도해도 94.5%가 나온 것으로 보아 성능이 좋다는 것을 확인할 수 있다. 
이 프로젝트는 롤 승패 예측에 대한 이해를 높이고, 승패 예측 모델의 성능을 향상시킬 수 있는 기반을 마련했다는 점에서 의의가 있다. 앞으로도 롤 승패 예측에 대한 연구가 활발히 이루어져 보다 정확하고 신뢰할 수 있는 승패 예측 모델이 개발될 수 있기를 기대한다.
















