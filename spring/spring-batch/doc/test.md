# 스프링 배치 도입
많은 엔터프라이즈 에플리케이션에서 중요한환경에 작업들을 처리하기위해 대량에 처리를 한번에 할필요가 있다. 이러한 비지니스적 작업들은 유저에 처리 없이 자동화되어 많은 양의 작업들이 한번에 처리되는것을 의미한다. 이러한 작업들은 대부분 시간 기반이다.(달 계산, 통지, 응답) 배치처리는 하루에 수백만건의 트랜잭션들을 처리하기위해 사용되어진다. 스프링 배치는 가볍고 종홥적고 일상적인 엔터프라이즈 시스템들에서 튼튼한 배치를 처리하기위해 설계되어졌다. 스프링배치는 스케줄링 프레임워크는 아니다. 많은 엔터프라이즈급 스케줄러들이 존재한다. 예를들어 오픈서스 기반에 쿼츠, Tivoli, Control-M 등이 있다. 배치는 스케줄러를 대체하는게 아닌 결합하여 사용하는것이다.

스프링 배치는 많은양의 작업처리, 기록, 로그처리, 트랜잭션관리, 잡통계처리, 잡 재시작, 스킵, 자원관리등을 해준다. 

## BackGround
기존 오픈소스나 커뮤니티들은 SOAA기반의 메시징기반에 초점을 맞췄었다. IT환경에 지속적으로 재사용가능한 배치처리가가 필요함에도 불구하고 이러한것들은 부족했었다. 이러한 재사용가능한 배치작업 표준이 없어기때문에 모든 사람들은 1회성 배치잡을 만들어 사용했다.

SpringSource 그리고 Accenture 는 콜라보하여 배치를 내놨다. 이 배치는 추가적인 상세사항 그리고 실제 제약조건의 문제들을 해결해줄 수 있는것들을 제공한다. 이러한 정교한 스프링 배치로인해 회사들 그리고 정부 대해생사들은 이러한 배치 표준으로인해 혜택을 누릴것이다.

## 사용 시나리오장