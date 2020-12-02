# GAN 

## 1. basic concept

### 1-1. 적대적 생성 신경망(generative adversarial networks)

* generative : 데이터 생성해내는 모델 - 정답없이 사람이 보기에 그럴싸한 데이터 만들어 낼 때 까지 생성 ex)GAN, VAE

        활용) 스노우 필터등 사진 필터들에 사용 가능
        

* adversarial : 경쟁자 존재 (discriminator-generator 둘 간의 경쟁)

* networks : linear2개, adversarial 2개 net, ...

    <img src="https://user-images.githubusercontent.com/52481037/100859388-84292e80-34d2-11eb-8de2-e58bece7737a.png" width="600"/>

### 1-2. NVDIA GAN 설명 영상: [Generating Photorealiastic Video with AI part 1](https://www.youtube.com/watch?v=ZXFmZsv0Ddw)

 * 단순 스케치 -> 사실적 풍경화 : [NVDIA GAN site1](http://nvidia-research-mingyuliu.com/gaugan/)

    <img src="https://user-images.githubusercontent.com/52481037/100862042-00714100-34d6-11eb-8403-3257d118b79d.jpg" width="400"/>


 * 반려 동물 -> 생성된 새로운 반려 동물들 : [NVDIA GAN site2](http://nvidia-research-mingyuliu.com/ganimal/)
 

### 1-3. 구조

![image](https://user-images.githubusercontent.com/52481037/100863153-77f3a000-34d7-11eb-969d-5b3b4cb3d78a.png)

* generator : NN or CNN (linear-linear 이기도 함) 
 * 데이터 셋 : MNIST사용가능
 
 
* discriminator : classification network(학습대상), discriminator가 판별하 내용을 generator로 보내기도 함
 * 보통 generator보다 discriminator를 더 먼저 학습시킴

### 1-4. loss

![image](https://user-images.githubusercontent.com/52481037/100863876-8b533b00-34d8-11eb-8edb-8395c3a43089.png)

* generator loss(1-d(G))작게, discriminator ; d(g)크게
* 사실 두개 같은 말이지만, log(d(g))를 maximize하는게 gradient값이 더 커서 학습이 더 잘됨(log(1-d(g))보다)


* label(0,1) : binary cross entropy loss사용가능 (torch.nn.BCELoss)


* 학습 good : discriminator 결과값은 0.5(찍는 것과 같은 확률)로 수렴, 진짜인지 아닌지 구별 불가능한 상태라는 것
 * but, discriminator의 성능을 잘 알지 못하므로 학습이 더이상 안되는 것일 수도 있음
 * 이것을 방지하기 위해서 FID등의 데이터셋의 품질을 평가하는 기준을 사용. 현재는 general metric 발전 많이 해서 데이터셋 품질 평가 가능
 

### 1-5. 학습 저해 요인

1. mode collapse
 - 다양하게 학습이 되지 않고, 어느 한 쪽에만 데이터 몰리는 현상
 
2. nash equilibrium
 - 힘의 균형이 깨짐
 - discriminator 성능이 더 빨리 좋아짐
 
## 2. models

### 2-1.DCGAN

<img src="https://user-images.githubusercontent.com/52481037/100869762-d8d3a600-34e0-11eb-924c-890945a45668.png
" width="600"/>

* generator : transposed convolution 적용해서 점점 높이와 너비를 넓혀가며 이미지 생성하는 모델
 * 기존 convolution : 이미지 사이즈 줄이는 식으로 학습 진행했음
 * 단순 linear layer보다 더욱 괜찮은 모델(conv로 구성함)
 
 
* discriminator : 기존 convolution과 동일

요즘은 BN(batch normalization)말고 다른 normalization사용함

parameter수 많음(많은 이미지 만들 수 있음) - 사이즈 큼(학습시 시간 많이 걸림:1~2주,4달...)

#### issue

모델이 그냥 외운것이 아닌가? 확인을 어떻게 하는가?
 - z를 interpolation하면서 어떻게 변하는지 확인
  - 이미지들에 대한 모든 분포를 보면서 확인해보면 됨
  - 급격하게 편하면 외운 것. 아니면 의미를 이해한 것
  - 두 이미지를 섞었을 때 부드럽게 이어지면 외운 것이 아님
 - 실제로는 그냥 외우는건 아님
 
    <img src="https://user-images.githubusercontent.com/52481037/100872665-79c46000-34e5-11eb-90bf-37879a386e35.png" width="400"/>
    
    start latent부터 end latent까지 smooth하게 연결되는 것을 볼 수 있음(부드럽게 이어짐)
    
#### application

1. vector arithmetic 가능

    안경없는 이미지 + 안경 이미지 -> 안경낀 여자 이미지

    <img src="https://user-images.githubusercontent.com/52481037/100872918-e3dd0500-34e5-11eb-81ca-c488f25828ee.png" width="400"/>

2. interpolation 가능

    두가지 다른 이미지를 하나로 합칠 수 있음

    왼쪽 얼굴 + 오른쪽 얼굴 = 정면 얼굴

    <img src="https://user-images.githubusercontent.com/52481037/100873042-1424a380-34e6-11eb-8870-6b23dac112cc.png" width="400"/>
    
### 2-2. infoGAN

조작할 수 있는 GAN만듦(labeling data 사용해서 원하는 데이터 생성할 수 있도록 condition줌-D,G모두)

ef) CGAN(Generator만 condition)

<img src="https://user-images.githubusercontent.com/52481037/100873750-f9066380-34e6-11eb-9e53-b0f6c4ccdb71.png" width="200"/>

loss : cross entropy (real및fake 데이터 말고도 condition에 대해서도 feedback들어가야 하므로)


## 3. GAN Hacks

GAN 학습 잘 되게 하는 꿀팁

1. input 정규화 : -1 ~ 1 값으로 정규화 시켜서 넣음
 - 데이터 분산이 z-normalize되어 있으면 학습이 안정적으로 됨
2. modified loss function 사용
 - dis 학습 먼저 시키고, gen 학습
3. Z(latent)를 normal gaussian에서 뽑기
 - uniform보다는 학습안정적?
4. BatchNom사용하고, batch들을 real끼리, fake끼리 묶어서 학습하기
 - real,fake 섞여있으면 loss를 평균낼 때 섞여서 중간지점에 머무르게 됨
5. sparse gradient피하기 위해서 ReLU대신 LeakyReLU, Max pooling대신 average pooling이나 conv stride2사용하기
 - gradient가 0이 되지 않도록..
6. label smoothing or noisy label
 - discriminator가 헷갈리게 하기 위해 label을 0또는 1이 아닌 continous값을 주기(모델이 학습할 범위를 넓혀주기)
 - real data에 대해서 1이 아닌 1~0.7을 주거나 확률적으로 real data임에도 라벨 0을 줘서 헷갈리게 만들기