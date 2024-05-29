```
apply plugin: 'jacoco'

jacoco {
    toolVersion = "0.8.7"
}

// Define a custom task for generating an aggregated HTML report with coverage summaries
task generateCoverageHTMLReport(type: JacocoReport) {
    dependsOn = subprojects.test

    // Configure where to generate the HTML report
    reports {
        html.destination file("$buildDir/reports/jacoco/html")
    }

    // Define a map to store module-wise coverage results
    def moduleCoverageMap = [:]

    doLast {
        // Iterate over each submodule and include its coverage report
        subprojects.each { subproject ->
            sourceSets subproject.sourceSets.main
            classDirectories = files(subproject.sourceSets.main.output.classesDirs)
            executionData subproject.tasks.withType(Test).collect {
                fileTree(dir: it.reports.junitXml.destination).include("**/jacoco/*.exec")
            }
            
            // Calculate and store coverage percentage for each submodule
            def jacocoReportFile = subproject.file("${subproject.buildDir}/reports/jacoco/test/jacocoTestReport.xml")
            if (jacocoReportFile.exists()) {
                def coverageXML = new XmlSlurper().parse(jacocoReportFile)
                def coveragePercent = (coverageXML.'counter'.@covered.toFloat() / coverageXML.'counter'.@missed.toFloat()) * 100
                moduleCoverageMap[subproject.name] = coveragePercent
            }
        }

        // Generate HTML report with coverage summaries
        def htmlReportFile = new File("$buildDir/reports/jacoco/html/index.html")
        htmlReportFile.text = """
            <html>
            <head>
                <title>Project-wise Coverage Summaries</title>
            </head>
            <body>
                <h1>Project-wise Coverage Summaries</h1>
                <table border="1">
                    <tr>
                        <th>Module</th>
                        <th>Coverage (%)</th>
                    </tr>
                    ${moduleCoverageMap.collect { moduleName, coveragePercent ->
                        "<tr><td>$moduleName</td><td>${coveragePercent.round(2)}</td></tr>"
                    }.join('\n')}
                </table>
            </body>
            </html>
        """
    }
}

```

```
apply plugin: 'jacoco'

jacoco {
    toolVersion = "0.8.7"
}

// Define a custom task for generating an aggregated HTML report with coverage summaries
task generateCoverageHTMLReport(type: JacocoReport) {
    dependsOn = subprojects.test

    // Configure where to generate the HTML report
    reports {
        html.destination file("$buildDir/reports/jacoco/html")
    }

    // Define a map to store module-wise coverage results
    def moduleCoverageMap = [:]

    doLast {
        // Iterate over each submodule and include its coverage report
        subprojects.each { subproject ->
            // Calculate and store coverage percentage for each submodule
            def jacocoReportFile = subproject.file("${subproject.buildDir}/reports/jacoco/test/jacocoTestReport.xml")
            if (jacocoReportFile.exists()) {
                def coverageXML = new XmlSlurper().parse(jacocoReportFile)
                def instructionCoverage = calculateInstructionCoverage(coverageXML)
                moduleCoverageMap[subproject.name] = instructionCoverage
            }
        }

        // Generate HTML report with coverage summaries
        def htmlReportFile = new File("$buildDir/reports/jacoco/html/index.html")
        htmlReportFile.text = """
            <html>
            <head>
                <title>Project-wise Coverage Summaries</title>
            </head>
            <body>
                <h1>Project-wise Coverage Summaries</h1>
                <table border="1">
                    <tr>
                        <th>Module</th>
                        <th>Coverage (%)</th>
                    </tr>
                    ${moduleCoverageMap.collect { moduleName, coveragePercent ->
                        "<tr><td>$moduleName</td><td>${coveragePercent.round(2)}</td></tr>"
                    }.join('\n')}
                </table>
            </body>
            </html>
        """
    }
}

// Function to calculate instruction-wise coverage from the Jacoco XML report
def calculateInstructionCoverage(coverageXML) {
    def coveredInstructions = coverageXML.counter.find { it.@type == 'INSTRUCTION' }.@covered.toInteger()
    def missedInstructions = coverageXML.counter.find { it.@type == 'INSTRUCTION' }.@missed.toInteger()
    def totalInstructions = coveredInstructions + missedInstructions
    return (coveredInstructions / totalInstructions) * 100
}
```

```
