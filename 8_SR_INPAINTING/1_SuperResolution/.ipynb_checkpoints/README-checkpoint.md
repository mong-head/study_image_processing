# super resolution(SR)

이미지 크기 키우기(행렬 크기 키움)(고해상도)

## 1. problem

이미지 크기 키울 때 단순 행렬 크기를 키울 때, 빈 공간이 생김

따라서, 아예 없던 내용을 새로 생성해야함(빈 공간을 채우기 위함)

-> GAN이 괜찮은 성능을 보임

## 2. interpolation(보간법)

* linear
* cubic
* nearest neighbor

### 2-1. linear interpolation(선형 보간법)

두 개 포인트에 대하여

<img src="https://user-images.githubusercontent.com/52481037/100995449-20b70380-359b-11eb-92f1-3f02c6af6f24.png" width="400"/>

실존 점 : x1,x2

중간 점 : x = x1*(d1/(d1+d2)) + x2*(d2/(d1+d2))

### 2-2. cubic interpolation(삼각 보간법)

선형보다 곡선이 덜 딱딱해 보임.(자연스러워 보임) but costly(time,...)

곡선을 만들고 뽑아 쓰고 싶은 점을 씀

<img src="https://user-images.githubusercontent.com/52481037/100995645-683d8f80-359b-11eb-978b-70f54919a0be.png" width="400"/>

### 2-3. nearest-neighbor

가장 가까운 곳의 값으로 채워넣는 보간법

보간법뿐만 아니라 머신러닝 알고리즘으로도 많이 쓰이는 편임

<img src="https://user-images.githubusercontent.com/52481037/100996131-0c273b00-359c-11eb-864d-2a1843bc68e5.png" width="200"/>

### 2-4. bilinear interpolation ([구현](./simple_linear_interpolate.ipynb))

선형이 아닌 행렬에서 사용되는 보간법

많이 쓰이는 방법

<img src="https://user-images.githubusercontent.com/52481037/100996190-1e08de00-359c-11eb-9eab-48027b31e43c.png" width="200"/>

# SR in Deep learning

|기존의 deep learning|GAN|
|---|---|
|이미지의 높이,너비 줄이는 식으로 함|이미지 upscaling과정 존재|

### * decorder

* pre-upsampling : 잘 사용하지는 않음(먼저 upsampling하면 후에 conv에서 연산량이 많아지기 때문에)

<img src="https://user-images.githubusercontent.com/52481037/101000094-c5880f80-35a0-11eb-84fd-b39b59e2244f.png" width="500"/>

* post-upsampling : conv후에 upsampling, 가볍게 돌아야 하는 경우 이거를 사용하긴 함 but 극단적인 방법

<img src="https://user-images.githubusercontent.com/52481037/101000316-1435a980-35a1-11eb-8a54-d70337cf0bcd.png" width="500"/>

* progressive upsampling : 중간중간에 여러단계 거쳐가며 이미지 사이즈 키워감. 가장 대중적인 방법

<img src="https://user-images.githubusercontent.com/52481037/101000456-48a96580-35a1-11eb-805d-d9f7fe197af0.png" width="500"/>

* iterative up and down sampling : up,down sampling 반복, receptive field(얼마나 특징을 반영했는지)크기 키워줌(global한 특징 알아내어야 할 때 사용)

<img src="https://user-images.githubusercontent.com/52481037/101000745-a473ee80-35a1-11eb-9d80-b39b827d6dba.png" width="500"/>

### * upsample layer

1x64x4x4 (b c h w)

* interpolation : h,w 크게 함(channel수는 건드리지 않음)(대중적 보간법)
* transpose convolution : 이미지 사이즈 크기 키우는데 사용하는 conv
* reshape : tensor 차원 수 바꾸는데 많이 사용(channel수 희생해서 h,w에 더하는 방식)
* pixelshuffle

#### 1) transpose convolution

- padding 키우기

- stride 역할이 조금 달라짐
     - 보통의 stride : 이미지 사이즈 해당 숫자만큼 줄어듦
     - transpose convolution stride : 이미지 사이즈 해당 숫자만큼 커짐

stride가 비싼 연산 - 따라서 일반적으로는 linear interpolation으로 이미지 사이즈 크기 키우는 식으로 함

#### 2) pixel shuffle

* 단순 reshape : 공간의 정보를 가지는 channel을 희생해서 h,w를 넓힘 - 별로 추천하지 않는 방법

* pixel shuffle의 reshape, permute(transpose)
    * 가벼운 연산
    * 단순 reshape보다는 고루고루 데이터 펴줌(편향되지 않고)
    
    <img src="https://user-images.githubusercontent.com/52481037/101002388-55c75400-35a3-11eb-81e3-9e52caa1d95f.png" width="500"/>

## 1. SRCNN

최초로 SR문제를 딥러닝으로 품, 성능 굿

mapping : 이미지 안에 patch라는 작은 조각을 sampling함(고,저화질 모두 같은 위치 patch에 대하여)
    
   * 저화질 patch에 대해 매칭되는 고화질 patch mapping가능
   * 이미지마다 patch대한 정보 많이 생성 가능
   
<img src="https://user-images.githubusercontent.com/52481037/101004463-e9992000-35a3-11eb-8fa2-90552979535b.png" width="500"/>

저자가 해당 block?마다 자신이 생각하는 기능을 토대로 이름붙임

## 2. VSDR

네트워크 깊이가 깊어지면서 정확도를 더 높임

## 3. SRGAN([구현](./a-PyTorch-Tutorial-to-Super-Resolution/srgan_inference.ipynb))

GAN의 학습방식을 SR에 적용함

[G : 저해상도를 고해상도로 만들기] vs [D : 저해상도를 주었을 때 F, 고해상도는 T로 분류하기]

loss : VGGloss(GANloss)도입(MSE는 성능이 안좋음)

   * gan으로 생성된 가짜 feature과 진짜 고해상도 feature 각각을 VGG등에 넣었을 때 중간 결과를 비교하는 식으로 하는 loss
   
   <img src="https://user-images.githubusercontent.com/52481037/101008426-0e41c780-35a5-11eb-8793-3aeeed18762c.png" width="500"/>

## 4. DFDNET

실제로는 하나 class에 대한 이미지 SR 모델이 성능이 좋음

그 중 하나 예시가 DFDNET : face만 전문적으로 하는 거

얼굴 feature dictionary만들어서 얼굴 이미지 복원할 때 feature을 꺼내서 사용함

<img src="https://user-images.githubusercontent.com/52481037/101010150-91631d80-35a5-11eb-951b-8e7b8bc4dc0c.png" width="400"/>


# application

예전 화질 구진 영상을 복원 : [스타워즈 ai upscaling 영상](https://www.youtube.com/watch?v=4Ow2wpsIMzo)