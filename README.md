# Zero Maven Repository

基于 GitHub Pages 托管的 Maven 制品仓库（groupId：`org.zero`），仅发布 release 版本。

- 仓库地址：`https://cnzeropro.github.io/maven-repository/`
- 托管方式：GitHub Pages（仓库根目录即仓库根，无需子路径）

## 使用方式

在消费者项目的 `pom.xml` 中声明本仓库（仅作为附加 repository，不要配置为 mirror）：

```xml
<repositories>
    <repository>
        <id>zero-github</id>
        <url>https://cnzeropro.github.io/maven-repository/</url>
    </repository>
</repositories>
```

推荐通过 `common-bom` 统一管理版本：

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.zero</groupId>
            <artifactId>common-bom</artifactId>
            <version>${zero.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

可用构件及版本以各构件目录下的 `maven-metadata.xml` 为准。

## 发布流程

用 `mvn deploy` 发布到本仓库目录（校验和与 metadata 由 Maven 生成，勿手工修改），再提交推送：

```bash
mvn deploy -DaltDeploymentRepository=local::default::file:///path/to/maven-repository
git add .
git commit -m "deploy <artifactId>-<version>"
git push
```

发布顺序：`common`（父 POM）→ `common-bom` → `common-api` → `common-core` / `common-data` / `common-job`。

## 约定

- release 版本发布后**不可变**：如需修复请发布新版本号（如 `1.0.1`），不要覆盖已发布版本
- GitHub Pages 走 CDN，新发布版本的元数据最多有数分钟缓存延迟，本地构建可用 `mvn -U` 强制刷新
