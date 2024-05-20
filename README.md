subprojects {
    apply plugin: 'jacoco'

    jacocoTestReport {
        dependsOn test
        reports {
            xml.enabled true
            html.enabled true
        }
    }

    task aggregateJacocoReport(type: JacocoReport) {
        dependsOn subprojects.test
        additionalSourceDirs = files(subprojects.sourceSets.main.allSource.srcDirs)
        sourceDirectories = files(subprojects.sourceSets.main.allSource.srcDirs)
        classDirectories = files(subprojects.sourceSets.main.output)
        executionData = files(subprojects.jacocoTestReport.executionData)

        reports {
            xml.enabled true
            html.enabled true
        }
    }
}

task aggregateReports {
    dependsOn subprojects.jacocoTestReport
}
