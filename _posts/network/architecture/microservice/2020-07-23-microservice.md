---
layout: post
title:  "Microservice의 특징"
comments: true
categories: [Network, Architecture, Microservice]
shortinfo: "클라우드 환경의 필수쯤으로 자리잡은 Microservice Architecture의 특징을 알아본다."
tags: [Network, Architecture, Microservice]
---

### Microservice란?

서비스 디자인 스타일로서 작은 서비스의 결합을 통해 하나의 응용 프로그램을 개발하는 방법   
각각의 서비스는 독립적인 비지니스 로직으로 구성   
완전 자동화된 개발/배포 환경에 의해 각각 독립적으로 배포   
최소한의 중심적인 관리 체계   
각 시스템들은 다른 프로그래밍 언어, 다른 데이터 스토리지 기술로 작성 가능

![Microservice Architecture](/assets/media/20200723_microservice_1.png)

### Microservice를 사용하는 이유?

서비스를 충분히 작은 크기로 나누어 개발하되 상호 연계를 통해 좀 더 복잡하고 거대한 시스템을 만들어 갈 수 있음   
스케일링에도 높은 자유도 -> 클라우드 컴퓨팅이나 고확장성 시스템의 요구조건에 정확하게 부합

#### Monolithic Architecture와 비교

`Monolithic 장점`
- 하나의 애플리케이션만 개발하면 되기때문에 배포 및 테스트가 편리함
- 컴포넌트간 함수 호출을 사용하기 때문에 높은 성능을 보임
- 개발 레포지터리나 패키지의 운영/관리가 용이함

`Monolithic 단점`
- 시스템 규모가 크면 빌드/배포 시간, 서버 가동시간등이 오래걸림
- 한 사람의 실수가 전체 시스템 빌드 실패를 유발함
- 프로젝트 규모가 클수록 협업이 어려움
- Tight Coupled되어 컴포넌트의 문제가 다른 컴포넌트에 영향을 미침
- 개발자 개개인이 대략적인 전체 시스템을 이해해야함
- 컴포넌트 별로 다른 기술을 도입하기 어려움

`Microservice 장점`
- 서비스를 독립적으로 개발 및 배포/운영할 수 있음(Loosely coupled)
- 독립적인 서비스별로 확장을 유연하게 할 수 있음(More Scalability)
- 소프트웨어의 구조가 조직 구조와 일치함(More Agility)
- 새로운 기술 도입 및 변경이 용이함(More Flexibility)
- 서비스의 재사용성이 높음(More Reusability)

`Microservice 단점`
- 다른 컴포넌트의 데이터를 API를 통해서 가져오기 때문에 성능이 떨어짐
- 서비스간의 트랜잭션을 묶을 수 없음
- 공통 기능에 대해서 서비스별로 중복이 발생하고 비효율적으로 메모리가 사용됨
- 서비스가 많고 기술이 다양해짐에 따라 End-to-end 테스팅의 복잡도가 높고 운영이 어려움
- 마이크로서비스에 대한 높은 기술적 이해와 개발 구성원의 높은 성숙도를 필요로 함

### Microservice 도입 시 고려사항

#### Performance

서비스 간의 호출이 통신을 통해서 이루어지기 때문에 네트워크 전송 오버헤드가 발생   
성능에 민감한 서비스에서는 Microservice 도입이 어렵거나 `Monolithic을 혼합하여 디자인`

#### Transaction Processing

Monolithic에서는 RDB를 사용하면 하나의 애플리케이션 내에서 트랜잭션에 문제가 있으면 DB의 기능을 이용해서 Rollback   
Microservice는 여러 서비스의 API 기반 트랜잭션을 묶는 것이 불가능

`해결책`
1. 애플리케이션 디자인 단계에서 여러개의 API를 하나의 트랜잭션으로 묶는 분산 트랜잭션 시나리오 자체를 없애는 방법
2. 분산 트랜잭션이 반드시 필요하고 중요시되는 시스템에서는 Monolithic으로 접근하는 방법 (트랜잭션 보장이 중요시되는 금융, 제조와 같은 엔터프라이즈 시스템에서는 적합하지 않고, 대규모 처리가 필요한 B2C형 서비스에 적합)
3. 트랜잭션 실패시 보상 트랜잭션을 통한 예외처리 로직을 애플리케이션에서 처리
4. 복합 서비스를 만들어서 트랜잭션을 묶어야 하는 두개의 시스템을 지원하는 네이티브 프로토콜을 이용해서 구현하여 하나의 API를 노출시키는 방법 (두개의 서비스가 tightly coupled되어 Microservice의 상호 독립성 사상에 위배되고 서비스 변경시 이 부분을 항상 고려해야 함)

