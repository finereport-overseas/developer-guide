# 项目结构说明

插件开发项目基于 Maven 多模块结构，每个插件对应一个独立的子模块。

---

## 典型目录结构

```
report-starter-10/                  ← 脚手架根项目
├── pom.xml                         ← 父 pom，管理公共依赖版本
├── webroot/                        ← FR 运行时目录（构建后生成）
│   └── WEB-INF/lib/                ← FR 所有 JAR 依赖
│
└── plugin-hello-world/             ← 插件子模块（一个插件一个模块）
    ├── pom.xml                     ← 模块依赖配置
    ├── plugin.xml                  ← 插件配置文件
    ├── build.xml                   ← Ant 打包脚本
    ├── lib/                        ← 插件自带的第三方 JAR（打入安装包）
    ├── dependence/                 ← 依赖的其他插件 JAR（不打入安装包）
    └── src/
        └── main/
            ├── java/               ← Java 源码
            └── resources/          ← 资源文件（国际化、JS、CSS 等）
```

---

## build.xml 关键配置说明

`build.xml` 是 Ant 打包脚本，用于将插件模块打包为可上传到应用中心的 zip 安装包。

### 必须修改的配置项

```xml
<!-- JDK 安装目录，根据本机实际路径修改 -->
<property name="jdk.home" value="G:/JDK/jdk1.8.0_45"/>

<!-- FR JAR 所在目录，指向 webroot/WEB-INF/lib -->
<property name="FRLibs" value="${basedir}/../webroot/WEB-INF/lib"/>

<!-- 插件名称，用于生成 JAR 和 zip 文件名 -->
<property name="plugin-name" value="my-demo"/>
```

### 可选配置项

```xml
<!-- 插件自带的第三方 JAR 目录（最终会打进安装包） -->
<property name="libs" value="${basedir}/lib"/>

<!-- 依赖的其他插件 JAR 目录（最终不会打进安装包） -->
<property name="dependenceLibs" value="${basedir}/dependence"/>
```

---

## 打包产物

执行 Ant 构建后，模块目录下会生成 zip 安装包，命名格式为：

```
fr-plugin-{plugin-name}-{version}.zip
```

zip 包内容：

```
fr-plugin-my-demo-1.0.zip
└── fr-plugin-my-demo-1.0/
    ├── plugin.xml
    ├── fr-plugin-my-demo-1.0.jar    ← 插件编译产物
    └── *.jar                        ← lib/ 下的第三方依赖（如有）
```

---

## 插件加密

`build.xml` 中内置了帆软插件引擎的加密任务，**加密不同于代码混淆**，使用常规反编译工具无法还原源码：

```xml
<taskdef name="pretreatment" classname="com.fr.plugin.pack.PluginPretreatmentTask">
    <classpath refid="compile.classpath"/>
</taskdef>
<pretreatment baseDir="${basedir}"/>
```

如不需要加密，可删除这两行。
