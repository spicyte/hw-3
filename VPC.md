Публичная сеть:
aws ec2 create-subnet --vpc-id vpc-xxxxxxxx --cidr-block 192.168.0.0/25 --availability-zone us-east-1a --region us-east-1


Приватная сеть: 
aws ec2 create-subnet --vpc-id vpc-xxxxxxxx --cidr-block 192.168.0.128/25 --availability-zone us-east-1a --region us-east-1

Создание интернет шлюза:
aws ec2 create-internet-gateway


Приклепление его к VPC:
aws ec2 attach-internet-gateway --vpc-id vpc-xxxxxxxx --internet-gateway-id igw-xxxxxxxx

Дальше:
aws ec2 create-route-table --vpc-id vpc-xxxxxxxx
aws ec2 create-route --route-table-id rtb-xxxxxxxx --destination-cidr-block 0.0.0.0/0 --gateway-id igw-xxxxxxxx

Привязка:
aws ec2 associate-route-table --route-table-id rtb-xxxxxxxx --subnet-id subnet-xxxxxxxx

Secure Group FRONT:
aws ec2 create-security-group --group-name sg-FRONT --description "Security group for FRONT" --vpc-id vpc-xxxxxxxx
aws ec2 authorize-security-group-ingress --group-id sg-FRONT-id --protocol tcp --port 22 --cidr 0.0.0.0/0

Secure Group BACK:
aws ec2 create-security-group --group-name sg-Back --description "Security group for Back" --vpc-id vpc-xxxxxxxx
aws ec2 authorize-security-group-ingress --group-id sg-Back-id --protocol tcp --port 22 --source-group sg-FRONT-id
aws ec2 authorize-security-group-egress --group-id sg-FRONT-id --protocol -1 --port all --cidr 0.0.0.0/0
aws ec2 authorize-security-group-egress --group-id sg-Back-id --protocol -1 --port all --cidr 0.0.0.0/0


ES2 instanse:
FRONT:
aws ec2 run-instances --image-id ami-xxxxxxxx --instance-type t2.micro --subnet-id subnet-xxxxxxxx --security-group-ids sg-FRONT-id --key-name spicyte
BACK:
aws ec2 run-instances --image-id ami-xxxxxxxx --instance-type t2.micro --subnet-id subnet-xxxxxxxx --security-group-ids sg-Back-id --key-name spicyte

настройка доступа к базе через SSH:

ssh -i spicyte.pem ec2-user@<WEB_PUBLIC_IP>
ssh -i spicyte.pem ec2-user@<DB_PRIVATE_IP>






