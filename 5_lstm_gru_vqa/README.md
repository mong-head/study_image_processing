# rnn : rnn, lstm(새로운 rnn cell), gru
cell 구현
보통 sequence 데이터 처리(자연어 처리)
mnist도 RNN으로 사용해도 괜찮은 성능나옴
word embedding - 데이터관련 성능올리기 위함(one hot vector를 개선한거)
word to vector : word embedding보다 더 나아짐
attention - 자연어 처리에서 많이 쓰이지만, vision에서도 이거 많이 사용

# 1. LSTM

cf) RNN : 이전 데이터에 많은 영향 많이 받음 (순서가 있는 데이터 처리)
 - 고질적 문제점
  - input 길이 길어지면(소설등 긴 문장) 성능 낮아짐; hidden state에 정보가 계속 덮어씌워지면서 앞의 정보가 계속 약해짐
  
LSTM & GRU : RNN 개선 버전, 실제로는 LSTM을 많이 사용함.

## 1-1.구조

|RNN vs LSTM 1|RNN vs LSTM 2|
|------|-----|
|<img src="https://user-images.githubusercontent.com/52481037/100770703-e89bad80-3440-11eb-8450-b09ea2f05b82.png" width="400"/>|<img src="https://user-images.githubusercontent.com/52481037/100770034-26e49d00-3440-11eb-8df0-26012b2cbd39.png" width="400">|

 - RNN과 비슷하지만, LSTM은 hidden state이외에도 cell state가 있음 

- cell 내부 구조

    <img src="https://user-images.githubusercontent.com/52481037/100769594-9f972980-343f-11eb-83bc-72d4aae7729d.png" width="600"/>

 - cell state : 전 단계 정보를 잘 지켜주기 위함. sigmoid, tanh등 gradient vanishing되는 것들을 최소화

 - forget gate layer : sigmoid(0:잊음 ~ 1:안잊음) 얼마나 정보를 유지할지 결정, 문장 내 중요한 정보 저장위함
   - a,an같은 의미상 중요하지 않은 input는 forget하게 함

 - input gate : cell state 정보를 얼마나 update할지 결정
   - sigmoid : forget gate와 비슷 (0:이전 정보 반영x ~ 1:완전반영) (범위 : 0 ~ 1), update정보 반영 정도
   - tanh : sigmoid gradient방향 고정을 막기 위하여 있음 (범위 : -1 ~ 1), update정보 생성
 
 - output gate
   - sigmoid x 기존 hidden state : 얼마나 유지할지 계산(input이 얼마나 섞여야 하는지)
   - tanh x cell state : 처리된 정보


# 2. GRU


<img src="https://user-images.githubusercontent.com/52481037/100779748-2baf4e00-344c-11eb-97da-3571236ac6b6.png" width="500"/>

LSTM 간소화

* forget gate + input gate -> update gate
* cell state + hidden state

parameter수 줄었음, 연산량도 줄음


# 3. word2vec

기존 one-hot encoding : 0이 너무 많음 -> 성능 저하

기존 word embedding : 단어간의 단순한 군집화 (카테고리별 군집)

word2vec : 단어간 관계성까지 모델링 (카테고리별 군집 + 관계성 군집) -> 정보 더 많이 담김
 - 관계성 벡터 : (성별 : 남->여성) (나라->수도) 
 - 한국어 word2vec : https://word2vec.kr/search/  (사람들이 저장해놓음)

![image](https://user-images.githubusercontent.com/52481037/100783778-8e571880-3451-11eb-9746-6d8b9eae00ff.png)

## 3-1. CBoW(continous bay of words) - encoding

중심 단어와 그 주변에 있는 단어 보고 벡터 생성

ex) I am a good boy 에서 a를 중심으로 볼 때, 앞 뒤 단어(주변 단어)관계성 봄(am <- a -> good)


## 3-2. Skip-gram - decoding

중심 단어를 사용해서 주변 단어를 예측함

|1|2|
|----|----|
|<img src="https://user-images.githubusercontent.com/52481037/100785214-afb90400-3453-11eb-90a8-91b9bf16c7e8.png" width="400"/>|<img src="https://user-images.githubusercontent.com/52481037/100785160-9e6ff780-3453-11eb-97eb-374b26c88c2d.png" width="400"/>|




