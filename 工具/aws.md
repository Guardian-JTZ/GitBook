# AWS

## 1. gobuster

&#x20;&#x20;

## 2. aws-cli

```shell
# 查看版本
aws --version

# AWS 登录
aws configure
AWS Access Key ID [None]: AKIA3GRZL6WIQEXAMPLE
AWS Secret Access Key [None]: k+ci5r+hAcM3x61w1example
Default region name [None]: ap-east-1
Default output format [None]: json
```

### 1. EC2

```shell
#修改实例类型,先stop
aws ec2 stop-instances --instance-ids i-1234567890abcdef0
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --instance-type "{\"Value\": \"m1.medium\"}"
 
##创建安全组
aws ec2 create-security-group --group-name test-sg  --description "test-sg" --vpc-id vpc-abcd1234
##查看安全组
aws ec2 --profile=prod describe-security-groups --group-ids sg-12345678abcdefghi
#添加规则
##添加源安全组
aws ec2 authorize-security-group-ingress --group-id sg-12345678abcdefghi --protocol tcp --port 6379 --source-group sg-12ab34cd
##给DB加IP源
aws ec2 authorize-security-group-ingress --group-id sg-12345678abcdefghi --ip-permissions IpProtocol=tcp,FromPort=3306,ToPort=3306,IpRanges=[{CidrIp=3.210.××.××/32}]
##删除IP源
aws ec2 revoke-security-group-ingress --group-id sg-12345678abcdefghi --ip-permissions IpProtocol=tcp,FromPort=3306,ToPort=3306,IpRanges=[{CidrIp=3.210.××.××/32}]
 
#创建snapshot
##先找volume-id 
aws ec2 describe-instances --filters "Name=tag:Name,Values=webserver"  --query "Reservations[*].Instances[*].[Tags[?Key==`Name`].Value,BlockDeviceMappings[*].Ebs.VolumeId]" 
vol-abcd1234
##再创建snapshot 
aws ec2 create-snapshot --volume-id vol-abcd1234 --tag-specifications "ResourceType=snapshot,Tags=[{Key=Name,Value=webserver}]" 
```

### 2. ELB

```shell
##ELB描述
aws elb describe-load-balancers --load-balancer-name WebELB
 
##查看参数配置
aws elb describe-load-balancer-attributes --load-balancer-name WebELB
 
##healthcheck:
aws elb describe-instance-health --load-balancer-name WebELB
 
##修改healthcheck参数
aws elb configure-health-check --load-balancer-name WebELB --health-check Target=HTTP:80/healthcheck,Interval=30,UnhealthyThreshold=5,HealthyThreshold=5,Timeout=8
 
##从ALB目标群组移除实例
aws elbv2 deregister-targets --target-group-arn arn:aws:elasticloadbalancing:us-east-1:123456789101:targetgroup/TestALB/abcdef12345abcde --targets Id=i-abcd1234
##注册实例到ALB目标群组
aws elbv2 register-targets --target-group-arn arn:aws:elasticloadbalancing:us-east-1:123456789101:targetgroup/TestALB/abcdef12345abcde --targets Id=i-abcd1234
```

### 3. S3

```shell
#ls
##查看默认的bucket
aws s3 ls
##查看默认环境的abert-test内容
aws s3 ls s3://abert-test
##查看目录大小,列出每个文件大小
aws s3 ls --summarize --human-readable --recursive s3://bucket-name 
 
#cp
##上传本地文件
aws s3 cp bstest.txt s3://abert-test 
##复制文件
aws s3 cp s3://mybucket/test.txt s3://mybucket/test2.txt 
##递归拷贝
aws s3 cp s3://mybucket . --recursive 
##排除拷贝
aws s3 cp myDir s3://mybucket/ --recursive --exclude "*.jpg"  
##拷贝并添加ACL权限控制
aws s3 cp s3://mybucket/test.txt s3://mybucket/test2.txt --acl public-read-write
 
#rm 
##删除对象
aws s3 rm  s3://mybucket/test.txt
 
#mb 
##创建bucket
aws s3 mb s3://newbucket
 
#rb
##删除bucket：
aws s3 rb s3://bucket-name
##删除非空：
aws s3 rb s3://bucket-name --force
```

### 4. DynamoDB

