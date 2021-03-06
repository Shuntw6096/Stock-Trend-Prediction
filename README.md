# Stock Trend Prediction
Stock Trend Prediction using LSTM and comparison with using pre-trained LSTM and not using.  
NTUT-EE Deep Learning 2020 project-2

# Project Introduction
從[Alphabet Inc. (GOOG)](https://finance.yahoo.com/quote/GOOG/history?p=GOOG)下載2004.08.09至2020.06.05期間股市資料，僅利用調整後的收盤價(adj. close)的欄位當作資料．我們想要預測未來五天的該股票的收盤價格．
比較有使用pre-trained LSTM以及沒有使用兩種的差別．資料集共3977筆資料，訓練集佔6成，測試集佔4成．

# LSTM Introduction
![lstm0](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/lstm0.JPG)  
一個LSTM cell有四個輸入與一個輸出，輸入分別是input、input gate、output gate、forget gate，輸出是output，內部有一個memory cell．
input不會直達memory cell，同樣memory cell的內容也不會直接輸出．memory cell有forget gate決定是否清除上次的狀態或某種程度削弱上次狀態留給下次狀態的影響．
與RNN不同，RNN無法清除上次狀態留下的影響，這會導致長期依賴等問題，也就是系統當前狀很容易受系統先前的狀態影響．相反地，RNN也無法擁有較長的記憶能力，而LSTM透過forget gate可以決定留多少資訊量給下次的狀態．
input gate會縮放input進memory cell，同樣output gate也會縮放memory cell的內容當作輸出．

# Model

## Predict-five Model(Primary Model)
Predict-five Model中的LSTM可以替換成Pre-trained LSTM．
![predict-5](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5.jpg)

## Pre-trained LSTM
### Predict-one Model
這個模型這個利用前30天的價格預測未來一天的價格．該模型表現為所有模型中表現最好．對LSTM的recurrent kernel使用L1和L2正則化就能有效抑制overfitting．

| Model | Performance | Training Loss |
|:---------:|:---------:|:---------:|
|![predict-one](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-1.jpg)|![predict-one performance](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-1_p.jpg)|![predict-one loss](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-1_loss.jpg)|

| Train Score(RMSE) | Test Score(RMSE) |
|:---------:|:---------:|
| 7.26 | 19.11 |

### LSTM Autoencoder
首先Encoder LSTM cell輸入是一個sequence，Encoder在輸入為sequence的結尾輸出一個lantent code(many2one)．Decoder重複使用lantent code當作輸入，每次輸入都對應一個輸出，直到輸出的sequence長度等於輸入，Loss Function為MSE．
非常容易overfitting，對LSTM的recurrent kernel使用L1和L2正則化和dropout以及發現對輸出使用L1和L2正則化對Test Score表現較好，該模型表現為所有模型中表現最差．

| Model | Performance | Training Loss |
|:---------:|:---------:|:---------:|
|![lstm-ae](https://media.springernature.com/full/springer-static/image/art%3A10.1038%2Fs41598-019-55320-6/MediaObjects/41598_2019_55320_Fig3_HTML.png?as=webp)|![lstm-ae performance](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/lstm-ae_p.jpg)|![lstm-ae loss](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/lstmae_loss.jpg)|

| Train Score(RMSE) | Test Score(RMSE) |
|:---------:|:---------:|
| 2.85 | 154.09 |

# Predict-five Model Experiment

|#| Performance  | Train Score(RMSE) | Test Score(RMSE) | Training Loss |
|:---------:|:---------:|:---------:|:---------:|:---------:|
| Pre-trained LSTM using Predict-one | ![predict-five predict-one performance](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5_predict-1_p.jpg) | 11.38 | 70.55 |![predict-five predict-one loss](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5_predict-1_loss.jpg)|
| Pre-trained LSTM using LSTM-AE | ![predict-five lstm-ae performance](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5_lstmae_p.jpg) | 9.30 | 56.95 |![predict-five lstm-ae loss](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5_lstmae_loss.jpg)|
| Not using Pre-trained LSTM | ![not using pre-trained lstm performance](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5_non_p.jpg) | 9.14 | 72.35 |![not using pre-trained lstm loss](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5_non_loss.jpg)|

# Dig deeper into the model

| Model | LSTM Weight |
|:---------:|:---------:|
| LSTM-AE |![lstm-ae lstm weight](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/lstmae_weight.jpg)|
| Predict-one |![predict-one lstm weight](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-1_weight.jpg)|
| Predict-five - Predict-one |![predict-five - predict-one lstm weight](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5_predict-1_w.jpg)|
| Predict-five - LSTM-AE |![predict-five - lstm-ae lstm weight](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5_lstmae_w.jpg)|
| Predict-five - Not using pre-trained LSTM |![predict-five - not using pretrained lstm lstm weight](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5_non_w.jpg)|

從各model看得出kernel的權重分布大同小異都接近uniform distribution，而關鍵應該是bias和recurrent kernel的權重分布．LSTM-AE的擬合能力有問題從它的recurrent kernel權重分布就察覺得到，剩下四個模型的recurrent kernel的權重分布明顯區分成兩種，但是擬合能力都沒有問題．在bias方面，Predict-five的三個模型中，就屬使用LSTM-AE Pre-trained LSTM的權重分布較不一樣，權重的豐富度較高，而沒有使用Pre-trained LSTM的Test Error較高，也從其bias的分布看出有明顯鴻溝．所以Predict-five三者明顯的差異應該是bias，三者在測試集的表現接近平行．  
![predict-5 performance comparison](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/master/img/predict-5_p_cmp.jpg)  

# References
1. Unsupervised Pre-training of a Deep LSTM-based Stacked Autoencoder for Multivariate Time Series Forecasting Problems：Alaa Sagheer & Mostafa Kotb (2019)







 
