## Create RM2PT Project

You will choose `RM2PT Project with Prototype`.

Project name must start with an upper case letter!

## Fix POM Error

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

In case your RM2PT chokes, delete `C:\Users\%UserName%\.m2` and restart RM2PT.

## Fix remodel file
Open a.remodel, its content is 

```
UseCaseModel A {Service DSystem {} Service ThirdPartyServices {}}
DomainModel A {}
```

If you right-click on the file, choose `RM2PT -> OO Prototype -> Generate Desktop Prototype (Internel)`, RM2PT will generate files in `src-gen` folder, with errors. For instance, `src-gen\entities\EntityManager.java` has error "Unreachable catch block for ClassNotFoundException". You may manually fix the auto-generated file and  right-click on `src-gen\gui\Main.java`, choose `Debug As -> Java Application`. RM2PT console will say "JavaFX runtime components are missing, and are required to run this application."

You must add `--module-path "C:\Program Files\javafx-sdk-14.0.2\lib" --add-modules javafx.controls,javafx.fxml`, assuming your JavaFX is installed in the mentioned path, to VM arguments to the run/debug configuration.

Debug again, you are expected to see a different error: "java.lang.NullPointerException: Location is required." If you add breakpoints, you will realize the program does run, but fails to find `Prototype.fxml`.

Confirmed with RM2PT developers, the initial project will not run, manual revision is required. Replace `a.remodel` with the following content. Note, if your project is not named `A`, you must replace it with your project name.

```
UseCaseModel A {

	UC::userProcess definedBySSD(UserProcessSSD) relatedService(UserProcessService)

	Actor User {
		userProcess
	}

	Interaction UserProcessSSD {

		[Participants: User UserProcessService]

	}

	Service TestSystem {

	}

	Service ThirdPartyServices {

	}

	Service UsecaseService {

		[Operation]
		login(name, password)
		register(id, name, password)

	}

	Service UserProcessService {

	}

	Service ASystem {

	}

	Contract UsecaseService::login(name:String, password:String): Boolean {

		definition:
			user:User = User.allInstance()->any(u:User | u.Name = name and u.Password = password)

		precondition:
			user.oclIsUndefined() = false

		postcondition:
			result = true
	}

	Contract UsecaseService::register(id:Integer, name:String, password:String): Boolean {

		definition:
			user:User = User.allInstance()->any(u:User | u.Id = id)

		precondition:
			user.oclIsUndefined() = true

		postcondition:
			let u:User in
			u.oclIsNew() and
			u.Name = name and
			u.Password = password and
			User.allInstance()->includes(u) and
			result = true
	}

}

DomainModel A {

	Entity User {
		Id : Integer
		Name : String
		Password : String

		[Refer]
		BelongedRole : Role Association
	}

	Entity Role {
		Id : Integer
		Name : String
		Password : String

		[Refer]
		ContainedUser : User* ! Association
	}

}
```

RM2PT may highlight a quick fix on `UseCaseModel A`, "Create system service", you will click it, and you will restart RM2PT.

With the magic content,  choose `RM2PT -> OO Prototype -> Generate Desktop Prototype (Internel)` again on `a.remodel`. This time you will see `Prototype.fxml` and `Prototype.css` generated in the `gui` package. Run/Debug `Main.java` again, a wonderful GUI window shall pop up.


[1] https://www.jpro.one/?page=docs/current/1.1/Let's%20get%20started <br>
[2] https://jfrog.com/blog/into-the-sunset-bintray-jcenter-gocenter-and-chartcenter/
