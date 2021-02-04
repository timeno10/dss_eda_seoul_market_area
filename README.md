# 서울시 골목상권 EDA
## 1. Intro
#### 1-1. Topic
- 서울시 상권을 상권 유형, 성별, 연령, 시간대 등 다양한 측면에서 분석

#### 1-2. Contents
1. 서울시 상권 유동인구 분석
2. 서울시 상권 매출 분석
3. 서울시 상권 유동인구 & 매출 상관관계 분석

#### 1-3. Dataset
[서울 열린데이터 광장](http://data.seoul.go.kr/dataList/3/literacyView.do)

1. 서울시 우리마을가게 상권분석서비스(상권-추정매출)_2019.csv
2. 서울시 우리마을가게 상권분석서비스(상권-추정유동인구).csv
3. 서울시 우리마을가게 상권분석서비스(상권-직장인구).csv
4. 서울시 우리마을가게 상권분석서비스(상권영역).csv

## 2. Preview

```python
# 패키지
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from matplotlib import pyplot
from matplotlib import font_manager
f_path = "C:\Windows\Fonts\Malgun.ttf"
font_manager.FontProperties(fname=f_path).get_name()
from matplotlib import rc
rc('font', family='Malgun Gothic')
sns.set_style('whitegrid', {'font.family':'Malgun Gothic'})
```
### 2.1 유동인구 데이터 분석
### 용어 정리
#### 유동인구란?
    - 서울시와 KT가 공공빅데이터와 통신데이터를 이용하여 추계한 서울의 특정지역, 특정시점에 존재하는 모든 인구
    
##### 상권 
    - 발달상권: 대로변과 대형 유통시설의 포함되는 범위
    - 골목상권: 대로변이 아닌 거주지 인근의 좁은도로를 따라 형성
    - 전통시장: 예전부터 있어 오던 시장을 백화점 따위의 물건 판매장소에 상대하여 칭함
    - 관광특구: 외국인 관광객 수가 10만명(서울은 50만명)이상

<img src="https://user-images.githubusercontent.com/72846844/104978819-187d2f80-5a46-11eb-8d6d-90e0d1728908.png"></img> 

##### 데이터 불러오기 및 결합
```python
# 서울시 유동인구 데이터 로드
pop = pd.read_csv("서울시 우리마을가게 상권분석서비스(상권-추정유동인구).csv", encoding='euc-kr')
# 서울시 상권 코드 데이터 로드
reg = pd.read_csv("서울시 우리마을가게 상권분석서비스(상권영역).csv", encoding="euc-kr")
# 서울시 2019년도 유동인구 & 상권 코드 데이터 결합
join = pd.merge(pop, reg, on="상권_코드")
# 시군구 코드 데이터 로드
code = pd.read_csv("서울특별시 건축물대장 법정동 코드정보.csv", encoding='euc-kr')
code = code[["시군구코드", "시군구명"]].drop_duplicates('시군구코드', keep='first')
# 서울시 2019년도 유동인구 & 상권 코드 & 시군구 코드 데이터 결합
join2 = pd.merge(join, code, left_on="시군구_코드", right_on="시군구코드")
join2["구"] = [gu[:2] if len(gu)<=3 else gu[:3] for gu in join2['시군구명']]
#서울시 2019년도 유동인구 & 상권 코드 & 시군구 코드 & 직장인 인구수 데이터 결합
work = pd.read_csv("서울시 우리마을가게 상권분석서비스(상권-직장인구).csv", encoding='euc-kr')
join3 = pd.merge(join2, work, on="상권_코드")
```

##### 상권별 유동인구 비교
###### 골목상권의 총 유동인구수가 많지만 개별 상권의 유동 인구수는 발달 상권의 1/3 수준

<img src="https://user-images.githubusercontent.com/71831714/104885618-a7893980-59ab-11eb-8f58-2e5bd2e93363.png"></img>

##### 직장인 유동인구와 골목상권 유동인구

###### 중구/강남/서초 직장인 유동인구가 높으며 전형적인 오피스 상권임을 보인다.

<img src='https://user-images.githubusercontent.com/71831714/106826810-013b7480-66cb-11eb-9f00-f9533fced91a.png'></img>

###### 중구/강남/서초는 상권 유동인구 또한 높은데, 이는 골목 상권이지만, 좀더 폭넓은 업종 선택과 발달상권의 이점을 기대 할 수 있다. 

<img src='https://user-images.githubusercontent.com/71831714/106826859-1d3f1600-66cb-11eb-83b4-5dfbd31309c2.png'></img>

###### 하지만 중구는 강남과 서초와는 달리 상주 인구가 적고 직장인이 많기 떄문에 주말 유동인구가 급혁하게 떨어지는 것을 확인 할 수있다.

<img src='https://user-images.githubusercontent.com/72846844/104942808-3de34c80-59f8-11eb-94ff-26db59c37cb4.png'></img>

###### 서대문구와 마포구에서 총 인구수 대비 20대 유동인구 비율이 높다. 

<img src='https://user-images.githubusercontent.com/71831714/106829180-f97dcf00-66ce-11eb-9936-a257eb945ed2.png'></img>

##### 서대문구의 발달 상권(신촌, 이대)과 마포구의 관광 특구(홍대, 신촌)에 20대가 많이 몰리는 것을 확인

<img src='https://user-images.githubusercontent.com/71831714/106828934-807e7780-66ce-11eb-9bab-739f6e2e4d66.png'></img>

##### 서대문구에서는 1, 20대 비율이 높고 오피스텔이 많은 중구에서는 30대 이상 비율이 높다.

<img src='https://user-images.githubusercontent.com/71831714/106828854-5036d900-66ce-11eb-9a68-4c5df606e8a0.png'></img>

## 3. Conclusion

1. 생각과는 다르게 주말에 상권 유동인구수가 적음 
2. 중구에서는 유동인구 대비 직장인 비율이 높아 주말 & 밤, 새벽 영업은 피하는 것이 좋아보임
3. 1,20대 대상 영업는 마포구와 서대문구가 가장 적절해 보이지만 지역별 임대료 데이터가 있다면 임대료 대비 매출액을 비교할 필요가 있음

## 4. Built With

1. 김성준 : 서울시 상권 유동인구 분석, 서울시 상권 유동인구 & 매출 상관관계 분석, README 작성, 발표 진행
2. 김종찬 : 서울시 구별 매출 지도 시각화, 서울시 상권 매출 분석, ppt작업
3. 김태성 :
