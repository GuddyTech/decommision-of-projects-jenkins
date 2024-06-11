pipeline {
    agent any
    parameters {
        booleanParam(name: 'Remove_Port_Monitoring', defaultValue: false, description: 'Remove Port Monitoring')
        booleanParam(name: 'Retire_Application', defaultValue: false, description: 'Retire Application')
        booleanParam(name: 'Remove_Application', defaultValue: false, description: 'Remove Application Only If Retired')
    }
    stages {
        stage('Remove Port Monitoring') {
            when {
                expression { return params.Remove_Port_Monitoring }
            }
            steps {
                script {
                    try {
                        sh '''
                        #!/bin/bash
                        API_URL="Place here Api Url for logicmonitor"
                        RESPONSE=$(curl -s -o /dev/null -w "%{http_code}" -X POST "$API_URL" -d '{ "port": "8080" }')

                        if [ "$RESPONSE" -eq 200 ]; then
                            echo "Port monitoring removed successfully."
                        elif [ "$RESPONSE" -eq 404 ]; then
                            echo "Port monitoring is already removed."
                        else
                            echo "Failed to remove port monitoring. HTTP Status: $RESPONSE"
                            exit 1
                        fi
                        '''
                    } catch (Exception e) {
                        echo "Error occurred: ${e.getMessage()}"
                        currentBuild.result = 'FAILURE'
                    }
                }
            }
        }
        stage('Retire Application') {
            when {
                expression { return params.Retire_Application }
            }
            steps {
                script {
                    try {
                        sh '''
                        #!/bin/bash
                        App_Path="/wasprofiles/IBM/WebSphere/Liberty/22_0_0_6/usr/servers/address-normalization"
                        New_App_Path="/wasprofiles/IBM/WebSphere/Liberty/22_0_0_6/usr/servers/ZZaddress-normalization"

                        if [ -d "$App_Path" ]; then
                            sudo mv "$App_Path" "$New_App_Path"
                            echo "Application retired successfully."
                        else
                            echo "Application path not found."
                            exit 1
                        fi
                        '''
                    } catch (Exception e) {
                        echo "Error occurred: ${e.getMessage()}"
                        currentBuild.result = 'FAILURE'
                    }
                }
            }
        }
        stage('Remove Application') {
            when {
                expression { return params.Remove_Application }
            }
            steps {
                script {
                    try {
                        sh '''
                        #!/bin/bash
                        Retired_App_Path="/wasprofiles/IBM/WebSphere/Liberty/22_0_0_6/usr/servers/ZZaddress-normalization"
                        App_Drops_Path="/path to applicationproperties"
                        Logs_Path="/path to logs"
                        Service_File="/path to service file"

                        if [ -d "$Retired_App_Path" ]; then
                            sudo rm -rf "$Retired_App_Path"
                            echo "Retired application removed."
                        else
                            echo "Retired application not found."
                            exit 1
                        fi

                        if [ -d "$App_Drops_Path" ]; then
                            sudo rm -rf "$App_Drops_Path"
                            echo "Application properties folder removed."
                        else
                            echo "Application properties folder not found."
                            exit 1
                        fi

                        if [ -d "$Logs_Path" ]; then
                            sudo rm -rf "$Logs_Path"
                            echo "Logs folder removed."
                        else
                            echo "Logs folder not found."
                            exit 1
                        fi

                        if [ -f "$Service_File" ]; then
                            sudo rm -f "$Service_File"
                            echo "Service file removed."
                        else
                            echo "Service file not found."
                            exit 1
                        fi
                        '''
                    } catch (Exception e) {
                        echo "Error occurred: ${e.getMessage()}"
                        currentBuild.result = 'FAILURE'
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Decommission process completed.'
        }
    }
}
