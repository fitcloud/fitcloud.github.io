---
layout : post
title : AWS CloudWatch 제대로 사용하기
---

### 1.Memory, Disk 메트릭 추가 (Amazon Linux 기준)

#### Amazon Linux 
 ~~~
 export CWHOME="/app/cloudwatch"
 sudo yum -y install perl-Switch perl-DateTime perl-Sys-Syslog perl-LWP-Protocol-https
 cd ${CWHOME}
 curl http://aws-cloudwatch.s3.amazonaws.com/downloads/CloudWatchMonitoringScripts-1.2.1.zip -O
 unzip CloudWatchMonitoringScripts-1.2.1.zip
 cd aws-scripts-mon
 cp awscreds.template awscreds.conf 
 vi awscreds.conf 
 ~~~
 
#### awscreds.conf 입력 (다음의 IAM 유저는 CloudWatch에 대한 PutMetrics 권한을 갖고 있어야 한다.)
 ~~~
 AWSAccessKeyId=XXXXXXXXXXXXXXXX
 AWSSecretKey=XXXXXXXXXXXXXXXX
 ~~~
 
#### crontab 등록
 ~~~
 crontab -e
 */5 * * * * /home/ec2-user/aws-scripts-mon/mon-put-instance-data.pl --mem-avail --mem-util --swap-util --disk-path=/ --disk-path=/data --disk-space-util  --aws-credential-file=/home/ec2-user/aws-scripts-mon/awscreds.conf --from-cron
 service crond restart
 ~~~
 
#### 대쉬보드 화면
  ![dash](https://user-images.githubusercontent.com/30482872/29114394-2383385e-7d2f-11e7-82f2-54847bfa5a78.JPG)

#### 출처
 http://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/mon-scripts.html

### 2.Log 수집
 
#### IAM 유저 권한 부여
 ~~~
 {
   "Version": "2012-10-17",
   "Statement": [
     {
       "Effect": "Allow",
       "Action": [
         "logs:CreateLogGroup",
         "logs:CreateLogStream",
         "logs:PutLogEvents",
         "logs:DescribeLogStreams"
     ],
       "Resource": [
         "arn:aws:logs:*:*:*"
     ]
   }
  ]
 }
 ~~~
 
#### AWS CloudWatch Log agent 설치 (Amazon Linux)
 ~~~
 yum install -y awslogs
 vi /etc/awslogs/awscli.conf
 ~~~
 
#### awscli.conf 입력 (다음의 IAM 유저는 위의 권한을 가지고 있어야 한다.)
 ~~~
 [plugins]
  cwlogs = cwlogs
 [default]
  region = ap-northeast-2
  aws_access_key_id = XXXXXXXXXXXXXXXXXX
  aws_secret_access_key = XXXXXXXXXXXXXXXXXXXXXX
 ~~~
 
#### awslogs.conf 변경
 ~~~
 [general]
  state_file = /var/lib/awslogs/agent-state
  encoding = utf-8
  
 [/etc/httpd/logs/access.log]
  datetime_format = %b %d %H:%M:%S
  file = /etc/httpd/logs/access.log
  buffer_duration = 5000
  log_stream_name = webserver1
  initial_position = start_of_file
  log_group_name = access-log
 ~~~
 
#### awslogs 서비스 설정 및 시작
 ~~~
 chkconfig --level 345 awslogs on
 service awslogs restart
 ~~~
 
#### 정상적인 화면
  ![access](https://user-images.githubusercontent.com/30482872/29114371-078ba910-7d2f-11e7-91e4-e576538f2bef.JPG)

#### 출처
 http://docs.aws.amazon.com/ko_kr/AmazonCloudWatch/latest/logs/QuickStartEC2Instance.html

### 3.알람 기능을 통한 Slack 알림 (writing...)
#### CloudWatch Alarm & SNS & Lambda 서비스를 조합하여 Slack을 통해서 알람을 받을 수 있다.
 
#### Lambda 코드 생성
 
#### CloudWatch Alarm 생성
 
 




