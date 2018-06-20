        
$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
$ chmod 700 get_helm.sh
$ ./get_helm.sh

Helm init需要连接google镜像
helm init --upgrade -i registry.cn-hangzhou.aliyuncs.com/google_containers/tiller:v2.8.2 --stable-repo-url https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts

查看tiller pods是否启动了
kubectl get pods --namespace kube-system

查看启动情况
kubectl describe pod tiller-deploy-2146525830-qq3ha -n kube-system

检查是否正常
Helm version
如果只有client版本， 说明没有启动

但是如果如下， 也有问题
[root@master-192 ~]# helm version
Client: &version.Version{SemVer:"v2.8.2", GitCommit:"a80231648a1473929271764b920a8e346f6de844", GitTreeState:"clean"}
E0312 19:16:58.415688   11926 portforward.go:331] an error occurred forwarding 50138 -> 44134: error forwarding port 44134 to pod tiller-deploy-2146525830-qq3ha_kube-system, uid : unable to do port forwarding: socat not found.
Error: cannot connect to Tiller

解决方法：在node节点安装
sudo yum install socat

After that :
[root@master-192 bin]# helm version
Client: &version.Version{SemVer:"v2.8.2", GitCommit:"a80231648a1473929271764b920a8e346f6de844", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.8.2", GitCommit:"a80231648a1473929271764b920a8e346f6de844", GitTreeState:"clean"}


Helm search
查看仓库里的所有

Helm search mysql
列出mysql相关的

Helm inspect stable/mariadb
查看helm详情

helm install stable/mariadb
安装

helm status happy-panda
查看详情

helm inspect values stable/mariadb
查看有什么可以被改

$ echo '{mariadbUser: user0, mariadbDatabase: user0db}' > config.yaml
$ helm install -f config.yaml stable/mariadb
然后通过这种方式修改, 这会使用新的用户名配置， 但是其他的配置会沿用以前的

Helm安装本地的配置如下
A chart repository (as we’ve seen above)
A local chart archive (helm install foo-0.1.1.tgz)
An unpacked chart directory (helm install path/to/foo)
A full URL (helm install https://example.com/charts/foo-1.2.3.tgz)

helm upgrade -f panda.yaml happy-panda stable/mariadb
使用upgrade去升级部分内容

helm get values happy-panda
mariadbUser: user1
使用get values查看是否新内容生效

helm rollback happy-panda 1
回退版本号 

helm delete happy-panda
删除部署在cluster中的版本

helm list
列出目前部署在cluster中的版本


helm list --all
helm list –deleted
列出部署在cluster中的所有和列出删除的

helm repo list
查看helm仓库

helm repo add dev https://example.com/dev-charts
一个新仓库


helm create deis-workflow
helm package deis-workflow
helm install ./deis-workflow-0.1.0.tgz
快速创建并发布helm chart的命令为


helm lint mychart
看chart是否有问题

示例
https://docs.helm.sh/chart_template_guide/#the-chart-template-developer-s-guide




## 遇到问题

[root@master-192 ~]# helm ls
Error: Get http://localhost:8080/api/v1/namespaces/kube-system/configmaps?labelSelector=OWNER%3DTILLER: dial tcp [::1]:8080: getsockopt: connection refused

去掉ipv6
vi /etc/sysctl.conf

#disable whole system IPV6
net.ipv6.conf.all.disable_ipv6=1

重启
sudo sysctl -p /etc/sysctl.conf

or 

sudo vi /etc/default/grub

GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap ipv6.disable=1 rhgb quiet"
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

重启机器 

        
        
        
        sh "export KUBECONFIG=/home/jenkins/.kube/kubeconfig_npe;helm upgrade  --wait --timeout 200 --install helm-poc-green ./helm-deployment-poc;helm status helm-poc-green"
