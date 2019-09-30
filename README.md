# Automating your API tests Second Part

On this workshop we are going to test the integration of two popular API's, Trello and PokeAPI, we can see how important are the information generated of the automated tests.

For the workshop we need to have installed those tools and have access to this API's:

* [Java]
* [Maven]
* [Trello]
* [Trello Key]
* [Trello API]
* [Pokemon API]
* [IntelliJ IDEA]



## Installation

For [Java] and [Maven], the installation information is available on the hyperlink.
If you want to check if you currently have them installed

```sh
$ java -version
```
The [Java] response will be

```sh
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
```
 And for [Maven]

```sh
$ mvn -v
```

The response is:

```sh
Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-24T13:41:47-05:00)
```


## Implementation

- First we have to open Intellij IDEA

- Create **new project** > **maven** > **Set project SDK to 1.8** > **Next**

- Fill the **GroupId** and **ArtifactId**  with our own information

- Set the **Project name** and click on **Finish**

- We need to install the [Cucumber Plugin] on IntelliJ IDEA

- Create new Files and Packages to have this structure
	- **root**
		 - **src**
		    - **main**
			- **test**
				- **java**
					- Controllers
					- Model
					- steps
					- Utils
				- **resources**
					- features


## Configuration of the POM
This properties setting are for the use of AspectJ:

```sh
    <properties>
        <aspectj.version>1.9.4</aspectj.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
```

This dependency is used for the Soft Assertions of the tests
```sh
		<dependency>
            <groupId>org.assertj</groupId>
            <artifactId>assertj-core</artifactId>
            <version>3.11.1</version>
        </dependency>
```

This dependency is used for the REST requests maded using Rest Assured
```sh
		<dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>3.3.0</version>
            <scope>compile</scope>
        </dependency>
```

This dependency is the base of the Logs of the solution
```sh
		<dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>

```
This dependency is for the integration of Allure with Rest Assured
```sh
	<dependency>
	        <groupId>io.qameta.allure</groupId>
            <artifactId>allure-rest-assured</artifactId>
            <version>2.10.0</version>
    </dependency>
```
This dependency is for the integration of Allure and Cucumber        
```sh
    <dependency>
            <groupId>io.qameta.allure</groupId>
            <artifactId>allure-cucumber4-jvm</artifactId>
            <version>2.13.0</version>
    </dependency>
```
This dependency is for the integration of Java and Allure    
```sh
     <dependency>
            <groupId>io.cucumber</groupId>
            <artifactId>cucumber-java</artifactId>
            <version>4.3.1</version>
            <scope>test</scope>
     </dependency>
```

This dependency is for the integration of Cucumber and TestNG
```sh
     <dependency>
            <groupId>io.cucumber</groupId>
            <artifactId>cucumber-testng</artifactId>
            <version>4.3.1</version>
        </dependency>
```

This dependency is for the integration of Allure and TestNG
```sh
       <dependency>
            <groupId>io.qameta.allure</groupId>
            <artifactId>allure-testng</artifactId>
            <version>2.13.0</version>
       </dependency>
```
This dependency is for the  TestNG utils
```sh
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>7.0.0</version>
            <scope>test</scope>
        </dependency>
```


## Set the plugins of the POM

This plugin is for set the action of set the report version and generate the report link the action report with Allure:
```sh
            <plugin>
                <groupId>io.qameta.allure</groupId>
                <artifactId>allure-maven</artifactId>
                <version>2.9</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>report</goal>
                        </goals>
                        <phase>post-integration-test</phase>
                    </execution>
                </executions>
                <configuration>
                    <reportVersion>2.4.1</reportVersion>
                </configuration>
            </plugin>
```

AspectJ is a dependency based on tags for the pre and post actions of each test or test suite.

The main plugin is for the actions of execute all tests defined on the **testng.xml** , generate the connection with Cucumber and set the destination folder of all tests, with this entire plugin we choose the **verify** for all the scope of tests:

