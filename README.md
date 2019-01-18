# Shared Gradle Build Scripts across projects

Reusable gradle build scripts for multi spring boot microservices projects on the wild (lol)

## The theory of everything

### Objectives 

You want:

* Every microservice project with its own git
* CI and pipelines separated, parallel and independent 
* Atomic separated deploys
* Standard build configurations (coverage, integration test, and others)

You DON'T want:

* Copy and past build logic.
* Make the life of your average developer miserable.
* Broke every single microservice pipeline in one shot. 

### Solution: _Git subtree + Gradle scripts = Magic_

#### Git side of the moon

Add the project as a git subtree:

```bash
git remote add -f gradle-scripts git@github.com:giovannicandido/gradle-scripts.git # please fork the code
git subtree add --prefix=gradle-scripts/ gradle-scripts master --squash
```

This will create a subdir **gradle-scripts** with the commits of the gradle-scripts master branch squashed

**Updating:**

```bash
git subtree pull --prefix=gradle-scripts/ gradle-scripts master --squash
```

**Contributing back:**

Please use a fork.

```bash
git remote add my-fork git@github.com:my-user/gradle-scripts.git
git subtree push --prefix=gradle-scripts my-fork master
```

Open a pull request and welcome to the party.

Note: Other devs do nothing (clone, pull, push stays the same), but let they know you are using 
"advanced" git stuff.

#### Gradle side of the moon

Apply the gradle plugins.
Example:

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

References:

[https://git-scm.com/book/en/v1/Git-Tools-Subtree-Merging](https://git-scm.com/book/en/v1/Git-Tools-Subtree-Merging)


[https://docs.gradle.org/current/userguide/plugins.html](https://docs.gradle.org/current/userguide/plugins.html#sec:script_plugins)

[https://www.atlassian.com/blog/git/alternatives-to-git-submodule-git-subtree](https://www.atlassian.com/blog/git/alternatives-to-git-submodule-git-subtree)
