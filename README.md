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
Then I will use helm to install the operator and it will install the Tiller in the cluster. Before that, I have to grant the privilige to the user <em>system:serviceaccount:kube-system:default</em>

```
cat << EOF | oc apply -f -
apiVersion: authorization.openshift.io/v1 
kind: ClusterRoleBinding
metadata:
  name: tiller-cluster-admin
roleRef:
  name: cluster-admin
subjects: 
- kind: ServiceAccount
  name: default
  namespace: kube-system
userNames:
- system:serviceaccount:kube-system:default
EOF
```

**clusterrolebinding.authorization.openshift.io/tiller-cluster-admin created**

### helm initialize - Install Tiller
<code>$ helm init</code><br/>

<strong>$HELM_HOME has been configured at /Users/dyangcht/.helm.
<p/>
Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.
<p/>
Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.<br/>
To prevent this, run `helm init` with the --tiller-tls-verify flag.<br/>
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation<br/>
Happy Helming!</strong><br/>

### Check the status
```
$ oc get deployments -n kube-system
NAME            READY   UP-TO-DATE   AVAILABLE   AGE
tiller-deploy   1/1     1            1           13m
```

Prepare your own YAML configuration file. Copy the original YAML file to a new one.
```
$ cp kubernetes/charts/weblogic-operator/values.yaml my-operator-values.yaml
```

Because I create a project called **"weblogic"**, I need to change the domainNamespaces from "default" to "weblogic" in my-perator-values.yaml.

```
$ oc adm policy add-scc-to-user anyuid -z default
```
**securitycontextconstraints.security.openshift.io/anyuid added to: ["system:serviceaccount:weblogic:default"]**

```
$ helm install kubernetes/charts/weblogic-operator \
     --name weblogic-operator \
     --namespace weblogic \
     --values my-operator-values.yaml \
     --wait
```     

