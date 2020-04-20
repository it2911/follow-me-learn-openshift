# First Try to Use OpenShift

### 1. Login the OpenShift
Kubernetes config file management always big problem.
As you know, community develop some components to let auth field to be perfect.
But official tools is better. The official tools means some person will maintain it. 
This point is very important, especially to Big Enterprise users.

I think OpenShift is good at this point. It easily to login to OpenShift cluster.
```
$ oc login -u kubeadmin -p kKdPx-pjmWe-b3kuu-jeZm3 https://api.crc.testing:6443
The server uses a certificate signed by an unknown authority.
You can bypass the certificate check, but any data you send to the server could be intercepted by others.
Use insecure connections? (y/n): y

Login successful.

You have access to 53 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```

And I found it is very smart to keep the old info in config file.
```
$ cat .kube/config 
apiVersion: v1
clusters:
- cluster:
    insecure-skip-tls-verify: true
    server: https://api.crc.testing:6443
  name: api-crc-testing:6443
- cluster:
    certificate-authority: /home/cheng/.minikube/ca.crt
    server: https://192.168.18.48:8443
  name: minikube
contexts:
- context:
    cluster: api-crc-testing:6443
    namespace: default
    user: kube:admin/api-crc-testing:6443
  name: default/api-crc-testing:6443/kube:admin
- context:
    cluster: minikube
    user: minikube
  name: minikube
current-context: default/api-crc-testing:6443/kube:admin
kind: Config
preferences: {}
users:
- name: kube:admin/api-crc-testing:6443
  user:
    token: d9jAikiVDDv2RTzjhDYfTrtmqNc0bYGBPy2mkegmwb4
- name: minikube
  user:
    client-certificate: /home/cheng/.minikube/client.crt
    client-key: /home/cheng/.minikube/client.key

```

### 2. Watch the Running Pods

At first I try to get the info by `kubectl` command. It is work too. 
That means OpenShift ApiService is compatible with basic `kube-apiservice` interface. 

