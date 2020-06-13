# Stock Trend Prediction
Stock Trend Prediction using LSTM and comparison with using pre-trained LSTM and not using.  
NTUT-EE Deep Learning 2020 project-2

# Project Introduction
從[Alphabet Inc. (GOOG)](https://finance.yahoo.com/quote/GOOG/history?p=GOOG)下載2004.08.09至2020.06.05期間股市資料，僅利用調整後的收盤價(adj. close)的欄位當作資料．我們想要預測未來五天的該股票的收盤價格．
比較有使用pre-trained LSTM以及沒有使用兩種的差別．資料集共3977筆資料，訓練集佔6成，測試集佔4成．

# LSTM Introduction
![lstm0](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/new1/img/lstm0.JPG)  
一個LSTM cell有四個輸入與一個輸出，輸入分別是input、input gate、output gate、forget gate，輸出是output，內部有一個memory cell．
input不會直達memory cell，同樣memory cell的內容也不會直接輸出．memory cell有forget gate決定是否清除上次的狀態或某種程度削弱上次狀態留給下次狀態的影響．
與RNN不同，RNN無法清除上次狀態留下的影響，這會導致長期依賴等問題，也就是系統當前狀很容易受系統先前的狀態影響．相反地，RNN也無法擁有較長的記憶能力，而LSTM透過forget gate可以決定留多少資訊量給下次的狀態．
input gate會縮放input進memory cell，同樣output gate也會縮放memory cell的內容當作輸出．

# Pre-trained LSTM
## Predict-one Model
這個模型這個利用前30天的價格預測未來一天的價格．該模型表現為所有模型中表現最好．對LSTM的recurrent kernel使用L1和L2正則化就能有效抑制overfitting．


| Model | Performance |
|:---------:|:---------:|
|![predict-one](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/new1/img/predict-one.jpg)|![predict-one performance](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/new1/img/predict-one_p.jpg)|

| Train Score(RMSE) | Test Score(RMSE) |
|:---------:|:---------:|
| 7.26 | 19.11 |

## LSTM Autoencoder
首先只有一個LSTM cell輸入是一個sequence，Encoder在輸入為sequence的結尾輸出一個lantent code(many2one)．Decoder重複使用lantent code當作輸入，每次輸入都對應一個輸出，直到輸出的sequence長度等於輸入，Loss Function為MSE．
非常容易overfitting，對LSTM的recurrent kernel使用L1和L2正則化和dropout以及發現對輸出使用L1和L2正則化對Test Score表現較好，該模型表現為所有模型中表現最差．

| Model | Performance |
|:---------:|:---------:|
|![lstm-ae](https://media.springernature.com/full/springer-static/image/art%3A10.1038%2Fs41598-019-55320-6/MediaObjects/41598_2019_55320_Fig3_HTML.png?as=webp)|![predict-one performance](https://github.com/Shuntw6096/Stock-Trend-Prediction/blob/new1/img/lstm-ae_p.jpg)|

| Train Score(RMSE) | Test Score(RMSE) |
|:---------:|:---------:|
| 2.85 | 154.09 |








 
