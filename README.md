# Splunk for Docker and Kubernetes

## Reference
- https://hub.docker.com/_/splunk-enterprise
- https://github.com/splunk/docker-splunk


## Kubernetes deployment to the local workstation (macOS only)

## Prep your local workstation (macOS only)
1. Clone this repo and work in it's root directory
1. Install Docker Desktop for Mac (https://www.docker.com/products/docker-desktop)
1. In Docker Desktop > Preferences > Kubernetes, check 'Enable Kubernetes'
1. Click on the Docker item in the Menu Bar. Mouse to the 'Kubernetes' menu item and ensure that 'docker-for-desktop' is selected.

Deploy (run these commands from the root folder of this repo)
~~~
mkdir -p /Users/Shared/Kubernetes/persistent-volumes/splunk
kubectl apply -f ./kubernetes/splunk-local-pv.yaml
kubectl apply -f ./kubernetes/splunk.yaml
~~~

See the status of the pods
~~~
kubectl get pods
kubectl describe pods
kubectl logs SPLUNKPODNAME
~~~

open it in a browser
~~~
open http://127.0.0.1:8000
~~~

Note: The default admin password is adminadmin

Delete
~~~
kubectl delete -f ./kubernetes/splunk.yaml
kubectl delete -f ./kubernetes/splunk-local-pv.yaml
rm -rf /Users/Shared/Kubernetes/persistent-volumes/splunk
~~~