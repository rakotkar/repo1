```
task jacocoMerge(type: JacocoMerge) {
    dependsOn(subprojects.test)
    
    def execFiles = []
    subprojects.each { subproject ->
        execFiles += fileTree(dir: subproject.buildDir, include: '**/jacoco/*.exec')
    }
    
    executionData execFiles

    // Define source directories for each module
    sourceDirectories = files(subprojects.sourceSets.main.allSource.srcDirs)
    
    // Define class directories for each module
    classDirectories = files(subprojects.sourceSets.main.output)
}
```
