---
description: how to create pods in Myanmar
---

# Creating pods

![](.gitbook/assets/pods3.png)

        Pods နဲ့ တခြား kubernetes resources တွေကို json သို့မဟုတ် YAML manifest တွေနဲ့ Kubernetes REST API endpoint သို့ post လုပ်ပြီး ဖန်တီးလို့ရနိုင်ပါတယ်။ နောက်တခြား နည်းလမ်းတစ်ခုကတော့ ရိုးရှင်းတဲ့ kubectl run command နဲ့ create လုပ်လို့ရနိုင်ပါတယ်။ kubectl command က အတော်အများ အသုံးဝင် ပေမယ့် တချို့ properties တွေကိုတော့ လုပ်လို့မရပါဘူး၊ limitation တွေ ရှိပါတယ် သို့သော်များလဲ cluster administrator တစ်ယောက် အနေနဲ့ နေ့စဉ် အမြဲ ထိတွေ့နေရတဲ့ အရေးပါတဲ့ နေရာမှာ ရှိပါတယ်။ pod manifest မှာ ဘာတွေ ပါဝင်နိုင်သလဲ ? Pod ရဲ့ အဓိက manifest ကတော့ အပိုင်း ၄ခု ပါဝင်ပါတယ်။ manifest တိုင်းမှာ ပထမဦးစွာ မပါမဖြစ်ပါဝင်တာကတော့ K8s ရဲ့ API version တွေပါ။ API version ဆိုတာ ကတော့ k8s မှာ pod သို့မဟုတ် တခြားသော resource တွေ ကို ဖန်းတီးတဲ့ အခါ မတူညီတဲ့ versions \(stable/ Beta/ Alpha\) တွေရဲ့ feature တွေကို ကိုယ်ဘာတွေ သုံးချင်လဲ အပေါ်မှာ မူတည်ပြီ ထည့်လို့ရနိုင်ပါတယ်။ ယေဘုယျ အားဖြင့်တော့ ကျွန်တော်တို့တွေ က stable တွေကို သုံးကြပါတယ်၊ stable ကတော့ vX \(eg. v1\) ဖြစ်ပြီး X ကတော့ အဲနေရာမှာ 1, 2 အစရှိသဖြင့် integer တွေ ဖြစ်ပါတယ်။ ခုနက ပြောသလိုဘဲ API version တစ်ခုနဲ့ တစ်ခုမှာ လုပ်လို့ရတာတွေ၊ လုပ်လို့မရတာတွေ၊ ပိုကောင်းမွန်တဲ့ feature တွေ၊ stable မှာ မရှိတော့တဲ့ deprecate feature တွေ၊ support လုပ်တဲ့ feature တွေ အစရှိသဖြင့် ကွဲပြားခြားနား ပါတယ်။ အသေးစိတ် ကိုတော့ kubernetes ရဲ့ [document](https://kubernetes.io/docs/concepts/overview/kubernetes-api/) မှာ ကြည့်နိုင်ပါတယ်။ တခြား ပါဝင်တာတွေကတော့ -

* **apiVersion** - ကိုယ်အသုံးပြုမဲ့ resource ရဲ့ API version \(နောက်ပိုင်းတွင် အသေးစိပ် ရှင်းပြမည်\) အလွတ်သိရမလို၊ doc တွင် \(သို့\) kubectl explain pod ဖြင့် သိနိုင်သည်။
* **kind** - ကိုယ်တည်ဆောက်မည့် resource အမျိုးအစား၊ ယခု အခါ တွင် pod resource ဖြစ်သည်။
* **Metadata** - မှာတော့ pod ရဲ့ name, namespace, labels , annotations စတဲ့ တခြား information တွေပါဝင်ပါတယ်။
* **Spec** - မှာတော့ တကယ် pod မှာပါဝင်တဲ့ ဖေါ်ပြချက်တွေ ပါဝင်ပါတယ်။ အဲဒါ တွေကတော့ ဘယ် containers တွေ၊ volumes တွေ၊ environment တွေနဲ့ configmap တွေ၊ အစရှိတဲ့ data တွေ ကို ဘယ်လို အသုံးပြုမယ်ဆိုတာတွေ ပါဝင်ပါတယ်။
* **Status** - ဒီထဲမှာတော့ လက်ရှိ pod ရဲ့ container တစ်ခုချင်းစီရဲ့ internal IP၊ running ဖြစ်နေလား မဖြစ်နေလား၊ စတဲ့ basic ကျတဲ့ informationတွေ၊ အခြေအနေတွေကို ဖေါ်ပြပေးတဲ့ information တွေ ပါဝင်ပါတယ်။

  ဒါတွေကတော့ pod manifest\(definition လို့လည်း ခေါ်တယ်၊ descriptor လို့လည်း ခေါ်တယ်\) ရဲ့ အဓိက ပါဝင်တဲ့ အပိုင်း ၄ ပိုင်း ဖြစ်ပါတယ်။ pod ဖန်တီးတဲ့ အခါမှာတော့ နောက်ဆုံးက status ဆိုတဲ့ အပိုင်းကို ထည့်ရေးစရာ မလိုပါဘူး။ status ကတော့ လက်ရှိ cluster ထဲမှာ တည်ဆောက်ထားတဲ့ pod ကို retrieve လုပ်လိုက်တဲ့ အခါမှာ cluster ဘက် အခြမ်းမှ ထုတ်ပြပေးတဲ့ information တွေသာ ဖြစ်ပါတယ်။ တချို့ အခြေနေ တွေမှာ information တွေ သိရဖို့ အသုံးဝင်ပါတယ်။ pod တခုကို ဖန်တီးသည့် အခါ Metadata, Spec တို့ရဲ့ field တွေကို သိရှိနိင်ဖို့ k8s API document တဆင့် သို့မဟုတ် kubectl explain command နဲ့ ဖြစ်နိုင်ခြေရှိတဲ့ API object fields တွေကို လဲ ရှာဖွေလို့ရနိုင်ပါတယ်၊ လွန်စွာ အသုံးဝင်ပါတယ်။

```yaml
# A basic pod manifest:
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

```bash
# Pod create using kubectl and without yaml manifest
$ kubectl run hola --image=quay.io/dther/hola --port=8080 --generator=run-pod/v1
pod/hola created

# retrieve Pod Object
$ kubectl get pod
NAME                                  READY   STATUS    RESTARTS   AGE
hola                                  1/1     Running   0          5s
```

```yaml
# retrieve Pod Object detail by name describe with yaml. you can use json also.
$ kubect get pod hola -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2019-11-22T04:20:16Z"
  labels:
    run: hola
  name: hola
  namespace: default
  resourceVersion: "23778097"
  selfLink: /api/v1/namespaces/default/pods/hola
  uid: 93fc57ec-4198-4134-8a9e-37901871ea30
spec:
  containers:
  - image: quay.io/dther/hola
    imagePullPolicy: Always
    name: hola
    ports:
    - containerPort: 8080
      protocol: TCP
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: default-token-gll68
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: node2
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: default-token-gll68
    secret:
      defaultMode: 420
      secretName: default-token-gll68
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2019-11-22T04:20:34Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2019-11-22T04:20:43Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2019-11-22T04:20:43Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2019-11-22T04:20:16Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://4caa1c5d607621a52bd392068c4386880eb2844070c3476e6e2cf6c90a16ce7d
    image: quay.io/dther/hola:latest
    imageID: sha256:b9d148f1a7ff4f1841bbc5a7ac848714eed3310b29643e10a104820a6a21bc94
    lastState: {}
    name: hola
    ready: true
    restartCount: 0
    state:
      running:
        startedAt: "2019-11-22T04:20:43Z"
  hostIP: 10.14.255.11
  phase: Running
  podIP: 10.233.96.7
  qosClass: BestEffort
  startTime: "2019-11-22T04:20:34Z"
```

```yaml
# Explain Pod resource using kubectl explain
$ kubectl explain pod
KIND:     Pod
VERSION:  v1

DESCRIPTION:
     Pod is a collection of containers that can run on a host. This resource is
     created by clients and scheduled onto hosts.

FIELDS:
   apiVersion    <string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#resources

   kind    <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#types-kinds

   metadata    <Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#metadata

   spec    <Object>
     Specification of the desired behavior of the pod. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#spec-and-status

   status    <Object>
     Most recently observed status of the pod. This data may not be up to date.
     Populated by the system. Read-only. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#spec-and-status

$ kubectl explain pod.spec #### detail    အသုံးပြုနိုင်တဲ့ spec တွေကို တွေ့ရပါမယ်
```

```text
Reference:
- https://kubernetes.io/docs/reference/
- https://kubernetes.io/docs/concepts/overview/kubernetes-api/
- https://kubernetes.io/docs/reference/kubectl/conventions/
```

