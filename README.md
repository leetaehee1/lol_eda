# League Of Lengend 승패 예측 분석

# 1. 개 요
리그 오브 레전드(이하 LOL)는 전 세계에서 수많은 게이머들에게 사랑받고 있는 대표적인 컴퓨터 게임으로, e-sports 산업에서는 이미 그 영향력을 확인할 수 있는 대표적인 콘텐츠로 자리매김하고 있다. 특히, 2022년 항저우 아시안 게임에서는 LOL이 정식 종목으로 채택되어 게임의 글로벌한 인지도와 영향력을 증명하였다.

게임의 제작사인 Riot Games는 LOL에 대한 플레이어들의 흥미를 더욱 높이기 위해 게임 데이터를 무료로 제공하고 있다. Riot Games API를 통해 LOL 소환사의 개인적인 게임 정보부터 경기 데이터까지 다양한 정보에 접근할 수 있다[1]. 매일 24시간 동안 수집되는 약 20만 건의 경기 데이터는 풍부한 정보를 포함하고 있으며, 소정의 절차를 거치면 이 데이터에 쉽게 접근할 수 있다[2].

이번 프로젝트에서는 LOL 게임의 대규모 데이터셋 중 하루치 20만 건의 데이터를 활용하여 승패를 예측하는 모델을 개발하고자 한다. 이를 통해 게임의 다양한 측면을 분석하고, 어떤 플레이어 행동이 승리에 어떠한 영향을 미치는지에 대한 통찰력을 얻고자 한다.

# 2. 데이터
## 2.1 데이터 소스
이번 프로젝트에 활용할 데이터는 온라인 게임 코칭 전문기업인 더매치랩(The Match LAB)에서 가공한 LOL 게임 데이터를 바탕으로 한다. 데이터는 2023년 8월 25일, 9월 15일, 9월 17일 각각 하루 동안 수집된 LOL 경기에 대한 세부 항목들로 구성되어 있다.

## 2.2 탐색적 데이터 분석
데이터는 5대5 솔로 랭크 경기 약 20만 건으로 구성되어 있으며, 포지션별로 데이터가 구분되어 있다. 5가지 포지션에 대한 내용은 다음과 같다.

