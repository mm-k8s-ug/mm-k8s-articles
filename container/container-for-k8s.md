# Containers for Kubernetes

လက်ရှိ ကမ္ဘာပေါ်မှာ container နဲ့ အလုပ်လုပ်နေတဲ့ organization တွေ အများကြီးရှိပါတယ်။ Container ဆိုသည်မှာ Docker တစ်ခု တည်းကို ပြောခြင်းမဟုတ်ဘဲ၊ Docker ဆိုသည်မှာ container ဟုခေါ်သော အရာ၏ အမျိုးအစား တစ်ခု ပင်ဖြစ်သည်။ Kubernetes သည် ထို container များစွာကို စီးချက်ဝါးချက် လိုက်လျော ညီညီ open standard နှင့် လွယ်ကူစွာ မောင်းနှင်နိုင်သည့် tool တစ်ခုဖြစ်သည်။ containers orchestration tool ဟုလည်း ခေါ်သည်။ container တွေရဲ့ အစောပိုင် ကာလ မှ ယခု အချိန်ထိ Docker သည် container နဲ့ ပက်သက်ရင် လူအများသိသည့် သမိုင်းဝင် container engine တစ်ခုပင်ဖြစ်သည်။ ဒီနေရာမှာ Docker ကို အဓိက ပြောသွားမှာ မဟုတ်ပါဘူး။ container တွေ၏ အစောပိုင်းကာလ တွင် container ပြုလုပ် ဖန်တီးခြင်း၊ container အသုံးပြု deployment Lifecycle တို့တွင် Docker သည် vendor-lock ပုံစံဖြင့် Developer များ၊ system Engineer များ အစရှိသည့် IT engineerများ လက်ထဲ ရောက်ရှိ ထိတွေ့ စမ်းသပ်၊ အသုံးပြု ခဲ့ကြပြီ နောက်ပိုင်တွင် လည်း သူ၏ တစ်ခြားသော feature အသစ်တွေ ကြောင့် နာမည်ရ လူသိများ၊ လူကြိုက် များလာသည်မှာ ယခုဖြစ်သည်။ ယခု အချိန်တွင် community များမှ contribute လုပ်သော အပြည့်အ၀ OpenSource ဖြစ်သည့် တခြား သော container engine များလဲ ထွက်ပေါ်လာပြီး အသုံးပြုလို့ရသည့် အခြေအနေ များသို့ ရောက်ရှိနေပြီ ဖြစ်သည်။ ထိုကဲ့သို့ တခြားသော container များ ထွက်ပေါ်လာခြင်းသည် kubernetes အနေဖြင့်လည်း Docker container engineer တစ်ခု တည်းအပေါ် မှီခိုစရာ မလိုအပ်ဘဲ၊ ပို၍ open ဖြစ်ပြီး independent လည်းပိုဖြစ်လာပါတယ်။

Container ဆိုသည့် ဝေါဟာရတွင် - container engine, container runtime, container image, container, container image layer, container registry အစရှိသည့်တို့ သည်အကြမ်းအားဖြင်ပါဝင်တယ်။ ဒီနေရာမှာ container engine နဲ့ container runtime ဘာကွာသလဲ? အရမ်းမကွာပါဘူး ဒါပေမဲ့ အတိအကျတော့ မတူညီပါဘူး။ဒါဆို ကျွန်တော်တို. container engine နဲ့ container runtime ဘာတွေ ကွာခြားသွားလဲဆိုတာကို 

**Container engine** သည် container image များ ပြုလုပ်ဖန်တီးခြင်း၊ container registry အပေါ်မှ container image များ ဆွဲချခြင်း လုပ်ငန်းစဉ် များ၊ container image များကို container အနေဖြင့် Os အပေါ်တွင်လွယ်ကူစွာ run ခြင်း၊ အသုံးပြု သူ၏ ဆန္ဒအလျောက် request များကို လက်ခံခြင်း response ပြန်ခြင်း စသည့် container နဲ့ ပတ်သက်သည့် operation များ ကို လုပ်ဆောင်နိုင်သည့် software တစ်ခု ဖြစ်ပါသည်။ 

