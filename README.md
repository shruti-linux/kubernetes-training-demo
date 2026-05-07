# kubernetes-training-demo
Hold the demo of kubernetes  from the scratch to advance

INSTALLATION OF KUBERNeTES  CLUSTER 

Step1: Install the CRE  (i,e cri-o)

open the website in the web-broswer    https://cri-o.io/



Define the Kubernetes version and used CRI-O stream
KUBERNETES_VERSION=v1.32CRIO_VERSION=v1.32   (  on master  and node1 and node2)


Distributions using rpm packagesAdd the Kubernetes repository (  on master  and node1 and node2)
cat <<EOF | tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/rpm/repodata/repomd.xml.keyEOF


Add the CRI-O repository    (  on master  and node1 and node2)
cat <<EOF | tee /etc/yum.repos.d/cri-o.repo
[cri-o]
name=CRI-O
baseurl=https://download.opensuse.org/repositories/isv:/cri-o:/stable:/$CRIO_VERSION/rpm/
enabled=1
gpgcheck=1
gpgkey=https://download.opensuse.org/repositories/isv:/cri-o:/stable:/$CRIO_VERSION/rpm/repodata/repomd.xml.keyEOF

 

Install package dependencies from the official repositories  (  on master  and node1 and node2)
dnf install -y container-selinux


Install the packages (  on master  and node1 and node2)
dnf install -y cri-o kubelet kubeadm kubectl

Start CRI-O (  on master  and node1 and node2)
systemctl start crio.service


Bootstrap a cluster  (  on master  and node1 and node2)
swapoff -a
modprobe br_netfilter
sysctl -w net.ipv4.ip_forward=1


EXECUTE THE COMMAND TO INITIALISE THE CLUSTER (  on master  )

kubeadm init --pod-network-cidr=10.244.0.0/16
export KUBECONFIG=/etc/kubernetes/admin.conf

vim /root/.bashrc
   export KUBECONFIG=/etc/kubernetes/admin.conf   ( copy and paster this command in the bashrc file)
kubectl get nodes

Join the Cluster -> on ALL Worker nodes
# kubeadm join 172.25.232.87:6443 --token 1dv3i8.ekfgu01i5ue7s80o \
    --discovery-token-ca-cert-hash sha256:8132dfdbd09ecacbbd4d8615a8fe8de1d11d7739b7be3cafef92768cf7bc5937
    (note: check the command from kubeadm init command output)

If you want to create new token along with print command then run below command on the master node then copy the output and paste on to the workers
# kubeadm token create --print-join-command



Network solutions (Calico)
 https://docs.tigera.io/calico/latest/getting-started/kubernetes/self-managed-onprem/onpremises#install-calico-with-kubernetes-api-datastore-50-nodes-or-less
curl https://raw.githubusercontent.com/projectcalico/calico/v3.30.3/manifests/calico.yaml -O

# gedit calico.yaml
Note: find docker.io and replace it with quay.io

# kubectl apply -f calico.yaml
# watch kubectl get pod -A  -> Once all the pods start running presss Ctrl + C
# kubectl get nodes

Bash Autocompletion
# kubectl completion bash > .kube/kube.sh
# source .kube/kube.sh
# gedit .bashrc
source /root/.kube/kube.sh


===============================================================

