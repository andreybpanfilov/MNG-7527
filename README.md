Demonstrates [MNG-7527](https://issues.apache.org/jira/browse/MNG-7527)

### preparation steps

```shell
MNG-7527 % ~/app/maven/4.0/bin/mvn -version
Apache Maven 4.0.0-alpha-1-SNAPSHOT (a53a1aa232bc383baf055d884a7c66319d10d404)

MNG-7527 % mkdir .mvn
```

### normal build

```shell
MNG-7527 % ~/app/maven/4.0/bin/mvn clean package
[INFO] Scanning for projects...
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] mng7527                                                                                                              [pom]
[INFO] mng7527-war                                                                                                          [war]
[INFO] mng7527-dep1                                                                                                         [jar]
[INFO] mng7527-dep2                                                                                                         [jar]
[INFO] 
[INFO] ----------------------------------------------< tel.panfilov.maven:mng7527 >----------------------------------------------
[INFO] Building mng7527 0.0.1-SNAPSHOT                                                                                      [1/4]
[INFO]   from pom.xml
[INFO] ---------------------------------------------------------[ pom ]----------------------------------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:3.1.0:clean (default-clean) @ mng7527 ---
[INFO] 
[INFO] --------------------------------------------< tel.panfilov.maven:mng7527-war >--------------------------------------------
[INFO] Building mng7527-war 0.0.1-SNAPSHOT                                                                                  [2/4]
[INFO]   from mng7527-war/pom.xml
[INFO] ---------------------------------------------------------[ war ]----------------------------------------------------------
...
[INFO] 
[INFO] --- maven-war-plugin:3.3.2:war (default-war) @ mng7527-war ---
[INFO] Packaging webapp
[INFO] Assembling webapp [mng7527-war] in [MNG-7527/mng7527-war/target/mng7527-war-0.0.1-SNAPSHOT]
[INFO] Processing war project
[INFO] Building war: MNG-7527/mng7527-war/target/mng7527-war-0.0.1-SNAPSHOT.war
[INFO] Packaging classes
[INFO] Building jar: MNG-7527/mng7527-war/target/mng7527-war-0.0.1-SNAPSHOT-classes.jar
[INFO] 
[INFO] -------------------------------------------< tel.panfilov.maven:mng7527-dep1 >--------------------------------------------
[INFO] Building mng7527-dep1 0.0.1-SNAPSHOT                                                                                 [3/4]
[INFO]   from mng7527-dep1/pom.xml
[INFO] ---------------------------------------------------------[ jar ]----------------------------------------------------------
...
[INFO] --- maven-jar-plugin:3.2.0:jar (default-jar) @ mng7527-dep1 ---
[WARNING] JAR will be empty - no content was marked for inclusion!
[INFO] Building jar: MNG-7527/mng7527-dep1/target/mng7527-dep1-0.0.1-SNAPSHOT.jar
[INFO] 
[INFO] -------------------------------------------< tel.panfilov.maven:mng7527-dep2 >--------------------------------------------
[INFO] Building mng7527-dep2 0.0.1-SNAPSHOT                                                                                 [4/4]
[INFO]   from mng7527-dep2/pom.xml
[INFO] ---------------------------------------------------------[ jar ]----------------------------------------------------------
....
[INFO] --- maven-jar-plugin:3.2.0:jar (default-jar) @ mng7527-dep2 ---
[WARNING] JAR will be empty - no content was marked for inclusion!
[INFO] Building jar: MNG-7527/mng7527-dep2/target/mng7527-dep2-0.0.1-SNAPSHOT.jar
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] Reactor Summary for mng7527 0.0.1-SNAPSHOT:
[INFO] 
[INFO] mng7527 .............................................................................................. SUCCESS [  0.150 s]
[INFO] mng7527-war .......................................................................................... SUCCESS [  0.818 s]
[INFO] mng7527-dep1 ......................................................................................... SUCCESS [  0.200 s]
[INFO] mng7527-dep2 ......................................................................................... SUCCESS [  0.026 s]
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] Total time:  1.306 s
[INFO] Finished at: 2022-08-08T19:36:28+10:00
[INFO] --------------------------------------------------------------------------------------------------------------------------
```

### partial build - mng7527-dep2 project only

succeeds because mng7527-dep2 depends on main artifact

```shell
MNG-7527 % ~/app/maven/4.0/bin/mvn package -f mng7527-dep2
[INFO] Scanning for projects...
[INFO] 
[INFO] -------------------------------------------< tel.panfilov.maven:mng7527-dep2 >--------------------------------------------
[INFO] Building mng7527-dep2 0.0.1-SNAPSHOT
[INFO]   from pom.xml
[INFO] ---------------------------------------------------------[ jar ]----------------------------------------------------------
...
[INFO] --- maven-jar-plugin:3.2.0:jar (default-jar) @ mng7527-dep2 ---
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] Total time:  0.768 s
[INFO] Finished at: 2022-08-08T19:41:04+10:00
[INFO] --------------------------------------------------------------------------------------------------------------------------
```

### partial build - mng7527-dep1 project only

fails because mng7527-dep2 depends on artifact with classifier

```shell
MNG-7527 % ~/app/maven/4.0/bin/mvn package -f mng7527-dep1
[INFO] Scanning for projects...
[INFO] 
[INFO] -------------------------------------------< tel.panfilov.maven:mng7527-dep1 >--------------------------------------------
[INFO] Building mng7527-dep1 0.0.1-SNAPSHOT
[INFO]   from pom.xml
[INFO] ---------------------------------------------------------[ jar ]----------------------------------------------------------
...
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] Total time:  0.201 s
[INFO] Finished at: 2022-08-08T19:42:04+10:00
[INFO] --------------------------------------------------------------------------------------------------------------------------
[ERROR] Failed to execute goal on project mng7527-dep1: Could not resolve dependencies for project tel.panfilov.maven:mng7527-dep1:jar:0.0.1-SNAPSHOT: Could not find artifact tel.panfilov.maven:mng7527-war:jar:classes:0.0.1-SNAPSHOT -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the '-e' switch
[ERROR] Re-run Maven using the '-X' switch to enable verbose output
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/DependencyResolutionException
```