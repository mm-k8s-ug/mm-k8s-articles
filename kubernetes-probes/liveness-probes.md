# Liveness Probes

![liveness probes](../.gitbook/assets/ezgif.com-crop-1.gif)

Container က အဆင်ပြေစွာ running ဖြစ်နေလား၊ \(alive\) ဖြစ်နေလား၊ ဆိုတာကို သိရှိဖို့၊ action ယူဖို့၊ liveness probes ကို အသုံးပြုနိုင်ပါတယ်။ အဲလို စစ်ဆေးနိုင်ဖို့ liveness probe ကို container တစ်ခု ချင်းစီ အတွက် pod definition မှာ ထည့်ပြီး configure ပြုလုပ်ရပါတယ်။ configure ပြုလုပ်ပြီး pod တည်ဆောက်ပြီးတဲ့ အခါ kubelet က configure ပြုလုပ်တဲ့ ပုံစံ အပေါ်မူတည် ပြီးတော့ liveness probe ကို အချိန်မှန် ပို့ပါတယ်။ liveness probe က Giving up မလုပ်ခင် အချိန်ထိ Probe ဘယ်နှစ်ကြိမ် fail၊ fail ပြသနာ မရှိပေမဲ့ liveness probe က giving up လုပ်တဲ့ အချိန်မှာ တော့ container ကို restart ချပါတယ်။ အဲတော့ liveness probe giving up မလုပ်ခင်မှာ probe success ဖြစ်မှသာ pass ဖြစ်မှာပါ။ liveness probe ကို method သုံးမျိုး ဖြင့် check နိုင်တယ်။ ဒါတွေကတော့ -

* **An HTTP GET probe** - HTTP probe က container ရဲ့ IP၊ Port နဲ့ Path ကို Get Request ပေး ပို့ ပြီးတော့ application မှ ပြန်လာတဲ့ HTTP response code `2xx` နဲ့ `3xx` ကလွဲပြီး အခြားသော error code များ ဒါမှ မဟုတ် လုံး၀ response မပြန်တဲ့ အခါ probe fail တယ်လို့ ယူဆပါတယ်။
* **A TCP Socket probe** -TCP probe ကတော့ container ကို TCP connection နဲ့ သတ်မှတ်ထားတဲ့ Port ကို ကြိုးစားပြီ connect လုပ်ပါတယ်။ အဲ အချိန်မှာ connection က established ဖြစ်သွားရင် probe success ဖြစ်ပြီး အဲလို မှ မဟုတ်ရင်တော့ fail တယ်လို့ ယူဆပါတယ်။
* **An Exec probe** -  ဒီ probe ကတော့ container အထဲမှာ command ကို executes လုပ်ပြီးတော့ အသုံးပြုပါတယ်။ တကယ်လို့ command ရဲ့ exit status code က `0` မဟုတ်ခဲ့ဖူးဆိုရင် fail ဖြစ်တယ်လို့ ယူဆပါတယ်။

