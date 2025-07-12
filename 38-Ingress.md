
# ✅ Expose Application Using Ingress on Minikube

## 1️⃣ Create Deployment and Service

Make sure your app is deployed and a service is exposed.

---

## 2️⃣ Create Ingress Resource

```yaml
# 38-ingress.yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-python-app
spec:
  rules:
  - host: "foo.bar.com"  # You can change this
    http:
      paths:
      - pathType: Prefix
        path: "/bar"
        backend:
          service:
            name: python-app-svc  # Change to your actual service name
            port:
              number: 80
````

### Check Ingress

```bash
kubectl get ingress
```

Sample Output:

```
NAME                 CLASS    HOSTS         ADDRESS   PORTS   AGE
ingress-python-app   <none>   foo.bar.com             80      26s
```

> 📌 At this point, the `ADDRESS` field is **empty**, so the domain won’t work yet.

---

## 3️⃣ Enable Ingress Controller on Minikube

Install the Ingress Controller:

```bash
minikube addons enable ingress
```

Check if Ingress pods are running:

```bash
kubectl get pods -n ingress-nginx
```

---

## 4️⃣ Now Check Ingress Again

```bash
kubectl get ingress
```

Output should now show an address:

```
NAME                 CLASS    HOSTS         ADDRESS          PORTS   AGE
ingress-python-app   <none>   foo.bar.com   192.168.59.193   80      20m
```

---

## 5️⃣ Fix Host Resolution

Since this is **domain-based routing**, update your hosts file on Windows:

### 🔧 Add `foo.bar.com` to Hosts File

1. Open **Notepad as Administrator**

2. Open file:

   ```
   C:\Windows\System32\drivers\etc\hosts
   ```

   *(Change file type to “All Files” to see it)*

3. Add this line at the bottom:

   ```
   192.168.59.193 foo.bar.com
   ```

4. Save the file.

5. Run this in Command Prompt (Admin):

   ```bash
   ipconfig /flushdns
   ```
> `ipconfig /flushdns` clears the DNS cache so your system recognizes changes made to the hosts file immediately.
---

## 6️⃣ Update Ingress Path to `/demo`

Your Django app supports `/demo`, not `/bar`.
Update your Ingress YAML:

```yaml
path: "/demo"
```

Apply the change:

```bash
kubectl apply -f ingress.yaml
```

---

## ✅ Test It

### In Terminal:

```bash
curl -L http://foo.bar.com/demo -v
```

### In Browser:

```
http://foo.bar.com/demo
```

You should now see your Django app working correctly! 🎉
```

