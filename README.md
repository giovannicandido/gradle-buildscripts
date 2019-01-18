# gradle-buildscripts

Reusable gradle build scripts for multi spring boot microservices projects on the wild (lol)

## The theory of everything

**Objectives**: 

You want:

* Every microservice project with its own git
* CI and pipelines separated, parallel and independent 
* Atomic separated deploys
* Standard build configurations (coverage, integration test, and others)

You DON'T want:

* Copy and past build logic.
* Make the life of your average developer miserable.
* Broke every single microservice pipeline in one shot. 

**Solution**: _Git subtree + Gradle scripts = Magic_

That's it go do it. Ahahaha (this is lol in portuguese). Documentation is coming... I sure is simple... 

Usage:

Add the project as a git subtree:

```bash

git remote add gradle_scripts git@github.com:giovannicandido/gradle-scripts.git # please fork the code
git fetch gradle_scripts
git read-tree --prefix=gradle-scripts/ -u gradle_scripts

```

Apply the gradle plugins exemple

```groovy
buildscript {
	ext {
		springBootVersion = '2.1.1.RELEASE'
	}
	repositories {
        maven {
            url "https://plugins.gradle.org/m2/"
        }
        mavenCentral()
	}
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
    classpath("org.sonarsource.scanner.gradle:sonarqube-gradle-plugin:2.5")
	}
}


apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'

group = 'br.com.awesomeness'
version = '0.0.1.RELEASE'
sourceCompatibility = 10

apply from: './gradle-scripts/defaults.gradle'

ext {
    jacocoMinimumCoverage = 0.999999 // Override defaults. We have chuck norris son coding
}

apply from: './gradle-scripts/repositories.gradle'
apply from: './gradle-scripts/spring.gradle'
apply from: './gradle-scripts/spring-cloud.gradle'
apply from: './gradle-scripts/quality.gradle'
apply from: './gradle-scripts/test.gradle'
apply from: './gradle-scripts/lombok.gradle'
apply from: './gradle-scripts/commons.gradle'

dependencies {
    implementation 'ch.obermuhlner:big-math:2.0.1'
}

```

Note: I did'nt find a way to reuse **buildscript blocks**

In the mean time:

[Git subtree](https://git-scm.com/book/en/v1/Git-Tools-Subtree-Merging)

[Gradle build scripts](https://docs.gradle.org/current/userguide/plugins.html#sec:script_plugins)