# Build and Run Log

Generated on 2026-06-18 20:43:25 +06:00.

## Java Version

Command:

```powershell
java -version
```

Output:

```text
java version "25.0.2" 2026-01-20 LTS
Java(TM) SE Runtime Environment (build 25.0.2+10-LTS-69)
Java HotSpot(TM) 64-Bit Server VM (build 25.0.2+10-LTS-69, mixed mode, sharing)

```

## Maven Version

Command:

```powershell
mvn -version
```

Output:

```text
Apache Maven 3.9.16 (2bdd9fddda4b155ebf8000e807eb73fd829a51d5)
Maven home: C:\apache-maven-3.9.16
Java version: 25.0.2, vendor: Oracle Corporation, runtime: C:\Program Files\Java\jdk-25.0.2
Default locale: en_US, platform encoding: UTF-8
OS name: "windows 11", version: "10.0", arch: "amd64", family: "windows"

```

## Targeted Demo Build

Command:

```powershell
mvn -pl jgrapht-demo -am -DskipTests package
```

Output:

```text
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] JGraphT - Parent                                                   [pom]
[INFO] JGraphT - Core                                                     [jar]
[INFO] JGraphT - I/O                                                      [jar]
[INFO] JGraphT - Ext                                                      [jar]
[INFO] JGraphT - Demo                                                     [jar]
[INFO] 
[INFO] ------------------------< org.jgrapht:jgrapht >-------------------------
[INFO] Building JGraphT - Parent 1.6.0-SNAPSHOT                           [1/5]
[INFO]   from pom.xml
[INFO] --------------------------------[ pom ]---------------------------------
[INFO] 
[INFO] --- javadoc:3.12.0:jar (attach-javadocs) @ jgrapht ---
[INFO] Not executing Javadoc as the project is not a Java classpath-capable package
[INFO] 
[INFO] --- source:3.4.0:jar-no-fork (attach-sources) @ jgrapht ---
[INFO] 
[INFO] ----------------------< org.jgrapht:jgrapht-core >----------------------
[INFO] Building JGraphT - Core 1.6.0-SNAPSHOT                             [2/5]
[INFO]   from jgrapht-core\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- resources:3.5.0:resources (default-resources) @ jgrapht-core ---
[INFO] skip non existing resourceDirectory E:\avra\2-2\dp\opensource\jgrapht\jgrapht-core\src\main\resources
[INFO] 
[INFO] --- compiler:3.15.0:compile (default-compile) @ jgrapht-core ---
[INFO] Nothing to compile - all classes are up to date.
[INFO] 
[INFO] --- bundle:6.0.2:manifest (default) @ jgrapht-core ---
[INFO] Writing manifest: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-core\target\classes\META-INF\MANIFEST.MF
[INFO] 
[INFO] --- resources:3.5.0:testResources (default-testResources) @ jgrapht-core ---
[INFO] Copying 2 resources from src\test\resources to target\test-classes
[INFO] 
[INFO] --- compiler:3.15.0:testCompile (default-testCompile) @ jgrapht-core ---
[INFO] Nothing to compile - all classes are up to date.
[INFO] 
[INFO] --- surefire:3.5.6:test (default-test) @ jgrapht-core ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- jar:3.5.0:jar (default-jar) @ jgrapht-core ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-core\target\jgrapht-core-1.6.0-SNAPSHOT.jar
[INFO] 
[INFO] --- javadoc:3.12.0:jar (attach-javadocs) @ jgrapht-core ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-core\target\jgrapht-core-1.6.0-SNAPSHOT-javadoc.jar
[INFO] 
[INFO] --- source:3.4.0:jar-no-fork (attach-sources) @ jgrapht-core ---
[INFO] 
[INFO] -----------------------< org.jgrapht:jgrapht-io >-----------------------
[INFO] Building JGraphT - I/O 1.6.0-SNAPSHOT                              [3/5]
[INFO]   from jgrapht-io\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- antlr4:4.13.2:antlr4 (antlr) @ jgrapht-io ---
[INFO] No grammars to process
[INFO] ANTLR 4: Processing source directory E:\avra\2-2\dp\opensource\jgrapht\jgrapht-io\src\main\antlr4
[INFO] 
[INFO] --- replacer:1.5.3:replace (default) @ jgrapht-io ---
[INFO] Replacement run on 32 files.
[INFO] 
[INFO] --- resources:3.5.0:resources (default-resources) @ jgrapht-io ---
[INFO] Copying 4 resources from src\main\resources to target\classes
[INFO] 
[INFO] --- compiler:3.15.0:compile (default-compile) @ jgrapht-io ---
[INFO] Recompiling the module because of changed dependency.
[INFO] Compiling 94 source files with javac [debug release 21 module-path] to target\classes
[WARNING] /E:/avra/2-2/dp/opensource/jgrapht/jgrapht-io/src/main/java/module-info.java:[42,30] requires directive for an automatic module
[WARNING] /E:/avra/2-2/dp/opensource/jgrapht/jgrapht-io/src/main/java/org/jgrapht/nio/DefaultAttribute.java:[38,15] non-transient instance field of a serializable class declared with a non-serializable type
[INFO] /E:/avra/2-2/dp/opensource/jgrapht/jgrapht-io/src/main/java/org/jgrapht/nio/tsplib/TSPLIBImporter.java: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-io\src\main\java\org\jgrapht\nio\tsplib\TSPLIBImporter.java uses or overrides a deprecated API.
[INFO] /E:/avra/2-2/dp/opensource/jgrapht/jgrapht-io/src/main/java/org/jgrapht/nio/tsplib/TSPLIBImporter.java: Recompile with -Xlint:deprecation for details.
[INFO] 
[INFO] --- bundle:6.0.2:manifest (default) @ jgrapht-io ---
[INFO] Writing manifest: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-io\target\classes\META-INF\MANIFEST.MF
[INFO] 
[INFO] --- resources:3.5.0:testResources (default-testResources) @ jgrapht-io ---
[INFO] Copying 3 resources from src\test\resources to target\test-classes
[INFO] 
[INFO] --- compiler:3.15.0:testCompile (default-testCompile) @ jgrapht-io ---
[INFO] Recompiling the module because of changed dependency.
[INFO] Compiling 28 source files with javac [debug release 21 module-path] to target\test-classes
[INFO] 
[INFO] --- surefire:3.5.6:test (default-test) @ jgrapht-io ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- jar:3.5.0:jar (default-jar) @ jgrapht-io ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-io\target\jgrapht-io-1.6.0-SNAPSHOT.jar
[INFO] 
[INFO] --- javadoc:3.12.0:jar (attach-javadocs) @ jgrapht-io ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-io\target\jgrapht-io-1.6.0-SNAPSHOT-javadoc.jar
[INFO] 
[INFO] --- source:3.4.0:jar-no-fork (attach-sources) @ jgrapht-io ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-io\target\jgrapht-io-1.6.0-SNAPSHOT-sources.jar
[INFO] 
[INFO] ----------------------< org.jgrapht:jgrapht-ext >-----------------------
[INFO] Building JGraphT - Ext 1.6.0-SNAPSHOT                              [4/5]
[INFO]   from jgrapht-ext\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- resources:3.5.0:resources (default-resources) @ jgrapht-ext ---
[INFO] skip non existing resourceDirectory E:\avra\2-2\dp\opensource\jgrapht\jgrapht-ext\src\main\resources
[INFO] 
[INFO] --- compiler:3.15.0:compile (default-compile) @ jgrapht-ext ---
[INFO] Recompiling the module because of changed dependency.
[INFO] Compiling 3 source files with javac [debug release 21 module-path] to target\classes
[WARNING] /E:/avra/2-2/dp/opensource/jgrapht/jgrapht-ext/src/main/java/module-info.java:[30,48] requires transitive directive for an automatic module
[INFO] 
[INFO] --- bundle:6.0.2:manifest (default) @ jgrapht-ext ---
[INFO] Writing manifest: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-ext\target\classes\META-INF\MANIFEST.MF
[INFO] 
[INFO] --- resources:3.5.0:testResources (default-testResources) @ jgrapht-ext ---
[INFO] skip non existing resourceDirectory E:\avra\2-2\dp\opensource\jgrapht\jgrapht-ext\src\test\resources
[INFO] 
[INFO] --- compiler:3.15.0:testCompile (default-testCompile) @ jgrapht-ext ---
[INFO] Recompiling the module because of changed dependency.
[INFO] Compiling 1 source file with javac [debug release 21 module-path] to target\test-classes
[INFO] 
[INFO] --- surefire:3.5.6:test (default-test) @ jgrapht-ext ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- jar:3.5.0:jar (default-jar) @ jgrapht-ext ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-ext\target\jgrapht-ext-1.6.0-SNAPSHOT.jar
[INFO] 
[INFO] --- javadoc:3.12.0:jar (attach-javadocs) @ jgrapht-ext ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-ext\target\jgrapht-ext-1.6.0-SNAPSHOT-javadoc.jar
[INFO] 
[INFO] --- source:3.4.0:jar-no-fork (attach-sources) @ jgrapht-ext ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-ext\target\jgrapht-ext-1.6.0-SNAPSHOT-sources.jar
[INFO] 
[INFO] ----------------------< org.jgrapht:jgrapht-demo >----------------------
[INFO] Building JGraphT - Demo 1.6.0-SNAPSHOT                             [5/5]
[INFO]   from jgrapht-demo\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- resources:3.5.0:resources (default-resources) @ jgrapht-demo ---
[INFO] skip non existing resourceDirectory E:\avra\2-2\dp\opensource\jgrapht\jgrapht-demo\src\main\resources
[INFO] 
[INFO] --- compiler:3.15.0:compile (default-compile) @ jgrapht-demo ---
[INFO] Recompiling the module because of changed dependency.
[INFO] Compiling 15 source files with javac [debug release 21 module-path] to target\classes
[INFO] 
[INFO] --- resources:3.5.0:testResources (default-testResources) @ jgrapht-demo ---
[INFO] skip non existing resourceDirectory E:\avra\2-2\dp\opensource\jgrapht\jgrapht-demo\src\test\resources
[INFO] 
[INFO] --- compiler:3.15.0:testCompile (default-testCompile) @ jgrapht-demo ---
[INFO] Recompiling the module because of changed dependency.
[INFO] Compiling 2 source files with javac [debug release 21 module-path] to target\test-classes
[INFO] 
[INFO] --- surefire:3.5.6:test (default-test) @ jgrapht-demo ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- jar:3.5.0:jar (default-jar) @ jgrapht-demo ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-demo\target\jgrapht-demo-1.6.0-SNAPSHOT.jar
[INFO] 
[INFO] --- javadoc:3.12.0:jar (attach-javadocs) @ jgrapht-demo ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-demo\target\jgrapht-demo-1.6.0-SNAPSHOT-javadoc.jar
[INFO] 
[INFO] --- source:3.4.0:jar-no-fork (attach-sources) @ jgrapht-demo ---
[INFO] Building jar: E:\avra\2-2\dp\opensource\jgrapht\jgrapht-demo\target\jgrapht-demo-1.6.0-SNAPSHOT-sources.jar
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for JGraphT - Parent 1.6.0-SNAPSHOT:
[INFO] 
[INFO] JGraphT - Parent ................................... SUCCESS [ 10.181 s]
[INFO] JGraphT - Core ..................................... SUCCESS [ 23.828 s]
[INFO] JGraphT - I/O ...................................... SUCCESS [ 31.242 s]
[INFO] JGraphT - Ext ...................................... SUCCESS [  2.504 s]
[INFO] JGraphT - Demo ..................................... SUCCESS [  3.903 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:12 min
[INFO] Finished at: 2026-06-18T20:43:11+06:00
[INFO] ------------------------------------------------------------------------

```

