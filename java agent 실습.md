[ Example ] 
````java
// 클래스를 로딩하며 적용되기 때문에 클래스 파일(바이트 코드)가 변경되지는 않음
// 클래스 로더가 클래스를 로드하면서 변경된 javaagent 자체를 읽어들여 사용
public class MasulsaAgent {
	public static void premain(String agentArgs, Instrumentation inst) {
		new AgentBuilder.Default()
				.type(ElementMatchers.any())
				.transform((builder, typeDescription, classLoader, javaModule) -> 			   		
				builder.method(named("pullOut")).intercept(FixedValue.value("Rabbit!"))).installOn(inst);
	}
}
````

[ Maven ]
````XML
<dependency>
	<groupId>net.bytebuddy</groupId>
	<artifactId>byte-buddy</artifactId>
	<version>1.10.1</version>
</dependency>

<build>
	<plugins>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-jar-plugin</artifactId>	
			<version>3.1.2</version>
			<configuration>
				<archive>
					<index>true</index>
					<manifest>
						<addClasspath>true</addClasspath>
					</manifest>
					<manifestEntries>
						<mode>development</mode>
						<url>${project.url}</url>
						<key>value</key>
						<Premain-Class>me.whiteship.MasulsaAgent</Premain-Class>
						<Can-Redefine-Classes>true</Can-Redefine-Classes>
						<Can-Retransform-Classes>true</Can-Retransform-Classes>
					</manifestEntries>
				</archive>	
			</configuration>			
		</plugin>
	</plugins>
</build>
````

[ 프로젝트 패키징 ]
mvn clean package

[ java agent 설정 ]
VM options : -javaagent: /Users/keesun/workspace/MasulsaJavaAgent/target/MasulsaAgent-1.0-SNAPSHOT.jar

[ Moja.java 클래스 실행 ]
````java
// Moja.class 파일을 확인해보면, 바이트코드가 "Rabbit!"으로 변경되어 있지 않지만 Rabbit! 으로 출력되는 것을 알 수 있음 
public class Moja {
	public String pullOut() {
		return "";
	}
}
````
