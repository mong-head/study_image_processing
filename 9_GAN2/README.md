# pix2pix

domain 변경하는 거

#### application
* 흑백 만화 -> 색입히기
* 겨울 풍경 -> 가을 풍경
   <img src="https://user-images.githubusercontent.com/52481037/101150723-fd0bc000-3663-11eb-9c8f-164c3143046f.png" width="400"/>


#### 특징

1. 일반 GAN과 비슷

2. 생성된 이미지와 바뀔 이미지 간에 L1 loss 추가함 : 없으면 어색한 이미지가 나올 수 있기 때문

#### 단점(제약 조건) : 데이터 수집 어려움

1. A 도메인과 B 도메이 데이터 모두 있어야 한다(easy task)

    * 사과 -> 오렌지 바꾸기 : 사과 데이터, 오렌지 데이터 둘 다 있어야함
    
2. 도메인 데이터들이 쌍을 이루어야 함 (hard task)

    * 사과이미지에서 사과 이외 모든 것이 오렌지 이미지에서 오렌지 제외 모든 나머지와 같아야함
    
# CycleGAN

pix2pix 2번째 단점 해결(paired 데이터 셋 필요하지 않음)

<img src="https://user-images.githubusercontent.com/52481037/101151997-cb93f400-3665-11eb-86ba-c9c48a644c22.png" width="600"/>

#### 특징

* generator가 두개 필요 : Gab(a도메인에서 b도메인 생성), Gba(b도메인에서 a도메인 생성)
    
    <img src="https://user-images.githubusercontent.com/52481037/101151533-21b46780-3665-11eb-8767-2e7f498e93e9.png" width="400"/>

* cycle-consistency loss : 선이 아니라 텍스처만 바꾸도록 학습 됨
    
    <img src="https://user-images.githubusercontent.com/52481037/101151765-7c4dc380-3665-11eb-8d20-b4b9d857b475.png" width="300"/>
    
    
#### application

[youtube: Fortnite -> PUBG](https://www.youtube.com/watch?v=xkLtgwWxrec)


#### 단점

* 사람->캐릭터 잘 안됨

* 사람 성별 바꾸기 잘 안됨

즉, shape이 비슷한 것만 잘되는 편

# SinGAN

하나의 이미지로만 GAN 학습시키는 방법 (기존은 하나 학습할 때 많은 데이터 필요했음)

학습 시킨 이미지와 비슷한 분위기, 해상도 커지거나 하는 이미지 만들 수 있음

#### 구조

![image](https://user-images.githubusercontent.com/52481037/101153031-31cd4680-3667-11eb-893d-d0aa25458541.png)

* 일반적 GAN과 구조가 조금 다름

* generator, discriminator를 여러개 사용함

* 노이즈 낀 이미지를 노이즈가 없는 fake image로 만들고, 작은 이미지로 만든 뒤, 또 다른 노이즈와 같이 input으로 주어지게 하고 또 노이즈 없는 또다른 fake image만듦

#### 특징

* 노이즈 : 노이즈가 끼어서 하나의 점뿐만아니라 그 점을 둘러싼 공간적인 것을 학습하게 됨

#### application

다양한 곳에 적용될 수 있다.

이미지 하나의 텍스쳐 및 특징을 다른 곳에 적용할 수 있음

<img src="https://user-images.githubusercontent.com/52481037/101153287-925c8380-3667-11eb-8eac-56555c8cfc31.png" width="400"/>

* 스케치->리얼한 이미지, 단일 이미지->여러프레임의 동영상,...

내생각 : 이거로 교묘하게 저작권 피해갈 수 있을 듯

미술 작품에 적용해보기(고흐) : 이미 있음(image harmonization)

![image](https://user-images.githubusercontent.com/52481037/101153875-50800d00-3668-11eb-8a22-3693fc814ea5.png)

[말미잘이미지-> 움직이는 말미잘동영상(animation)](https://www.youtube.com/watch?v=xk8bWLZk4DU)

#### 단점

영상 형태 복잡해지면 그건 singan으로 못 만듦(사람이 뛰어다니는 영상 같은거는 못 만듦)

# style GAN([code; readme참고](./stylegan2-pytorch))

거의 대부분의 사람 얼굴 복원 가능한 모델

얼굴 이미지(1024x1024)를 대표적으로 여러가지 스타일 적용할 수 있게 하는 모델

but 자동차나 다른 가구 등도 복원할 때 사용하기도 함

일반적인 모델 구조와는 살짝 다르다.

정제된 latent가 style gan에 들어감 (latent 역할 조금 더 확장함)

<img src="https://user-images.githubusercontent.com/52481037/101156248-a5715280-366b-11eb-9ee0-a49cd9b13947.png" width="300"/>

noise와 latent역할이 약간 다름

* latent : style씌워주는 역할

    * small style latent : 얼굴 모양
    * middle style latent : 눈 모양, 코 모양 결정
    * 나머지 : 컬러감 등등 디테일한 부분 담당

* noise : 세부적인 디테일 살림, 매번 이미지 생성할 때마다 각기 다른 이미지 생성에 도움 줌, 굉장히 자연스럽게 보이게 해줌

    <img src="https://user-images.githubusercontent.com/52481037/101156788-760f1580-366c-11eb-866d-5fd941944544.png" width="200"/>
    
    * a : noise 줌
    * b : noise 안줌
     

#### application

1. style mixing 

    <img src="https://user-images.githubusercontent.com/52481037/101157358-575d4e80-366d-11eb-9129-a72ae3932a92.png" width="300"/>
    
    * styleGAN 결과물(하나의 source에 대해 여러 latent 형태)이용해서 다양한 사람 얼굴 생성 가능 

    * 하나의 사람 이미지에 대해 여러 latent 조정 -> 여러 이미지 생성 ([age,gender대해 변경](./stylegan2-pytorch/latent_change.ipynb))
        * 안경x -> 안경씌우기 가능
        * 나이 조절 가능(7살->80살)
        * 성별 조절 가능(여->남,남->여)
    
    * toonify(서로 다른 domain 섞기) ([jupyter:수지 디즈니 캐릭터화](./stylegan2-pytorch/toonify.ipynb),[Colab환경 코드](https://colab.research.google.com/drive/1ytDq2l1FQlyOT0MTxDWq1vbVTAplirpC?usp=sharing))
        
        * 사람 - 디즈니 캐릭터 가능 
        * 사람 - 그림(옛 고화)
        * 사람 - 지구

2. projecting ([trump 얼굴 복원](./stylegan2-pytorch/projecting.ipynb))

    특정 사람의 얼굴을 복원하고 싶을 때 사용(latent vector encoding)

    deep learning

    * (latent->image만들기->원래 이미지(target)와 비교(loss구함) -> 최적화 : latent대해 backpropagation) 반복 - target에 가까워질때까지
    * backpropagation시 미분이 필요한데 deep learning network는 미분이 가능
    
    시간이 오래걸림, GPU이용하는 것이 좋음