---
description: Basic Concept of Job
---

# Job

ကျွန်တော်တို့ [Replication Controller](https://blog.k8smm.org/replication-controller), [ReplicaSet](https://blog.k8smm.org/replicaset), [Daemonset](https://blog.k8smm.org/ds) တို့ကို ရှေ့ blog တွေမှာ လေ့လာခဲ့ကြပါတယ်။ ဒီ resources တွေရဲ့ပုံစံက pod ရဲ့ အခြေအနေကို အမြဲစောင့်ကြည့်နေပီး pod သေသွားရင် pod အသစ် ပြန်ဆောက်ပေးလေ့ရှိပါတယ်။ ဒီလိုမျိုး pod ရဲ့လုပ်ဆောင်မှူကို အစဥ်မပြတ် running state ဖြစ်နေတာကို continuous task လို့သုံးနှုန်းပါတယ်။

ဒီလိုဆိုရင် continuous task အဖြစ် process အားလုံးကို run သင့်သလား? ဆိုပါစို့ database migration \(သို့\) backup လုပ်တဲ့ bash script run မယ့် pod ရှိမယ်။ ဒီ pod ရဲ့လုပ်ဆောင်မှုက လိုချင်တဲ့အချိန်မှာ လိုအပ်တဲ့ backup file ရရင် သူ့အလုပ်က ပီးပီ။ ဒီအခြေအနေကိုသာ အရှေ့ကပြောခဲ့တဲ့ resource type တွေနဲ့သုံးမယ်ဆိုရင် အမြဲတမ်း running state နဲ့ backup ထိုင်လုပ်နေလိမ့်မယ်။ ဒါဘယ်လိုမှ အဆင်မပြေနိုင်ဘူး။ ဒီအခြေအနေမျိုးအတွက် kubernetes မှာ Job resource သုံးပြီး ဖြေရှင်းလို့ရပါတယ်။

Job ကိုအသုံးပြုပီး pod ရဲ့လုပ်ဆောင်မှု အောင်မြင်ပြီးဆုံးသွားရင် completed state လို့သတ်မှတ်ပီး ထိုလုပ်ငန်းစဥ် ပြီးဆုံးသွားတဲ့ pod ကိုဖျက်လိုက်ရင်လဲ pod အသစ် \(restarted\) ပြန်လည်မဆောက်ပေးတော့ပါဘူး။ Job သည်လည်း replicaset နည်းတူ Job run နေတဲ့အချိန် pod ဆောက်နေတဲ့ node failed သွားခဲ့ရင် တခြား node ပေါ် အလိုအလျောက် reschedule လုပ်ပေးပါတယ်။ ဒါဆိုရင် Job manifest တစ်ခုလောက် ရေးကြည့်ရအောင်...

```yaml
apiVersion: batch/v1
kind: Job
metadata:
   name: math-job
spec:
 template:
   spec:
     containers:
     -  name: math-job
        image: ubuntu
        command: ['expr', '3', '+', '2']
     restartPolicy: Never
```

Job က batch API group ထဲက ဖြစ်ပီးတော့ လက်ရှိ version 1 နဲ့ ဖြစ်ပါတယ်။ ဒီ Job က ဂဏန်း ၂လုံးကို math operation \(+\) နဲ့ ပေါင်းပေးမယ် output ထုတ်ပေးပီးရင် အလုပ်ပြီးပြီ။ **`kubectl create command`** နဲ့ job ေဆာက်လိုက်ပီး job ေဆာက်ပီးသည်နှင့် pod ကိုပါ ချက်ချင်းဆောက်ပေးသွားတာကိုတွေ့ရပါမယ်။

```bash
$ kubectl get job,po

NAME                 DESIRED   SUCCESSFUL   AGE
job.batch/math-job   1         1            8s


NAME                 READY     STATUS       RESTARTS   AGE
pod/math-job-p6s9k   0/1       Completed    0          8s
```

ဒီနေရာမှာ နည်းနည်းပြောစရာတစ်ခုက pod ရဲ့ spec ထဲက restartPolicy ဟာ ပုံမှန် pod ေဆာက်နေကျအတိုင်းဆိုရင် Always ဖြစ်တယ်။ အပေါ်က manifest ကို pod သီးသန့်ဆောက်ပီး run ကြည့်မယ်ဆိုရင် completed state ရောက်ပီးရင် container restart ဖြစ်သွားတာကို တွေ့ရပါမယ်။ Job မှာတော့ container ရဲ့ အလုပ်ပြီးဆုံးသွားဖို့ကိုသာ ရည်ရွယ်တဲ့အတွက် restart မဖြစ်အောင် restartPolicy ကို **Never** \(သို့\) **OnFailure** သတ်မှတ်ပေးရပါတယ်။ အန်စာတုံးခေါက်တဲ့ programကို Job တစ်ခုဆောက်လိုက်မယ်။

```yaml
apiVersion: batch/v1
kind: Job
metadata:
   name: throw-dice
   labels:
     app: throw-dice
spec:
 template:
   spec:
   containers:
   - name: throw-dice
     image: k8smm/throw-dice
   restartPolicy: Never
```

0 မှ 6 ထိ random နံပါတ် output ထုတ်ပေးပီး 6 ရရင် Job success ဖြစ်ပီး ကျန်တဲ့ နံပါတ်ရရင် Job error ဖြစ်မယ်။ ဒီနေရာမှာ poilcy ကို Never ေပးထားတဲ့အတွက် Job success မဖြစ်သေးသ၍ Job controller က pod အသစ်တစ်ခု ဆောက်ပါတယ်။

```bash
$ kubectl  get po
NAME              READY    STATUS     RESTARTS   AGE
throw-dice-ktpkf  0/1      Error      0          11m
throw-dice-l62bf  0/1      Error      0          11m
throw-dice-z9dq   0/1      Completed  0          11m

$ for i in $(kubectl  get po --no-headers -o custom-columns=NAME:.metadata.name); do kubectl logs $i; done
4
2
6
```

အကယ်၍ policy ကို OnFailure နဲ့ဆိုရင် Job controller က Pod အသစ်ဆောက်ပေးတာမျိုးမလုပ်ပဲ process မပီးသေးသ၍ container ကို ပြန် run ေနပါတယ်။ Pod တစ်ခုပဲ run မှာဖြစ်တဲ့အတွက် pod ရဲ့ status ကို -w option နဲ့ real-time ကြည့်မှသာ လုပ်ဆောင်ချက်ကို သေချာမြင်ရမှာဖြစ်တယ်။

```bash
$ kubectl  get po
NAME               READY   STATUS             RESTARTS   AGE
throw-dice-h6z82   0/1     ContainerCreating  0          1s
throw-dice-h6z82   0/1     Error              0          3s
throw-dice-h6z82   0/1     Error              1          5s
throw-dice-h6z82   0/1     CrashLoopBackOff   1          6s
throw-dice-h6z82   0/1     Completed          2          21s

$ kubectl  get po
NAME               READY   STATUS             RESTARTS   AGE
throw-dice-h6z82   0/1     Completed          2          32s

$ kubectl logs  throw-dice-on-h6z82
6
```

နံပါတ် 0 မှ 5 ထိ random ၂ ခါ ဖြစ်သွားသည့်အတွက် Error status ပြပါတယ်။ သို့သော် log ကြည့်လိုက်တဲ့အခါ 6 ကိုပဲတွေ့ရပါမယ်။ ထို့ကြောင့် ကိုယ်ရဲ့ program ရဲ့ ရည်ရွယ်ချက်ပေါ်မူတည်ပြီး အသုံးပြုပုံချင်းကွာခြားပါတယ်။



**Job တစ်ခုမှာ Multiple Pod ေတွ run လို့ရလား ?** 

Job မှာ တစ်ခုနဲ့အထက်ပိုတဲ့ pod တွေ ဆောက်လို့ရပါတယ်။ ဒီလို ဆောက်တဲ့နေရာမှာ  
pod တစ်ခုပီးမှတစ်ခု run တဲ့နည်း \( **completions** \) နဲ့   
pod တွေ တစ်ပြိုင်နက်တည်း run တဲ့နည်း \( **parallelism** \) ဆိုပီး နည်းလမ်း ၂မျိုးရှိပါတယ်။ Job spec မှာ completions \(သို့\) parallelism value ထည့်ပေးရုံပါပဲ။

```yaml
apiVersion: batch/v1
kind: Job
metadata:
   name: random-error
   labels:
     app: random-error
spec:
 completions: 3
 template:
   spec:
    containers:
    - name: random-error
      image: k8smm/random-error
    restartPolicy: Never
```

completions တန်ဖိုးကို 3 ပေးထားတဲ့အတွက် successful pod အရေအတွက် ၃ခု မဆောက်ရသေးသ၍ pod တစ်ခုပီးမှနောက်တစ်ခု run မှာဖြစ်ပါတယ်။ ဥပမာ ပထမ pod completed ဖြစ်တဲ့အတွက် ဒုတိယ pod run ေပမဲ့ fail သွားတယ်ဆိုရင် တတိယ pod ကို ထပ်ဆောက်လို့ success ဖြစ်သွားမယ်ဆိုရင် pod ၃ခု ဆောက်သွားတယ်ဆိုပေမဲ့ completed ဖြစ်တဲ့ pod ၂ခု ပဲဖြစ်သေးတဲ့အတွက် သတ်မှတ်ထားတဲ့ ၃ခု မရောက်မချင်း pod အသစ်တစ်ခုထပ်ဆောက်နေမှာပါ။

```bash
$ kubectl  get po
NAME                READY   STATUS       RESTARTS  AGE
random-error-xkbqs  0/1     Completed    0         2m
random-error-zcmp9  0/1     Error        0         2m
random-error-zmvpi  0/1     Completed    0         2m
```

နောက်တစ်နည်းကတော့ Job process ကို pod တွေ parallel ဆောက်ပီး ပီးမြောက်စေချင်တဲ့အခါ သုံးကြပါတယ်။ completions နဲ့တွဲပီးလဲသုံးလို့ရတယ်။ Job process ၅ခုလုပ်မယ်ဆိုရင် pod တစ်ခုပီးမှ တစ်ခု run ေပမဲ့ တစ်ခါ run ရင် pod ၂ခုဆီ ပေး run ချင်တယ်ဆိုတာမျိုးလဲရတယ်။ ခုနက manifest မှာ parallelism value ထပ်ထည့်ပီး စမ်းကြည့်လို့ရပါတယ်။

```yaml
spec:
   completions: 5
   parallelism: 2
   template:
```

\*\*\*\*

#### **Job လုပ်ဆောင်ချိန်ကို ကန့်သတ်လို့ရနိုင်လား ?**

 အကယ်၍ Pod run ရင်း cpu, memory, storage, network စသဖြင့် တစ်နေရာမှာ stuck ဖြစ်နေလို့ Job က Pod ပီးမယ့်ချိန်ကို စောင့်ရတဲ့ချိန်အရမ်းကြာနေတာမျိုး ဖြစ်လာခဲ့ရင် pod မြန်မြန် မ run နိုင်တဲ့အခါမျိုးဆိုရင် kubernetes Job ရဲ့ spec မှာ activeDeadlineSeconds ဆိုပီး pod ရဲ့ process time ကို ကန့်သတ်လို့ရပါတယ်။ သတ်မှတ်ထားတဲ့ အချိန်စက္ကန့်ရောက်တဲ့အခါ Pod ကို terminate လုပ်ပီး Job ကိုလဲ failed လို့ သတ်မှတ်လိုက်ပါတယ်။



Job နဲ့ Pod ကို ဘယ်လို delete လို့ရသလဲ**?**

Kubernetes Job လုပ်ဆောင်မှုပြီးသွားတယ်ဆိုရင် pod ကို အလိုအလျောက် မဖျက်ပေးပါဘူး။ ဒီ feature ကြောင့် run ပီးသွားတဲ့ job၊ pod တွေရဲ့ status၊ log တွေကို ပြန်ကြည့်လို့အဆင်ပြေတယ်။ `kubectl delete job`  command နဲ့ ဖျက်လိုက်ရင်တော့ Pod ပါ တစ်ခါတည်း ပျက်သွားပါတယ်။ 

Kubernetes ရဲ့ resource တစ်ခုဖြစ်တဲ့ Job နဲ့ပတ်သက်တာတော့ ဒီလောက်မျှနဲ့ အဆင်ပြေမယ်လို့ မျှော်လင့်ပါတယ်။ ပို၍သိရှိလိုသည်ဖြစ်စေ၊ မရှင်းတာရှိသည်ဖြစ်စေ k8smm ကို အချိန်မရွေး Q&A လုပ်လို့ရပါတယ်။ community အတွက် တ်စွမ်းသလောက် ကူညီမယ့်သူများကိုလဲ အမြဲ ဖိတ်ခေါ်လျက်ပါ။ ကျေးဇူးတင်ပါတယ်။