#### Code Duplication

다양한 언어와 기술을 사용하여 개발될 수 있기때문에 다른 서비스와는 독립적으로 최적화된 언어/기술을 사용하여 개발할 수 있다는 장점   
- 동일한 기능을 수행하는 코드를 서비스별로 중복 작성하게 될 수 있음
- 중복 문제를 해결하기 위해서는 API Gateway에서 이러한 공통 기능을 처리
- API Gateway는 최대한 가볍게 가져가야 한다는 설계 원칙을 고려

**※ API Gateway**   
API Gateway란 프록시 서버처럼 각 서비스의 API들 앞에서 EndPoint(API의 URL)를 통합하고, 공통 기능등의 추가 기능을 제공하는 미들웨어   
SOA의 ESB(Enterprise Service Bus)의 경량화된 버전으로, API Gateway에서 무거운 로직을 수행하거나 잘못 설계되면 실패 가능성이 매우 높은 컴포넌트

#### Data Duplication

서비스별로 적절한 데이터 스토리지 솔루션을 사용하여 구성될 수 있기 때문에 이에따른 데이터 중복이 발생   
- 사용하는 솔루션에 따라서 적당한 데이터 모델링으로 디자인하여 해결
- 다양한 데이터 스토리지 솔루션 사용에 의한 비용/성능의 장점을 고려하여 데이터 중복을 허용할 수 있음

#### Memory Duplication

서비스 별로 독립된 서버 또는 컨테이너에 분할 배치되기 때문에 동일한 모듈이 중복으로 메모리에 올라갈 수 있음

#### Operation Overhead

서비스 개수가 증가함에 따라서 많은 양의 배포 및 릴리즈 작업이 수반   
각 서비스들이 서로 다른 기술을 사용할 수 있기때문에 필요한 기술도 늘어남   
- 배포 및 릴리즈에 수반되는 모든 작업들이 철저하게 자동화
- DevOps의 도입이 필수적

#### Testing Challenges

각 서비스가 분리되어 비동기적으로 동작하기 때문에 런타임 환경에서의 상호작용을 테스트하기 어려움   
- 각 서비스의 테스트 초기단계에서 최대한 Coverage를 높여서 End-to-end 테스트를 최소화하는 전략[Testing Strategies in a Microservice Architecture](http://martinfowler.com/articles/microservice-testing/)
- Unit > Integration > Component > Contract > End-to-end 순으로 Unit Test를 통해서 최대한 Test Coverage를 달성하고, 까다로운 End-to-end 테스트를 최소화

![Microservice Testing Summary](/assets/media/20200723_microservice_2.png)

#### Team Management

도메인 단위의 서비스가 독립적으로 개발/배포되기 때문에 팀 운영에 있어서도 중앙집권적 체계를 벗어나 각 팀이 독립적으로 운영(self-organized team)
- 최적화된 팀 규모: 의사소통이 효율적이고 의사결정 속도가 빨라져서 생산성이 향상되고 변화에 대한 수용과 대응에서 큰 장점을 지님
- 독립적인 운영: 팀 운영에 있어서 유연성이 향상되고 팀원에게 높은 권한과 책임이 주어져 주인의식을 가지고 일함
- 아키텍쳐 선택의 자율성
- 상향식 개발
- 팀의 영속성 보장: 팀별로 다른 형태의 표준과 기술 프로세스를 통해서 개발하기 때문에 잦은 조직 이동이 있는 경우, 개개인이 가진 기술 노하우를 재활용하기 어렵고 새로운 팀원에 대한 교육과 적응이 필요
- 높은 수준의 팀 성숙도 요구: 팀내에서 기획/개발/운영을 독립적으로 수행해야 하기때문에 개발과 운영에 높은 성숙도가 요구

### 참조 링크

[What are Microservices?](https://medium.com/@goinhacker/microservices-a9d773c159cc)