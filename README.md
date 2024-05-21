```
task mergeJacocoReports(type: JacocoReport) {
    dependsOn(subprojects.test)

    def jacocoFiles = subprojects.collect {
        fileTree(it.buildDir).include("**/jacoco/*.exec")
    }

    executionData jacocoFiles

    sourceDirectories = files(subprojects.sourceSets.main.allSource.srcDirs)
    classDirectories = files(subprojects.sourceSets.main.output)

    reports {
        xml.enabled false // Disable XML report for merged report
        html.enabled true
        html.destination file("${buildDir}/jacocoHtml")
    }
}

```
