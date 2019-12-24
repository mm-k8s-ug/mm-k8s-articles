# ReplicaSet
```
                        +--------------+
                        | ReplicaSet   |
                        +--------------+
                              |
               _______________|_________________
               |              |                |
               |              |                |
          +----|-----+  +-----|----+    +------|----+
          |   pod    |  |   pod    |    |     pod   |
          +----------+  +----------+    +-----------+
        {  app=hola      app=hola          app=hola  }== lablel
```
   
ReplicaSet ဆိုသည်မှာ k8s ရဲ့ pods တွေကို manage လုပ်တဲ့ controller တစ်ခုပါပဲ။ ReplicaSet က ReplicationController ရဲ့ next generation ပါ။ ReplicaSet ရဲ့ အလုပ်လုပ် ပုံတွေကတော့ ReplicationController ရဲ့ အလုပ်လုပ်ပုံနဲ့ အတိအကျ နည်းပါး အတူတူပါပဲ။ ဒါဆို ဘာတွေ ကွာခြားသလဲ ReplicationController နဲ့ ReplicaSet ? တကယ်က ကွာခြားတာ မဟုတ်ပါဘူး ReplicationController ရဲ့ selector က pod တွေကို တိကျတဲ့ label (key=value) နဲ့ သာလျှင် match  လုပ်လို့ ရပြီး ReplicaSet ကတော့ ReplicationController ရဲ့  selector ထက်  advance ဖြစ်တဲ့ **matchLabels** / **matchExpressions** တွေကို အသုံးပြုပြီး pod တွေကို match လုပ်နိုင်တဲ့ အားသာချက်ပဲ ဖြစ်ပါတယ်။ ပြောရမယ် ဆိုရင်တော့ ReplicaSet မှာ ပိုပြီး effective ဖြစ်တဲ့ ကြွယ်ဝတဲ့ selector ရှိနေတာ ဖြစ်ပါတယ်။ ဘယ်လို effective ဖြစ်တာလဲ  ? ဥပမာ env=dev နဲ့ env=qa label ရှိတဲ့ pods တွေကို တချိန်ထဲမှာ တပြိုင်နက်တည်း ReplicationController ရဲ့ selector က match မလုပ်ပေးနိုင်ပါဘူး။ ဒါပေမဲ့ ReplicaSet ကတော့ တိကျတဲ့ label တွေ မပါဘဲ (သို့)  label ရဲ့ value တွေကို အတိအကျ မသုံး မပြုဘဲ match ပြုလုပ်နိုင် ပါတယ်။ ဥပမာ env=* ကဲ့သို့  env key ပါသည့် pod အားလုံး (သို့)  env!=* ကဲ့သို့ label တွင် env key မှလွဲ၍ ကျန်သည့် pod များ အားလုံး အစ ရှိ သဖြင့် match လုပ် လို့ ရနိုင်ပါသည်။
    ReplicaSet Resource ကို yaml manifest ရေးသား ပြီး ReplicaSet Object များ ကို တိုက်ရိုက် တည်ဆောက် အသုံးပြုနိုင်သော်လည်း များသော အားဖြင့် သူချည်းဘဲ အသုံးပြုလေ့ မရှိပေ။  Deployment ဟု ခေါ်သော Deployment Controller မှ တဆင့် ReplicaSet ကို ဖန်တီး တည်ဆောက် အသုံး ပြုကြသည်။

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: hola
spec:
  replicas: 3
  selector:
    matchLabels:  # RC တွင် အသုံပြု၍မရပါ။
      app: hola
  template:
    metadata:
      labels:
        app: hola
    spec:
      containers:
      - name: hola
        image: quay.io/dther/hola
```
 
 ဒါ ကတော့ ReplicaSet ရဲ့ manifest sample ဖြစ်ပါတယ်။  Selector section မှလွဲ ၍ ကျန်တာ ReplicationController နှင့် မထူးမခြားနားပင် ဖြစ်သည်။ **matchExpressions** ဖြင့်လဲ expression များကို အသုံးပြုနိုင်သည်။ expression ဖြင့် အသုံးပြုပါက မဖြစ်မနေ key တစ်ခု၊ operator တစ်ခု နဲ့ ဖြစ်နိုင်ခြေ ရှိတဲ့  value အများကြီးကို list လုပ်ပြီး အသုံးပြုနိုင်ပါသည်။ operator ဆိုတာကတော့ ကျွန်တော် နားလည် သလောက် filter လုပ်တာနဲ့ တူပါတယ်။ လက်ရှိတွင် သိသလောက် စုစု ပေါင်း operator ၄ ခု ရှိပါတယ်။

- **In** -  selector.matchExpressions ထဲမှာ ရှိတဲ့ values တွေထဲက တစ်ခုနဲ့  pod ရဲ့ label ရဲ့ value နဲ့ မဖြစ်မနေ တူညီရပါမယ်။
- **NotIn** - အပေါ်က **In** ရဲ့ ပြောင်းပြန် ဖြစ်ပါတယ်။ selector.matchExpressions ထဲမှာ ရှိတဲ့ values တွေထဲက တစ်ခုနဲ့  pod ရဲ့ label ရဲ့ value နဲ့ လုံး၀ မတူညီရပါဘူး။
- **Exists** - ဒါကို သုံးမယ် ဆိုရင်တော့ selector.matchExpressions ထဲက values field ကို သေချာ ထည့် မလိုအပ်ပါဘူး။   key အပေါ်မူတည်ပြီ match လုပ်ခြင်းဖြစ်ပါတယ်။
- **DoesNotExist** - အပေါ်က **Exists** ရဲ့ ပြောင်းပြန် ဖြစ်ပါတယ်။ selector.matchExpressions ထဲက key field နဲ့ values field (value တမျိုးနဲ့ match မလုပ်ပေးပါဘူး) နှစ်ခုစလုံး မတူညီတဲ့ pod ရဲ့ label ကို ရွေးတဲ့ အခါ သုံးပါတယ်။

```yaml
## pod ရဲ့ label တွင် app=holo ရှိသမျှကို match မဖြစ်မနေ လုပ်ပေးသွားမှာပါ။
selector:
  matchExpressions:
    - key: app
      operator: In  ## ဒီနေရာမှာ NotIn ဆိုရင်တော့ app=holo မှ လွဲ၍ ကျန်သော app=hello,
      values:       ## app=hi, app=* တွေကို select လုပ်သွားမှာပါ။
        - hola   
```




```
Reference:                                                                                                                                                    
- https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
- kubernetes in action book
```
