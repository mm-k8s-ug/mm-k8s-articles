# Service

## Kubernetes Service

Kubernetes ရဲ့ Service အကြောင်းမပြောခင်မှာ မှာ Kubernetes ရဲ့ resource တွေထဲကမှ အသေးဆုံး Unit ဖြစ်တဲ့ Pod တွေရဲ့ သက်တမ်းအကြောင်းလေးနည်းနည်းအစချီလိုက်မယ်။ Deployment တစ်ခုက create လုပ်လိုက်တဲ့ Podတွေဟာ replica count အရ K8S cluster အတွင်းမှာ တည်ဆောက်သွားမယ်။ ကျွန်တော်တို့က Replica Count ကိုထပ်တိုးလိုက်တယ်ဆိုရင် လက်ရှိ Pod ၃လုံးကနေ ကိုယ်တိုးလိုက်တဲ့ အရေအတွက် အလိုက်ပြောင်းသွားမယ် နောက်ပြီးပြန်လျော့လိုက်တယ်ဆိုရင် လက်ရှိ Podအရေအတွက်ကနေလျော့သွားမယ် Pod တစ်လုံးပဲကျန်တော့တာမျိုးဖြစ်နိုင်သလို Pod တစ်လုံးမှမကျန်တဲ့အထိလည်းဖြစ်သွားနိုင်တယ်။ နောက်ထပ်တစ်ခုက လက်ရှိ Pod runနေတဲ့ Node Down သွားတဲ့ caseတွေမှာ Node နဲ့အတူ Kubernets Cluster မှာ runနေတဲ့ Pod ကပါအတူတူလိုက် Down သွားပါတယ်။ ဆိုလိုချင်တာက Pod‌တွေရဲ့သက်တမ်းကကြာကြာမခံဘူး။ Pod တစ်ခု IP Address တစ်ခုဆိုပေမယ့် Podတွေကစောစောကပြောသလိုကြာကြာမခံတဲ့ အတွက် IP Address ကအမြဲအပြောင်းအလဲရှိတယ်။ ဘယ်လိုနေရာမျိုးမှာလည်းဆိုရင် အရင်က Non-Kubernetes ခေတ်မှာကျွန်တော်တို့က Web Server ကနေ Database Serverကို connect မယ်ဆိုရင် Web Server ရဲ့ Configuration မှာ DBHost ကဘယ် IP Address ပါဆိုပြီးထည့်ထားပေးရတယ် Kubernetes မှာကြတော့ ကျွန်တော်တို့ Web Server Pod ကနေ Database Pod ကိုဘယ် IP Address ပါဆိုပြီးချိတ်ထားမယ်ဆိုပါစို့ အပေါ်မှာကျွန်တော်ပြောပြခဲ့သလို Podတွေက IP Addressတွေအမြဲပြောင်းနေတဲ့အတွက် IP Address တစ်ခါပြောင်းတိုင်း Configuration တစ်ခါလိုက်ပြောင်းပေးရမယ်ဆိုရင် အဆင်မပြေတော့ဘူး။ အဲ့တော့ကျွန်တော်တို့မှာ IP Address အတည်တကျဖြစ်ဖို့လိုလာတယ်။ အဲ့ဒါကိုဖြေရှင်းဖို့ Kubernetes မှာ Service ဆိုတဲ့ Resource လိုအပ်လာပါတယ်။

Kubernets ရဲ့ **Service** ဆိုတာကတော့ Cluster ထဲမှာရှိတဲ့ များစွာသော Node တွေပေါ်မှာ Runနေတဲ့ Pod တွေကို Label တူရာတစ်စုတစ်စည်းတည်း စုပေးထားတယ်။ အလွယ်ပြောရင် Label တူ Pod Group လေးတွေပေါ့ဗျာ။ အဲ့ဒီ Label တူ Pod Group တွေကို internally ဖြစ်ဖြစ် externally ဖြစ်ဖြစ် အလွယ်တစ်ကူ access လုပ်လို့ရအောင်ပြုလုပ်ပေးတဲ့ Kubernets resource တစ်ခုဖြစ်ပါတယ်။ Kubernets Service က Label Selector တူရာ Podတွေကို အဲသည်လိုစုပေးထားတဲ့အတွက် ဒီ Serviceရဲ့အနောက်မှာ Pod အရေအတွက်ဘယ်လောက်ပဲရှိရှိ ကျွန်တော်တို့ကဂရုစိုက်စရာမလိုတော့ဘူး။ ဥပမာ ကျွန်တော်တို့မှာ Frontend Pod Group တစ်ခုရှိမယ် အဲဒီ Frontend Pod Group ထဲမှာ Pod ၃လုံးရှိမယ်ဆိုရင် Service က ထို Pod Group ကိုကိုယ်စားပြုပြီး IP Address တစ်ခုထွက်မယ် အဲ့သည်နောက် External Client က Frontend Service ကိုလာခေါ်ရင် Frontend Service ကသူအောက်မှာရှိတဲ့ Podတွေဆီကို Traffic LoadBalance လုပ်ပေးမယ်။ Frontend Service ရဲ့ IP address က သူကိုမဖြတ်မချင်း ဒီအတိုင်းရှိနေမှာဖြစ်လို့ Podတွေလို ခဏခဏ IP ပြောင်းတာမျိုးလည်းမဖြစ်တော့ဘူး။ နောက်တစ်ခုက 2 Tier architecture လို Frontend Web Service ကနေ Backend Database Service ကို လှမ်းပြီး connect လုပ်တဲ့နေရာမှာလည်း အဆင်ပြေသွားတာပေါ့။

![](https://i.imgur.com/4aUuivD.png)

![](https://i.imgur.com/ahpQU73.png)

ပုံမှာဆိုရင် Client အနေနဲ့

