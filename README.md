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

#### 일자별  주문량
```
date_order.plot.line(x="일자",figsize=(20,10))
plt.grid("white")
plt.show()
```
<img width="1161" alt="일자별_배달업종별_주문량" src="https://user-images.githubusercontent.com/72846894/99191573-c4896c80-27b0-11eb-8bc8-a625996bdcca.png">

#### 월별 특성 분석
```
date_order = date_order.set_index("일자")
monthly_order = date_order.resample('MS').mean().round(1).reset_index()
months = MonthLocator()  # every month
fig, ax = plt.subplots(figsize=(20,10))

ax.plot_date(monthly_order["일자"] , \
             monthly_order[["족발/보쌈","중식","치킨","피자"]], '-')      # x: datetime, y: random % values
ax.xaxis.set_major_locator(months)   # define xtick major location
monFmt = DateFormatter('%y년 %m월')      # define xtick string format
ax.xaxis.set_major_locator(months)   # apply xtick location
ax.xaxis.set_major_formatter(monFmt) # apply xtick label
plt.xticks(rotation=90)
plt.legend(monthly_order[["족발/보쌈","중식","치킨","피자"]])
plt.show()
```
<img width="1152" alt="월단위_배달업종별_주문량추이" src="https://user-images.githubusercontent.com/72846894/99191566-c2271280-27b0-11eb-9d2f-931beec9e984.png">

- 치킨 주문량이 감소, 중식 주문량이 증가한 것으로 보이나, 이는 배달 어플리케이션의 등록된 치킨 업체가 중식 업체보다 많기 때문으로 판단된다.

#### 월별 주문 특성 분석
```
monthly_order_2 = date_order.resample('M').sum().reset_index()
monthly_order_2["일자"] = monthly_order_2["일자"].dt.month
monthly_order_2 = monthly_order_2.groupby("일자").mean().round(1)
monthly_order_2.plot.line(figsize=(20,10))
plt.xticks([1,2,3,4,5,6,7,8,9,10,11,12])
plt.xlabel(None)
plt.show()
```
<img width="1158" alt="월별_배달업종별_주문량" src="https://user-images.githubusercontent.com/72846894/99191569-c3583f80-27b0-11eb-9b64-3d95d0e8a41f.png">

- 배달업종에 따른 주문량의 scale차이가 크게 나므로 정규화 진행 

```
col = ["족발/보쌈", "중식", "치킨", "피자"]
monthly_order_2_norm = ((monthly_order_2[col]-monthly_order_2[col].min()) / (monthly_order_2[col].max()-monthly_order_2[col].min())).round(3)
monthly_order_2_norm.plot.line(figsize=(20,10))
plt.xlabel(None)
plt.xticks([1,2,3,4,5,6,7,8,9,10,11,12])
plt.show()
```
<img width="1155" alt="월별_정규화_배달업종별_주문량" src="https://user-images.githubusercontent.com/72846894/99191572-c3f0d600-27b0-11eb-9646-e73cbbb37307.png">

* 치킨은 3월과 12월에 주문량이 두드러진다.
* 중식과 족발/보쌈은 7월과 8월에 주문량이 두드러진다.
* 피자는 12월에 주문량이 두드러진다. 

#### 일별 특성분석
```
date_order.boxplot(figsize=(12,8))
```
<img width="715" alt="배달업종별_boxplot" src="https://user-images.githubusercontent.com/72846894/99191564-bf2c2200-27b0-11eb-8540-628b36eb2328.png">

* 치킨과 중식의 배달량이 가장많고
* 치킨, 족발/보쌈, 피자의 outlier는 upper fence 상단에, 
* 중식의 outlier는 lower fence 하단에 분포

**여기에는 각 outlier 나오게된 그 파일 넣으면 좋을듯**

#### 요일별 특성분석
```
day_order = data.pivot_table("통화건수", "요일", "업종").round(1)
day_order_sort = pd.DataFrame(columns=["요일", "족발/보쌈", "중식", "치킨", "피자"])

day_order_sort.loc[0] = day_order.loc["월"]
day_order_sort.loc[1] = day_order.loc["화"]
day_order_sort.loc[2] = day_order.loc["수"]
day_order_sort.loc[3] = day_order.loc["목"]
day_order_sort.loc[4] = day_order.loc["금"]
day_order_sort.loc[5] = day_order.loc["토"]
day_order_sort.loc[6] = day_order.loc["일"]
day_order_sort["요일"] = ["월", "화", "수", "목", "금", "토", "일"]
day_order_sort.set_index("요일")

# 정규화
day_order_norm = pd.concat([day_order_sort["요일"], \
                            (day_order_sort[col]-day_order_sort[col].min()) / (day_order_sort[col].max()-day_order_sort[col].min())], axis=1)
day_order_norm

day_order_norm.plot.line(x="요일", figsize=(20,10))
```

<img width="1155" alt="요일별_배달업종별_주문량" src="https://user-images.githubusercontent.com/72846894/99191565-c0f5e580-27b0-11eb-9ce7-4659f373055f.png">