## HelloJGraphT Demo Run

Command:

```powershell
mvn -pl jgrapht-demo "-Dexec.mainClass=org.jgrapht.demo.HelloJGraphT" exec:java
```

Output:

```text
[INFO] Scanning for projects...
[INFO] 
[INFO] ----------------------< org.jgrapht:jgrapht-demo >----------------------
[INFO] Building JGraphT - Demo 1.6.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- exec:3.6.3:java (default-cli) @ jgrapht-demo ---
-- toString output
([v1, v2, v3, v4], [{v1,v2}, {v2,v3}, {v3,v4}, {v4,v1}])

-- traverseHrefGraph output
http://www.jgrapht.org
http://www.wikipedia.org
http://www.google.com

-- renderHrefGraph output
strict digraph G {
  www_google_com [ label="http://www.google.com" ];
  www_wikipedia_org [ label="http://www.wikipedia.org" ];
  www_jgrapht_org [ label="http://www.jgrapht.org" ];
  www_jgrapht_org -> www_wikipedia_org;
  www_google_com -> www_jgrapht_org;
  www_google_com -> www_wikipedia_org;
  www_wikipedia_org -> www_google_com;
}


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  5.302 s
[INFO] Finished at: 2026-06-18T20:43:25+06:00
[INFO] ------------------------------------------------------------------------

```

