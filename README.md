# ap-home

## pepperlauncherlibrary ##

### ライブラリ作成方法 ###

* Android StudioのGradleのTasksのuploadにuploadArchivesコマンドが追加されてる
* pepperlauncherlibraryのuploadArchivesを実行する
* 実行するとmaven-repogitoryディレクトリが作成され、必要なファイル一式が作成される
* 修正したソースとともにmaven-repogitoryディレクトリ内の変更もgit pushしておく
* developに変更が反映されるとライブラリが更新される

### ライブラリ使用方法 ###
* ssh経由でgithubのap-homeをcloneできる状態に端末を設定しておく
* プロジェクトのbuild.gradleに下記のようにgradleのライブラリを追加

```groovy:app/build.gradle
buildscript {

    ・・・・

    dependencies {
       ・・・・

        classpath('org.ajoberstar:gradle-git:1.7.2')
    }
}
```

* app/build.gradleに下記のようにrepositoryを読み込むコードを追加
```groovy:app/build.gradle
android {
    ・・・・
}

//Load pepperlauncherlibrary repository
import org.ajoberstar.grgit.Grgit
def repoDir = new File(rootDir, ".gitRepos")
def gitUrl = "git@github.com:Palsbots/ap-home.git"
def branch = "develop"
def mavenRepoDic = "pepperlauncherlibrary/maven-repository"
def gitRepo
if(repoDir.directory || project.hasProperty("offline")) {
    gitRepo= Grgit.open(dir: repoDir)
    if(!project.hasProperty("offline")) {
        gitRepo.pull()
    }
} else {
    gitRepo= Grgit.clone(dir: repoDir, uri: gitUrl)
    gitRepo.checkout(branch: branch , startPoint: 'origin/'+ branch, createBranch: true)
}
project.repositories.maven {
    url repoDir.getAbsolutePath() + "/" + mavenRepoDic
}

dependencies {
    ・・・・
}
```

* app/build.gradleに他のライブラリと同様に下記のようにpepperlauncherlibraryを追加
```groovy:app/build.gradle
dependencies {
    ・・・・

    compile 'jp.softbank.robot:pepperlauncherlibrary:0.1.6'
}
```

* Gradleをsyncすれば完了
* プロジェクトのルートに.gitReposが追加されているので、ソース管理で除外するなどしてください
