# study_image_processing
영상처리 공부 
* jupyter notebook사용 (anaconda)
* pytorch 사용

## 1. pytorch tutorial ([pytorch_tutorial_cnn](https://github.com/mong-head/study_image_processing/tree/master/1_pytorchtutorial_cnn))

tensor 사용방법 : [tensor tutorial](https://github.com/mong-head/study_image_processing/blob/master/1_pytorchtutorial_cnn/1_tensor_tutorial.ipynb)
* torch : numpy와 비슷한 기능 제공. 간혹 numpy와 torch 다른 부분이 있을 때에는 totensor등을 사용해서 tensor로 변환하기
* cuda : GPU사용관련 기능

convolution filter 연산 구현 : [filter 구현해보기](https://github.com/mong-head/study_image_processing/blob/master/1_pytorchtutorial_cnn/3_convolution_filter.ipynb)
* convolution filter 연산 구현 : padding, stride도 구현해봄

CNN 구현 : [CNN 구현](https://github.com/mong-head/study_image_processing/blob/master/1_pytorchtutorial_cnn/5_my_cnn.ipynb)
* ( convolution layer -> maxpooling layer ) * 5 -> classification layer(linear layer)

## 2. 이미 잘 알려진 Net 구현 ([classification_net](https://github.com/mong-head/study_image_processing/tree/master/2_classification/make_net))

구현 : Lenet, Alexnet, VGGnet, googlenet, ResNet

1. Lenet([Lenet 구현](https://github.com/mong-head/study_image_processing/blob/master/2_classification/make_net/make_lenet.ipynb)) : 시초 net, 굉장히 간단한 구조, 5x5 filter 적용 

2. Alexnet([Alexnet 구현](https://github.com/mong-head/study_image_processing/blob/master/2_classification/make_net/make_alexnet.ipynb)) : Lenet보다는 복잡하지만 그래도 단순한 구조, parameter 및 연산량이 Lenet보다 많아짐
    * 보통 parameter수와 연산량은 convolution layer보다는 classification하는 linear layer에서 굉장히 많음
      * convolution layer parameter 수 : (filter 11x11x3가 96개 있는 경우 : (11x11x3+1)x96 = 14kB) 
      * linear layer parameter 수 : (linear(4096,4096) -> 4096*4096 = 160M)
    * linear layer보다 더 적은 parameter수를 가지는 convolution layer의 parameter수도 임베디드/모바일에서 처리하기 힘든 양임

3. VGGnet([VGGnet 구현](https://github.com/mong-head/study_image_processing/blob/master/2_classification/make_net/make_vggnet.ipynb)) : alexnet보다 더 복잡해진 구조, 더 성능이 좋아졌으나 연산량도 굉장히 많아짐
    * VGG net은 여러 버전이 있음 (11 layers ~ 19 layers)
    * 성능은 19layer가 가장 좋음(VGG19)
    * 많이 쓰이는 것은 16layer(VGG16)
    * 구현 : VGG11, VGG16, VGG19

4. GoogLenet([GoogLenet 구현](https://github.com/mong-head/study_image_processing/blob/master/2_classification/make_net/make_googlenet.ipynb)) : 층은 더 깊어졌지만, 연산량을 대폭 줄임(1x1 convolution layer, 중간 결과 내높음 - 자세한 설명은 해당 링크에 가면 있음)
    * 구현은 inception block만 구현해봄

5. ResNet([ResNet 구현](https://github.com/mong-head/study_image_processing/blob/master/2_classification/make_net/make_resnet.ipynb)) : 층을 더 깊이 만들면서도 본래의 특징을 유지하는 방법, 요즘 대부분의 net은 resnet의 개념을 적용함
    * resnet도 여러 layer가 있지만, 34 layer 구현해봄