But one little problem attract my attention. That is I deploy this OpenShift tens of minutes ago.
Why some components life age is `22d`? I think maybe CRC deploy is not real deploy. 
It did import the **etcd backup file** into etcd.
```
$ kubectl get pod -A 
NAMESPACE                                               NAME                                                              READY   STATUS      RESTARTS   AGE
openshift-apiserver-operator                            openshift-apiserver-operator-747d99b97-lzddn                      1/1     Running     0          22d
openshift-apiserver                                     apiserver-xcbk8                                                   1/1     Running     0          23m
openshift-authentication-operator                       authentication-operator-666c5cd958-7rgc9                          1/1     Running     0          22d
openshift-authentication                                oauth-openshift-74746598dd-9v4cl                                  1/1     Running     0          22m
openshift-authentication                                oauth-openshift-74746598dd-v9zwg                                  1/1     Running     0          22m
openshift-cloud-credential-operator                     cloud-credential-operator-7d86577b8d-xsvp8                        1/1     Running     0          22d
openshift-cluster-machine-approver                      machine-approver-88bbf5df7-57hxv                                  2/2     Running     0          22d
openshift-cluster-node-tuning-operator                  cluster-node-tuning-operator-5cdbfd75b8-77zzk                     1/1     Running     0          22d
openshift-cluster-node-tuning-operator                  tuned-nk4d4                                                       1/1     Running     0          22d
openshift-cluster-samples-operator                      cluster-samples-operator-854fdc79c4-5fx9g                         2/2     Running     0          22d
openshift-cluster-storage-operator                      cluster-storage-operator-568974dd9-wvdwt                          1/1     Running     0          22d
openshift-cluster-version                               cluster-version-operator-bcffdff5c-dzn57                          1/1     Running     0          22d
openshift-console-operator                              console-operator-644498f9db-mlc26                                 1/1     Running     0          22d
openshift-console                                       console-5b749cb7bb-gnwhd                                          1/1     Running     0          22d
openshift-console                                       console-5b749cb7bb-skjdf                                          1/1     Running     0          22d
openshift-console                                       downloads-6f4898c5c9-zpm5g                                        1/1     Running     0          22d
openshift-console                                       downloads-6f4898c5c9-zsmfg                                        1/1     Running     0          22d
openshift-controller-manager-operator                   openshift-controller-manager-operator-6cfc9cd9d7-d44wj            1/1     Running     0          22d
openshift-controller-manager                            controller-manager-27qv9                                          1/1     Running     0          21d
openshift-dns-operator                                  dns-operator-74f8b5dcc7-8fsks                                     2/2     Running     0          22d
openshift-dns                                           dns-default-smtfd                                                 2/2     Running     0          22d
openshift-etcd                                          etcd-member-crc-45nsk-master-0                                    2/2     Running     0          22d
openshift-image-registry                                cluster-image-registry-operator-5c4dc465b-ncpkf                   2/2     Running     0          22d
openshift-image-registry                                image-registry-7fc96b9dc4-tcb4j                                   1/1     Running     0          22d
openshift-image-registry                                node-ca-crff4                                                     1/1     Running     0          22d
openshift-ingress-operator                              ingress-operator-867b5447c6-d588v                                 2/2     Running     0          22d
openshift-ingress                                       router-default-5b8c469f6b-k2bxq                                   1/1     Running     0          22d
openshift-kube-apiserver-operator                       kube-apiserver-operator-6558cd776f-ffspl                          1/1     Running     0          22d
openshift-kube-apiserver                                installer-10-crc-45nsk-master-0                                   0/1     Completed   0          21m
openshift-kube-apiserver                                installer-7-crc-45nsk-master-0                                    0/1     Completed   0          22d
openshift-kube-apiserver                                installer-8-crc-45nsk-master-0                                    0/1     Completed   0          21d
openshift-kube-apiserver                                installer-9-crc-45nsk-master-0                                    0/1     Completed   0          21m
openshift-kube-apiserver                                kube-apiserver-crc-45nsk-master-0                                 3/3     Running     0          20m
openshift-kube-apiserver                                revision-pruner-10-crc-45nsk-master-0                             0/1     Completed   0          19m
openshift-kube-apiserver                                revision-pruner-6-crc-45nsk-master-0                              0/1     Completed   0          22d
openshift-kube-apiserver                                revision-pruner-7-crc-45nsk-master-0                              0/1     Completed   0          22d
openshift-kube-apiserver                                revision-pruner-8-crc-45nsk-master-0                              0/1     Completed   0          21d
openshift-kube-apiserver                                revision-pruner-9-crc-45nsk-master-0                              0/1     Completed   0          21m
openshift-kube-controller-manager-operator              kube-controller-manager-operator-d68c96b8f-4g6dn                  1/1     Running     0          22d
openshift-kube-controller-manager                       installer-8-crc-45nsk-master-0                                    0/1     Completed   0          21m
openshift-kube-controller-manager                       installer-9-crc-45nsk-master-0                                    0/1     Completed   0          20m
openshift-kube-controller-manager                       kube-controller-manager-crc-45nsk-master-0                        3/3     Running     0          20m
openshift-kube-controller-manager                       revision-pruner-7-crc-45nsk-master-0                              0/1     Completed   0          22d
openshift-kube-controller-manager                       revision-pruner-8-crc-45nsk-master-0                              0/1     Completed   0          20m
openshift-kube-controller-manager                       revision-pruner-9-crc-45nsk-master-0                              0/1     Completed   0          19m
openshift-kube-scheduler-operator                       openshift-kube-scheduler-operator-b77db8f5c-xgbpp                 1/1     Running     0          22d
openshift-kube-scheduler                                installer-7-crc-45nsk-master-0                                    0/1     Completed   0          21m
openshift-kube-scheduler                                openshift-kube-scheduler-crc-45nsk-master-0                       1/1     Running     0          21m
openshift-kube-scheduler                                revision-pruner-6-crc-45nsk-master-0                              0/1     Completed   0          22d
openshift-kube-scheduler                                revision-pruner-7-crc-45nsk-master-0                              0/1     Completed   0          19m
openshift-marketplace                                   certified-operators-5fbfff8dc4-f94bq                              1/1     Running     0          22d
openshift-marketplace                                   community-operators-59f67bb4dd-5x8jq                              1/1     Running     0          22d
openshift-marketplace                                   marketplace-operator-75f49679d7-nppc5                             1/1     Running     0          22d
openshift-marketplace                                   redhat-operators-7489754448-bdbw9                                 1/1     Running     0          22d
openshift-multus                                        multus-admission-controller-fjjxp                                 1/1     Running     0          22d
openshift-multus                                        multus-cqx56                                                      1/1     Running     0          22d
openshift-network-operator                              network-operator-6bf6c87974-9rk65                                 1/1     Running     0          22d
openshift-operator-lifecycle-manager                    catalog-operator-7648d9cff4-zplwf                                 1/1     Running     0          22d
openshift-operator-lifecycle-manager                    olm-operator-7bc9747fc8-8lh7f                                     1/1     Running     0          22d
openshift-operator-lifecycle-manager                    packageserver-56d4b6c4b8-5zgjr                                    1/1     Running     0          23m
openshift-operator-lifecycle-manager                    packageserver-56d4b6c4b8-rq4qk                                    1/1     Running     0          23m
openshift-sdn                                           ovs-dshwt                                                         1/1     Running     0          22d
openshift-sdn                                           sdn-controller-kqwrv                                              1/1     Running     0          22d
openshift-sdn                                           sdn-l8crh                                                         1/1     Running     0          22d
openshift-service-ca-operator                           service-ca-operator-66989dccd9-9qxh8                              1/1     Running     0          22d
openshift-service-ca                                    apiservice-cabundle-injector-79c8b45d78-rf5qc                     1/1     Running     0          22d
openshift-service-ca                                    configmap-cabundle-injector-65cffb78f4-scd29                      1/1     Running     0          22d
openshift-service-ca                                    service-serving-cert-signer-5b7cdbf964-pwf2v                      1/1     Running     0          22d
openshift-service-catalog-apiserver-operator            openshift-service-catalog-apiserver-operator-68c966c5d-kj72v      1/1     Running     0          22d
openshift-service-catalog-controller-manager-operator   openshift-service-catalog-controller-manager-operator-db954mng6   1/1     Running     0          22d
```

### 3. Confirm the Kubernetes Version

When I write this page. Kubernetes 1.18 version have been release. But Kubernetes 1.16 is very stable.
Maybe to Enterprise user, safe and save (money) is first. 
```
$ kubectl version                       
Client Version: version.Info{Major:"1", Minor:"17", GitVersion:"v1.17.0", GitCommit:"70132b0f130acc0bed193d9ba59dd186f0e634cf", GitTreeState:"clean", BuildDate:"2019-12-07T21:20:10Z", GoVersion:"go1.13.4", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"16+", GitVersion:"v1.16.2", GitCommit:"aa10b5b", GitTreeState:"clean", BuildDate:"2020-03-16T18:11:23Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"linux/amd64"}
```

### 4. Confirm the Metrics Customer of Each Components
CRC is not install the metrics server on OpenShift cluster.
```
$ kubectl top node                                                                                                 
Error from server (NotFound): the server could not find the requested resource (get services http:heapster:)
```

