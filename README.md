# Put Oracle WebLogic 12c on OpenShift 4.x

## Prerequisites
* git client
* helm client
* openshift command line - oc
* docker

## Steps
1. Get WebLogic Kubernetes Operator from Oracle's github (I'm using the version 2.5)
2. Install the operator to OpenShift 4.x via helm
3. Prepare your WebLogic container image with domain home by yourself or download an existing one
4. Expose your endpoint to public network


## Get WebLogic Kubernetes Operator from Oracle's github
```
git clone https://github.com/oracle/weblogic-kubernetes-operator
```

### Log into OpenShift cluster

<p><code>$ oc login https://api.cluster-xxxx.xxxx.openshift.com:6443</code><br/>
Authentication required for https://api.cluster-xxxx.xxxx.openshift.com:6443:6443 (openshift)<br/>
Username: admin<br/>
Password: *<password>*<br/>
Login successful.<br/>

<p>You have access to 56 projects, the list has been suppressed. You can list all projects with 'oc projects'<br/>
Using project "default".<br/>

---

This is 
