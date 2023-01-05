## 📌 1. <span style='background-color: #dcffe4'>Broken Access Control</span>
응용 프로그램이 시간이 지남에 따라 발전하고 수많은 기능이 로드되면 오류 발생하고 응용 프로그램의 보안이 저하될 수 있다.

## 📌 2. <span style='background-color: #dcffe4'>Cryptographic Failures</span>
암호화 및 알고리즘의 잘못된 사용으로 일어난다. SFTP, HTTPS, SSH 사용을 권장하고 모등 데이터를 권장하는 해싱 알고리즘으로 저장하는 것이 좋다.

## 📌 3. <span style='background-color: #dcffe4'>Injection</span>
가장 위험하고 널리 퍼진 공격으로 SQL Injection과 XSS(Cross-site Scripting)이 있다.
안전한 API와 서버 측 입력 유효성 검사를 사용하면 인젝션을 방지하는데 도움이 될 것이다.

## 📌 4. <span style='background-color: #dcffe4'>Insecure Design</span>
개발자는 설계하는 동안 안전한 설계 패턴과 안전하게 생성된 위협 모델링 사용을 권장한다.
보안 구성 요소 라이브러리와 도구 및 방법론을 사용하여 구축하는 것이 좋다. 보안 설계 수준을 결정해야 한다.

## 📌 5. <span style='background-color: #dcffe4'>Security Misconfiguration</span>
부정확하거나 안전하지 않게 구성된 보안 제어이다. 불필요한 기능 활성화나 설치, 오래된 소프트웨어 등을 사용할 때 발생한다.
Application 스택, 클라우드나 네트워크 계층에 영향을 미쳐 중요한 정보가 노출될 가능성이 높다.
예방을 위해 보안 설치 프로세스를 구현하고 자동화된 프로세스를 사용하는 것이 좋다.

## 📌 6. <span style='background-color: #dcffe4'>Vulnerable and Outdated Components</span>
취약하고 오래된 구성 요소는 전체 애플리케이션 손상 가능성을 높여준다.
개발자는 사용 중인 구성 요소의 버전을 항상 알아야 하고, 취약점을 정기적으로 스캔하여 문제를 방지해야 한다.
필요한 기능과 사용하지 않는 종속성, 구성 요소, 파일 및 문서를 정리하여 보호할 수 있다.

## 📌 7. <span style='background-color: #dcffe4'>Identification and Authentication Failures</span>
2단계 인증 도입으로 실패 횟수는 줄어들었지만 여전히 빈번하게 일어난다.
로그인 실패 시도를 제한하고 로그인할 때마다 새로운 임의 세션 ID를 생성하면 이전보다 문제를 방지할 수 있다.

## 📌 8. <span style='background-color: #dcffe4'>Software and Data Integrity Failures</span>
시스템에 설치된 패키지를 확인하고 데이터가 신뢰할 수 있는 소스에서 제공되었는지, 어떤 단계에서도 변경되지 않았는지 확인하는 것이 중요하다.
방지하기 위해서는 라이브러리 및 종속성을 구현하고 소프트웨어 공급망 보안 도구 사용, 코드 및 구성 변경에 대한 검토 프로세스를 도입해야 한다.

## 📌 9. <span style='background-color: #dcffe4'>Security Logging and Monitoring</span>
보안 로깅과 모니터링은 보안 인프라 유지/관리에 필수적이다. 정기적인 로그 확인은 빠른 조치에 도움이 될 수 있다.

## 📌 10. <span style='background-color: #dcffe4'>SSRF(Server-Side Request Forgery)</span>
SSRF는 웹 애플리케이션이 사용자가 제공한 URL의 유효성을 검사하지 않고 먼 리소스를 조달할 때 발생한다.
공격자는 방화벽이나 VPN으로 보호되는 경우에도 예기치 않은 대상으로 SSRF를 보낼 수 있다.
이를 방지하기 위해서는 입력한 모든 데이터를 주의 깊게 확인하고 각 URL 체계를 허용 목록과 비교하여 확인해야 한다.
