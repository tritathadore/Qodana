[//]: # (title: Gradle plugin)

[![official project](https://jb.gg/badges/official-flat-square.svg)](https://confluence.jetbrains.com/display/ALL/JetBrains+on+GitHub)

The [Gradle Qodana plugin](https://github.com/JetBrains/gradle-qodana-plugin) provides the Gradle interface for running 
code inspections provided by %product%. To get started, apply the Gradle plugin `org.jetbrains.qodana` in the Gradle 
configuration file.

 <tabs group="languages">
  <tab title="Groovy" group-key="groovy">

Add the following to the `build.gradle` configuration file.

  ```groovy
  plugins {
      id "org.jetbrains.qodana" version "<plugin-version>"
  }
  ```

  </tab>
  <tab title="Kotlin DSL" group-key="kotlin-dsl">

Add the following to the `build.gradle.kts` configuration file:

  ```kotlin
  plugins {
      id("org.jetbrains.qodana") version "<plugin-version>"
  }
  ```

  </tab>
 </tabs>

<note>

The latest Gradle Qodana plugin version is currently `0.1.13`. 

</note>


<tip>

For details on working with Gradle in IntelliJ IDEA, see the [IntelliJ IDEA Gradle documentation](https://www.jetbrains.com/help/idea/?Gradle).

</tip>

## 'qodana { }' extension configuration

To configure the plugin, use the following options in the top level `qodana { }` configuration node.

| Name                  | Description                                                               | Type      | Default Value                    |
| --------------------- | ------------------------------------------------------------------------- | --------- | -------------------------------- |
| `autoUpdate`          | Automatically pull the latest Qodana Docker image before running the inspection. | `Boolean` | `true`                           |
| `cachePath`           | Path to the cache directory.                                              | `String`  | `null`                           |
| `dockerContainerName` | Name of the Qodana Docker container.                                      | `String`  | `idea-inspections`               |
| `dockerImageName`     | Name of the Qodana Docker image.                                          | `String`  | `jetbrains/qodana:latest`        |
| `executable`          | Docker executable name.                                                   | `String`  | `docker`                         |
| `projectPath`         | Path to the project folder to inspect.                                    | `String`  | `project.projectDir`             |
| `resultsPath`         | Path to directory to store the execution results.                           | `String`  | `"${projectPath}/build/results"` |
| `saveReport`          | Generate HTML report.                                                     | `Boolean` | `false`                          |
| `showReport`          | Serve an HTML report on `showReportPort` port.                            | `Boolean` | `false`                          |
| `showReportPort`      | Default port used to serve the HTML report.                                 | `Int`     | `8080`                           | 

## Gradle Qodana tasks

After the Gradle Qodana plugin is applied and configured, you can run the provided tasks:
 - [](#runInspections)
 - [](#updateInspections)
 - [](#stopInspections)
 - [](#cleanInspections)

### runInspections

Starts Qodana code analysis in a Docker container. The task runs according to the `qodana { }` extension configuration but also provides [additional properties](#Properties) and [helper methods](#Helper+methods) to configure the Docker image.

<note>

<include src="lib_qd.xml" include-id="docker-ram-note"/>

</note>

#### Properties

| Name                  | Description                                                                                                                      | Type           | Default Value |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------------- |
| `profilePath`         | Path to the profile file to be mounted as `/data/profile.xml`.                                                                   | `String`       | `null`        |
| `disabledPluginsPath` | Path to the list of plugins to be disabled in the Qodana IDE instance to be mounted as `/root/.config/idea/disabled_plugins.txt` | `String`       | `null`        |
| `changes`             | Inspect uncommitted changes and report new problems.                                                                             | `Boolean`      | `false`       |
| `jvmParameters`       | JVM parameters to start the IntelliJ IDEA JVM with.                                                                                                | `List<String>` | `empty`       |

#### Helper methods

| Name                                            | Description                                           |
| ----------------------------------------------- | ----------------------------------------------------- |
| `bind(localPort: Int, dockerPort: Int)`         | Adds a new port binding.                                |
| `mount(localPath: String, dockerPath: String)`  | Mounts a local directory to the given Docker path.      |
| `env(name: String, value: String)`              | Adds an environment variable.                         |
| `dockerArg(argument: String)`                   | Adds a Docker argument to the executed command.       |
| `arg(argument: String)`                         | Adds a Docker image argument to the executed command. |

<tip>

You can study the detailed description of the Qodana Docker image configuration using the [Docker image configuration](docker-image-configuration.xml) page.

</tip>

### updateInspections

Pulls the latest Qodana Inspections Docker container. The task automatically runs before `runInspections` if the `qodana.autoUpdate` property is set to `true`.

### stopInspections

Stops the Qodana Inspections Docker container.

### cleanInspections

Cleans up the Qodana Inspections output directory.

## Example

<tabs group="languages">
  <tab title="Groovy" group-key="groovy"> 

Add the following to the `build.gradle` configuration file:

  ```groovy
  plugins {
      // applies the Gradle Qodana plugin to use it in the project
      id "org.jetbrains.qodana" version "0.1.12"
  }
  
  qodana {
      // by default, the results path is $projectPath/build/results
      resultsPath = "some/output/path"
  }

  runInspections {
      // by default qodana.recommended will be used
      profilePath = "./someExternallyStoredProfile.xml"
  }
  ```
  </tab>

  <tab title="Kotlin DSL" group-key="kotlin-dsl">

Add the following to the `build.gradle.kts` configuration file:

  ```kotlin
  plugins {
      // applies the Gradle Qodana plugin to use it in the project
      id("org.jetbrains.qodana") version "0.1.12"
  }
  
  qodana {
      // by default, qodana.recommended will be used
      profilePath.set("./someExternallyStoredProfile.xml")
  }

  tasks {
      runInspections {
          // by default qodana.recommended will be used
      // profilePath.set("./someExternallyStoredProfile.xml")
      }
  }
  ```
  </tab>
</tabs>

To run the inspections, start the `runInspections` Gradle task:

```shell
gradle runInspections 
// or
./gradlew runInspections
```