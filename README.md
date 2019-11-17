# JavaByteCode
인프런 - 더 자바, 코드를 조작하는 다양한 방법

#### 1. 코드 커버리지는 어떻게 측정할까 ? 

- 코드 커버리지 
  - 테스트 코드가 확인한 소스 코드를 %로 측정
  - 테스트 코드가 특정 소스 코드를 얼만큼 커버(테스트)하는지 측정

- JaCoCo 
  - Maven : plugin 설정  
  - mvn clean verify 실행 시 report 파일(index.html) 제공 
  - if문(분기문)의 일부만 테스트 됐을 경우, 노란색 다이아몬드 라인이 생김
  - 노란색 : 일부만 테스트된 라인(분기문)
  - 빨간색 : 테스트 되지 않은 라인
  - 초록색 : 테스트 된 라인
  - 특정 커버리지 비율을 넘기지 못하면 빌드를 실패시킬 수 있음
  ```` maven
  // JaCoCo - 특정 커버리지 비율을 넘기지 못하면 빌드에 실패하도록 설정하는 방법
  // 콘솔에 ERROR 로그 찍힘 - Failed to execute goal org.jacoco:jacoco-maven-plugin:0.8.4:check(jacoco-check)
  // on project classloader-sample: Coverage checks have not benn met. See log for details -> [Help 1]
  <execution>
    <id>jacoco-check</id>
    <goals>
      <goal>check</goal>
    </goals>
    <configuration>
      <rules>
        <rule>
          <element>PACKAGE</element>
          <limits>
            <limit>
              <counter>LINE</counter>
              <value>COVEREDRATIO</value>
              // 최소 50% 이상의 커버리지를 통과해야 빌드가 성공하도록 설정
              <minimum>0.50</minimum>
            </limit>
          </limits>
        </rule> 
      </rules>
    </configuration>
  </execution>
  ````

- 그렇다면 코드 커버리지를 어떻게 측정할 수 있었을까 ?
  1. 바이트 코드를 읽어서 코드 커버리지를 측정해두어야 할 라인의 갯수를 세어둠
  2. 코드가 실행이 될 때 그중에 몇개를 실행했는지 카운팅 함
  3. 실행한 라인과 실행되지 않은 라인을 비교 

```` java
// Moin 클래스 소스 코드
public class Moin { 
   int maxNmberOfAttendess; // 최대 참가자수 
   int numberOfEnrollment;    // 현재 참가자수

   public boolean isEnrollmentFull() { 
      // 최대 참가자수가 0인 경우
      if (maxNmberOfAttendess == 0) {
          return false; 
      } 
      // 현재 참가자수가 최대 참가자수 보다 작은 경우 (아직 꽉 차지 않은 경우)
      if (numberOfEnrollment < maxNmberOfAttendess) {
          return false;
      }
      return true;
   } 
}
```` 

```` java
// Moin 클래스 테스트 코드
public class MoinTest {
  @Test
  public void isFull() {
    Moin moim = new Moim();
    moim.maxNmberOfAttendess = 100;
    moim.numberOfEnrollment = 10;
    // 결과가 False이면 성공
    Assert.assertFalse(moim.isEnrollmentFull());
  }
}
````
