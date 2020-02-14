# Workshop Part-1 \(Kind\)

![Kubernetes In Docker](../.gitbook/assets/logo.png)

#### Kind = Kubernetes In Docker

Kind သည် Docker ကို အသုံးပြု ၍ Kubernetes Cluster များကို Local \(laptop or Desktop\) တွင် တည်ဆောက်နိုင်သော Tools တစ်ခုဖြစ်သည်။ Local တွင် multi node၊ HA control plane များ ပုံစံဖြင့် လည်း တည်ဆောက်နိုင်ပါသည်။ Local development ပြုလုပ်ရာတွင် minikube ကဲ့သို့အသုံးပြုနိုင်သည်။ multi node ရ၍ cluster admin များ အတွက် ပိုပြီး အသုံးဝင်သည်။ ယခု workshop တွင် Kind ကို အသုံးပြု၍ single control plane/worker node၊ multi nodes၊ control plane HA နှင့် Ingress ကို Setup လုပ်သွားပြီး 2048 game နှင့် hola webapp ကို တည်ဆောက် သွားမှာ ဖြစ်သည်။

#### Workshop Prerequisite

* [docker](https://www.docker.com/)
* [kind](https://github.com/kubernetes-sigs/kind)
* [kubectl](https://github.com/kubernetes/kubectl)

{% tabs %}
{% tab title="Linux" %}
Kind Install in Linux

```bash
$ curl -Lo ./kind https://github.com/kubernetes-sigs/kind/releases/download/v0.7.0/kind-$(uname)-amd64
$ chmod +x ./kind
$ mv ./kind /usr/local/bin/
```
{% endtab %}

{% tab title="Mac" %}
Kind Install in Mac

```bash
$ brew install kind
```
{% endtab %}

{% tab title="Window" %}
Kind Install in Window

```bash
cmd> curl.exe -Lo kind-windows-amd64.exe https://github.com/kubernetes-sigs/kind/releases/download/v0.7.0/kind-windows-amd64
cmd> Move-Item .\kind-windows-amd64.exe c:\some-dir-in-your-PATH\kind.exe
```
{% endtab %}

{% tab title="Golang" %}
Kind install using golang

```bash
$ GO111MODULE="on" go get sigs.k8s.io/kind@v0.7.0
```
{% endtab %}
{% endtabs %}

Kind ကို install ပြုလုပ်ပြီးသွားလျှင် အောက်ဖော်ပြပါ commands ဖြင့် kubernetes cluster များကို တည်ဆောက် နိုင်ပါသည်။ `--name=<name>` ဖြင့် မတူညီသော cluster များအတွက် မိမိ စိတ်ကြိုက် နာမည် များ ပေးနိုင် သည်။

```bash
$ kind create cluster
$ # or
$ kind create cluster --name=k8smm # create cluster with name.
```

တည်ဆောက် ပြီးသော Clusters များ ကို အောက်ဖော်ပြပါ command ဖြင့် list လုပ်နိုင်သည်။

```bash
$ kind get clusters
```

တည်ဆောက် ပြီးသော Clusters များ ၏ cluster information များကို အောက်ဖော်ပြပါ command ဖြင့် ကြည့်နိုင်ပါသည်။

```bash
$ kubectl cluster-info --context <cluster-name>
$ kubectl cluster-info --context k8smm
```

မိမိ အသုံးပြုပြီးသည့် အခါ \(သို့မဟုတ်\) မလိုအပ်တော့သည့် အခါ အောက်ဖော်ပြပါ command ဖြင့် cluster များကို delete ပြုလုပ်နိုင်ပါသည်။

```bash
$ kind delete cluster
$ kind delete cluster --name k8smm
```

အထက်ပါ command များသည် kind ကို အသုံးပြု၍ kubernetes cluster ကို မိမိ laptop တွင် အလွယ်တကူ အသုံးပြုနိုင်သော basic command များ ဖြစ်သည်။ ဆက်လက် kind ကို အသုံးပြုပြီး ကျွန်တော်တို့ multi nodes cluster၊ multi control-plane HA နှင့် ingress များကို တည်ဆောက်သွားမှာ ဖြစ်တယ်။

{% tabs %}
{% tab title="Multi-node clusters" %}
ဒါကတော့ multi node cluster တည်ဆောက်ရန် configuration ဖြစ်သည်။ worker node များကို ကိုယ်လိုချင် သလောက် `- role: worker` ဖြင့် ထပ်တိုး တည်ဆောက်နိုင်သည်။ label၊ nodeSelector နှင့် scheduling တို့ လေ့ကျင့် စမ်းသပ်ရာတွင် အသုံး ဝင်သည်။

{% code title="multi-node-clusters.yaml" %}
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```
{% endcode %}

```bash
$ kind create cluster --config multi-node-clusters.yaml --name=k8smm
```
{% endtab %}

{% tab title="Control-plane HA" %}
Control plane node ကို high availability ဖြစ်စေရန် တစ်ခုထက် ပိုထားချင်သည့် အခါ အောက်ဖေါ်ပြပါ configuration အတိုင်း `-role: control-plane` တိုးပြီး တည်ဆောက်နိုင်ပါသည်။

{% code title="control-plane-ha.yaml" %}
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: control-plane
- role: control-plane
- role: worker
- role: worker
- role: worker
```
{% endcode %}

```bash
$ kind create cluster --config control-plane-ha.yaml --name=k8smm
```
{% endtab %}

{% tab title="Multi-node Ingress" %}
nginx ingress controller ကို setup လုပ်ရန် control-plane တွင် http နှင့် https အတွက် hostport 80/443 ကို ဖွင့်ပြီး kind ကို setup လုပ်ရပါမည်။ controller ကို control plane node တွင် schedule လုပ်ရန် kind ဖြင့် cluster စ တည်ဆောက် ကတည်းက label `ingress-ready=true` ဖြင့် configure ပြုလုပ်ထားခြင်းဖြစ်သည်။

{% code title="multi-node-ingress.yaml" %}
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
        authorization-mode: "AlwaysAllow"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
- role: worker
- role: worker
```
{% endcode %}

```bash
$ kind create cluster --config multi-node-ingress.yaml --name=k8smm
```

လိုအပ်သည့် ingress controller နှင့် service ကို ထည့်သွင်းရမည်။

```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.27.0/deploy/static/mandatory.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.27.0/deploy/static/provider/baremetal/service-nodeport.yaml
```

```bash
$ kubectl get pod -n ingress-nginx -o wide
```

ထည့်သွင်းပြီး သော ingress controller ကို control plane ပေါ်သာ လျှင် run ရန် patch ပြုလုပ်ပြီး reschedule ပြန်လုပ်သည်။

```javascript
{
  "spec": {
    "template": {
      "spec": {
        "containers": [
        {
          "name": "nginx-ingress-controller",
          "ports": [
          {
            "containerPort": 80,
            "hostPort": 80
          },
          {
            "containerPort": 443,
            "hostPort": 443
          }
          ]
        }
        ],
        "nodeSelector": {
          "ingress-ready": "true"
        },
        "tolerations": [
        {
          "key": "node-role.kubernetes.io/master",
          "operator": "Equal",
          "effect": "NoSchedule"
        }
        ]
      }
    }
  }
}
```

```bash
$ kubectl patch deployments -n ingress-nginx nginx-ingress-controller -p '{"spec":{"template":{"spec":{"containers":[{"name":"nginx-ingress-controller","ports":[{"containerPort":80,"hostPort":80},{"containerPort":443,"hostPort":443}]}],"nodeSelector":{"ingress-ready":"true"},"tolerations":[{"key":"node-role.kubernetes.io/master","operator":"Equal","effect":"NoSchedule"}]}}}}'
$ kubectl get pod -n ingress-nginx -o wide 
```
{% endtab %}
{% endtabs %}

cluster အတွင်းရှိ node များကို list ထုတ်ကြည့်ရန်အတွက် အောက်တွင်ဖော်ပြထားအသော command ကိုအသုံးပြုပါ။

```bash
$ kubectl get nodes
```

တည်ဆောက်ပြီး သော multi nodes cluster ပေါ်တွင် Pod တစ်ခုကို စမ်းပြီး တည်ဆောက်ရန်၊

{% code title="hola.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hola
spec:
  containers:
  - image: quay.io/dther/hola
    name: hola
    ports:
    - containerPort: 8080
      protocol: TCP
```
{% endcode %}

```bash
$ kubectl create -f hola.yaml
$ kubectl get pod -o wide
```

Pod တည်ဆောက်ပြီး အဆင်ပြေလျှင်၊ 2048 game အတွက် သီးသန့် project တစ်ခုအနေဖြင့် namespace အသစ် တည်ဆောက်ရန် manifest ကို file ထဲတွင် save ပြီး အောက်ဖေါ်ပြပါ command များဖြင့် တည်ဆောက်နိုင်ပါသည်။

{% code title="namespace.yaml" %}
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: "2048-game"
```
{% endcode %}

```bash
$ kubectl get namespaces
$ kubectl create -f namespace.yaml
# or
$ kubectl create ns 2048-game
$ kubectl get ns
```

2048 game ကို k8s cluster တွင် deployment အနေဖြင့် တည်ဆောက်ရန် အောက်ဖေါ်ပြပါ manifest ကို save ပြီး `kubectl create -f` ဖြင့် တည်ဆောက်ရန်၊

{% code title="deployment.yaml" %}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: "2048-deployment"
  namespace: "2048-game"
spec:
  selector:
    matchLabels:
      app: "2048"
  replicas: 5
  template:
    metadata:
      labels:
        app: "2048"
    spec:
      containers:
      - image: alexwhen/docker-2048
        imagePullPolicy: Always
        name: "2048"
        ports:
        - containerPort: 80
```
{% endcode %}

```bash
$ kubectl get deployment -n 2048-game
# or 
$ kubectl get deploy -n 2048-game
$ kubectl create -f deployment.yaml
$ kubectl get deploy -n 2048-game
$ kubectl get pod -n 2048-game
# or
$ kubectl get deploy,rs,pod -n 2048-game
$ kubectl -n 2048-game port-forward deployment/2048-deployment 8080:80 # Ctl+C
```

အပေါ်မှ command များဖြင့် တည်ဆောက်ခဲ့ သော 2048 application ၏ pods များ running ဖြစ်၊ မဖြစ် check ရန် လိုအပ်ပါသည်။ Pod များ running ဖြစ်သွားပြီးလျှင် အောက်ဖေါ်ပြပါ manifest ကို အသုံးပြုပြီ service ကို expose ထုတ်ရန်၊

{% code title="service.yaml" %}
```yaml
apiVersion: v1
kind: Service
metadata:
  name: "service-2048"
  namespace: "2048-game"
spec:
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
  selector:
    app: "2048"
```
{% endcode %}

```bash
$ kubectl get service -n 2048-game
$ kubectl create -f service.yaml
$ kubectl get svc -n 2048-game
$ kubectl -n 2048-game port-forward svc/service-2048 8080:80 # Ctl+C
```

{% hint style="warning" %}
ingress ကို ဆက်လက်တည်ဆောက် လိုလျှင် kind ဖြင့် cluster စတည်ဆောက် ကတည်းက ingress controller ကို setup လုပ် ပြုလုပ်ခဲ့ရန် လိုအပ်ပါသည်။
{% endhint %}

{% code title="ingress.yaml" %}
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: 2048-ingress
  namespace: 2048-game
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: service-2048
          servicePort: 80
```
{% endcode %}

အထက်ပါ manifest ကို save ပြီ အောက်ဖေါ်ပြပါ command များဖြင့် ingress ကို တည်ဆောက် ရန်။

```bash
$ kubectl get ingress -n 2048-game
$ kubectl create -f ingress.yaml
$ kubectl get ing -n 2048-game
$ google-chrome-stable localhost
```

ယခု အခါတွင် 2048 game ကို အောင်မြင်စွာ local kubernetes cluster တွင် တည်ဆောက်ပြီး ဖြစ်သည်။ browser တွင် localhost ကို ဖွင့်ပြီ game ကို ဆော့နိုင် ပြီ ဖြစ်သည်။

## Exposing Hola app

အပေါ်မှ စမ်းသပ် တည်ဆောက်ခဲ့သော hola pod ကို service expose ပြုလုပ် ပြီး ingress မှာ တဆင့် localhost/hola တွင် ခေါ်ဆိုနိုင်ရန် စမ်းကြမှာ ဖြစ်ပါသည်။

အရင်ဆုံး service expose ပြုလုပ်နိုင်ရန် အတွက် pod ကို `app=hola` တပ်ပေးရန်လိုအပ်ပါသည်။ ထိုနောက် အောက်ဖေါ်ပြပါ command များ ဖြင့် service ကို expose ပြုလုပ်ရန်၊

```bash
$ kubect get pod --show-lables
$ kubectl label pod hola app=hola
$ kubectl expose pod hola --port=8080
$ kubectl get svc
```

{% code title="hola-ingress.yaml" %}
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hola-ingress
  annotations:
     nginx.ingress.kubernetes.io/rewrite-target: /$2
    nginx.ingress.kubernetes.io/configuration-snippet: |
      rewrite ^/static/(.*)$ /hola/static/$1 redirect;
spec:
  rules:
  - http:
      paths:
      - path: /hola(/|$)(.*)
        backend:
          serviceName: hola-svc
          servicePort: 8080
```
{% endcode %}

အထက်ပါ hola ingress manifest ကို အသုံးပြုပြီး hola app အတွက် အောက်ဖေါ်ပြပါ command များတည်ဆောက်ရန်၊

```bash
$ kubectl get ingress
$ kubectl create -f hola-ingress.yaml
$ kubectl get ing
$ google-chrome-stable localhost/hola/
```

ယခု အခါတွင် localhost မှာ 2048 game ကို ခေါ်ဆိုနိုင်ပြီး၊ localhost/hola/ မှ hola application ကို ခေါ်ဆိုနိုင်ပြီ ဖြစ်သည်။



