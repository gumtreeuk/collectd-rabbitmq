#!/usr/bin/env groovy
// :vim set et ts=4 sw=4

// stdeb pipeline
// This pipeline builds a .deb package from a python package

// Configure these properties
projectDir = 'collectd-rabbitmq'
projectRepoURL = 'https://github.com/gumtreeuk/collectd-rabbitmq.git'
debName = 'collectd-rabbitmq'
projectBranch = env.JOB_NAME.replaceFirst('.+/', '')
architecture = "all"


node {


    withPython {

        checkoutProject(projectDir, projectRepoURL, projectBranch)
        dir(projectDir) {
            version = getVersionFromPythonSetupTools()

            cleanPythonWorkspace()
            stage 'Test'
            runPythonTests()
            stage 'Devpi Upload'
            uploadPython()
            stage 'Build Debian Package'
            buildDebian(debName, version)
            stage 'Aptly Upload'
            uploadDebianToStaging(debName, version, architecture, "..")
            if (projectBranch == globals.releaseBranch) {
                stage 'Production Upload'
                uploadDebianToProduction(debName, version, architecture, "..")
            }
        }
    }
}
