# Multi-class Prediction of Gold Price Movement (BUY / HOLD / SELL)

Овој проект имплементира машинско учење за предвидување на идното движење на цената на златото (XAU/USD) со користење на:  
Историски цени на златото (price, open, high, low, change %)  
Sentiment анализа од вести поврзани со злато (V2Tone, Themes)  
Технички индикатори (RSI, MACD, Moving Averages, Momentum, Volatility)  

Проектот предвидува дали во наредните 3 дена ќе треба да се:  
BUY (2) – ако цената порасне значително  
SELL (0) – ако цената падне значително  
HOLD (1) – ако нема доволно јасен сигнал за купување или продавање

# Податоци што се користат
Gold price dataset: XAU/USD историски податоци (price, open, high, low, %change)  
Gold news dataset:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;v2 Tone dataset.csv: вкупен тон на текстот (sentiment)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;second dataset.csv: линкови и теми за верификација (дали се поврзани со GOLD)  

# Feature Engineering
Креирани се дополнителни features:  
Rolling статистики:  
Moving Averages (MA_3, MA_7), Volatility (Volatility_3d), Price lag (Price_t-1)  
Технички индикатори:  
RSI (14), MACD (12-26-9), Momentum (5)  

Sentiment features:  
Average, max, std на V2Tone по ден  
Број на статии за ден  
Binary indicator has_gold_theme

# Target Variable (Multi-class)  
Креирана е динамична мета (Target_hold_focused) според волатилност на пазарот:  
Ако порастот е > (dynamic threshold * multiplier) → BUY (2)  
Ако падот е < - (dynamic threshold * multiplier) → SELL (0)  
Во спротивно → HOLD (1)  
Со тоа HOLD стана основна класа, но тежината е балансирана.

# Sampling & Class Balancing  
Користиме RandomOverSampler со custom sampling strategy:  
BUY ≈ SELL ≈ HOLD * 2.5 (boost за HOLD)  
Дополнително се применуваат sample weights при fit.

# Model & Evaluation
XGBoost Classifier со multi:softprob за multiclass:  
n_estimators=120, max_depth=5, learning_rate=0.1  
Rolling evaluation:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Train на 200 денови, Test на следни 50, поместување со чекор 50.  
Мери:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Macro F1-score  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;F1-score и Accuracy по класа (BUY, HOLD, SELL)  
![image](https://github.com/user-attachments/assets/c1872323-294f-482a-9acc-9e86ebb8c7bc)
![image](https://github.com/user-attachments/assets/14276e15-7d30-4a8f-8328-e093b691371c)



# Резултати
Средна Macro F1-score: ≈ 0.43  
Средна Accuracy:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;BUY: ≈ 0.47  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;HOLD: ≈ 0.51  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SELL: ≈ 0.44  








