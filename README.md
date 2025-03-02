딥러닝(Faster R-CNN) 과 OpenCV를 활용한 한라봉 병징 자동 감지 시스템

기후 변화로 인해 한라봉 병해 발생 빈도가 증가하면서 자동 탐지 및 분석 시스템이 필요해졌습니다.
본 프로젝트에서는 딥러닝(Faster R-CNN)과 OpenCV를 활용하여 한라봉 병징을 자동 감지하고, 색상 및 면적을 분석하는 시스템을 구축하였습니다.

1. 연구 목표
   -Faster R-CNN을 활용하여 한라봉 잎과 과실 클래스 검출
   -OpenCV 를 활용하여 검출된 클래스에 맞게 병징 탐지
   -잎(과실) 대비 병징의 면적을 계산하여 병징 진행도 판단

2. 연구 개요
   (1) 주요 병해 및 탐지 대상
     잎 : 궤양병, 점무늬병, 귤귤나방
     과실 : 총채벌레
     *병징별 특징은 논문 파일 참고 

   (2) 데이터셋 구축
     데이터 출처 : 한국지능정보사회진흥원(NIA) '국내 재배 아열대/열대 병해충 데이터' 에서 4,320장의 한라봉 이미지 사용
     훈련/검증/테스트 를 8:1:1 비율로 분할

   (3) 딥러닝 기반 한라봉 객체 검출 모델
     -Faster R-CNN 모델을 활용하여 객체를 검출하고 바운딩 박스 회귀
     -PyTorch 기반 학습 진행
       (a) SGD 옵티마이저 사용
       (b) StepLR 스케줄러 적용
       (c) epochs : 100 / batchsize : 8
       (d) Class(6개) : 정상 잎/정상 과실/궤양병/점무늬병/귤귤나방/총채벌레

   (4) OpenCV 기반 이미지 처리 및 병징 분석
      - 딥러닝 모델로 탐지된 영역을 HSV 색 공간으로 변환 후 필터링
      - Blur mask 로 배경 제거
         ![image](https://github.com/user-attachments/assets/6b079803-0118-4da2-b203-d2a40b8f0ff2)

      - Grabcut 알고리즘으로 잎/과실 윤곽 검출 => 녹색 윤곽선
        * 촏채벌레의 경우 Circle Mask 추가
          
      - 각 클래스별로 다른 병징탐지 로직을 적용하여 병징 탐지 => 빨간색 윤곽선
        * 각 클래스별 20장의 샘플이미지를 분석하여 최소-최대의 HSV 값 사용
        (a) 궤양병 : 진한 갈색~노란색 탐지
        (b) 점무늬병 : 녹색 계열 (H 44-79) 를 제외한 범위중 낮은 S값 및 V값 탐지
            ![image](https://github.com/user-attachments/assets/37572729-7631-4215-b84a-85a6a5f8677a)

        (c) 귤귤나방 : 낮은 S값 및 높은 V값 탐지
        (d) 총채벌레 : 과실의 평균 H값과 차이가 많이나는 부분 탐지
            ![image](https://github.com/user-attachments/assets/da2cffdf-e238-4c71-bdf0-5d3194d74867)



3. 연구 결과
   (1) 병징 검출 모델 성능 평가
   ![image](https://github.com/user-attachments/assets/dc64b64a-8fde-42d1-8d37-c32e04a26daa)

   -Faster R-CNN 모델의 mAP(mean Average Precision) = 0.7812
   -Precision-Recall 곡선 분석을 통해 성능 평가
      클래스별 mAP 값 : 최소: 0.4278/ 최대: 0.9771

   일부 병해에서 성능이 낮지만, 대부분의 병해에서 높은 정확도 달성

   (2) 병징 부위 분석 결과
    ![image](https://github.com/user-attachments/assets/cc36bcd8-9bac-4639-ba6f-cce58fb2f456)
    ![image](https://github.com/user-attachments/assets/e5ce49b3-b47e-4c7d-bac1-a9d8f44d76cc)


4. 향후 연구 방향
   - 초기 병징 탐지 성능 개선 필요
     병징이 초기 단계에서 나타나는 미세한 변화까지 탐지할 수 있도록 추가 데이터 확보 필요

   - 다양한 환경에서의 성능 검토 필요
     다양한 조명 환경 및 촬영 조건에서도 높은 정확도를 유지할 수 있도록 개선

   
   
   
   
