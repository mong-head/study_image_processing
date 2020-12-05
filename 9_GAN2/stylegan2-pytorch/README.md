# styleGAN


## 1. 여러 latent 적용 ([gender,age적용 code](./latent_change.ipynb))

seed고정해서 하나의 얼굴을 생성한 뒤, 생성한 얼굴에 대해서 age, gender latent 수정하여 보기

### result

#### age

|1|2|3|4|5|
|---|---|---|---|---|
|<img src="./sample/age/000000.png" width="100"/>|<img src="./sample/age/000001.png" width="100"/>|<img src="./sample/age/000002.png" width="100"/>|<img src="./sample/age/000003.png" width="100"/>|<img src="./sample/age/000004.png" width="100"/>|

#### gender

|1|2|3|4|5|
|---|---|---|---|---|
|<img src="./sample/gender/000000.png" width="100"/>|<img src="./sample/gender/000001.png" width="100"/>|<img src="./sample/gender/000002.png" width="100"/>|<img src="./sample/gender/000003.png" width="100"/>|<img src="./sample/gender/000004.png" width="100"/>|


## 2. projection ([projection code : trump](./projecting.ipynb))

원본 이미지 -> project -> latent들 뽑아냄,뽑아낸 것으로 재생성한 이미지

 * CPU 환경 - GPU로 하고 싶다면 projector.py에서 device=GPU하면 될 듯
 * CPU라서 step500으로 뽑아내는 데 시간이 많이 걸림. 정확하게 재보지는 않았지만, 3시간이상 걸린듯함
 * step수가 적어서 사람 얼굴이 아닌 것 같음.. 복원 되다가 만 얼굴 생성 
 * latent 뽑아낸 것 : ./trump_face.jpgproj.pt

|원본 이미지|step 50|step 500|
|---|---|---|
|<img src="./trump_face.jpg" width="100"/>|<img src="./sample/trump/Trump_50.png" width="100"/>|<img src="./sample/trump/Trump.png" width="100"/>|

## 3. toonify ([toonify code : 실제 돌린 환경은 Colab](./Toonify.ipynb))

디즈니 캐릭터처럼 만듦

 * GPU 환경 : step 1500이라도 빨리 생성(몇 분 안 걸린 듯)

### 아이유

|원본 이미지|projection|toonify|
|---|---|---|
|<img src="./toonify_images/IU/example.jpg" width="150"/>|<img src="./toonify_images/IU/example_01.png" width="100"/>|<img src="./toonify_images/IU/example_01-toon.jpg" width="100"/>|


### 수지

|원본 이미지|projection(step 1500)|toonify|
|---|---|---|
|<img src="./toonify_images/suzy/example.jpg" width="100"/>|<img src="./toonify_images/suzy/example_01.png" width="100"/>|<img src="./toonify_images/suzy/example_01-toon.jpg" width="100"/>|