## OpenShift GitOps (ArgoCD)

Since I am sharing my configution publicly, I will be using sealed-secrets to encrypt certificates and passwords. In your lab environent you can just use your certs/passwords as normal - or I'd encourage you to experiment with sealed-secrets which is open-source/community supported.

---
### Install Argo & Sealed Secrets
After installing the cluster, we'll need to install ArgoCD and the Sealed Secrets Operator(optional). \
`kubectl apply -k https://github.com/ocpdude/ocp-argocd/tree/main/bootstrap/sealed-secrets-operator/base`

Now configure OpenShift GitOps. \
`kubectl apply -k https://github.com/ocpdude/ocp-argocd/tree/main/bootstrap/openshift-gitops/base`

To ensure security, I'm running the sealed-secrets-operatator from the command line. \
`kustomize build bootstrap/sealed-secrets/sealed-secrets-operator/base | kubectl apply -f -`
##### Note: Use "kustomize" instead of "kubectl -k" to prevent patching errors.

---
### Setup & Install
The filesystem is layed out like this: \
openshift-cluster > cluster-name > argocd > manifests (direct the install to tasks on github) \
openshift-cluster > cluster-name > argocd > tasks (initiates the apps for automation) \
openshift-cluster > cluster-name > app-source > (installs the app and supporting configuration data) \

Kickoff the install and let the magic happen. \
`kubectl apply -k https://github.com/ocpdude/ocp-argocd/tree/main/openshift-cluster/redcloud/argocd/apps/manifests/base`

What's installed?
1. The api-server is configured with a redcloud.land certificate
2. The cluster-proxy is configured with the redcloud.land ca
3. The ingress-controller is configured with a redclou.land wildcard certificate
4. The "resource-locker" operator is installed can configured with the service-accounts for the above certificates and the internal registry.
5. Setup the required node-labels.
6. Configure Chrony using the default MachineConfigs
7. OpenShift Container Storage (4.7) operator and subscription (defaults) are installed and configured.
8. The internal registry is patched to use OCS replica storage.
