# ecs-logging

we have to send logs from ecs fargate to elk and we do not have proper doc for that so here is a doc

we need to run logstash over http 

input {
  http {
    port      => 24224
    type => "ecs_fluent_bit_logs"
  }
}
output {
 if [type] == "ecs_fluent_bit_logs" {
   elasticsearch {
     hosts => ["https://###########################"]
     user => "###########"
     password => "############"
     ssl => true
     ssl_certificate_verification => false
     index => "##########-ecs-%{+YYYY.MM.dd}"
   }
 }
 
 then we have to change in task defination
 
 {
    "taskDefinition": { 
        "containerDefinitions": [
            {
                "memoryReservation": 700, 
                "environment": [], 
                "name": "#################", 
                "mountPoints": [], 
                "image": "##########", 
                "logConfiguration": {
                    "logDriver": "awsfirelens", 
                    "options": {
                        "Host": "172.31.8.54", 
                        "Format": "json", 
                        "Port": "24224", 
                        "Name": "http"
                    }
                }, 
                "essential": true, 
                "portMappings": [
                    {
                        "protocol": "tcp", 
                        "containerPort": 4000, 
                        "hostPort": 4000
                    }
                ], 
                "startTimeout": 5, 
                "stopTimeout": 5, 
                "entryPoint": [], 
                "memory": 700, 
                "command": [], 
                "cpu": 1, 
                "volumesFrom": []
            }, 
            {
                "memoryReservation": 50, 
                "environment": [], 
                "name": "housing_log_router", 
                "mountPoints": [], 
                "image": "#####################:fluentd_1", 
                "firelensConfiguration": {
                    "type": "fluentbit"
                }, 
                "essential": true, 
                "portMappings": [], 
                "user": "0", 
                "memory": 128, 
                "cpu": 120, 
                "volumesFrom": []
            }
        ], 
        "revision": 22
    }
}
