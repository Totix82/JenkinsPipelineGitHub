# JenkinsPipelineGitHub

This project is for a demo of how to create a Jenkins job and pipeline that clones a GitHub repo. 

# Integrate Jenkins and GitHub in a Jenkins job

Create a job as a free-style called "clone-apache-commons-io"

Under Source Code Management section of your job configuration choose Git option

Copy the repository URL from your GitHub project (in our case https://github.com/apache/commons-io)

Paste the copied link to Repository URL text box

Next click the Add button under credentials drop-down

Add your GitHub account and password in the populated dialog

Choose githubweb from Repository browser drop-down

Paste your GitHub project URL in the URL text box (as per above)


# The Jenkinsfile for the pipeline

A Jenkinsfile created using the classic UI is stored by Jenkins itself (within the Jenkins home directory).

In our case will be therefore /var/lib/jenkins/jobs/JenkinsPipelineGitHubPack/config.xml

In this repo 2 versions have been provided: the XML version that Jenkins uses in the background as well (as a file) the Groovy script that Jenkins uses in the GUI. 

To create a basic Pipeline through the Jenkins classic UI:

From the Jenkins home page (i.e. the Dashboard of the Jenkins classic UI), click New Item at the top left.

Scroll down and click Pipeline, then click OK at the end of the page to open the Pipeline configuration page (whose General tab is selected).

Enter a name, click Pipeline and then click OK

In the Pipeline section, ensure that the Definition field indicates the Pipeline script option.

Enter your Pipeline code into the Script text area.


The build will be composed by 3 stages. The notification stages of start/end of the project will be ignored, since they will fail because currently I don't have an SMTP server available.

To skip then use a script step and try-catch block (similar to previous proposition by R_K but in declarative style), e.g.

stage('someStage') {
    steps {
        script {
            try {
                build job: 'system-check-flow'
            } catch (err) {
                echo err.getMessage()
            }
        }
        echo currentBuild.result
    }
}

Or use catchError
stage('someStage') {
    steps {
        catchError {
            build job: 'system-check-flow'
        }
        echo currentBuild.result
    }
}
In both cases the build won't be aborted upon exception in build job: 'system-check-flow'. In both cases the echo step (and any other following) will be executed.

But there's one important difference between these two options. In first case if the try section raises an exception the overall build status won't be changed (so echo currentBuild.result => SUCCESS). In the second case you overall build will fail (so echo currentBuild.result => FAILURE).

This is important, because you can always fail the overall build in first case (by setting currentBuild.result = 'FAILURE') but you can't repair build in second option (currentBuild.result = 'SUCCESS' won't work).


# Run the Pipeline

Run the pipeline and find the clone in /var/lib/jenkins/workspace/clone-apache-commons-io

