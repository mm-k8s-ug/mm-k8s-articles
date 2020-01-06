# RKT

RKT \(rocket\) သည် Docker ပြီးလျင် လူသိများပြီး mature ဖြစ်သည့် Container Engine တစ်ခုဖြစ်ပါသည်။ Docker မှာကဲ့သို့ပဲ CLI client tools ရှိသည်။ 2014 ခုတွင် CoreOS မှ စတင်မိတ်ဆက်ပြီး ယခု အခါ တွင် Cloud Native Computing Foundation သို့ လှူဒါန်းခဲ့ပြီးဖြစ်သည်။ rkt သည် security အတွက် အသားပေးထုတ်လုပ်ထားပြီး single binary အနေနဲ့ script တွေ၊ DevOps pipeline တွေ မှာ အလွယ်တကူ အသုံးပြုနိုင်သည်။ နောက်ထပ် ထူးခြားတာတစ်ခုက build-in pod unit ပါရှိ တည်ဆောက်နိုင်ခြင်းဖြစ်သည်။ RTK သည် appc specification ကို အသုံးပြုပြီ Docker, appc နှင့် OCI images တွေကို run နိုင်ပါသည်။ rkt သည် Docker ကို လွယ်ကူစွာ အစားထိုးဖို့ မဖြစ်နိုင်သေးပေးမဲ့လည်း CRI-O သည် Kubernetes ၏ official incubator မဖြစ်လာခင် အချိန် အထိ Docker ကို အစားထိုးဖို့ attention အပေးခံရဆုံး Conatiner engine တစ်ခုဖြစ်သည်။ စိတ်မကောင်းစွာဖြင့် ယခုအချိန်တွင် rkt Project သည် CNCFမှ အပြည့်အဝဆက်လက်ပြီး support မလုပ်တော့ပါ။

![](../.gitbook/assets/rocket-logo-horizontal-color.png)

{% hint style="info" %}
Reference:

* [https://coreos.com/rkt/docs/latest/app-container.html\#pods](https://coreos.com/rkt/docs/latest/app-container.html#pods)
* [https://github.com/appc/spec/blob/master/spec/pods.md\#app-container-pods-pods](https://github.com/appc/spec/blob/master/spec/pods.md#app-container-pods-pods)
* [https://coreos.com/rkt/](https://coreos.com/rkt/)
{% endhint %}

