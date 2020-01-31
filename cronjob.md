# CronJob

Kubernetes ရဲ့ [Job](https://blog.k8smm.org/job) resource အလုပ်လုပ်ပုံကို လေ့လာသိရှိပြီးကြပီဆိုရင် ဒီ Job တွေကို schedule နဲ့ run ချင်တဲ့အခါဆို ဘယ်လိုမျိုး လုပ်လို့ရမလဲ ?

Job တွေ များလာတဲ့အခါ၊ အချိန်တစ်ခုပေါ်မူတည်ပီး ပုံမှန် လုပ်ရမယ့် Job မျိုး၊ ကိုယ် run ရမယ့် Job က daily process ဖြစ်နေမယ်ဆိုရင် နေ့တိုင်း Job ကို သွား run ပေးနေရတာမျိုးက အဆင်မပြေနေဘူး။  
Linux OS တွေမှာ ကျွန်တော်တို့ လုပ်ချင်တဲ့ process တွေ run ချင်တဲ့ script တွေကို cronjob အဖြစ် crontab ထဲမှာ schedule ပေးပီး run ကြတယ်။  
Kubernetes မှာလဲ Job တွေကို cron format နဲ့ schedule ထားပီး run ပေးနိုင်တဲ့ **CronJob** resource ရှိတယ်။

CronJob တည်ဆောက်ဖို့အတွက် အရမ်း မရှုပ်ထွေးပါဘူး။  
kind မှာတော့ သူ့ရဲ့ resource name ဖြစ်တဲ့ CronJob  
spec မှာ schedule parameter နဲ့ cron format သတ်မှတ်ပေးရပီး  
cronjob က ဆောက်ရမယ့် job ကို jobTemplate property မှာ Job ရဲ့ spec ကို ထည့်ပေးရုံပါပဲ

```text
apiVersion: batch/v1beta1
kind: CronJob
metadata:
 name: throw-dice
 labels:
   app: throw-dice
spec:
 schedule: "0 * * * *"
 jobTemplate:
   spec:
   template:
    spec:
      containers:
      - name: throw-dice
        image: k8smm/throw-dice
      restartPolicy: Never  
```