![롤라인1](https://github.com/leetaehee1/LOL_EDA/assets/79897716/f11495fb-1528-4547-a923-ace72525416f)


* LOL 게임에 대한 간략한 설명
* 영상을 삽입하던지

| 포지션        | 역할 |
|-----|-------------|
| 탑(TOP)     | 탑 라이너는 상단 라인을 지키며 1대1 전투와 상대 터렛 파괴에 중점을 두며, 팀에서 최전방 탱커 역할과 이니시에이팅을 맡는다. *위에 이미지 기준으로 라인은 핑크색에 해당함* |
| 정글(JUNGLE) | 정글러는 맵 전체를 돌아다니며 중립 몬스터를 처치하고 각 라인에 도움을 주며, 게임의 전략적인 요소를 담당하며 오브젝트 관리(드래곤, 내셔남작 등)와 라인 갱킹, 이니시에이팅을 수행한다. *위에 이미지 기준으로 라인은 초록색에 해당함* |
| 미드(MID)    | 미드는 팀의 메인 딜러로 모든 싸움에서 높은 영향력을 발휘하며 중앙 라인을 통해 빠른 경험치와 골드를 획득하여 게임을 주도하며, 상대를 제압하고 중립 몬스터에도 적극적으로 참여하여 팀에 유리한 상황을 만든다. *위에 이미지 기준으로 라인은 파란색에 해당함* |
| 원딜(ADC)    | 원딜은 바텀 라인에서 원거리 딜러로 활약하고, 후방에서 원거리 딜링을 통해 팀의 주요 딜량을 담당한다. *위에 이미지 기준으로 라인은 노란색에 해당함* |
| 서포터(SPT)    | 서포터는 팀원을 지원하고 보호하며, 다양한 아이템과 스킬을 활용하여 팀의 생존 능력을 높이며, 시야확보, 이니시에이팅 같은 다양한 역할을 수행한다. *위에 이미지 기준으로 라인은 노란색에 해당함* |   

 *# 대부분은 이러한 역할을 가지고 있지만 아닌 경우도 존재하긴 한다.*

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
| Diffdpm        | DPM 격차                       | 전투에서 팀의 강세를 보여주는 중요한 지표이다. |
| Goldearned100  | 블루팀 총 획득 골드                                | 블루 팀의 전체 경제적 성과를 나타내어 팀의 경제 효율성을 평가하는 중요한 지표이다. |
| Goldearned200  | 레드팀 총 획득 골드                                | 레드 팀의 전체 경제적 성과를 나타내어 팀의 경제 효율성을 평가하는 중요한 지표이다. |
| Diffgold       | 팀 간의 Gold 격차                                    | 팀의 경제적 우위를 나타내어 아이템 구매, 경험치, 전투력 등에 영향을 미치며, 큰 차이일수록 전투에서 강세를 보일 수 있다.|
| Dragon         | 드래곤 처치 수                                   | 드래곤 각 속성은 팀의 전략과 팀워크에 큰 영향을 미치며, 이를 활용하는 것이 게임에서의 승리에 기여하는 중요한 요소이다. |
| Baron          | 내셔남작 처치 수                                  | 바론 버프는 팀의 골드 및 전투 능력을 향상시켜 게임에서 결정적인 우위를 차지할 수 있는 중요한 요소이다. |
| Tower100       | 블루팀 타워 처치 수                                | 블루 팀이 처치한 타워 수를 통해 맵 컨트롤과 길의 개방을 확보하여 승패에 긍정적인 영향을 미치는 전략적인 지표이다. |
| Tower200       | 레드팀 타워 처치 수                                | 레드 팀이 처치한 타워 수를 통해 맵 컨트롤과 길의 개방을 확보하여 승패에 긍정적인 영향을 미치는 전략적인 지표이다. |
| Visionscore100 | 블루팀 시야점수                                   | 블루 팀의 시야점수로 팀의 지도 및 시야 확보 능력을 측정하는 지표이며, 팀의 전략적인 우위를 위해 중요한 지역을 통제하며, 이는 게임 승패에 긍정적인 영향을 미친다. |
| Visionscore200 | 레드팀 시야점수                                   | 레드 팀의 시야점수로 팀의 지도 및 시야 확보 능력을 측정하는 지표이며, 팀의 전략적인 우위를 위해 중요한 지역을 통제하며, 이는 게임 승패에 긍정적인 영향을 미친다. |

## 2.3 데이터 전처리
20만 건의 경기 데이터는 수준 별로 편차가 어느정도 존재할 것으로 예상된다. 따라서 일정 수준 이상의 데이터로 지표의 상관성을 통해 승패예측 모델을 만드는 것이 합리적일 것이다.

이에 이번 프로젝트에서는 다음과 같이 정의되는 LOL게임의 등급체계(tier)를 바탕으로, 모든 플레이어가 플래티넘 이상인 경기를 추출하여 분석해보고자 한다.

* 티어에 대한 정보 (표)
* 티어별 히스토그램
* 플래티넘 이상인 경기의 수

도출된 플래티넘 이상의 경기에 대해서, 핵심 데이터 속성으로 ???, ???, ??? 등을 추출했다. 그 이유는 ~~~때문이다. 그리고 승패를 예측하는 것이기 때문에, 경기 데이터를 기준으로 데이터 속성별 정규화(normalize)를 수행했다. 

## 2.4 데이터 프레임 설계

탐색적 데이터 분석과 데이터 전처리를 통해 다음과 같은 데이터 프레임을 만들고자 한다.

| Id  | 팀  | 주요지표 | TOP | JUG | MID | ADC | SPT |
|-----|-----|---------|-----|---|---|---|---|
| 고유번호 | Red/Blue | kda, dpd, ...| ... |...|

# 3. 승패예측 모델

## 3.1 모델 개요
CNN을 썼다. 단순한 합산을 썼다.

## 3.2 성능
0825
0915
0927
플래티넘 이상 4.5만건
데이터 전체 20만건

## 3.3 소결
* 성능에 대한 의미
* 핵심 데이터 항목에 대한 추정 및 분석

# 4. 결론 및 배운점
