
Right click on pom.xml, `Run As -> Maven Install`. You are expected to see a failure.

```
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< com.rm2pt:com.rm2pt.c >------------------------
[INFO] Building com.rm2pt.c 1.0.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] Downloading from : https://sandec.bintray.com/repo/org/apache/maven/plugins/maven-surefire-plugin/3.0.0-M1/maven-surefire-plugin-3.0.0-M1.pom
...
[INFO] Downloading from : https://repo.maven.apache.org/maven2/org/apache/maven/plugins/maven-install-plugin/2.4/maven-install-plugin-2.4.jar
[INFO] Downloaded from : https://repo.maven.apache.org/maven2/org/apache/maven/plugins/maven-install-plugin/2.4/maven-install-plugin-2.4.jar (27 kB at 88 kB/s)
[WARNING] The POM for com.sandec.jpro:jpro-webapi:jar:2019.2.7 is missing, no dependency information available
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  8.063 s
[INFO] Finished at: 2021-05-18T11:28:10+08:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal on project com.rm2pt.c: Could not resolve dependencies for project com.rm2pt:com.rm2pt.c:jar:1.0.0-SNAPSHOT: Failure to find com.sandec.jpro:jpro-webapi:jar:2019.2.7 in https://sandec.bintray.com/repo was cached in the local repository, resolution will not be reattempted until the update interval of jpro - sandec repository has elapsed or updates are forced -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/DependencyResolutionException
```

Replace https://bintray.com/sandec/repo to https://sandec.jfrog.io/artifactory/repo/ [1] in the whole POM because the former has been sunset [2].

Then run `Maven Install` again, you should see a different error: "Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:3.0.0-M1:test (default-test) on project com.rm2pt.c: No tests were executed!" This indicates a correct setup.

[1] https://www.jpro.one/?page=docs/current/1.1/Let's%20get%20started <br>
[2] https://jfrog.com/blog/into-the-sunset-bintray-jcenter-gocenter-and-chartcenter/
