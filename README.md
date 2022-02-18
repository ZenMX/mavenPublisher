# How to publish JAVA & Android library to private nexus

suppose project structure looks like below.  

```shell
-Logger-Android
-build.gradle
-settings.gradle
-local.properties
...other files
-logger
    -build.gradle
    ...other files
```


## config build.gradle of root project

add followings to the end of `Logger-Android/build.gradle`

```groovy

static def getBuildNumber() {
    return System.getenv("BUILD_NUMBER") as Integer ?: 0
}

static def getPublisherVersion() {
    return "1.0.${getBuildNumber()}"
}

ext {
    publishGroupId = 'com.mxplay.code'
    publishVersion = "${getPublisherVersion()}"
    publishScript = 'https://raw.githubusercontent.com/ZenMX/mavenPublisher/master/publish.gradle'
}

```

## config build.gradle of your library

Optionally, add followings to `Logger-Android/logger/build.gradle` for customizing your publish.

```groovy

ext {
    publishArtifactId = "your-customized-artifact-id"//optional
    publishSource = false//optional
    publishFile = 'build/outputs/aar/your-library-debug.aar'//optional
}

```

NOTE: action below is necessary.  

add following to the end of `Logger-Android/logger/build.gradle`

```groovy

apply from: "${publishScript}"

```

## config nexus access

add followings to end of `Logger-Android/local.properties`

```shell

publishUrlSnapshot=http\://13.22.79.223\:8098/nexus/content/repositories/snapshots/
publisherPassword=your password
publishUrlRelease=http\://13.22.79.223\:8098/nexus/content/repositories/releases/
publisherUserName=your account

```

or you can add above to your `ENV` variable.  

## enjoy

now you can build your library and publish it to your private nexus.

```shell

./gradlew logger:assemble publishMavenReleasePublicationToReleaseRepository

```
