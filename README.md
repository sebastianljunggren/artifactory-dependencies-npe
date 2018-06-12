# Artifactory dependencies null pointer exception

Demonstration of how `artifactory-maven-plugin` in combination with the `copy-dependencies` goal from `maven-dependency-plugin` can be configured to produce a NPE.

**Steps to reproduce**:

* Start a local Artifactory instance (default settings are fine): `docker run -p 8081:8081 docker.bintray.io/jfrog/artifactory-oss:latest`
* Try to deploy this project to the local instance: `mvn clean deploy -e -Dusername=admin -Dpassword=password `

This results in the following NPE:

```
[ERROR] NullPointerException
java.lang.NullPointerException
    at org.jfrog.build.api.builder.BuildInfoMavenBuilder.mergeModuleDependencies (BuildInfoMavenBuilder.java:389)
    at org.jfrog.build.api.builder.BuildInfoMavenBuilder.mergeModule (BuildInfoMavenBuilder.java:333)
    at org.jfrog.build.api.builder.BuildInfoMavenBuilder.addModule (BuildInfoMavenBuilder.java:290)
    at org.jfrog.build.extractor.maven.BuildInfoRecorder.addFilesToCurrentModule (BuildInfoRecorder.java:480)
    at org.jfrog.build.extractor.maven.BuildInfoRecorder.finalizeAndAddModule (BuildInfoRecorder.java:464)
    at org.jfrog.build.extractor.maven.BuildInfoRecorder.finalizeModule (BuildInfoRecorder.java:392)
    at org.jfrog.build.extractor.maven.BuildInfoRecorder.projectSucceeded (BuildInfoRecorder.java:210)
    at org.apache.maven.lifecycle.internal.DefaultExecutionEventCatapult.fire (DefaultExecutionEventCatapult.java:74)
    at org.apache.maven.lifecycle.internal.DefaultExecutionEventCatapult.fire (DefaultExecutionEventCatapult.java:42)
    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:127)
    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:81)
    at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build (SingleThreadedBuilder.java:51)
    at org.apache.maven.lifecycle.internal.LifecycleStarter.execute (LifecycleStarter.java:128)
    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:309)
    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:194)
    at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:107)
    at org.apache.maven.cli.MavenCli.execute (MavenCli.java:955)
    at org.apache.maven.cli.MavenCli.doMain (MavenCli.java:290)
    at org.apache.maven.cli.MavenCli.main (MavenCli.java:194)
    at sun.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:62)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke (Method.java:498)
    at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:289)
    at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:229)
    at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode (Launcher.java:415)
    at org.codehaus.plexus.classworlds.launcher.Launcher.main (Launcher.java:356)
```

The line that seems to lead to the issue seems to be `<addParentPoms>true</addParentPoms>`. When removed a deploy will succeed.