{% code title="hola-liveness-probe.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hola-liveness
spec:
  containers:
  - image: quay.io/dther/hola:unhealthy
    name: hola
    livenessProbe:
      httpGet:
        path: /
        port: 8080
```
{% endcode %}

ဒါကတော့ liveness probe ကို HTTP GET နည်းလမ်းဖြင့် စမ်း run ကြည့်နိုင်မဲ့ manifest ပါ။ ဒီ pod ကို create လုပ်လိုက်ပြီးတော့ `kubectl get pod -w`\(watch\) ဒီ command ကို run လိုက်လျှင် အောက်က ပုံ အတိုင်း အချိန်နဲ့ အလိုက် pod ရဲ့ အခြေအနေ နဲ့ restart count တွေကို တွေ့ရမှာပါ။

![](../.gitbook/assets/getpod.png)

restart ကျရတဲ့ အကြောင်းအရင်မှာ `statuscode: 500` နဲ့ liveness probe ရဲ့ HTTP probe failed သွားလို့ ဖြစ်ပါတယ်။ အောက်က ပုံက `Event` ကို ကြည့်ပြီး သိနိုင်ပါတယ်။ ဒါပေမဲ့ HTTP status code 500 ကြောင့် container က အမှန် တကယ် restart ကျသွားတာ မဟုတ်ပါဘူး။

![](../.gitbook/assets/event1.png)

 ပထမ အကြိမ် restart ကျတဲ့ အချိန်မှာ kubectl command ဖြင့် `describe` လုပ်ကြည့်မယ်ဆိုရင် အောက်က ပုံကို မြင်ရမှာပါ။ `Liveness:` ဆိုတဲ့ အကြောင်းမှာ Liveness probe ကို ဘယ်လို configure ချထားလဲ ဆိုတာ နှင့် တခြား properties ကို ဖော်ပြ ထားပါတယ်။ အဲဒီမှာ `delay=0s` ဆိုတာကတော့ container start စစ ချက်ချင်း probe ကို စပြီ ပို့လွှတ်ပါတယ်၊ `timeout=1s` ဆိုတာကတော့ probe လုပ်တဲ့ ကြာချိန်ပါ \(ဥပမာ - probe ကို ၁ စက္ကန့် အတွင်း response မပြန်နိုင်ရင် probe fail တယ်လို့ သတ်မှတ်တာပါ\)၊ `period=10s` ကတော့ ၁၀ စက္ကန့် တိုင်းမှာ probe ကို kubelet ကနေ ပို့ပြီ စစ်ပါတယ်။ `#failure=3` ကတော့ ခုနက period ၃ခါ \(၁၀ x ၃ = ၃၀ စက္ကန့်\) ပြည့်တဲ့ အခါမှာ giving up ဖြစ်ပြီး container ကို restart ချသွားတာပါ။ \(`#success=1` ကတော့ ဒါရဲ့ ဆန့်ကျင့်ဘက်ပါ။\) အဲတော့ အချိန် တစ်ခု ရောက်တဲ့ အခါမှာ ပထမ ဆုံး အကြိမ် probe က HTTP status 500 response ကို ရရှိတဲ့ အခါမှာ တစ်ခါ စပြီးတော့ probe fail ပါတယ်။ နောက် ၁၀ စက္ကန့် ကြာတဲ့ အချိန်မှာ 500 response ကို ထပ်ပြီး လက်ခံရရှိတဲ့ အခါမှာ probe fail တာ ၂ ကြိမ် ဖြစ်သွားပါပြီ၊ နောက် ၁၀ စက္ကန့်မှာ ထပ် fail ရင် ၃ ကြိမ် ပြည့်လို့ probe က action ယူပြီး container ကို restart ချမှာ ဖြစ်ပါတယ်။

![](../.gitbook/assets/error-137.png)

နောက် တခု ထပ် ပြောချင်တာကတော့ describe result မှာ `Exit Code: 137` ပါ၊ ဒါကတော့ external signal ပို့လွှတ်ပြီးတော့ process ကို terminated ပြုလုပ်ခဲ့တယ်၊ kill ခဲ့တယ် ဆိုတဲ့ အဓိပ္ပါယ်မျိုးပါ၊ 137 သည် `128 + n` ဖြစ်ပါတယ်။ ဒီနေရာမှာ `n` တန်ဖိုးကတော့ 9 ဖြစ်ပါတယ်။ 9 သည် ကျွန်တော်တို့ process တွေကို Kill တဲ့ အခါ အသုံးပြုတဲ့ `SIGKILL` signal number ဖြစ်ပါတယ်။ ဒါကြောင့်မလို့ process က force လုပ်ပြီးတော့ kill ခံရတာဖြစ်ပါတယ်။ ဒါသည် probe က ၃ ကြိမ် fail လို့ giving up လုပ်သွားတဲ့ အခါမှာ container ကို kill လိုက်တာ ဖြစ်ပါတယ်။ Container ကို kill လိုက်တယ်ဆိုတာက လုံး၀ အသစ် တစ်ခု ပြန်တည်ဆောက်လိုက် ခြင်းဖြစ်ပါတယ်။ ဒါဟာ ကျွန်တော်တို့ ပြော အလွယ်ပြောပြောနေတဲ့ probe fail တဲ့ အခါမှာ container restart ချသွားခြင်းဖြစ်ပါတယ်။

| Exit Code Number | Meaning | Example | Comments |
| :---: | :---: | :---: | :---: |
| 128 | Invalid argument to exit | exit 3.14159 | exit takes only integer args in the range 0 - 255 \(see first footnote\) |
| 128+n | Fatal error signal "n" | kill -9 $PPID of script | $? returns 137 \(128 + 9\) |

တစ်ချို့ application အမျိုးအစားတွေ က application startup time ကြာကြပါတယ်။ များသော အားဖြင့် java application တွေ မှာ တွေ့ရတတ်ပါတယ်။ ဒီလို အခြေအနေမျိုးမှာ container create လုပ်လုပ်ခြင်း \(`delay=0s`\) probe ကို စလုပ်တာ အဆင်မပြေနိုင်ပါဘူး၊ ဒီလို အခြေနေမျိုး အတွက် application startup time ကို စောင့် ဖို့ probe ရဲ့ properties တစ်ခု ဖြစ်တဲ့ `initialDelaySeconds: 30s` ဖြင့် probe စ လုပ်မဲ့ အခြေနေကို ထိန်းထားနိုင်ပါတယ်။ ဒီ properties သာ pod manifest ထဲ ထည့်ပြီ pod ကို create လုပ်ခဲ့မယ်ဆိုရင် kubelet က container တည်ဆောက်ပြီး ၃၀ စက္ကန့် ကြာမှ ပထမဆုံး probe ကို စတင် ပို့လွှတ်မှာ ဖြစ်ပါတယ်။ ဒါကတော့ kubernetes ကနေ kubelet မှ တဆင့် container တစ်ခု ကောင်းမွန်စွာ အသက်ရှိမရှိ၊ health check နိုင်တဲ့ liveness probe ရဲ့ အကြမ်းဖျင်း ဖြစ်ပါတယ်။

