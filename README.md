# k8s-alpine
ansible playbook for installing k8s klaster. based on  https://wiki.alpinelinux.org/wiki/K8s documentations.

ansible-playbook -i inventory k8s-cluster.yaml

looks like ipw4 forwarding doesn't save after reload, so need to do this:
- echo net.ipv4.ip_forward=1 | tee -a /etc/sysctl.conf && sysctl -p
this ansible playbook used Alpine linux vmware installation (2 vCPU, 4Gb RAM) and can be started after setup-alpine script (https://wiki.alpinelinux.org/wiki/Installation#Installation_Handbook) after script was finished we have k8s node ready for using as control-pane or worker.

using HELM for installing Ingress Controller (https://kubernetes.github.io/ingress-nginx/deploy/#quick-start)
helm upgrade --install ingress-nginx ingress-nginx   --repo https://kubernetes.github.io/ingress-nginx   --namespace ingress-nginx --create-namespace
if we using claster inside LAN for testing purpose we need to change EXTERNAL_IP:
kubectl edit service ingress-nginx-controller --namespace=ingress-nginx
spec:
  externalIPs:
  - <eth0 IP>
after configuring Ingress Controller we can use INgres for exposing resourses (dasbord, application etc) - just need create kind: Ingress resourse, for example - use dashboard-ingress.yaml

or we can use this command helm install nginx-ingress ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace --set controller.service.externalIPs[0]=_PUT_HERE_EXTERNAL_IP
