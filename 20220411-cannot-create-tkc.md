## 問題描述
Supervisor Cluster works fine, but cannot create TKC


## 錯誤排查
排查方式一律下 kubectl get tkc 確認 mesage

```
[root@harbor ~]# kubectl get tkc -o yaml > ns001-tkc-info.yaml
[root@harbor ~]# cat ns001-tkc-info.yaml
```

確認錯誤訊息：
> [ns001-tkc-info.yaml](https://github.com/sapphirelin/tkgs-troubleshooting/blob/main/ns001-tkc-info.yaml)
> 
> L59:
> unable to reconcile kubeadm ConfigMap''s CoreDNS info: unable to retrieve kubeadm Configmap from the guest cluster: configmaps "kubeadm-config" not found
> 
> L98:
> message: 'unable to retrieve kube-proxy daemonset from the guest cluster: daemonsets.apps "kube-proxy" not found'
> 
> L125&L136:
> message: "2 errors occurred:\n\t* failed to configure DNS for /, Kind= ns001/ns001-tkc:
>        unable to reconcile kubeadm ConfigMap's CoreDNS info: unable to retrieve kubeadm
>        Configmap from the guest cluster: configmaps \"kubeadm-config\" not found\n\t*
>        failed to configure kube-proxy for /, Kind= ns001/ns001-tkc: unable to retrieve
>        kube-proxy daemonset from the guest cluster: daemonsets.apps \"kube-proxy\"
>        not found\n\n"


## 解決方式
以錯誤訊息來看，初步判斷為網路問題，檢查 Frontend 與 Management/Workload 網段是否重疊

個人解法：
登入至 HAProxy 修改 anyip-routes.cfg 中的 VIP 範圍
指令： `vi /etc/vmware/anyip-routes.cfg`

此錯誤訊息會發生於使用 HA Proxy 的環境，建議改採用 NSX-ALB 來建置 vSphere with Tanzu 環境
