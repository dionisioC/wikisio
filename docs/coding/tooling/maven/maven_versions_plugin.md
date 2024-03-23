---
title: Localstack and DynamoDb
date: 20240317
author: Dionisio
---

Sometimes we have (or must) update dependencies in a project. There is a plugin
that helps us to achieve that in an easy way.
The plugin is the [Versions Maven Plugin](https://www.mojohaus.org/versions/versions-maven-plugin/index.htm)

Not going to be specific as each project is different, but the basics are:

# Displaying dependency updates

With the following command,
we can check which dependencies have a newer version:

```shell
mvn versions:display-dependency-updates
```

To omit some versions like beta versions or other unwanted versions, we can use
a regular expression, like the following:

```shell
mvn versions:display-dependency-updates "-Dmaven.version.ignore=.*-M.*,.*-alpha.*,.*-Alpha.*,.*-beta.*,.*-Beta.*"
```

We can see the updates in different places.

The first part is:
**The following dependencies in Dependency Management have newer versions**

Which are dependencies that are most likely managed by Spring Boot if you have
a Spring Boot project.

After that, we can see:
**The following dependencies in Dependencies have newer versions**

Those are our dependencies.

The [Versions Maven Plugin](https://www.mojohaus.org/versions/versions-maven-plugin/index.htm)
also takes care of the plugins.

We have the two sections we had before but for plugins:
**No dependencies in pluginManagement of plugins have newer versions**

Tell us the plugins in the pluginManagement that have updates.

Lastly, the ones that we do not manage are bellow the following line:
**The following dependencies in Plugin Dependencies have newer versions**

# Generating dependency updates report

If we want something more sophisticated than the command line output, we can
generate an HTML report in `target/site`.

```shell
mvn versions:dependency-updates-report
```

# Adding a dependency updates report to the lifecycle

To autogenerate it when doing:

```shell
mvn site
```

We have to add this below build in the `pom.xml`

```xml
<reporting>
    <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>versions-maven-plugin</artifactId>
            <version>2.16.2</version>
            <configuration>
                <ignoredVersions>.*-M.*,.*-alpha.*,.*-Alpha.*,.*-beta.*,.*-Beta.*</ignoredVersions>
            </configuration>
            <reportSets>
                <reportSet>
                    <reports>
                        <report>dependency-updates-report</report>
                        <report>plugin-updates-report</report>
                        <report>property-updates-report</report>
                    </reports>
                </reportSet>
            </reportSets>
        </plugin>
    </plugins>
</reporting>
```

# Updating automatically

We can update the pom.xml automatically with:

```shell
mvn versions:use-next-releases
```

# Revert the update

To revert to the previous version, we can do:

```shell
mvn versions:revert
```

The `mvn versions:use-next-releases` command generates a
`pom.xml.versionsBackup` file so we can roll back to the previous versions.
