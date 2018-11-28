# 将jar或者aar发布到到mvn 中（用github作为仓库）， 通过gradle dependencies 方式集成

------

使用Android Studio开发的用户，都希望通过maven远程仓库的方式来集成jar、aar文件，但是这些文件时如何发布的呢？

下面就为大家讲解如何在github上创建仓库，将jar和aar文件发布到maven中，以及如何集成。

## 第一步：

       在github.com 上创建一个repo，专门用作maven仓库使用。 例如https://github.com/1136346879/UtilsToolDx

## 第二步：

      使用git clone 命令将文件clone到本地。

## 第三步：

      ### 1、发布aar文件

        在工程目录的build.gradle文件中添加：
--------------------- 
apply plugin: 'maven'
 
android{
    // ...
}
 
// ext is a gradle closure allowing the declaration of global properties
ext {
    PUBLISH_GROUP_ID = 'com.dx'
    PUBLISH_ARTIFACT_ID = 'xfs'
    PUBLISH_VERSION = android.defaultConfig.versionName
}
 
 
 
     // uploadArchives{    repositories.mavenDeployer {    
	 //def mavenRepositoryPath = file(getProperty('aar.maventest'))  
	 //repository(url: "file://${mavenRepositoryPath.absolutePath}")     
	 //pom.project{            groupId "com.chengang"           
							 artifactId "工程名"          
							 version “版本号”        }    }}
uploadArchives {
    repositories.mavenDeployer {
        def deployPath = file(getProperty('aar.deployPath'))
        repository(url: "file://${deployPath.absolutePath}")
        pom.project {
            groupId project.PUBLISH_GROUP_ID
            artifactId project.PUBLISH_ARTIFACT_ID
            version project.PUBLISH_VERSION
        }
    }
}


其中aar.deployPath 可在gradle.properties 文件中指定:


aar.deployPath=E:\\dev\\workspace\\mvn-repo-ione\\repository
路径为第二步clone repo选择的目录。

发布aar文件前，只需要在工程目录下执行命令：
gradlew uploadArchives
即可生成上传至以github作为maven仓库的所需文件。然后把在第二步clonerepo选择的目录中更新的文件push到github上，即可完成发布。
  ### 2、发布jar或aar文件

通过以下命令将本地的jar或aar包安装到此目录下：（F:\gitDemo\libs_local\app-release.aar   arr文件的路径）

> * (使用  mvn 命令时 需先集成mvn，并配置其环境变量)

mvn install:install-file -DgroupId=com.dx -DartifactId=xfs -Dversion=1.0 -Dfile=F:\gitDemo\libs_local\app-release.aar -Dpackaging=aar -DgeneratePom=true -DlocalRepositoryPath=F:\gitDemo\libs_local -DcreateChecksum=true
// mvn install:install-file -DgroupId=com.dx -DartifactId=xfs -Dversion=1.0 -Dfile=F:\gitDemo\libs_local\app-release.jar -Dpackaging=jar -DgeneratePom=true -DlocalRepositoryPath=F:\gitDemo\libs_local -DcreateChecksum=true

--------------------- 
注意包名和路径
运行上述命令后在本地目录下就有了需要的maven文件， 例如:


mvn-repo-demogit:(master) tree
  .
  ├── LICENSE
  ├── README.md
  └── repository
      └── com
          └── ione
              └── demo
                  ├── 1.0
                  │   ├── demo-1.0.jar
                  │   ├── demo-1.0.jar.md5
                  │   ├── demo-1.0.jar.sha1
                  │   ├── demo-1.0.pom
                  │   ├── deno-1.0.pom.md5
                  │   └── demo-1.0.pom.sha1
                  ├── maven-metadata-local.xml
                  ├── maven-metadata-local.xml.md5
                  └── maven-metadata-local.xml.sha1
 
  5 directories, 11 files


然后通过git push 将生成的maven 文件push 到github 上去，即可完成发布。
## 第四步：

在Android Studio中集成jar文件或者aar文件

在工程的build.grade 文件中添加：

	repositories {
			  jcenter()
			  //配置私有maven格式：https://raw.githubusercontent.com/+项目地址+分支master
			  maven { url "https://raw.githubusercontent.com/ione/mvn-repo-ione/master/repository" }
	  }

	dependencies {
	//implementation "groupId:artifactId:version"
		  compile 'com.ione:demo:1.0'
	  }

--------------------- 
即可完成集成。