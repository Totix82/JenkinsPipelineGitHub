# JenkinsPipelineGitHub

A Jenkinsfile created using the classic UI is stored by Jenkins itself (within the Jenkins home directory).

To create a basic Pipeline through the Jenkins classic UI:

From the Jenkins home page (i.e. the Dashboard of the Jenkins classic UI), click New Item at the top left.

Scroll down and click Pipeline, then click OK at the end of the page to open the Pipeline configuration page (whose General tab is selected).

Enter a name, click Pipeline and then click OK

In the Pipeline section, ensure that the Definition field indicates the Pipeline script option.

Enter your Pipeline code into the Script text area.


The build will be composed by X stages. The notification stages will be ignored, since they will fail.

Currently I don't have an SMTP server available.

To skip then use script step and try-catch block (similar to previous proposition by R_K but in declarative style), e.g.

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


