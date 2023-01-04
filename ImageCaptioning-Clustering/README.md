# Image Captioning과 Clustering을 활용한 문장 이미지 분류
Modeling 설명 블로그 링크 : https://brave-greenfrog.tistory.com/100
### Image Captioning Overview

<img width="951" alt="스크린샷 2023-01-04 오후 1 15 57" src="https://user-images.githubusercontent.com/79692017/210483780-0544752d-9f73-4a8f-8536-d4cb145908ad.png">

### 결과
<img width="715" alt="스크린샷 2023-01-04 오후 1 15 33" src="https://user-images.githubusercontent.com/79692017/210483787-f3abdd0e-299c-43b5-94bd-bebd7341b77f.png">


### 과정 
### 1. EncoderCNN

입력 이미지를 CNN을 통과시켜 특징들을 추출한다.

 

EncoderCNN의 input은 이미지로 , 입력이미지를 CNN에 통과시켜 feature vector를 만든 뒤

각각 class 에 대한 확률 값을 예측하는 방식으로 CNN을 구성한다. 

그렇게 학습된 CNN을 활용하여 들어온 이미지로부터 context vector을 만든다.

- Context Vector란 문장에 대한 정보를 갖고있는 feature값임 - 

 ![image](https://user-images.githubusercontent.com/79692017/210483999-409c1a4c-ca9e-4d25-84b1-91b8360f0b7b.png)


CNN의 모델로 Resnet101을 사용하였고 Decoder에 들어갈 수 있도록 임베딩 차원과 동일하게 새로운 Fullyconnected 레이어를 만들어서 약간 변형을 했다.

아래 구조는 기존 Resnet 101이고 fc layer를 변형한것을 확인할 수 있다. 


### 2. DecoderRNN

추출한 특징들을 RNN에 대입하여 문장을 만들 수 있도록, 이미지 데이터셋과 이미지에 대한 캡션들을 토대로 학습시킨다

 <img width="894" alt="image" src="https://user-images.githubusercontent.com/79692017/210484065-025e1727-e501-4010-a4e2-79561986a219.png">


CNN을 통해 만들어진 context vector를 디코더에 넣어서 디코더가 사진이 설명하는 내용을 단어 하나씩 출력함.

매번 생성된 단어들을 다시 입력으로 넣고 이전까지에 대한 정보를 포함하는 hidden state를 또 받아서 다음 출력을 만들어내고를 <eos>가 나올 때 까지 반복하여 단어를 뽑아내게 되면 그 단어들이 구성한 문장이 출력문장이 되게됨

### 3. Data Preprocessing

 

#### 3-1. Image Preprocessing

resieze 256x256
train 60000개, validation 10000개, Test 10000개씩 생성
![image](https://user-images.githubusercontent.com/79692017/210484172-c74f1a1e-29ea-424f-846f-be6fa1dd70e0.png)

#### 3-2. Caption Preprocessing

Caption을 사용하여 단어사전 (Vocab)생성하기 위한 Class 생성. 
PAD, UNK, START, END도 vocab에 넣어줌
word_threshold를 지정하여 이보다 단어 등장의 빈도수가 많으면 vocab에 넣어줍니다.


이미지가 I이고

목표문장을 이루는 단어들 a, woman, holding, ...., branch 가 S1,...,Sn이다

그니까 저 목표문장을 이루는 단어들이 가장 확률이 높은 단어를 채택하는 것이 전체 로직임
![image](https://user-images.githubusercontent.com/79692017/210484252-f74d2e97-8a1b-4dac-a6b1-5ddacc8814bf.png)
(사진 + a woman) 이 NN의 input으로 들어오면 is일 확률이 가장 높기 때문에 is라는 단어를 추가하여 a woman is라는 문장을 만들게됨

이렇게 계속 학습을 진행하면서 빈칸에 올 확률이 가장 높은 단어를 추출하게끔 Neural Network 가 학습됨



