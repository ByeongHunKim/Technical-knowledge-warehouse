# eks cluster vpc 스펙보기

{% code fullWidth="false" %}
```bash
# 변수 설정
CLUSTER_NAME="main"
REGION="ap-southeast-1"

# 클러스터 VPC ID 가져오기
VPC_ID=$(aws eks describe-cluster --name $CLUSTER_NAME --query "cluster.resourcesVpcConfig.vpcId" --output text --region $REGION)

# 가져온 VPC ID를 출력 (디버깅용)
echo "VPC ID: $VPC_ID"

# VPC의 CIDR 블록 가져오기
VPC_CIDR=$(aws ec2 describe-vpcs --vpc-ids $VPC_ID --query "Vpcs[0].CidrBlock" --output text --region $REGION)

# 가져온 VPC CIDR 블록을 출력 (디버깅용)
echo "VPC CIDR Block: $VPC_CIDR"

# 서브넷 정보 가져오기 (CIDR 블록 포함)
aws ec2 describe-subnets --filters "Name=vpc-id,Values=$VPC_ID" --query "Subnets[*].{ID:SubnetId,Public:MapPublicIpOnLaunch,CIDR:CidrBlock}" --output json --region $REGION

```
{% endcode %}



