# Readerlib demo

## Config Gradle

`readerlibx/build.gradle`

```
plugins {
    id 'com.android.library'
    id 'kotlin-android'
    id 'maven-publish'
}


afterEvaluate { // <-- needed because only here components are available
    publishing {
        publications {
            release(MavenPublication) {
                from components.release
                groupId 'com.github.kevinzhow'
                artifactId 'readerlibx'
                version '1.0.0'
            }
        }

        repositories {
            maven {
                name 'GitHub'
                url "file://Users/zhoukaiwen/GitHub/readerlibdemo/github_publish" // This path is your repo's sub folder
            }
        }
    }
}
```

## Run Gradle Task

Make sure all your gralde tasks were built.

run `publishAllPublicationsToGitHubRepository` task to generate all files to the `github_publish` folder

## Use it

In your app's `settings.gradle` add a maven url and make sure you follows the `raw/${branch}/` format

```
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()

        maven {
            url "https://github.com/kevinzhow/readerlibdemo/raw/main/github_publish
        }
    }
}
```

finally implements it

```
dependencies {
  implementation 'com.github.kevinzhow:readerlibx:1.0.0'
}
```