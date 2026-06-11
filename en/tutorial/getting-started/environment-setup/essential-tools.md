# Essential Tools Installation and Configuration

> Note: After configuring environment variables, you must **restart the command prompt** for the changes to take effect.

> Since June 2020, plugin development projects and builds have switched to the modern build tool **Gradle**. Refer to the subsequent documentation for details.

---

## Install JDK

Since January 2019, Oracle JDK has become a paid product. It is recommended to use Oracle JDK 1.8.0_201 or earlier, or switch to [Amazon Corretto JDK](https://docs.aws.amazon.com/corretto/latest/corretto-8-ug/downloads-list.html).

Download and install the version matching your operating system. After installation, run the following in a terminal:

```bash
java -version
```

If the output looks like the following, the installation was successful:

```
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
```

---

## Install Gradle

Gradle is used to manage plugin project configuration, dependency downloads, and build packaging. **Version 6.4** is recommended.

Download (choose one):

- Provided by FanRuan (recommended): [gradle-6.4-bin.zip](https://fine-doc.oss-cn-shanghai.aliyuncs.com/tools/gradle-6.4-bin.zip)
- Official: [https://services.gradle.org/distributions](https://services.gradle.org/distributions)

After downloading, extract the archive and add the `bin` directory to the system `PATH` environment variable. Then run:

```bash
gradle -v
```

If the output looks like the following, the installation was successful:

```
------------------------------------------------------------
Gradle 6.4
------------------------------------------------------------

Build time:   ...
Revision:     ...

Kotlin:       ...
Groovy:       ...
Ant:          ...
JVM:          1.8.0_181 (Oracle Corporation 25.181-b13)
OS:           ...
```

---

## Install Git

Git is used to manage plugin project source code and to download plugin development samples (optional but strongly recommended).

Download (choose one):

- Provided by FanRuan: [Git-2.27.0-64-bit.exe](https://fine-doc.oss-cn-shanghai.aliyuncs.com/tools/Git-2.27.0-64-bit.exe) (Windows)
- Official: [https://git-scm.com/](https://git-scm.com/)

After installation, run:

```bash
git --version
```

If the output looks like the following, the installation was successful:

```
git version 2.27.0.windows.1
```

> **Note**: Git is not strictly required, but it is strongly recommended for downloading plugin samples. You can also use the built-in Git tool in IntelliJ IDEA as an alternative.

---

## Install IntelliJ IDEA

IntelliJ IDEA is the recommended IDE for plugin development. The **Community Edition** is sufficient.

Download (choose one):

- Official: [https://www.jetbrains.com/idea/download](https://www.jetbrains.com/idea/download)
- Provided by FanRuan:
  - Windows: [ideaIC-2020.2.exe](https://fine-doc.oss-cn-shanghai.aliyuncs.com/tools/ideaIC-2020.2.exe)
  - Mac: [ideaIC-2020.2.dmg](https://fine-doc.oss-cn-shanghai.aliyuncs.com/tools/ideaIC-2020.2.dmg)

Follow the installation wizard to complete the setup:

![IntelliJ IDEA installation complete](images/idea_install_complete.png)

---

## Legacy: Building Plugins with Ant

<details>
<summary>Click to expand (only applicable to projects prior to June 2020)</summary>

### Install Ant

Ant is used to build plugin packages. Download Ant from [http://ant.apache.org/](http://ant.apache.org/) and configure the environment variables. After installation, run:

```bash
ant -version
```

If the output looks like the following, the installation was successful:

```
Apache Ant(TM) version 1.10.3 compiled on March 24 2018
```

### Install Maven

Maven is used to manage plugin project configuration and dependency downloads. Download Maven from [http://maven.apache.org/download.html](http://maven.apache.org/download.html) and configure the environment variables. After installation, run:

```bash
mvn -v
```

If the output looks like the following, the installation was successful:

```
Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-18T02:33:14+08:00)
Maven home: C:\Apache\apache-maven-3.5.4\bin\..
Java version: 1.8.0_181, vendor: Oracle Corporation, runtime: C:\Program Files\Java\jre1.8.0_181
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

</details>
