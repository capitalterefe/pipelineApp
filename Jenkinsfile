def logfile = new File("/tmp/pipeline.txt")
def final_result = []

pipeline {
    agent {
        node {
            label 'master'
        }
    }
    stages {
        stage('Clone repo') {
            steps {
              // git url: 'https://github.com/capitalterefe/pipelineApp.git'
                 sh '''
                echo "Hello World"
                '''
            }
        }
        stage('Create report file') {
            steps {
                sh '''
                echo "" > /tmp/pipeline.txt
                chmod 777 /tmp/pipeline.txt
                '''
            }
        }
    stage('Building report') {
            steps {
                
                script {
                    get_list = new File("${WORKSPACE}/pipeline-CheckList").text.readLines()
                    for(jobName in get_list){
                        def job = jenkins.model.Jenkins.instance.getItemByFullName(jobName)
                        last_job_num = job.getLastBuild().getNumber()
                              
                        def date_and_time = job.getBuildByNumber(job.getLastBuild().getNumber()).getTime().toString()
                        def result = job.getLastBuild().getResult().toString()
                        def duration = job.getLastBuild().getDurationString()
                        echo "|"+ jobName.toUpperCase() + "| STATUS: " + result + "| Latest Job Completed at: "+date_and_time +"| Latest Job Took: "+duration 
                       
                        def final_result_get = jobName + " --> STATUS: " + result + "\t" + "Job Completed: "+date_and_time +"\t" +"Job Took: "+duration  + "\n"
                        logfile.append(final_result_get)
                        final_result.add(jobName + " --> STATUS: " + result + "\t" + "Job Completed: "+date_and_time +"\t" +"Job Took: "+duration  + "\n")
                        
                    }
                }
                println(final_result)
               // cleanWs()
            }
        }
    stage('Sending Email') {
            steps {
                sh '''
                pipeline_result=`cat /tmp/pipeline.txt | awk {'print $3'} | awk /./ |  sort | uniq`
                if [[ $pipeline_result -eq "SUCCESS" ]]
                then
                pipeline_status="PASSED"
                elif  [[ $pipeline_result -eq "null" ]]
                then
                pipeline_status="PASSED"
                else
                pipeline_status="FAILED"
                fi
               
                echo "" >> /tmp/pipeline.txt
                echo "Pipeline URL: http://jenkins:8080/view/pipeline/" >> /tmp/pipeline.txt
                cat /tmp/pipeline.txt | mailx -S smtp=smeserver AdminValidation@gmail.com -s "Pipeline Result - $pipeline_status" managern@gmail.com,seconManger@gmail.com
                '''
            }
         
    }
}
}

