# Kubernets Helm Charts Example 1

The code in this repository is based on the
[Package Management with Helm](https://youtu.be/zka4lJbA-y4)
video.

Note, failed getting wordpress to install in a non / url.
So for future reference install wordpress to / and other sites to
/some-thing-else.

```bash
kind get clusters
kind create cluster --config kind-cluster-conf-1.yaml
kind get clusters
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
# simple non helm install
kubectl apply -f echo-pod-svc-ingress.yaml
kubectl delete -f echo-pod-svc-ingress.yaml
# helm nginx webserver install
helm repo list
helm repos add bitnami https://charts.bitnami.com/bitnami
helm search repo nginx
helm uninstall webserver
helm install -f webserver.helm.vals.yaml webserver bitnami/nginx --dry-run --debug
helm install -f webserver.helm.vals.yaml webserver bitnami/nginx
kubectl apply -f webserver.ingress.yaml
kubectl exec -it webserver-nginx-5d88f4d8b-fkgvr -- curl webserver-nginx.default.svc.cluster.local
# helm wordpress install
helm install -f wordpress.helm.vals.yaml wp bitnami/wordpress
kubectl apply -f wordpress.ingress.yaml
helm uninstall wp
kubectl delete pvc data-wp-mariadb-0
# change base url via commands below - i did not get it to work
kubectl exec -it wp-mariadb-0 -- /bin/bash
mysql --user=root --password=test1234
use bitnami_wordpress;
update wp_options set option_value = 'http://127.0.0.1/wordpress' where option_name = 'siteurl';
select * from wp_options where option_name = 'siteurl';
update wp_options set option_value = 'http://127.0.0.1/wordpress' where option_name = 'home';
select * from wp_options where option_name = 'home';
\q
exit
# cause reload of wp with latest db info
kubectl delete pod wp-wordpress-55dfd5f79d-x768r
```
