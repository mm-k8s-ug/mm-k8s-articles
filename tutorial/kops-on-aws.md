---
description: Kops on AWS
---

# How to complete setup Kubernetes Cluster on AWS using Kops

Thinking Basic requirement 

* Installing Cluster \( IAC \)
* Authentication \( IAM \)
* Authorization \( K8s resources\)
* Ingress Controller \( ALB \)
* External DNS 

#### Prerequisites before setup cluster

* [Kops](https://github.com/kubernetes/kops)
* [aws cli](https://aws.amazon.com/cli/)
* [aws-iam-authenticator](https://github.com/kubernetes-sigs/aws-iam-authenticator)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

#### What is Kops ? 

**Kops** သည် K for kubernetes၊ ops for Operations ၂ခု ပေါင်းသည့်အခါ Kubernetes Operations ဟုတ် အဓိပ္ပါယ်ရသည်။ အမျိုးမျိုးသော cloud platforms များပေါ်တွင် kubernetes cluster ကို version control system ဖြင့် install ပြုလုပ်ခြင်း၊ manage ပြုလုပ်ခြင်း၊ စသည့် operation များကို ကူညီ အထောက်အပံ ဖြစ်ရန် ရည်ရွယ် ထုတ်လုပ်ထားသည့် CLI tool တစ်ခုလည်း ဖြစ်ပါသည်။ Kops သည် kubernetes project အတူဖြစ်ပေါ်လာပြီး K8s team မှ ထိန်းသိမ်းသည့် tools တစ်ခု ဖြစ်သည်။ 

#### What is AWS CLI ? 

**AWS CLI** သည် Developer များ၊ Ops များ အတွက် AWS cloud platform ၏ resources များကို command line မှ တဆင့် အလွယ်တကူ အသုံးပြုနိုင်ရန် အထူးပြုလုပ်ထားသော Amazon Web Services မှ tools တစ်ခုဖြစ်သည်။

#### What is aws-iam-authenticator ?

Kubernetes Cluster ကို AWS ၏ **IAM** ကို အသုံးပြု၍ authentication ပြုလုပ်နိုင်ရန် အတွက် ထုတ်လုပ်ထားသော tools တစ်ခုဖြစ်သည်။ aws-iam-authenticator သည် kubernetes special interest groups \( SIG \) ၏ project တစ်ခု ဖြစ်ပြီး ယခု လက်ရှိတွင် Heptio and Amazon EKS OSS မှ ထိန်းသိမ်ထားပါသည်။  

#### What is kubectl ? 

Kubectl သည် kubernetes cluster အတွက် client command line interface tool တစ်ခု ဖြစ်သည်။ kubectl သည် kubernetes cluster ကို ဆက်သွယ် ချိတ်ဆက် ရန် $HOME/.kube directory အောက် တွင် ရှိသောက် config file များကို ဖတ်သည်။ environment variable _**KUBECONFIG**_ ကိုလဲ config file အစား အသုံး ပြုနိုင်သည်။



### Let's SETUP

Kops ကို ဒီ [link](https://github.com/kubernetes/kops/releases) တွင် download ပြုလုပ်နိုင်ပါသည်။

{% code title="Terminal" %}
```text
$ wget https://github.com/kubernetes/kops/releases/download/1.14.0-alpha.2/kops-linux-amd64
$ chmod +x kops-linux-amd64
$ sudo mv kops-linux-amd64 /usr/local/bin/kops
```
{% endcode %}

Kops ကို အသုံးပြုရန် AWS ၏ IAM user တစ်ယောက် လိုအပ်ပါသည်။ ထို IAM user တွင် Kops မှ AWS ၏ resource များကို အသုံးပြုရန် အနည်းဆုံး အောက်ပါ permission များ လိုအပ်ပါသည်။ 

* `AmazonEC2FullAccess`
* `AmazonRoute53FullAccess`
* `AmazonS3FullAccess`
* `IAMFullAccess`
* `AmazonVPCFullAccess`

လိုအပ်သော Permission နှင့် User  တစ်ယောက်ဆောက်ပြီးသည့်အခါတွင် အောက်ဖော်ပြပါ Command နဲ့ AWS CLI ကို Setup လုပ်ပေးရမည်။ 

{% code title="Terminal" %}
```bash
$ aws configure
```
{% endcode %}

AWS CLI  ကို setup လုပ်ပြီသောအခါ AWS ၏ S3 bucket တစ်ကို  kops state file များ သိမ်းရန် အောက်ပါ command များကို အသုံးပြု၍ တည်ဆောက်နိုင်ပါသည်။

{% code title="Terminal" %}
```bash
$ export REGION=<your desire region>
$ export NAME=<your desire cluster name>
$ export KOPS_STATE_STORE=s3://${NAME} #your s3 bucket name
$ aws s3 mb $KOPS_STATE_STORE # create s3 bucket with your cluster name
$ aws s3api put-bucket-versioning --bucket ${NAME}  --versioning-configuration Status=Enabled
$ aws s3api put-bucket-encryption --bucket ${NAME} --server-side-encryption-configuration '{"Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"AES256"}}]}'
```
{% endcode %}

AWS S3 တည်ဆောက်ပြီသောအခါ မှာကျွန်တော်တို့ လိုချင်သည့် ပုံစံဖြင့် kubernetes cluster ကို တည်ဆောက်ရန် အဆင်သင့် ဖြစ်နေပြီပဲ ဖြစ်ပါသည်။ Kops ကို အသုံးပြု၍ private နဲ့ public topology ၂ မျိုးဖြင့် cluster ကို တည်ဆောက် နိုင်ပါသည်။ 

ဒီ scenario မှာတော့ private topology ကျွန်တော် အသုံးပြုသွားမှာ ဖြစ်သည်။ t3.medium instance type နှင့် weave network plugin ကို အသုံးပြုပြီး masters ၃ လုံး၊ nodes ၃လုံးကို အသုံးပြု၍တည်ဆောက်သွားမည်ဖြစ်ပါသည်။

Kops supports the following topologies on AWS 

| Topology | Value | Description |
| :---: | :---: | :---: |
| Public Cluster | public | All masters/nodes will be launched in a **public subnet** in the VPC |
| Private Cluster | private | All masters/nodes will be launched in a **private subnet** in the VPC |

ထို.နောက် အောက်ဖော်ပြပါ Command နဲ့ လိုအပ်သော setup များဖြင့် cluster ကိုတည်ဆောက်ပြီး၊ထို cluster ရဲ့ config များကို ပြန်အသုံးပြုနိုင်ရန် **mycluster.yml** ဖိုင် ထဲကို standard output ဖြင့်ထည့်လိုက်ပါမယ်။ 

{% code title="Terminal" %}
```bash
$ kops create cluster ${NAME} --state=${KOPS_STATE_STORE} --master-count 3 --master-size t3.medium --node-count 3 --node-size t3.medium --zones us-east-1a,us-east-1b,us-east-1c --topology private --networking weave --ssh-public-key ~/.ssh/id_rsa.pub  -o yaml >> mycluster.yaml
```
{% endcode %}

ထို.နောက် **mycluster.yml** ဖိုင်ကို edit လုပ်ပီး additional policies section တွင် AWS IAM Policy မှ ELB and Route 53 policy များကိုပြင်ဆင်ပြီးနောက် Cluster Authentication အတွက်   
Line number  ၁၀ နှင့် ၁၁ တွင်ရှိသော အောက်ဖော်ပြပါ **authentication:aws:{}**  ကိုထည့်ပေးရန်လိုအပ်ပါသည်။

{% hint style="warning" %}
AWS IAM နှင့် Authentication ကို Integrate မလုပ်ချင်ပါက ယခု Config ကို ပြင်ဆင်စရာမလိုအပ်ပါ။ပြီးတော့ aws-iam-authenticator ကို  ထည့်သွင်းပေးရန်မလိုပါ။
{% endhint %}

{% code title="mycluster.yaml" %}
```yaml
apiVersion: kops/v1alpha2
kind: Cluster
metadata:
  creationTimestamp: null
  name: <your cluster name> # no need to touch
spec:
  api:
    loadBalancer:
      type: Public
  authentication:      #need to add this [authenication]
    aws: {}            #need to add this [aws: {}]
  authorization:
    rbac: {}
  channel: stable
  cloudProvider: aws
  configBase: s3://<your s3 name>/<your cluster name> #no need to touch
  etcdClusters:
  - cpuRequest: 200m
    etcdMembers:
    - instanceGroup: master-ap-southeast-1a
      name: a
    - instanceGroup: master-ap-southeast-1b
      name: b
    - instanceGroup: master-ap-southeast-1c
      name: c
    memoryRequest: 100Mi
    name: main
  - cpuRequest: 100m
    etcdMembers:
    - instanceGroup: master-ap-southeast-1a
      name: a
    - instanceGroup: master-ap-southeast-1b
      name: b
    - instanceGroup: master-ap-southeast-1c
      name: c
    memoryRequest: 100Mi
    name: events
  iam:
    allowContainerRegistry: true
    legacy: false
  kubelet:
    anonymousAuth: false
  kubernetesApiAccess:
  - 0.0.0.0/0
  kubernetesVersion: 1.14.1
  masterPublicName: api.yourcluster.com  #no need to touch
  networkCIDR: 172.20.0.0/16
  networking:
    weave:
      mtu: 8912
  nonMasqueradeCIDR: 100.64.0.0/10
  additionalPolicies:     #add this additional policy from this line to until line 160
    node: |
      [
        {
          "Effect": "Allow",
          "Action": [
            "acm:DescribeCertificate",
            "acm:ListCertificates",
            "acm:GetCertificate"
          ],
          "Resource": "*"
        },
        {
          "Effect": "Allow",
          "Action": [
            "ec2:AuthorizeSecurityGroupIngress",
            "ec2:CreateSecurityGroup",
            "ec2:CreateTags",
            "ec2:DeleteTags",
            "ec2:DeleteSecurityGroup",
            "ec2:DescribeInstances",
            "ec2:DescribeInstanceStatus",
            "ec2:DescribeSecurityGroups",
            "ec2:DescribeSubnets",
            "ec2:DescribeTags",
            "ec2:DescribeVpcs",
            "ec2:ModifyInstanceAttribute",
            "ec2:ModifyNetworkInterfaceAttribute",
            "ec2:RevokeSecurityGroupIngress"
          ],
          "Resource": "*"
        },
        {
          "Effect": "Allow",
          "Action": [
            "elasticloadbalancing:AddTags",
            "elasticloadbalancing:CreateListener",
            "elasticloadbalancing:CreateLoadBalancer",
            "elasticloadbalancing:CreateRule",
            "elasticloadbalancing:CreateTargetGroup",
            "elasticloadbalancing:DeleteListener",
            "elasticloadbalancing:DeleteLoadBalancer",
            "elasticloadbalancing:DeleteRule",
            "elasticloadbalancing:DeleteTargetGroup",
            "elasticloadbalancing:DeregisterTargets",
            "elasticloadbalancing:DescribeListeners",
            "elasticloadbalancing:DescribeLoadBalancers",
            "elasticloadbalancing:DescribeLoadBalancerAttributes",
            "elasticloadbalancing:DescribeRules",
            "elasticloadbalancing:DescribeSSLPolicies",
            "elasticloadbalancing:DescribeTags",
            "elasticloadbalancing:DescribeTargetGroups",
            "elasticloadbalancing:DescribeTargetGroupAttributes",
            "elasticloadbalancing:DescribeTargetHealth",
            "elasticloadbalancing:ModifyListener",
            "elasticloadbalancing:ModifyLoadBalancerAttributes",
            "elasticloadbalancing:ModifyRule",
            "elasticloadbalancing:ModifyTargetGroup",
            "elasticloadbalancing:ModifyTargetGroupAttributes",
            "elasticloadbalancing:RegisterTargets",
            "elasticloadbalancing:RemoveTags",
            "elasticloadbalancing:SetIpAddressType",
            "elasticloadbalancing:SetSecurityGroups",
            "elasticloadbalancing:SetSubnets",
            "elasticloadbalancing:SetWebACL"
          ],
          "Resource": "*"
        },
        {
          "Effect": "Allow",
          "Action": [
            "iam:GetServerCertificate",
            "iam:ListServerCertificates"
          ],
          "Resource": "*"
        },
        {
          "Effect": "Allow",
          "Action": [
            "waf-regional:GetWebACLForResource",
            "waf-regional:GetWebACL",
            "waf-regional:AssociateWebACL",
            "waf-regional:DisassociateWebACL"
          ],
          "Resource": "*"
        },
        {
          "Effect": "Allow",
          "Action": [
            "tag:GetResources",
            "tag:TagResources"
          ],
          "Resource": "*"
        },
        {
          "Effect": "Allow",
          "Action": [
            "waf:GetWebACL"
          ],
          "Resource": "*"
        },
        {
          "Effect": "Allow",
          "Action": [
            "route53:*"
          ],
          "Resource": "*"
        }
      ]
  sshAccess:
  - 0.0.0.0/0
  subnets:
  - cidr: 172.20.32.0/19
    name: ap-southeast-1a
    type: Private
    zone: ap-southeast-1a
  - cidr: 172.20.64.0/19
    name: ap-southeast-1b
    type: Private
    zone: ap-southeast-1b
  - cidr: 172.20.96.0/19
    name: ap-southeast-1c
    type: Private
    zone: ap-southeast-1c
  - cidr: 172.20.0.0/22
    name: utility-ap-southeast-1a
    type: Utility
    zone: ap-southeast-1a
  - cidr: 172.20.4.0/22
    name: utility-ap-southeast-1b
    type: Utility
    zone: ap-southeast-1b
  - cidr: 172.20.8.0/22
    name: utility-ap-southeast-1c
    type: Utility
    zone: ap-southeast-1c
  topology:
    dns:
      type: Public
    masters: private
    nodes: private

---

apiVersion: kops/v1alpha2
kind: InstanceGroup
metadata:
  creationTimestamp: null
  labels:
    kops.k8s.io/cluster: hearu.org
  name: master-ap-southeast-1a
spec:
  image: kope.io/k8s-1.12-debian-stretch-amd64-hvm-ebs-2019-05-13
  machineType: t3.medium
  maxSize: 1
  minSize: 1
  nodeLabels:
    kops.k8s.io/instancegroup: master-ap-southeast-1a
  role: Master
  rootVolumeSize: 60
  rootVolumeType: gp2
  subnets:
  - ap-southeast-1a

---

apiVersion: kops/v1alpha2
kind: InstanceGroup
metadata:
  creationTimestamp: null
  labels:
    kops.k8s.io/cluster: yourcluster.com # no need to touch
  name: master-ap-southeast-1b
spec:
  image: kope.io/k8s-1.12-debian-stretch-amd64-hvm-ebs-2019-05-13
  machineType: t3.medium
  maxSize: 1
  minSize: 1
  nodeLabels:
    kops.k8s.io/instancegroup: master-ap-southeast-1b
  role: Master
  rootVolumeSize: 60
  rootVolumeType: gp2
  subnets:
  - ap-southeast-1b

---

apiVersion: kops/v1alpha2
kind: InstanceGroup
metadata:
  creationTimestamp: null
  labels:
    kops.k8s.io/cluster: yourcluster.com #no need to touch
  name: master-ap-southeast-1c
spec:
  image: kope.io/k8s-1.12-debian-stretch-amd64-hvm-ebs-2019-05-13
  machineType: t3.medium
  maxSize: 1
  minSize: 1
  nodeLabels:
    kops.k8s.io/instancegroup: master-ap-southeast-1c
  role: Master
  rootVolumeSize: 60
  rootVolumeType: gp2
  subnets:
  - ap-southeast-1c

---

apiVersion: kops/v1alpha2
kind: InstanceGroup
metadata:
  creationTimestamp: null
  labels:
    kops.k8s.io/cluster: yourcluster.com #no need to touch
  name: nodes
spec:
  image: kope.io/k8s-1.12-debian-stretch-amd64-hvm-ebs-2019-05-13
  machineType: t3.medium
  maxSize: 3
  minSize: 3
  nodeLabels:
    kops.k8s.io/instancegroup: nodes
  role: Node
  rootVolumeSize: 60
  rootVolumeType: gp2
  subnets:
  - ap-southeast-1a
  - ap-southeast-1b
  - ap-southeast-1c
```
{% endcode %}

အားလုံးပြင်ဆင်ပြီးနောက် cluster ကို အောက်ပါ command များဖြင့် bootstrap လုပ်နိုင်ပါပြီ။  
Bootstrap လုပ်နေချိန်တွင် မိနစ်အနည်းငယ် စောင့်ရန်လိုအပ်ပါသည်။

{% code title="Terminal" %}
```bash
$ kops create -f mycluster.yaml 
$ kops create secret --name ${NAME} sshpublickey admin -i ~/.ssh/id_rsa.pub
$ kops update cluster ${NAME} --yes
$ kops kops validate cluster # validate cluster is check for your cluster state
```
{% endcode %}

Cluster တည်ဆောက်ပြီးဖြစ်သော်လည်း Status ကတော့  valid ဖြစ်သေးမှာမဟုတ်ပါဘူး။ ဘာလို့လဲဆိုတော့ authentication plugin ၏ configuration လိုနေသောကြောင့်ဖြစ်ပါသည်။

{% code title="json" %}
```javascript

//K8sAdmin
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "sts:DecodeAuthorizationMessage",
                "sts:GetCallerIdentity"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "sts:*",
            "Resource": "arn:aws:iam::<account id>:role/K8sAdmin"
        }
    ]
}
```
{% endcode %}

{% code title="json" %}
```javascript
//K8sDeveloper
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::<account id:role/K8sDeveloper"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "sts:GetCallerIdentity",
            "Resource": "*"
        }
    ]
}

```
{% endcode %}

{% hint style="warning" %}
အောက်တွင်ဖော်ပြထားသော commands ဖြင့် K8sDeveloper နှင့် K8sAdmin အစရှိသော role နှစ်ခုကို configmap အတွက်  တည်ဆောက်သည့်အခါတွင် အပေါ် မှာ json များထွက်လာမည်ဖြစ်ပါသည်။ 

Example ပြထားခြင်းဖြစ်ပါသည်။   
{% endhint %}

{% code title="Terminal" %}
```bash
$ export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query 'Account')
$ export DEVPOLICY=$(echo -n '{"Version":"2012-10-17","Statement":[{"Sid":"VisualEditor0","Effect":"Allow","Action":["sts:DecodeAuthorizationMessage","sts:GetCallerIdentity"],"Resource":"*"},{"Sid":"VisualEditor1","Effect":"Allow","Action":"sts:*","Resource":"arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/K8sDeveloper"}]}')
$ aws iam create-role \
  --role-name K8sDeveloper \
  --description "Kubernetes developer role (for AWS IAM Authenticator for AWS)." \
  --assume-role-policy-document "$DEVPOLICY" \
  --output text \
  --query 'Role.Arn'
$ export ADMPOLICY=$(echo -n '{"Version":"2012-10-17","Statement":[{"Sid":"VisualEditor0","Effect":"Allow","Action":["sts:DecodeAuthorizationMessage","sts:GetCallerIdentity"],"Resource":"*"},{"Sid":"VisualEditor1","Effect":"Allow","Action":"sts:*","Resource":"arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/K8sAdmin"}]}')
$ aws iam create-role \
  --role-name K8sAdmin \
  --description "Kubernetes administrator role (for AWS IAM Authenticator for AWS)." \
  --assume-role-policy-document "$ADMPOLICY" \
  --output text \
  --query 'Role.Arn'
```
{% endcode %}

 ကျွန်တော်တို. IAM role နှင့် policies များကိုတည်ဆောက်ပြီးနောက် Kubernets resources များတွက် authorization ကို configure ချပေးရန်လိုအပ်ပါသည်။ 

ထို.နောက် RBAC authorization ကို အသုံးပြုပြီး developer role အတွက် production နှင့် development namespace နှစ်ခုကို configure ချပေးထားပါသည်။ 

{% hint style="info" %}
K8sDeveloper role အတွင်းရှိသော user များသည် ထို namespace နှစ်ခုနှင့် resource အချို.သာလျင် အသုံးပြုနိုင်မည်ဖြစ်သည်။ 
{% endhint %}

{% code title="developer-rbac.yaml" %}
```yaml
# RBAC Policy for developer
# create 2 Roles on for developer and binding roles
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  namespace: development
  name: developer
rules:
- apiGroups: ["*"]
  resources: ["deployments","configmaps","pods","secrets","services","endpoints","persistentvolumeclaims","events","namespaces","statefulsets","replicasets","jobs","cronjobs"]
  verbs: ["create","get","delete","list","update","edit","watch","exec","patch"]
- apiGroups:
  - '*'
  resources:
  - 'pods/exec'
  - 'pods/portforward'
  - 'services/portforward'
  verbs:
  - create
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer
  namespace: development
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: Group
  namespace: development
  name: k8s.developer
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  namespace: production
  name: developer
rules:
- apiGroups: ["*"]
  resources: ["deployments","configmaps","pods","secrets","services","endpoints","persistentvolumeclaims","events","namespaces","statefulsets","replicasets","jobs","cronjobs"]
  verbs: ["create","get","delete","list","update","edit","watch","exec","patch"]
- apiGroups:
  - '*'
  resources:
  - 'pods/exec'
  - 'pods/portforward'
  - 'services/portforward'
  verbs:
  - create
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer
  namespace: production
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: Group
  namespace: production
  name: k8s.developer
```
{% endcode %}

{% code title="Terminal" %}
```bash
$ kubectl create namespace development
$ kubectl create namespace production
$ kubectl create -f developer-rbac.yaml
```
{% endcode %}

aws-iam-authenticator အလုပ်လုပ်ရန် နင့် သတ်မှတ်ထားသည့် user group policy  များ အသက်ဝင်ရန် အောက်ပါ configmap ကို create ပြုလုပ်ပေးရန်လိုအပ်ပါသည်။ 

{% code title="aws-auth.yaml" %}
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: kube-system
  name: aws-iam-authenticator
  labels:
    k8s-app: aws-iam-authenticator
data:
  config.yaml: |    
    clusterID: api.mycluster.com 
    server:
      mapRoles:
      - roleARN: arn:aws:iam::<account id>:role/K8sAdmin
        username: kubernetes-admin
        groups:
        - system:masters
      - roleARN: arn:aws:iam::<account id>:role/K8sDeveloper
        username: kubernetes-developer
        groups:
        - k8s.developer
```
{% endcode %}

{% code title="Terminal" %}
```bash
$ kubectl create -f aws-auth.yaml
```
{% endcode %}

ယခုအခြေအနေတွင် Kubernetes cluster နှင့် authentication/authorization setup ပြုလုပ်ခြင်းမှာ ပြီးမြောက်လုနီးပါးဖြစ်ပါသည်။ Kubernetes client \(kubectl\) မှ cluster ကို access လုပ်ရန် configure ချဖို. လိုအပ်ပါသည်။  
အောက်ဖော်ပြပါ config နှစ်ခုသည် Developer နှင့် Administrator အတွက် ဖြစ်ပါသည်။   
ကျွန်တော် ကတော့ Admin config အသုံးပြုပြီး AWS Application LoadBalancer ကိုဆက်လက် setup လုပ်သွားမှာ ဖြစ်ပါသည်။ 

{% code title="~/kube/.config \(Admin\)" %}
```yaml
# this is for Kubernetes Administrator
# you must change this the value of 'certificate-authority-data' with your ca.
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwekNDQWJ1Z0F3SUJBZ0lNRmFlcHBua0lEUnRwNkpub01BMEdDU3FHU0liM0RRRUJDd1VBTUJVeEV6QVIKQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13SGhjTk1Ua3dOakV4TURVd05USTRXaGNOTWprd05qRXdNRFV3TlRJNApXakFWTVJNd0VRWURWUVFERXdwcmRXSmxjbTVsZEdWek1JSUJJakFOQmdrcWhraUc5dzBCQVFFRkFBT0NBUThBCk1JSUJDZ0tDQVFFQXpmMzlOOUIvaGpUQnViM0tETEhzM1NJUmhVL2pVcUt2RWhTWnpCQngwR0tXNmtONmRUVkkKK3dwUnRoeU9Yd2ZWRHA4RmxRYUFYbWt0UGtGTG9jU2VSelBUWXRod1VBamhSbVNoZFJtcW1yaXBZTlhFdEZYMAp6SkZFS2NRNDJ0TEhYanZZbVc3SGxYc0FDSDRCKy82SEtwVmxGSVdNTDAwb2tTUmEzZVBvdTdWYzlqTkZhd3R0CmFoMThrNStLcHVoL0FyUEs2dmdhcEhLclB2dUNaZG9FWkNtaE4rSVdZTjN6Uk1Wb1A1TEVoTEx5RFFzUzYvN0sKQ3prQ09Pam1QWW01UmxnOVRvb25ZcStwbS9LMXc4V1NLVnZick9VZXF0dXkxUlJYWUxtc0VNVmx0NDRQSS9YTgpEeUlubmtpVmRwc1A4ejAwRjZ5MGEwYXFpT3RGUVVyd3NRSURBUUFCb3lNd0lUQU9CZ05WSFE4QkFmOEVCQU1DCkFRWXdEd1lEVlIwVEFRSC9CQVV3QXdFQi96QU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFqUm5ndnJ6SmRMWm8KRFdlbVFaMi83N0xLTmkzTmpRQU9NZUg3WXVpZnlNbTZNdTB2V3F6UU54SDJ1TGhFd0JRN0VocEpYSnFoMmp0dQpldnR6YUF6N2o5MEVKdlN6WHRtSDRQaHd1NFZFMHJWRGVlbUhrMXRZRjhsQ3hVM2xtZFRGZjJ4M29qVnVKNndpCkd3c3RaU0s5V2J5VW53RjNLUXdkMndmd3VUVVpSRGtuSkI2ZGhOcUxNakN3V1M1ekpDUjcvYXFuM0tqb0xvWEgKWkI1VW1UcGd2Sm9rWm0wcUF0ODRNS0hFb2ZCYXJ0M1BUcnplWGI0N1VHRlMrM0h1anVQUTBreW1DT0crK1ZEMwo0K0M5eVlXaTRhYnd6SE9rTmQvMlFKZVoyQjhjbjhlVU5kUGFSSnkweS9keVpSd0NZdXFKSkxZeTVGUTFiQlUwCm96bThnMG01ZWc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    server: https://api.yourcluster.com
  name: yourcluster.com
contexts:
- context:
    cluster: yourcluster.com
    user: kubernetes-admin
  name: aws
current-context: aws
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1alpha1
      args:
      - token
      - -i
      - api.yourcluster.com
      - -r
      - arn:aws:iam::<account id>:role/K8sAdmin
      command: aws-iam-authenticator
      env: null
```
{% endcode %}

{% code title="~/kube/.config \(Developer\)" %}
```yaml
# this is for Kubernetes Developer
# you must change this the value of 'certificate-authority-data' with your ca.
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwekNDQWJ1Z0F3SUJBZ0lNRmFlcHBua0lEUnRwNkpub01BMEdDU3FHU0liM0RRRUJDd1VBTUJVeEV6QVIKQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13SGhjTk1Ua3dOakV4TURVd05USTRXaGNOTWprd05qRXdNRFV3TlRJNApXakFWTVJNd0VRWURWUVFERXdwcmRXSmxjbTVsZEdWek1JSUJJakFOQmdrcWhraUc5dzBCQVFFRkFBT0NBUThBCk1JSUJDZ0tDQVFFQXpmMzlOOUIvaGpUQnViM0tETEhzM1NJUmhVL2pVcUt2RWhTWnpCQngwR0tXNmtONmRUVkkKK3dwUnRoeU9Yd2ZWRHA4RmxRYUFYbWt0UGtGTG9jU2VSelBUWXRod1VBamhSbVNoZFJtcW1yaXBZTlhFdEZYMAp6SkZFS2NRNDJ0TEhYanZZbVc3SGxYc0FDSDRCKy82SEtwVmxGSVdNTDAwb2tTUmEzZVBvdTdWYzlqTkZhd3R0CmFoMThrNStLcHVoL0FyUEs2dmdhcEhLclB2dUNaZG9FWkNtaE4rSVdZTjN6Uk1Wb1A1TEVoTEx5RFFzUzYvN0sKQ3prQ09Pam1QWW01UmxnOVRvb25ZcStwbS9LMXc4V1NLVnZick9VZXF0dXkxUlJYWUxtc0VNVmx0NDRQSS9YTgpEeUlubmtpVmRwc1A4ejAwRjZ5MGEwYXFpT3RGUVVyd3NRSURBUUFCb3lNd0lUQU9CZ05WSFE4QkFmOEVCQU1DCkFRWXdEd1lEVlIwVEFRSC9CQVV3QXdFQi96QU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFqUm5ndnJ6SmRMWm8KRFdlbVFaMi83N0xLTmkzTmpRQU9NZUg3WXVpZnlNbTZNdTB2V3F6UU54SDJ1TGhFd0JRN0VocEpYSnFoMmp0dQpldnR6YUF6N2o5MEVKdlN6WHRtSDRQaHd1NFZFMHJWRGVlbUhrMXRZRjhsQ3hVM2xtZFRGZjJ4M29qVnVKNndpCkd3c3RaU0s5V2J5VW53RjNLUXdkMndmd3VUVVpSRGtuSkI2ZGhOcUxNakN3V1M1ekpDUjcvYXFuM0tqb0xvWEgKWkI1VW1UcGd2Sm9rWm0wcUF0ODRNS0hFb2ZCYXJ0M1BUcnplWGI0N1VHRlMrM0h1anVQUTBreW1DT0crK1ZEMwo0K0M5eVlXaTRhYnd6SE9rTmQvMlFKZVoyQjhjbjhlVU5kUGFSSnkweS9keVpSd0NZdXFKSkxZeTVGUTFiQlUwCm96bThnMG01ZWc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    server: https://api.yourcluster.com
  name: yourcluster.com
contexts:
- context:
    cluster: yourcluster.com
    namespace: development  #you will notiace this. This is means developere default namesapce is development.
    user: kubernetes-developer
  name: aws
current-context: aws
kind: Config
preferences: {}
users:
- name: kubernetes-developer
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1alpha1
      args:
      - token
      - -i
      - api.yourcluster.com
      - -r
      - arn:aws:iam::<account id>:role/K8sDeveloper
      command: aws-iam-authenticator
      env: null
```
{% endcode %}

{% code title="Terminal" %}
```bash
$ wget https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.4.0/aws-iam-authenticator_0.4.0_linux_amd64
$ chmod +x aws-iam-authenticator_0.4.0_linux_amd64
$ sudo mv aws-iam-authenticator_0.4.0_linux_amd64 /usr/local/bin/aws-iam-authenticator
$ aws-iam-authenicator version
{"Version":"0.4.0","Commit":"9999cd8047782c94c08e2d24b77dc9488947aa03"}
$ kubectl get nodes # test what you authorized 
```
{% endcode %}

ထို့နောက် AWS Application LoadBalancer ကို Kubernetes Ingress Controller နဲ့ integrate ပြုလုပ်ရန် အောက်ဖော်ပြပါအတိုင်း RBAC နှင့် alb-ingress-controller deployment ကို kube-system တွင် create လုပ်ရမည်ဖြစ်ပါသည်။ လိုအပ်သော additional policy များကို cluster bootstrap လုပ်ခဲ့တုန်း ကထည့်သွင်းခဲ့ပြီးဖြစ်ပါသည်။

{% code title="alb-rbac.yaml" %}
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    app: alb-ingress-controller
  name: alb-ingress-controller
rules:
  - apiGroups:
      - ""
      - extensions
    resources:
      - configmaps
      - endpoints
      - events
      - ingresses
      - ingresses/status
      - services
    verbs:
      - create
      - get
      - list
      - update
      - watch
      - patch
  - apiGroups:
      - ""
      - extensions
    resources:
      - nodes
      - pods
      - secrets
      - services
      - namespaces
    verbs:
      - get
      - list
      - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  labels:
    app: alb-ingress-controller
  name: alb-ingress-controller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: alb-ingress-controller
subjects:
  - kind: ServiceAccount
    name: alb-ingress
    namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    app: alb-ingress-controller
  name: alb-ingress
  namespace: kube-system
```
{% endcode %}

{% code title="alb-ingress-controller.yaml" %}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: alb-ingress-controller
  name: alb-ingress-controller
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: alb-ingress-controller
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: alb-ingress-controller
    spec:
      containers:
        - args:
            - --ingress-class=alb
            - --cluster-name=yourcluster.com
          env:
          image: 894847497797.dkr.ecr.us-west-2.amazonaws.com/aws-alb-ingress-controller:v1.0.0
          imagePullPolicy: Always
          name: server
          resources: {}
          terminationMessagePath: /dev/termination-log
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      securityContext: {}
      terminationGracePeriodSeconds: 30
      serviceAccountName: alb-ingress
      serviceAccount: alb-ingress
```
{% endcode %}

{% code title="Terminal" %}
```bash
# setup with your administrator account.
$ kubectl create -f alb-rbac.yaml
$ kubectl create -f alb-ingress-controller.yaml
$ kubectl get pod -n kube-system 
```
{% endcode %}

Ingress controller setup လုပ်ပီးနောက် external DNS အတွက် DNS records များကို Ingress create လုပ်လိုက်သည်နှင့် တပြိုင်နက်ထဲ AWS route 53 တွင် automatically update ပြုလုပ်သွားရန် အောက်ဖော်ပြပါ  service account, rbac for external dns, deployment များကို ဆက်လက်တည်ဆောက်ရမည်ဖြစ်သည်။ 

{% code title="external-dns.yaml" %}
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: external-dns
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: external-dns
rules:
- apiGroups: [""]
  resources: ["services"]
  verbs: ["get","watch","list"]
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get","watch","list"]
- apiGroups: ["extensions"] 
  resources: ["ingresses"] 
  verbs: ["get","watch","list"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["list"]
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: external-dns-viewer
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: external-dns
subjects:
- kind: ServiceAccount
  name: external-dns
  namespace: kube-system
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: external-dns
spec:
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: external-dns
    spec:
      serviceAccountName: external-dns
      containers:
      - name: external-dns
        image: registry.opensource.zalan.do/teapot/external-dns:v0.5.9
        args:
        - --source=service
        - --source=ingress
        - --domain-filter=yourcluster.com # will make ExternalDNS see only the hosted zones matching provided domain, omit to process all available hosted zones
        - --provider=aws
        - --policy=upsert-only # would prevent ExternalDNS from deleting any records, omit to enable full synchronization
        - --aws-zone-type=public # only look at public hosted zones (valid values are public, private or no value for both)
        - --registry=txt
        - --txt-owner-id=my-identifier

```
{% endcode %}

{% code title="Terminal" %}
```bash
$ kubectl create -f external-dns.yaml
```
{% endcode %}

ယခုအခြေအနေတွင်အားလုံးတည်ဆောက်ပြီး ဖြစ်ပါသည်။ 

တည်ဆောက်ခဲ့သည့် Cluster, IAM authentication and k8s RBAC, ALB ingress and automatic external DNS service များကို စမ်းသပ်ရန်အတွက် အောက်ဖော်ပြပါ 2048 web application game ဖြင့်စမ်းသပ်နိုင်ပါသည်။ 

{% code title="Terminal" %}
```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.0.0/docs/examples/2048/2048-namespace.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.0.0/docs/examples/2048/2048-deployment.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.0.0/docs/examples/2048/2048-service.yaml
$ kubectl get po,deploy,svc -n 2048-game
```
{% endcode %}

{% code title="2048-ingress.yaml" %}
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: "2048-ingress"
  namespace: "2048-game"
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
  labels:
    app: 2048-ingress
spec:
  rules:
  - host: yourcluster.com 
    http:
      paths:
      - path: /*
        backend:
          serviceName: "service-2048"
          servicePort: 80

```
{% endcode %}

{% code title="Terminal" %}
```bash
$ kubectl create -f 2048-ingress.yaml
$ kubectl get ingress -n 2048-game
$ dig yourcluster.com # see and wait
$ google-chrome-stable yourcluster.com
```
{% endcode %}

{% embed url="https://www.youtube.com/embed/zPl11ivpajE" caption="k8s cluster setup on aws ec2 using kops" %}

{% hint style="success" %}
ယခု ကျွန်တော်တို. လုပ်ပြထားသော tutorial လေးသည် Non-Profit Organization တစ်ခုအတွက် ကျွန်တော်တို. Setup လုပ်ပေးခဲ့သော Production Grade Kops Cluster Setup Use-case မှ ကောက်နုတ်ဖော်ပြထားခြင်းဖြစ်သည့်အတွက် စာဖတ်သူတို.စိတ်ထဲတွင် မရှင်းမလင်းတစ်ခုခုခံစားခဲ့ရရင်ကျွန်တော်တို.ရဲ့ Facebook Group or Page ကိုဆက်သွယ်မေးမြန်းနိုင်ပါသည်။ 
{% endhint %}

