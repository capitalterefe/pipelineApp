def logfile = new File("/tmp/pipeline.txt")
def final_result = []
import java.util.concurrent.TimeUnit
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
                        def props = getParams()
                        echo "properties new: " + props
                        def date_and_time = job.getBuildByNumber(job.getLastBuild().getNumber()).getTime().toString()
                         def  date_and_time_woutTostring = job.getBuildByNumber(job.getLastBuild().getNumber()).getTimeInMillis()
                        echo  "woutTostring " + date_and_time_woutTostring
                         def miltoDate =  new Date(date_and_time_woutTostring).format("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'")
                        echo "milToDate "+ miltoDate
                        def miltoDateMon =  new Date(date_and_time_woutTostring).format("EEE MMM d HH:mm:ss 'EDT' yyyy ")
                        echo "milToDateMon "+ miltoDateMon
                        
                        def fiftenmin= date_and_time_woutTostring + (15*60*1000)
                        echo "fiftenmin_milim" + fiftenmin
                        def miltoDateMon_fifteen =  new Date(fiftenmin).format("EEE MMM d HH:mm:ss 'EDT' yyyy ")
                        echo "milToDatefiftennMin "+ miltoDateMon_fifteen
                        
                        def result = job.getLastBuild().getResult().toString()
                        def duration = job.getLastBuild().getDurationString()
                        def duration_mil = job.getLastBuild().getDuration()
                        echo "duration in miliseconds " + duration_mil 
                        def minutes_val = TimeUnit.MILLISECONDS.toMinutes(duration_mil);
                        echo "duration in Minute format" + minutes_val 
                        echo "|"+ jobName.toUpperCase() + "| STATUS: " + result + "| Latest Job Completed at: "+date_and_time +"| Latest Job Took: "+duration 
                       
                        def final_result_get = "|"+ jobName.toUpperCase() + "| STATUS: " + result + "| Latest Job Completed at: "+date_and_time +"| Latest Job Took: "+duration  + "\n"
                        logfile.append(final_result_get)
                        final_result.add("|"+ jobName.toUpperCase() + "| STATUS: " + result + "| Latest Job Completed at: "+date_and_time +"| Latest Job Took: "+duration   + "\n")
                        
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
def getParams(){

  def params=[]
  def job = jenkins.model.Jenkins.instance.getItemByFullName("tr")
                       
  job.getBuildByNumber(job.getLastBuild().getNumber()).getActions(ParametersAction)
  .each { action ->
    action.getParameters().each {
      params << it
    }
  }

  return params
}
def getParam(){

 def params=[]
  def job = jenkins.model.Jenkins.instance.getItemByFullName("tr")
                       
  job.getBuildByNumber(job.getLastBuild().getNumber()).getActions(ParametersAction)
  .each { action ->
    action.getParameter("stack").each {
      params << it
    }
  }

  return params
}

