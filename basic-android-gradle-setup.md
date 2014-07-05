title: Basic Android Gradle Setup

This post outlines how I setup my Android project with Gradle. I don't think any
JAR files should be in my repository, so I refuse to add any Gradle wrapper to
the repository directly. Instead I create a `wrapper.gradle` file as follows:

    task wrapper(type: Wrapper) {
        gradleVersion = '1.12'
    }

To create the wrapper I use the system version of Gradle with the following
command: `gradle -b wrapper.gradle wrapper`. After, I build the project using
`./gradlew`. To keep my Git repository clean I have the following entries in
`.gitignore`:

    /.gradle/
    /gradle/
    /gradlew
    /gradlew.bat

This is the setup I currently like to keep everything clean and organized.
Here's my example `build.gradle` for completeness:

    buildscript {
        repositories {
            mavenCentral()
        }
        dependencies {
            classpath 'com.android.tools.build:gradle:0.11.2'
        }
    }
    apply plugin: 'android'
    
    android {
        compileSdkVersion 19
        buildToolsVersion "19.1"
    }