* 치킨과 족발/보쌈은 토요일에 주문량이 가장 많고
* 피자와 중식을 일요일에 주문량이 가장 많다

### 3. 지역을 기준으로 한 주문량 분석

#### 지역구별 주문량
```
df_sigu = datas.groupby(['시군구']).sum()
df_sigu = df_sigu.drop(['일자','시간대'],axis=1).reset_index()
df_sigu= df_sigu.sort_values('통화건수',ascending =False)
df_sigu.head()
```
* 주문건수 높은 5개구 

|시군구|통화건수|
|---|---|
|강남구|1047814|
|강서구|1014216|
|중구|752301|
|서초구|730401|
|서대문구|668737|

#### 2019년 서울시 구별 주문건수 합계
```
geo_path = './03_skorea_municipalities_geo_simple.json'
geo_str = json.load(open(geo_path, encoding='utf-8'))

df_map = folium.Map(location=[37.5502,126.982], zoom_start = 11,\
                    tiles= 'cartodbpositron')
# folium
folium.Choropleth(
    geo_data = geo_str,
    data = df_sigu, columns = ['시군구','통화건수'],
    nan_fill_color = 'purple', nan_fill_opacity =0.3,
    key_on = 'feature.id',fill_color= 'Blues',
    legend_name = '2019년 구별 주문건수 합계'
).add_to(df_map)

# 주문건수별 바 그래프 
plt.figure(figsize=(12,6))
sns.barplot(x = '통화건수', y='시군구',data =df_sigu[:6],palette ='Blues_d' )

```
<img width="600" alt="지역별주문량folium" src="https://user-images.githubusercontent.com/72846894/99193132-49c54f00-27ba-11eb-918a-18c091f2aa69.png">
<img width="600" alt="주문건수별바그래프" src="https://user-images.githubusercontent.com/72846894/99193129-46ca5e80-27ba-11eb-8004-52a2759b2d2d.png">



#### 2019년 서울시 구별 인구수
```
df_ppl = pd.read_csv("./0. raw_datas/ppl_report.txt",sep='\t',encoding='utf-8',thousands = ',')
df_ppl = df_ppl[3:]
df_ppl['인구'] = df_ppl['인구'].str.replace(',','').astype('int')
ppl_sigu = df_ppl[['자치구','인구']]
ppl_sigu = ppl_sigu.rename({'자치구':'시군구'},axis= 1)
ppl_sigu = ppl_sigu.sort_values('인구',ascending =False)
geo_path = './03_skorea_municipalities_geo_simple.json'
geo_str = json.load(open(geo_path, encoding='utf-8'))

# folium
df_map = folium.Map(location=[37.5502,126.982], zoom_start = 11,\
                    tiles= 'cartodbpositron')
folium.Choropleth(
    geo_data = geo_str,
    data = ppl_sigu, columns = ['시군구','인구'],
    nan_fill_color = 'purple', nan_fill_opacity =0.3,
    key_on = 'feature.id',fill_color= 'Blues',
    legend_name = '2019년 서울시 구별 인구수'
).add_to(df_map)


# 인구별 바 그래프 
plt.figure(figsize=(12,6))
sns.barplot(x = '인구', y='시군구',data =ppl_sigu[:6],palette ='Blues_d' )
```
<img width="600" alt="지역별인구수folium" src="https://user-images.githubusercontent.com/72846894/99193131-47fb8b80-27ba-11eb-8a6e-148dfacbd1ff.png">
<img width="600" alt="인구별바그래프" src="https://user-images.githubusercontent.com/72846894/99193127-45993180-27ba-11eb-9412-f3b83a5935a4.png">



#### 인구수 대비 주문건수가 높은 지역
```
df['주문율'] = df['통화건수'] / df['인구']
df.sort_values('주문율',ascending= False,inplace= True)

# folium
df_map = folium.Map(location=[37.5502,126.982], zoom_start = 11,\
                    tiles= 'cartodbpositron')
folium.Choropleth(
    geo_data = geo_str,
    data = df, columns = ['시군구','주문율'],
    nan_fill_color = 'purple', nan_fill_opacity =0.3,
    key_on = 'feature.id',fill_color= 'Blues',
    legend_name = '2019년 서울시 구별 거주인구 대비 통화량(주문율)'
).add_to(df_map)

# 주문율 바 그래프 
plt.figure(figsize=(10,3))
sns.barplot(x = '주문율', y='시군구',data =df[:5],palette ='Blues_d' )
```
<img width="641" alt="주문율 바그래프" src="https://user-images.githubusercontent.com/72846894/99193130-47fb8b80-27ba-11eb-85e7-89c3d8adca04.png">



## Built With
- [이정려](https://github.com/jungryo): 시간별 데이터 분석, Readme 작성
- [전예나](https://github.com/Yenabeam): 지역별 데이터 분석, ppt 작성
- [최재철](https://github.com/kkobooc): 배달업종별 데이터 분석, 발표

