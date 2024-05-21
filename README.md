```
task mergeJacocoReports(type: JacocoMergeReport) {
    dependsOn(subprojects.test)

    def jacocoFiles = subprojects.collect {
        fileTree(it.buildDir).include("**/jacoco/*.exec")
    }

    executionData jacocoFiles

    subprojects.each { subproject ->
        sourceSets {
            subproject.name {
                java {
                    srcDirs = subproject.sourceSets.main.allSource.srcDirs
                }
            }
        }
    }

    subprojects.each { subproject ->
        classDirectories += subproject.sourceSets.main.output
    }

    destinationFile = file("$buildDir/reports/jacoco/merged.exec")
}


```
