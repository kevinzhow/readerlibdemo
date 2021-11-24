# Readerlib demo

3 ways of how to share Android Module

## Way 1 Publish to Github Repo

### Config Gradle

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

### Run Gradle Task

Make sure all your gralde tasks were built.

run `publishAllPublicationsToGitHubRepository` task to generate all files to the `github_publish` folder

### Use it

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

## Way 2 Github Package

This works with Github Action

### Edit Build.gradle

`readerlibx/build.gradle`

```
plugins {
    id 'com.android.library'
    id 'kotlin-android'
    id 'maven-publish'
}

afterEvaluate {
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
                url "https://maven.pkg.github.com/kevinzhow/readerlibdemo"
                credentials {
                    username = project.findProperty("gpr.user") ?: System.getenv("USERNAME")
                    password = project.findProperty("gpr.token") ?: System.getenv("TOKEN")
                }
            }
        }
    }
}
```

### Edit Github Action

`.github/workflows/publish_library.yml`

```
name: "Publish library to GitHub Packages"
on:
  push:
    branches:
      - main

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Publish library
      env:
        USERNAME: kevinzhow
        TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: ./gradlew assembleRelease publish
```

Every time we push the commits, it will build and publish to github package

[Package Page](https://github.com/kevinzhow/readerlibdemo/packages/1113213)

### Use it

`settings.gradle`

```
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven {
            name = "GitHubPackages"
            url = uri("https://maven.pkg.github.com/kevinzhow/readerlibdemo")
        }
    }
}
```


`app/build.gradle`

```
dependencies {
    implementation 'com.github.kevinzhow:readerlibx:1.0.0'
}
```

## Included Git repositories plugin for Gradle

https://melix.github.io/includegit-gradle-plugin/0.1.2/index.html

## Ref

https://notificare.com/blog/2020/07/10/Share-Your-Android-Code-Between-Projects/
https://qiita.com/Horie1024/items/be2b5eb768f36794c4f1