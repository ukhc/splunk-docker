# Splunk for Docker and Kubernetes

## Reference
- https://hub.docker.com/_/splunk-enterprise
- https://github.com/splunk/docker-splunk

## Docker deployment to the local workstation

~~~
docker run -d -p 8000:8000 -e 'SPLUNK_START_ARGS=--accept-license' -e 'SPLUNK_PASSWORD=<password>' store/splunk/splunk:7.3
~~~

## Kubernetes deployment to the local workstation (macOS only)

### Prep your local workstation (macOS only)
1. Clone this repo and work in it's root directory
1. Install Docker Desktop for Mac (https://www.docker.com/products/docker-desktop)
1. In Docker Desktop > Preferences > Kubernetes, check 'Enable Kubernetes'
1. Click on the Docker item in the Menu Bar. Mouse to the 'Kubernetes' menu item and ensure that 'docker-for-desktop' is selected.

### Deploy (run these commands from the root folder of this repo)
~~~
mkdir -p /Users/Shared/Kubernetes/persistent-volumes/splunk
kubectl apply -f ./kubernetes/splunk-local-pv.yaml
kubectl apply -f ./kubernetes/splunk.yaml
~~~

### See the status of the pods
~~~
kubectl get pods
kubectl describe pods
kubectl logs SPLUNKPODNAME
~~~

### Open it in a browser
~~~
open http://127.0.0.1:8000
~~~

Note: The default admin password is adminadmin

### Delete
~~~
kubectl delete -f ./kubernetes/splunk.yaml
kubectl delete -f ./kubernetes/splunk-local-pv.yaml
rm -rf /Users/Shared/Kubernetes/persistent-volumes/splunk
~~~

## How to override values in the Kubernetes deployment

### Use this pattern in a script
~~~
# copy the file to a temp file
cp ./kubernetes/splunk.yaml yaml.tmp

# replace the values with sed in the temp file
sed -i '' 's/storage:.*/storage: default/' yaml.tmp

# deploy from the temp file
kubectl apply yaml.tmp

# delete the temp file
rm -f yaml.tmp
~~~

### Values to override

storage: 5Gi (the size of the persistent volume claim)
~~~
sed -i '' 's/storage:.*/storage: 20Gi/' yaml.tmp
~~~

password: YWRtaW5hZG1pbg==  (the password for the splunk admin account)
~~~
# base64 encode the password, use the result in the sed
echo -n admin | base64

sed -i '' 's/password:.*/password: Base64EncodedPassword/' yaml.tmp

# base64 decode the password, if you need to see what it is
echo YWRtaW5hZG1pbg== | base64 --decode
~~~

storageClassName: splunk-storage  (if you're deploying to a cloud provider, look at what they offer)
~~~
sed -i '' 's/storageClassName:.*/storageClassName: managed-premium/' yaml.tmp
~~~