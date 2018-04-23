import org.apache.commons.codec.binary.Base64;
import org.apache.commons.io.IOUtils;

/**
Documentation:
    This is a skeleton for a Jenkins Pipeline script that would
    kick off test runs on ION server via Rest API requests.
**/

/**
Sample method that sends Http REST API request to Twitter API.
**/
def authenticateRequest()
{
    try {
        // this is a sample method that tests HTTP REST API request with Twitter
        URL url = new java.net.URL("https://api.twitter.com/oauth2/token");

        // authorization string is Consumer Key and Consumer Secret separated by a colon
        def authString = "UugjqF0hS2NPWCzZXGJK4mpNm" + ":" + "rH3odTBPNzggLkpTiDeYH3wnFumj36dtrPIPRAvT7kldwZn0OL";
        byte[] authEncBytes = Base64.encodeBase64(authString.getBytes());
        String authStringEnc = new String(authEncBytes);

        // initialize http request method and params
        URLConnection connection = url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Authorization", "Basic " + authStringEnc);
        connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded;charset=UTF-8");
        connection.setRequestProperty("Accept", "application/xml");

        // write to the request body
        connection.setDoOutput(true);
        OutputStream outStream = connection.getOutputStream();
        IOUtils.write("grant_type=client_credentials", outStream, "UTF-8");
        outStream.close();

        // send http request
        connection.connect();

        // get response content
        InputStream inStream = connection.getInputStream();
        body =  "HTTP Response Code: " + connection.getResponseCode()
        body += "\nHTTP Response Mesg: " + connection.getResponseMessage()
        body += "\nHTTP Response Body: " + IOUtils.toString(inStream, "UTF-8");

        println(body);
    }
    catch (java.io.NotSerializableException e) {
        println("NotSerializableException occurred:" + e.toString());
    }
}


pipeline {
    //specify the agent where the job will run
    agent any

    //optional step for setting local environment variables
    environment {
        DISABLE_AUTH = 'true'
        DB_ENGINE    = 'sqlite'
    }

    stages {
        stage('Confirm Env') {
            steps {
                echo 'print out and verify environment variables...'
                //bat 'SET'
            }
         }
        stage('Trigger Test Run') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    retry(3) {
                        echo 'Sending test run request to ION Server'
                        authenticateRequest()
                        echo 'Test run request to ION Server has been sent successfully. Session ID is BLA'
                    }
                }
            }
        }
        stage('Monitor Test Run') {
            steps {
                timeout(time: 12, unit: 'HOURS') {
                    retry(2) {

                        echo 'Sending test run STATUS request to ION Server'

                        script {
                                def status = "COMPLETE"
                                echo 'ION Server responded with status: IN PROGRESS | COMPLETE | STOPPED'
                                assert status == "COMPLETE"
                        }
                    }
                }
            }
        }
        stage('Publish Test Results') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    retry(3) {
                        echo 'Sending test run REPORT request to ION Server'

                        echo 'ION Server responded with test report'

                        echo 'Test report was saved to /jenkins/ION_Test_Reports/this_file.html'
                    }
                }
            }
        }
    }
    //post-job steps
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'

        }
        failure {
            echo 'This will run only if failed'

            // mail to: 'anas.alsalah@gmail.com',
            // subject: "Successful Pipeline: ${currentBuild.fullDisplayName}",
            // body: "Something went wrong with ${env.BUILD_URL}"
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}