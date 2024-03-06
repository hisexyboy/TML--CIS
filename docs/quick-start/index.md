# 准备工作

CIS以K8S Deployment的形式部署在`tmlake`命名空间中，通过[List-Watch](https://github.com/topics/list-watch)机制监听k8s侧发生的资源事件，并将其转化并下发为TML应用交付能力。

为了部署并正常运行CIS，我们需要：

* K8S侧：创建ServiceAccount
* K8S侧：分配相应ClusterRole权限
* K8S侧：创建TML-ADC登陆信息
* TML侧：根据CNI类型配置基础网络


## K8S侧创建相关资源

  - 创建serviceaccount 和 RBAC

    ```shell
    $ cd k8s-adc-controller/deploy/static
    $ kubectl create -f ./rbac.yaml
    ```

  - 可根据实际需要修改rbac权限范围。

  - 注册TML CRD资源

    ```shell
    $ kubectl create -f ./crds/
    ```    

  - TML CRD资源List

    ```shell
    k8s.tmlake.com_aclprofile.yaml
    k8s.tmlake.com_healthcheck.yaml
    k8s.tmlake.com_healthcheck.yaml-bak
    k8s.tmlake.com_httpprofile.yaml
    k8s.tmlake.com_persistenceprofile.yaml
    k8s.tmlake.com_rules.yaml
    k8s.tmlake.com_sslprofile.yaml
    k8s.tmlake.com_tcpudpprofile.yaml
    k8s.tmlake.com_wafclasses.yaml
    k8s.tmlake.com_wafrules.yaml
    k8s.tmlake.com_websecurityprofile.yaml
    ```

    注意：*使用CIS之前请仔细阅读CRD资源文件*

  - 创建 CIS Controller 以及 TML-ADC 登陆信息

    ```shell
    $ kubectl create -f ./k8s-adc-controller.yaml
           - name: ADC_SERVER_IP
              value: 192.168.34.99
            - name: ADC_SERVER_PORT
              value: "10443"
            - name: KUBERNETES_SERVICE_HOST
              value: 192.168.34.100
            - name: KUBERNETES_SERVICE_PORT
              value: "6443"
          args:
            - -password=password
            - -username=admin
    ```

    注意：*使用 K8s 和 TML 实际url及密码替换示例中的https://10.10.10.10:443及mypassword*