```shell
##创建DynamoDB表
aws dynamodb create-table --table-name bettytest --attribute-definitions AttributeName=pk,AttributeType=N AttributeName=sk,AttributeType=N --key-schema AttributeName=pk,KeyType=HASH AttributeName=sk,KeyType=RANGE --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1
 
##修改DynamoDB表读写容量
aws dynamodb update-table --table-name bettytest --provisioned-throughput ReadCapacityUnits=10,WriteCapacityUnits=10
 
##删除DynamoDB表
aws dynamodb delete-table --table-name betty-test
 
##加stream
aws dynamodb update-table --table-name betty-test --stream-specification StreamEnabled=true,StreamViewType="NEW_AND_OLD_IMAGES"
 
##关闭stream
aws dynamodb update-table --table-name betty-test --stream-specification StreamEnabled=false
 
##查看DynamoDB表
aws dynamodb describe-table --table-name betty-test
```

### 5. Elasticache

```shell
#Memcache
 
##创建
aws elasticache create-cache-cluster --cache-cluster-id bettytest --az-mode cross-az --preferred-availability-zones us-east-1a 	us-east-1d --num-cache-nodes 2 --cache-node-type cache.m4.large --engine memcached --engine-version "1.4.24" --cache-parameter-group-name bettycacheparameter --cache-subnet-group-name test-cache-subnet-group --security-group-ids "sg-abcd1234" "sg-1234abcd" --port 11211 --notification-topic-arn arn:aws:sns:us-east-1:123456780123:test-Alarm --preferred-maintenance-window sun:08:00-sun:09:00 --auto-minor-version-upgrade
 
##查看memcache集群信息
aws elasticache describe-cache-clusters --cache-cluster-id bettytest
 
##新增节点
aws elasticache modify-cache-cluster --cache-cluster-id bettytest --num-cache-nodes 3 --new-availability-zones ap-southeast-1a --apply-immediately
##删除节点
aws elasticache modify-cache-cluster --cache-cluster-id bettytest --num-cache-nodes 1 --cache-node-ids-to-remove 0002 --apply-immediately
##删除两个节点
aws elasticache modify-cache-cluster --cache-cluster-id bettytest --num-cache-nodes 1 --cache-node-ids-to-remove 0002 0003 --apply-immediately
 
##删除cache
aws elasticache delete-cache-cluster --cache-cluster-id bettytest
 
【##修改type --不支持
aws elasticache modify-cache-cluster --cache-cluster-id bettytest --cache-node-type cache.m4.large --apply-immediately
An error occurred (InvalidParameterCombination) when calling the ModifyCacheCluster operation: Scaling is not supported for engine memcached】
```

### 6. Redis

```shell
#Redis
 
##创建redis集群
aws elasticache create-replication-group --replication-group-id betty-redis --replication-group-description "betty-redis-test" --automatic-failover-enabled --num-cache-clusters 2 --cache-node-type cache.m3.medium --engine redis --engine-version 5.0.0 --cache-parameter-group-name default.redis5.0 --cache-subnet-group-name betty-cache-subnet-group --security-group-ids sg-0123abcd4567efghi
 
##查看redis集群
aws elasticache describe-replication-groups --replication-group-id betty-redis
 
##修改备份保留期为1天
aws elasticache modify-replication-group --replication-group-id betty-redis --snapshotting-cluster-id betty-redis-002 --snapshot-retention-limit 1
```

### 7. Kinesis

```shell
##放入数据
aws kinesis put-record --stream-name Betty_Stream --partition-key 123 --data testdata
##读取数据
aws kinesis get-shard-iterator --shard-id shardId-000000000000 --shard-iterator-type TRIM_HORIZON --stream-name Betty_Stream
```

### 8. SQS

```shell
##读取消息
aws sqs receive-message --queue-url https://sqs.us-east-1.amazonaws.com/123456780123/MY_SQS_TEST --attribute-names All --message-attribute-names All --max-number-of-messages 1
 
##删除消息
aws sqs delete-message --queue-url https://sqs.us-east-1.amazonaws.com/123456780123/MY_SQS_TEST --receipt-handle
```

### 9. SNS

```shell
##查看所有的IOS push
aws sns list-platform-applications 
```

### 10. SNS

```shell
##查看所有的IOS push
aws sns list-platform-applications 
```

### 11. SES

```shell
##查看已验证域名
aws ses list-identities
```

### 12. IAM

```shell
##为Application Auto Scaling创建服务角色
aws iam create-role --role-name MyIAMAutoscalingServiceRole --assume-role-policy-document file://trust-relationship.json
 
##为服务角色创建 IAM 策略
aws iam create-policy --policy-name MyIAMAutoscalingServicePolicy --policy-document file://service-role-policy.json
arn:aws:iam::123456780123:policy/MyIAMAutoscalingServicePolicy
 
##将策略附加到服务角色
aws iam attach-role-policy --role-name MyIAMAutoscalingServiceRole --policy-arn arn:aws:iam::123456780123:policy/MyIAMAutoscalingServicePolicy
```
