# 데브옵스(DevOps)

> DevOps(Development + Operations)
{: .prompt-info}

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/continuous-development-visual.png" alt="continuous-development-visual" style="zoom:50%;" />

## 등장배경

* 소프트웨어 시장이 `서비스` 로 넘어가면서, 개발 만큼이나 안정적인 운영의 중요성이 대두됨
* <u>안정적인 운영을 위해서는 개발의 속도가 낮아지는 문제점이 존재했음</u>
  * 애자일 개발 문화에서 이러한 점은 치명적이였음
* 애자일 개발 방법론에 맞추어 개발자들은 **운영의 자동화를 고민**하게 되었음
* 하나의 서비스에 대해서 `개발 / 운영` 부서를 나누어서 관리하는 것은, 비효율적이였음
  * 업무의 공유 및 처리 속도 지연
  * 커뮤니케이션 문제

## 장점

* 더 우수한 제품을 빠르게 제공
* 신속한 문제 해결 및 복잡성 감소
* 확장성 및 가용성
* 안정적인 운영환경
* 리소스 활용률
* 시스템 결과에 대한 가시성

## 과정

1. Continuous Development
   * 지속적으로 코드를 유지보수할 수 있도록 `SCM(Source Code Management)` 를 활용
2. **Continuous Integration**
   * SCM에서 소스코드를 가져와서
   * 빌드한 다음
   * 소스 코드의 품질을 검토하고 (빌드된 결과물에서 품질을 측정하고, 이를 보고서 형태로 생성)
   * 빌드된 artifact를 저장한다
3. Continuous Testing
   * 테스트 코드를 기반으로 테스팅을 수행하고
   * 코드 스멜을 체크할 수 있다
4. **Continuous Deployment / Continuous Delivery**
   * Continuous Deployment := `테스트와 빌드 단계를 모두 완료한 다음, 자동으로 애플리케이션을 프로덕션 환경에 배포하는 프로세스`
   * Continuous Delivery := `테스트와 빌드 단계를 모두 완료한 다음, 애플리케이션을 프로덕션 환경에 수동으로 배포하는 프로세스`
     * Continuous Integration 과정을 자동화하지만, 수동적으로 배포를 수행
5. **Continuous Monitoring**
   * DevOps 환경에 대한 안정성을 위해서, 지속적인 모니터링을 자동화하는 과정도 중요함
   * 문제가 발생하기 전에 알림을 받을 수 있음
   * CPU/Memory 사용률, 네트워크 트래픽, 애플리케이션 응답 시간, 오류율 등의 성능 측정값들을 수집할 수 있음
     * 시각화 툴과 함께 사용한다면, 효과가 더 좋음
6. Continuous Feedback
   * endpoint user가 실제 애플리케이션을 사용할 때, 사용자 경험에 영향을 미치는 요소에 대한 피드백을 제공
   * 이러한 피드백들을 반영하여 개발 팀들에게 전달하는 과정을 수행하며 애플리케이션의 성능을 높히고 버그를 줄인다
7. Continuous Operations
   * 각 과정들은 주기적으로 개선될 필요가 있으며
   * 더 나은 프로세스를 위한 노력도 결합되어야함


