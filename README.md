**Kover** - Gradle plugin for Kotlin code coverage agents

# Main features of the plugin
* uses IntelliJ or JaCoCo agent to collect the code coverage for JVM test tasks
* uses IntelliJ or JaCoCo reporter to generate XML and HTML reports
* allows to specify the version of the IntelliJ and JaCoCo agents
* works with Kotlin/JVM and Kotlin Multiplatform sources
* supports the work with Kotlin Android sources without dividing them into build types and flavours
* supports custom filtering instrumented classes

###### features of the IntelliJ Coverage
* supports Kotlin/JVM projects
* supports Kotlin Multiplatform projects
* supports `inline` functions, including those declared in multiplatform sources or called from tests
* generates test coverage HTML report
* generates test coverage XML report compatible with JaCoCo's XML
* supports custom filtering instrumented classes by RegExp


# Basic Gradle Setup

###### Add repository
for `settings.gradle`
```
pluginManagement {
    repositories {
        // ... other dependencies
        maven { url 'https://maven.pkg.jetbrains.space/public/p/jb-coverage/maven' }
    }
}
```
###### Apply plugin to project
for `build.gradle.kts`
```
plugins {
    // ... other plugins
    id("kotlinx-kover") version "0.2.2"
}
```
for `build.gradle`
```
plugins {
    // ... other plugins
    id 'kotlinx-kover' version '0.2.2'
}
```
# Customize settings

for `build.gradle.kts`
```
tasks.test {
    extensions.configure(kotlinx.kover.KoverTaskExtension::class) {
        useJacoco = false
        xmlReport = true
        htmlReport = false
        xmlReportFile.set(file("$buildDir/custom/report.xml"))
        htmlReportDir.set(file("$buildDir/custom/html"))
        binaryFile.set(file("$buildDir/custom/result.bin"))
        includes = listOf("com\\.example\\..*")
        excludes = listOf("com\\.example\\.subpackage\\..*")
    }
}
```

for `build.gradle`
```
tasks.test {
    kover {
        useJacoco = false
        xmlReport = true
        htmlReport = false
        xmlReportFile.set(file("$buildDir/custom/report.xml"))
        htmlReportDir.set(file("$buildDir/custom/html"))
        binaryFile.set(file("$buildDir/custom/result.bin"))
        includes = ['com\\.example\\..*']
        excludes = ['com\\.example\\.subpackage\\..*']
    }
}
```

###### Change version of agents
for `build.gradle.kts` and `build.gradle`
```
kover {
    intellijAgentVersion.set("1.0.611")
    jacocoAgentVersion.set("0.8.7")
}
```

###### Verification
For each test task, you can specify one or more rules that check the values of the code coverage counters.

Validation rules work for both types of agents.

*The plugin currently only supports line counter values.*

for `build.gradle.kts`
```
tasks.test {
    extensions.configure(kotlinx.kover.KoverTaskExtension::class) {
        verificationRule {
            name = "The project doesn't have to be big"
            maxValue = 100000
            valueType = kotlinx.kover.VerificationValueType.COVERED_LINES_COUNT
        }
        verificationRule {
            // rule without custom name
            minValue = 1
            maxValue = 1000
            valueType = kotlinx.kover.VerificationValueType.MISSED_LINES_COUNT
        }
        verificationRule {
            name = "Minimal line coverage rate in percents"
            minValue = 50
            // valueType is kotlinx.kover.VerificationValueType.COVERED_LINES_PERCENTAGE by default
        }
    }
}
```

for `build.gradle`
```
tasks.test {
    kover {
        verificationRule {
            name = "The project doesn't have to be big"
            maxValue = 100000
            valueType = 'COVERED_LINES_COUNT'
        }
        verificationRule {
            // rule without custom name
            minValue = 1
            maxValue = 1000
            valueType = 'MISSED_LINES_COUNT'
        }
        verificationRule {
            name = "Minimal line coverage rate in percents"
            minValue = 50
            // valueType is 'COVERED_LINES_PERCENTAGE' by default
        }
    }
}
```

