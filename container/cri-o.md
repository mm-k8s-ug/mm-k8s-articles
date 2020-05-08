# CRI-O

CRI-O သည် kubernetes Container Runtime Interface နဲ့ Open Container Initiative ရဲ့ runtimes ကို implement ပြုလုပ်ထားခြင်းဖြစ်သည်။ CRI-O သည် lightweight ဖြစ်ပြီး Kubernetes ရဲ့ kubelet နှင့် အသင့်လျော်ဆုံး Optimize ဖြစ်ရန် Design ဆွဲထားခြင်းဖြစ်သည်။ runc ဆို default container runtime အဖြစ် အသုံးပြုပြီး Kata Containers ကိုလဲ container runtimes အဖြစ် အသုံးပြုနိုင်သည်။ Pod networking အတွက် CNI\(Container Network Interface\) ကို အသုံးပြုပြီး အချို့သော network plugin များဖြစ်သော Flannel, Weave and OpenShift-SDN တို့နဲ့ စမ်းသပ်ပြုလုပ်ပြီးပြီ ဖြစ်ပါသည်။ OpenSource စစ်စစ်ဖြစ်ပြီး Redhat, Intel, SUSE, Hyper, IBM တို့မှ ဝိုင်းဝန်း contribute လုပ်ထားသော community-driven project တစ်ခုလည်းဖြစ်ပါသည်။ CRI-O သည် ယခုအချိန်တွင် Cloud Native Computing Foundation \( CNCF \) မှာ incubate လုပ်နေသော Project တစ်ခုလည်းဖြစ်ပါသည်။

![](../.gitbook/assets/68747470733a2f2f63646e2e7261776769742e636f6d2f6b756265726e657465732d736967732f6372692d6f2f6d61737465722f6c6f676f2f6372696f2d6c6f676f2e737667.svg)

{% hint style="warning" %}
ယခု နောက်ပိုင်းတွေမှာ CRI\(container runtime interface\)၊ CNI\(container network interface\)၊ CSI\(container storage interface\) အစရှိသည့် interface များ ပြုလုပ် အသုံးပြုခြင်းတွင်ကျယ်လာပါသည်။ Interface များသည် adaptable ဖြစ်သည်။
{% endhint %}

{% hint style="info" %}
Reference:

* [https://cri-o.io/](https://cri-o.io/)
* [https://github.com/cri-o/cri-o/blob/master/README.md](https://github.com/cri-o/cri-o/blob/master/README.md)
{% endhint %}

