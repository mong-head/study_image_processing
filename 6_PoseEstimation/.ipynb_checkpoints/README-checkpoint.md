# Pose estimation

## 0. Data Set

1. coco dataset 
    * 대부분 이거 사용
    * 약 25000만명의 사람
    * 상태 label : 가려짐,안보임,있음
    * 17개의 관절 (목은 평균으로 구함 - 양쪽어깨의 평균?)
    
2. MPII dataset  
    * 위보다는 적은 사람 및 이미지
    * 상태 label : 가려짐,안보임

## 1. single person

 <img src="attachment:de6c26ce-7e8a-4498-8771-b3ffe02b1648.png" alt="drawing" width="400"/>

 이미지 하나에 하나의 사람 있는 task (어렵지 않음)
 (x,y)로 표현 - 성능 bad

### 1-1.heatmap based

![image.png](attachment:83cbdddc-d841-410f-8991-9bccc74f93ac.png)

 히트맵 channel들 : 장마다 각각 관절이 있음
 
 관절 좌표 직접 예측X, 있을법한 곳을 히트맵으로 보여줌(관절있을법한 확률을 히트로 매핑)
 
 이것처럼 (x,y)좌표가 아니라 있을법한 곳을 보여줌 - 성능 good
 
#### 1-1-1.heatmap

 가우시안으로(확률값이니깐)
 
 사람 많을 때에는 어떻게 표현??
 
     사람 1명 : 있을법한 곳에 확률 1
     사람많을때?? 다른 사람의 확률이 전의 사람의 확률을 덮어버림
     --> 사람마다 channel만듦, maxium연산으로 나중에 합침
     

 <img src="attachment:a257b796-3a48-4c8a-baae-e4b7e7495222.png" alt="drawing" width="500"/>
 
## 2. Multi-person

 누구의 어깨인지 어떻게 알까??
 
### 2-1. Top-down

detection사용해서 사람있는 박스 찾기 -> 그 후 pose estimation
(faster R-CNN (person detector) -> ResNet)

* 사람이 겹쳐져 있을때에는?(top-down의 주요 task)

 * 1의 팔이 아니라 2의 팔을 1의 팔이라고 잘못착각해서 1의 몸에 2의 팔을 이을 수 있음
 
 
* 특징 
 * 연산량이 많은 편(찾아낸 모든 영역을 처리하기 때문) - 단점
 * 정밀하게 사람 자세 예측 가능(예측 성능 굿) - 장점
 * 코드양은 작은 편(간단한 편)
 
#### 2-1-1. stacked hourglass 여러개 합쳐진 형태

encoding으로 이미지 줄이고, decoding으로 이미지 늘림 (인코더,디코더 모래시계형태)

<img src="attachment:181da8ac-84bb-4589-8a69-34cd9fb8ea9b.png" alt="drawing" width="500"/>

<img src="attachment:750947d4-3731-4c41-99f6-3cddafd5efcb.png" alt="drawing" width="500"/>


피라미드 풀링과 유사한 형태

### 2-2. bottom-up

* 목적 : 한번에 이미지 내부의 모든 관절 위치 찾음
* 누구의 관절인지 조립하기 위해서 부수적인 정보를 예측함(조립 알고리즘 필요)
 * grid algorithm : 가까이 있는 관절을 연결한다던지 - 사람이 겹쳐지지 않은 경우 잘 작동, 겹쳐져있는 경우 한계 존재
 
 
* 특징
 * top-down보다 빠름
 * top-down보다 정밀하진 않음

* 간단한 재미용으로 사용하는 편(캐릭터를 입힌다던지)

#### 2-2-1. openpose

* open source
* 예측 잘하는 편
* opencv에도 있음

![image.png](attachment:9807447c-22f6-4650-887e-a6d32a7296a4.png)

    a) input image
    b) 모든 관절에 대해 히트맵으로 출력(누구의 것인지는 모름)
    c) 관절 조립 보조 수단; 어깨에서 팔목으로 가는 방향성등을 표현하는 히트맵 비슷한 형태 - 방향 예측을 이미지 형태로 출력
        ex) 어깨에서 팔로 가는 방향 
    d) 매칭 알고리즘 ; 조립


* 구조 
 
 <img src="attachment:affac573-b045-42a2-b416-d974a0b5a4f9.png" alt="drawing" width="400"/>

    backbone : VGG19

    F : feature

    part affinity map 

    * 모든 관절에 대해서 방향성 구함 (A.s - A.w, A.s - B.w, B.s - B.w, B.s - A.w) s :shoulder, w : wrist

        * 방향의 확률을 모두 구해서 조립에 도움이 되는 정보를 처리하는 것
        

#### 2-2-2. associative embedding

사람마다 그 사람이 있을법한 위치를 색깔로 구분 - 같은 색깔이면 같은 사람의 관절 - 이를 바탕으로 조립

![image.png](attachment:15445c5f-5562-4eab-a44e-cd34abc92ffb.png)

만든 사람 주장으로 잘 작동한다고 주장함

알고리즘, 코드 모두 쉬운편

#### 2-2-3. Personlab

multi-person method

heat map + offsets

- (short/mid)offsets 벡터 - 관련 관절이 얼마만큼 떨어져있을지 예측
 
 - short offset으로 mid offset(관절사이 연결성) 미세 보정함
 
segmentation도 동시에 할 수 있음

성능 측정으로 AP(average precision) 사용


### 2-3. 3D pose estimation

![image.png](attachment:f582f387-49f9-459f-89d0-722f17a9e909.png)

(x,y,z)예측

z를 위해서 특수 장비가 필요 (일반인이 하기는 어려움)
   - sensor달린 옷을 입어서 좌표마다 센서가 달려서 카메라와 센서사이 거리를 구해 데이터로 사용해야함
   
dataset : MPI-INF-3DHP, H3.6M (영상 데이터)

한계 : 스튜디오 환경 데이터를 사용 - 야외에서 작동 잘 안됨

#### 2-3-1. VNect

top-down방식

3D 좌표 히트맵 예측함 (heatmapss + Location maps)

![image.png](attachment:a76f5152-6f6e-4e56-8507-b584f818e628.png)

성능 : (예측y - 실제y)제곱후 root 값 사용(MPJPE) mm단위로 표현해줌

### 3. other application

#### 3-1. Everybody dance now논문

- 춤 못추는 사람들도 이거를 이용해서 잘추게 되도록

https://www.youtube.com/watch?v=PCBTZh41Ris

Pose estimation과 GAN을 사용한 retargeting결합

#### 3-2. SMPL  
- 사람 모형 사용해서 딥 러닝으로 마네킹 자세 예측 
- 실제 취할 수 있는 자세인지 모델링한 마네킹
- 데이터 만드는데 이거 사용하기도 함

![image.png](attachment:1aef8988-aaaf-495d-8c09-3836c5d59f03.png)