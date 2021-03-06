





# 태양광발전량 예측모델
to predict solar energy

환경은 colab 으로 진행하였다.

# 주제: 과거 기상정보와 기상예보를 통한 일별 발전량 예측

입찰방식: 전날 10시 이전에 예측한값, 전날 10~17시 사이에 예측한 값으로 다음날 태양발전량과 비교후 (실제발전량-예측발전량)/발전용량 의 평균으로 예측오차를 산정한다.
수행기간 : 2021-07-20~2021-07-30







### 참고사항
use_data 파일은 원본 weather1의 파일을 local 환경에서 이미 전처리 하였다.
require.txt 에 local 환경에설치된 library 버젼이 기록되어있으므로 참고하면된다.



## Data processing and Modeling

처리방식은 weather1의 pressure 에 5가지 null 값이 들어가있어서 다른예보의 값으로 대체해주었고 
중복된 값은 duplicates 함수를 통해 마지막것만 남기고 삭제해주었다(이유는 제일 최근에 예측한값이 가장정확할것이기때문)


1. 시간의 간격이 일치하지않아 1시간별로 맞추는 작업을 진행하였고 duplicates 함수를 통해 마지막의 값을 남겨놓았다(마지막에 예측한값이 가장 정확하니까)
2. 결측치의 개수를 파악한다음 불연속으로 존재한다면 평균 or 다른 weather 이나 forecast 파일에서 알아내기 or bfill ffill 을 통해 채워주는 식으로 진행하였다
3. 시간의 연속적인 값을 표현하기위해 주기가 24인 sin,cos함수와  , 월을 표현하기위해 주기가 12인 sin,cos을 linear combination 하여도입하였다 ( 학부과정떄 배운 미분방저정식의 푸리에급수에서 아이디어를받음)
4. 모델링은 fbprophet,Neural prophet,xgboost,catboost 를 통해 제일 잘 예측하는 값을 찾아보았다.
5. Neural prophet은 예측력이 상당히 떨어져서 적용할수없었다.catboost 또한 xgboost 만큼의 성능을내지못함 (알아보니 classification 에 최적화되어있음,나중에 분류문제에 써야겠다)
6. xgboost,catboost 의 regressor 로 사용하였다.간단하게설명하자면 based on decision tree 로 regression 을 진행한다.
7. hyperparameter 가 상당히많아 전체 파라미터를 수정하려면 grid search 로 수시간이 걸려서 중요하다고 생각되는 test_size,depth,min_tree,gamma,est 를 grid search 로 진행하였다.
8. hyper parameter tuning 전과 후는 rmse 58-> 50, SMAPE -> 6.1->5.4 로 상당한 성능 향상이있었다.
9. 내장함수가 아닌 custom loss function 으로 과소추정하는 것을 방지하였다 



 ### 총평가: 
 비록 현재 7.28일 누적평균오차 3.15% 으로 나름 괜찮게 예측을 한것같지만 순위안에 들지못지못했다 ㅠㅠ.

 다음에 시간나면 다른분야도 도전해볼 예정


<img width="400" alt="스크린샷 2021-07-30 오전 12 14 54" src="https://user-images.githubusercontent.com/76778082/127518509-aaefcffc-b62e-4d3e-92a6-a1b53a0c10be.png">

<img width="400" alt="스크린샷 2021-07-29 오전 1 16 26" src="https://user-images.githubusercontent.com/76778082/127359862-fdb8a136-f695-44ba-a5b9-c6a52d72ace7.png">
<img width="400" alt="스크린샷 2021-07-29 오전 1 16 38" src="https://user-images.githubusercontent.com/76778082/127359872-189e667e-1942-43ec-b924-72c639dfeb09.png">