```sh
<plugin>
     <artifactId>maven-failsafe-plugin</artifactId>
     <version>3.0.0-M3</version>
     <configuration>
         <testFailureIgnore>true</testFailureIgnore>
         <suiteXmlFiles>
            <suiteXmlFile>testng.xml</suiteXmlFile>
         </suiteXmlFiles>
         <argLine>
              -javaagent:"${settings.localRepository}/org/aspectj/aspectjweaver/${aspectj.version}/aspectjweaver-${aspectj.version}.jar"
              -Dcucumber.options="--plugin io.qameta.allure.cucumber4jvm.AllureCucumber4Jvm"
         </argLine>
         <systemProperties>
              <property>
                 <name>allure.results.directory</name>
                 <value>${project.build.directory}/allure-results</value>
              </property>
         </systemProperties>
             </configuration>
             <dependencies>
                <dependency>
                      <groupId>org.aspectj</groupId>
                       <artifactId>aspectjweaver</artifactId>
                       <version>${aspectj.version}</version>
                </dependency>
             </dependencies>
             <executions>
                <execution>
                   <goals>
                     <goal>integration-test</goal>
                     <goal>verify</goal>
                   </goals>
                </execution>
            </executions>
</plugin>

```
We define the version of Java that will work with **Maven** in this plugin:

```sh
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
```
This plugin skip the tests of one part of the main lifecycle of  **Maven**:
```sh
     <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
          <configuration>
               <skipTests>true</skipTests>
          </configuration>
     </plugin>
```

***

## Creating the TestNG Configuration

 1. Create a new **File** on the **POM Folder**
 2. The new **File** is called "**testng.xml**"
 3. Inside **testng.xml** you need to put this information:

```sh
<?xml version="1.0" encoding="UTF-8"?>  
	<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">  
	<suite name={Name of our tests suite} verbose="1" parallel="tests" thread-count="1" configfailurepolicy="continue">  
	<test name={Name of the test} >  
		<classes>  
			<class name={Name of the Main class of tests}/>  
		</classes>  
	</test>  
</suite>
```

## Creating the configuration file of the application

On the route **src/test/resources** we create a new file named **config.properties**, this file will contain important information for out application and it's operation, we will include some endpoints of the API of Pokemon and [Trello API] to use it after.

The **config.properties** file must contain the next information:

```sh
key = {{ourPersonalKey}}
token = {{ourPersonalToken}}
GET_POKEMON_URL = http://pokeapi.co/api/v2/pokemon/
GET_BOARDS_LIST = https://api.trello.com/1/members/me/boards
GET_CARDS_LIST = https://api.trello.com/1/boards/
CREATE_BOARD = https://api.trello.com/1/boards
CREATE_LIST = https://api.trello.com/1/lists
CREATE_CARD = https://api.trello.com/1/cards
```
## Creating our user stories
For the creation of user stories we will use [Cucumber] BDD and to make it undestandable we use [Gherkin], to do this we will create a new file called **trello.feature** on the route **src/test/resources/features**.



## Execution
To execute the API tests you have to run on your console

```sh
$ mvn clean verify
```

To generate your report and deploy it

```sh
$ mvn allure:serve
```

Your Web Browser will deploy a new window with the Allure report





   [Cucumber Plugin]: <http://letzdotesting.com/install-cucumber-plugin-for-intellij/>
   [Gherkin]: <https://cucumber.io/docs/gherkin/reference/>
   [Cucumber]: <https://cucumber.io/docs/installation/>
   [IntelliJ IDEA]: <https://www.jetbrains.com/idea/download/>
   [Maven]: <https://maven.apache.org/install.html>
   [Java]: <https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html>
   [Trello API]: <https://developers.trello.com/reference#introduction>
   [Trello Key]: <https://trello.com/app-key>
   [Trello]: <https://trello.com/>
   [Pokemon API]: <http://pokeapi.salestock.net/docsv2/#info>
