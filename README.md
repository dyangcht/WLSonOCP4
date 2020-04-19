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
  Password: <strong><em>&lt;password&gt;</em></strong><br/>
Login successful.<br/>

<p>You have access to 56 projects, the list has been suppressed. You can list all projects with 'oc projects'<br/>
Using project "default".<br/>

---

### Create a new project - weblogic
<code>$ oc new-project weblogic</code>
<p/>Now using project "weblogic" on server "https://api.cluster-xxxx.xxxx.openshift.com:6443:6443".<br/>
You can add applications to this project with the 'new-app' command. For example, try:
<p/>
    <code>oc new-app django-psql-example</code>
<p/>
to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:
<p/>
    <code>kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node</code>

<p/>
Then I will use helm to install the operator. Before that, I have to grant the privilige to the user <em>system:serviceaccount:kube-system:default</em>
<br/><code>apiVersion: authorization.openshift.io/v1 <br/>
kind: ClusterRoleBinding <br/>
metadata: <br/>
  name: tiller-cluster-admin <br/>
roleRef: <br/>
  name: cluster-admin <br/>
subjects: <br/>
- kind: ServiceAccount<br/>
  name: default<br/>
  namespace: kube-system<br/>
userNames:<br/>
- system:serviceaccount:kube-system:default<br/>
EOF</code>



### helm initialize
<code>$ helm init</code><br/>