**Container runtime** သည် container တစ်ခုကို run နိူင်အောင် အဓိက ပြုလုပ်ပေးသည့် container engine ၏ low-level component တစ်ခုဖြစ်သည်။ container runtime တစ်ခု ရှိရုံဖြင့် container များကို run နိုင်သည်၊ အသုံးပြုနိုင်သည်။ Open Containers Initiative \(OCI\) ၏ runc သည် container runtime များ ပြုလုပ်ဖန်တီးခြင်း၊ implement ပြုလုပ်ခြင်းတွင် reference ယူရသည့် runtime standard တစ်ခု ဖြစ်သည်၊ container spawning နဲ့ running ပြုလုပ်နိုင်သည့် command line tool တစ်ခုလဲ ဖြစ်သည်။ OCI သည် အလွယ်ပြောရလျင် ပေါ့ပါးတဲ့၊ stucture မှန်တဲ့၊ container format တွေ၊ container runtime အတွက် standard တွေကို ဖန်တီး ထိန်သိမ်းပေးတဲ့ project တစ်ခုဖြစ်ပါတယ်။ လက်ရှိ အချိန်အခါ မှာတော့ ဒီ Runtime Specification နဲ့ Image Specification ကို OIC က develope ပြုလုပ်ထားပါတယ်။ container runtime ရဲ့ တာဝန်တွေကတော့ -

* Container Engine ကနေ provide လုပ်ပေးတဲ့ container mount point တွေကို container running လုပ်တဲ့ အခါ အသုံးပြုပေးတယ် \(Consuming လုပ်တယ်လို့လဲ အခေါ်အဝေါ်အနေနဲ့ သုံးတယ်\)
* Container Engine ကနေ provide လုပ်ပေးတဲ့ metadata တွေကိုလဲ container running ဖို့ အသုံးပြုပေးတယ်
* Containerized process တွေ စတင်ဖို့ အတွက် kernel နဲ့ ဆက်သွယ်ချိတ်ဆက် ပေးတယ်။\(system call တွေလုပ်ဖို့\)
* Setting up cgroups
* Setting up SELinux Policy
* Setting up App Armor rules - အစ ရှိတဲ့ လုပ်ဆောင် ချက်တွေကို လုပ်ဆောင်ပေးတယ်။

Docker Engine ကတော့ Kubernetes ရဲ့ default/native container engine အဖြစ် ရွေးချယ် သတ်မှတ် အသုံးပြုခြင်းခံရပြီ သိပ်မကြာခင်မှာတော့ ဖယ်ရှားခြင်းခံရလာနိုင်ဖွယ် ရှိသည်။ ၎င်း Docker engine အစား CRI\(Container Runtime Interface\) plugin system ကို အစားထိုးမည့် အချိန် ရောက်ရှိလာတော့မှာ ဖြစ်ပါတယ်။ RKT, CRI-O, Containerd အစရှိသည့် တခြားသော community support container engine များသည်လည်း ယခု အချိန်တွင် နာမည်ကြီး ပြီးပိုရျ်လည်းရင့်ကျတ်လာကြပါတယ်။ 

pod အတွင်းရှိ container ကို run ရန် kubernetes သည် kubelet မှ တဆင့် container runtime အသုံးပြုသည်။ ထိုသို့ ပြုလုပ်နိုင်ရန် container runtime interface\(cri\) သည် kubelet နှင့် container runtime ကိုလွယ်ကူစွာ integration ပြုလုပ်နိုင်ရန် အဓိကတာဝန်ယူပေးသည်။ အလွယ်တကူ ပြောရလျင် kubelet ၏ အတွင်းပိုင် ကို နက်နက်နဲနဲ သိရှိစရာမလိုအပ်ဘဲ မည်သည့် container runtime\(docker,cri-o,containerd,rkt,etc...\) နှင့်မဆို kubernetes ၏ kubelet နဲ့ အလွယ် တကူ integrate ပြုလုပ်နိုင်ရန် \(API အတွက် protobuf method၊ specifications နှင့် libraries အစရှိသည်တို့ နှင့်\) interface တစ်ခု တီထွင် ထုတ်လုပ်ပေးထားခြင်းဖြစ်ပါသည်။



{% hint style="info" %}
Reference:

* [https://github.com/kubernetes/cri-api/](https://github.com/kubernetes/cri-api/)
* [https://kubernetes.io/docs/setup/production-environment/container-runtimes/](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)
* [https://kubernetes.io/blog/2016/12/container-runtime-interface-cri-in-kubernetes/](https://kubernetes.io/blog/2016/12/container-runtime-interface-cri-in-kubernetes/)
* [https://kubedex.com/kubernetes-container-runtimes/](https://kubedex.com/kubernetes-container-runtimes/)
{% endhint %}

