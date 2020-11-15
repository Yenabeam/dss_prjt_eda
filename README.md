# 서울시 배달업종 통화량 EDA - 시간, 업종, 지역 중심으로

- 서울시의 배달업종별 주문통화량에 대해 탐색적데이터분석을 진행한다. 
- 시간별, 지역별, 배달 업종별 주문건수를 분석하여 지역선정, 업종선정, 영업 시간 등에 활용할 수 있다.


## Getting Started

You will require Python 3 and the following libraries

### Prerequisites

* os - Used to check file list in os
* requests- Used to download the dataset.
* numpy - Used for fast matrix operations.
* pandas - Used for performing Data Analysis.
* seaborn - Used for statistical data visualization.
* matplotlib - Used to create plots and Korean character
* folium - Used for the map visualization. 
* json - Used to get the json type data.
* urllib - Used for URL parsing
* bs4 - Used to get the html type data.
* warnings - Used to warning control

### Dataset

* 2013년 10월 ~ 2019년 9월 서울시 배달 업종별 주문건수 데이터
    * [SKT data hub](https://www.bigdatahub.co.kr/index.do)
* 한국 공휴일 데이터
    * [공공데이터포털](https://data.go.kr/index.do)
* 자치구단위 서울생활인구 일별 집계표, 서울시 주민등록인구 (구별) 통계
    * [서울시열린데이터광장](https://data.seoul.go.kr/)
    

## Data Exploration(EDA)

* 연간 주문량 변동 추이는 2013. 10. 01 ~ 2019. 09. 30 기간 데이터를 사용한다. 
* 그 외 시간별 주문량과 배달 업종별 주문량 분석은 2016. 10. 01 ~ 2019. 09. 30 기간 데이터를 사용한다. 
* 지역별 주문량 분석은 2018. 10. 01 ~ 2019. 09. 30 기간 데이터를 사용한다. 

### 1. 시간을 기준으로 한 주문량 분석

#### 연도별/월별/시간별/업무일별 데이터 분석
```
fig = plt.figure(figsize=[12, 10])
ax1 = fig.add_subplot(3, 2, 1)
ax1 = sns.barplot(x='year', y='count',
                  data=df_2013_to_2019.groupby('year')['count'].mean().reset_index())

ax2 = fig.add_subplot(3, 2, 2)
ax2 = sns.barplot(x='hour', y='count',
                  data=df.groupby('hour')['count'].mean().reset_index())



ax3 = fig.add_subplot(3, 2, 3)
ax3 = sns.barplot(x='month', y='count',
                  data=df.groupby('month')['count'].mean().reset_index())


ax4 = fig.add_subplot(3, 2, 4)
ax4 = sns.barplot(x='season', y='count',
                  data=df.groupby('season')['count'].mean().reset_index())

ax5 = fig.add_subplot(3, 2, 5)
ax5 = sns.barplot(x='weekday', y='count', order = ["월","화","수","목","금","토","일"],
                  data=df.groupby('weekday')['count'].mean().reset_index())

ax6 = fig.add_subplot(3, 2, 6)
ax6 = sns.barplot(x='holiday', y='count',
                  data=df.groupby('holiday')['count'].mean().reset_index())
```
<img width="727" alt="delivery_by_date" src="https://user-images.githubusercontent.com/72846894/99190863-7a05f100-27ac-11eb-9173-91bc2b79d5fe.png">


1. 연도별 주문통화량은 감소한다.
    - 배달어플리케이션 이용자의 증가에 따른 감소로 예상
2. 시간대별 주문량은 점심과 저녁에 몰려있고 점심보다 저녁 주문량이 많다. 
3. 겨울철 주문량이 다른 계절 주문량보다 많다. 
4. 주말 주문량이 평일 주문량보다 많다.

#### 시간과 계절, 요일, 메뉴에 따른 주문량
```
fig = plt.figure(figsize=(12, 10))

# 1. 시간과 휴일에 따른 count
ax1 = fig.add_subplot(2, 2, 1)
ax1 = sns.pointplot(x='hour', y='count', hue="holiday",
                    data=df.groupby(['holiday', 'hour'])['count'].mean().reset_index())

# 2. 시간과 요일에 따른 count
ax2 = fig.add_subplot(2, 2, 2)
ax2 = sns.pointplot(x='hour', y='count', hue="weekday", hue_order=['월', '화','수', '목','금', '토',"일"],
                    data=df.groupby(['weekday', 'hour'])['count'].mean().reset_index())
# 3. 시간과 계절에 따른 count
ax3 = fig.add_subplot(2, 2, 3)
ax3 = sns.pointplot(x='hour', y='count', hue="season",
                    data=df.groupby(['season', 'hour'])['count'].mean().reset_index())

# 4. 시간과 메뉴에 따른 count
ax4 = fig.add_subplot(2, 2, 4)
ax4 = sns.pointplot(x='hour', y='count', hue="menu",
                    data=df.groupby(['menu', 'hour'])['count'].mean().reset_index())

```
<img width="733" alt="delivery_by_hour" src="https://user-images.githubusercontent.com/72846894/99190880-8db15780-27ac-11eb-9b2e-03c16583690a.png">


1. 주말 저녁 주문을 평일 저녁 주문보다 일찍한다. 
2. 금요일과 토요일의 주문은 늦은시간까지 이어지는 반면, 일요일은 저녁시간대가 지나면 주문량이 급격하게 감소한다.
3. 겨울, 가을의 저녁 주문을 봄, 여름의 저녁 주문보다 일찍한다.
4. 점심에는 중국음식이 저녁에는 치킨이 대세 주문음식이다.

### 2. 배달업종을 기준으로 한 주문량 분석
