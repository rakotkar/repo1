```
jacocoTestReport {
    dependsOn = subprojects.test
    reports {
        xml.enabled false // We'll only use HTML report for the aggregated one
        html.destination file("$buildDir/reports/jacoco")
    }

    doLast {
        // Collect coverage files from all subprojects
        subprojects.each { subproject ->
            from(subproject.tasks["jacocoTestReport"]) {
                // Rename the file to avoid conflicts
                rename { fileName ->
                    "${subproject.name}-${fileName}"
                }
            }
        }
    }
}

```
