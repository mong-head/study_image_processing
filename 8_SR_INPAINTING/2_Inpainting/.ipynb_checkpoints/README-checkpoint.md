# inpainting

애초에 이미지에 손상이 있어서 그 손상된 부분을 복원하고자 할 때 사용하는 방법

application : 지우고 싶은 부분 걍 흰색으로 지우고 나서 지운 부분을 배경으로 채울 때 사용

<img src="https://user-images.githubusercontent.com/52481037/101017146-0d149880-35ad-11eb-842f-8963c13caa79.png" width="300"/>


## 1. patch match

deep learning이전 방식

최대한 비슷한 패치를 이미지에서 찾아서 빈공간으 메꿔나가는 방식

application : 배경에 잡티 제거에 사용(파란하늘에 전깃줄 - 전깃줄 없앨 때 사용)

단점 : patch를 사용해서 복원하기 때문에 새로운 것을 이용해서 복원하지 못함 - 좀 부자연스럽기도 함


-> GAN의 필요성

## 2. GAN based inpainting

deep learning 기반 inpainting : GANloss 기반으로 둠

### 2-1. GLCIC

<img src="https://user-images.githubusercontent.com/52481037/101018449-da6b9f80-35ae-11eb-95e3-0a828a6435e7.png" width="500"/>

* global : 이미지 전체 input

* local : completion network가 복원한 부분을 input으로 받음

* 두 discrimator 모두 진짜인지 아닌지 판단 함

* 복원해야 하는 부분과 그걸 포함하는 전체 이미지 둘다 있어야 어색하지 않은지 판단할 수 있음


<img src="https://user-images.githubusercontent.com/52481037/101018397-ccb61a00-35ae-11eb-83b2-b8cb0d0cce22.png" width="500"/>

* 이미지 전체적으로 채우고 나서 디테일한 텍스처를 복원하는 구조도 있음

* 대략적인 형태 복원 후 디테일 복원

#### 한계점

     1. 빈공간이 너무 커지면 잘 동작하지 않음

     2. 불규칙한 마스크에 대해서 약함

     3. 좋은 복원을 위해 추가한 로스함수가 잘못 동작할 때도 있음 (이상한 이미지를 생성해버림)
        

#### 한계점 해결 노력 : NVDIA 

<img src="https://user-images.githubusercontent.com/52481037/101020953-91b5e580-35b2-11eb-9dde-4a71e55d7393.png" width="300"/>

* nvdia연구

* 다양한 마스크 데이터셋 사용(마스크셋은 미리 생성해 놓은거 사용)

* unet 아키텍쳐 사용, 로스 함수 수정

[nvdia site]https://www.nvidia.com/research/inpainting/)

<img src="https://user-images.githubusercontent.com/52481037/101020014-2ddeed00-35b1-11eb-909c-ba186d4c76e4.jpg" width="400"/>


## video inpainting

복원 성능이 단일 이미지보다 훨씬 좋음(여러 프레임이 있어서 사용할 수 있는 정보가 더 많아지므로)

optical flow : 움직이는 것들의 움직인 시각화 - 이용해서 이미지 복원에도 쓰임