###  EKS Pod Information Collector (EPIC)  

This project was created to collect Amazon EKS resource information which includes collecting a specific POD Configurations/Specification, logs _(optional)_, PV/PVC etc. for troubleshooting Amazon EKS customer support cases.

#### Usage

At a high level, you run this script from your machine's terminal against your desired EKS cluster and it will collect below included information and create a Tarball sharable folder in a user's current directoy location. This collected logs will help AWS support and service team engineers in assisting AWS Customers with their EKS cluster related issues. AWS support and service team engineers can use this collected information once provided via a customer support case to investigate/troubleshoot the issue effectively and efficiently.

```NOTE: User must have an Admin permission to view/access all the resources in their respective EKS cluster before they run this script.```

* Run this project as the EKS cluster root user and `Podname (-p) & Namespace (-n)` are mandatory input parameter.

```
curl -O https://github.com/aws-samples/eks-pod-information-collector.git
sudo bash eks-cluster-log-collector.sh <Provide Complete POD name> <Provide Complete Namespace Name In which that POD is running>
OR  
./eks-pod-information-collector.sh <Provide Complete POD name> <Provide Complete Namespace Name In which that POD is running>
```

NOTE: If you do not pass the required arguments `Podname (-p) & Namespace (-n)` , By default script will collect Default resources running in `KUBE-SYSTEM namespace`.

Confirm if the tarball file was successfully created (it can be .tgz or .tar.gz) in your current Directory. `EX: /~/Desktop/<EKS-Cluster-Name>_<CurrentTime-stamp-UTC>.tar.gz`
#### Share the logs Over EKS Support Case

You can run this script prior to creating a EKS support case with AWS and attach the generated Log tarball folder as an attachment on your case.

#### List of files that gets generated by the EKS script

The `aws-eks-cluster-log-collector-script` will create a main folder under your current directory with your EKS cluster name and time stamp(<EKS-Cluster-Name>_<CurrentTime-stamp-UTC>.tar.gz) which will include below listed files with a proper naming convention to segregate captured EKS cluster information and kubernetes resource configuration and logs. 

Folder Structure:
```
├── Cluster_Info.json        // Cluster ARN and control plane server URL.
├── ConfigMaps.yaml          // AWS-Auth , CoreDNS, Kube-Proxy config map information.
├── DaemonSets.yaml          // Manifests of default Daemonsets such as aws-node, kube-proxy  
├── Deployments.yaml         // Manifests of default Deployment such as corends  
├── MutatingWebhook.json     // Manifests of All MutatingWebhookConfigurations
├── Storage_Classes.json     // Manifests of All StorageClasses
├── ValidatingWebhook.json   // Manifests of All ValidatingWebhookConfigurations
└── pod_name_namespace
    ├── Deployment_name.json
    ├── Deployment_name.txt
    ├── Node_name.json
    ├── Node_name.txt
    ├── Pod_name.json
    ├── Pod_name.txt
    ├── ReplicaSet_name.json
    ├── ReplicaSet_name.txt
    ├── SA_name.json
    ├── Services.json
    ├── Services.txt
    └── pod_name.log
```

```NOTE: K = Kubectl``` 
 - Script will create following list of files within the main folder.

  1. `Cluster_Info.json` --> It will include current-context EKS Cluster information such as Cluster ARN and control plan server Url.
  
  2. `ConfigMap.yaml` --> It will include AWS-Auth , CoreDNS, Kube-Proxy config map information.
    
  3. `DaemonSets.yaml` --> It will include AWS-Node, Kube-Proxy DaemonSets information.
  
  4. `Deployments.yaml` --> It will include CoreDNS Deployments information.
  
  5. `MutatingWebhook.json` --> It will include currently configured MutatingWebhook information. Output of:`K get mutatingwebhookconfiguration`
  
  6. `ValidatingWebhook.json` --> It will include currently configured ValidatingWebhook information. Output of: `K get validatingwebhookconfiguration`
  
  7. `Storage_Classes.json` --> It will include currently deployed Storage class information. Output of: `K get sc`
 
  8. `Node_${NODE}.txt & Node_${NODE}.json` --> It will include the WorkerNode information on which user provided Pod (Pod name provided as input parameter) is running. Output Of: `K get/describe node`

  9. `SA_${POD_SA_NAME}.json`--> It will include currently configured Service Accounts information. Output of: `K get serviceaccount`
  
  10. `Pod_${POD_NAME}.txt & Pod_${POD_NAME}.json & ${POD_NAME}.log` -->  It will include the configuration spec of the pods and log of that application pod. Output Of: `K get/describe/logs  pod`
  
  11. `Ingress.json` --> It will include the information about the configured ingress. Output Of: `K get/describe ingress` 

  12. `aws_lbc.log & aws_lbc.json` --> It will include the information about the configured AWS ALB Pod spec and Pod logs. Output Of: `K get/describe/log <aws-lb-deployment>`

  13. `ebs-node-${container}.log & ebs-csi-${container}.log` --> It will include the information about the configured ebs-node Pod spec and ebs-csi Pod logs. Output Of: `K get/describe/log <ebs-deployment>`

  14. `efs-node-${container}.log & efs-csi-${container}.log` --> It will include the information about the configured efs-node Pod spec and efs-csi Pod logs. Output Of: `K get/describe/log <efs-deployment>`

  15. `PVC_${claim}.json & PV_${PV}.json & PV_${PV}.txt & PVC_${claim}.txt` --> It will include the information about the configured persistance volume and claims. Output Of: `K get/describe <pv & pvc deployment>`
  
  16. `<Your-EKS-Cluster>.<DATE>-<TimeStamp-UTC>.tar.gz` --> It is the Archived version of all the above-mentioned files which you can share on your EKS support case for AWS support engineer review so they can efficiently assit you to troubleshoot your EKS related issue



#### Use-case Scenarios

This script can be executed from your machine against any AWS EKS cluster and below included test execution use-cases will help you understand the script execution workflow (**Note: User who is running this script must have an appropriate Admin permission to access EKS cluster and should configure AWS_Profile with appropriate assume_role prior to running the script**).

#### Examples

```
$ sudo bash ./eks-pod-information-collector.sh --help

USAGE: ./eks-pod-information-collector.sh -p <Podname> -n <Namespace of the pod> are Mandatory Flags

MANDATORY FLAGS NEEDS TO BE PROVIDED IN THE SAME ORDER

   -p  Pass this flag to provide the EKS pod name

   -n  Pass this flag to provide the Namespace in which above specified pod is running

OPTIONAL:
   -h Or -help to Show this help message.
```

```
SCENARIO 1: When you do not provide `Podname` & `Namespace` to the script

$ sudo ./eks-pod-information-collector.sh

RESULT:


    [WARNING] POD_NAME & NAMESPACE Both are required!!

	[WARNING] Collecting Default resources in KUBE-SYSTEM namespace!!

Collecting information in Directory: /~/Desktop/<EKS-Cluster-Name>_2023-04-17_2314-UTC
Collecting Cluster Details, Review File: Cluster_Info.json
Collecting Default resources in KUBE-SYSTEM, Review Files ConfigMaps.yaml, DaemonSets.yaml, Deployments.yaml
###
Done Collecting Information
###
#### Bundling the file ####

	Done... your bundled logs are located in /~/Desktop/<EKS-Cluster-Name>_2023-04-17_2314-UTC.tar.gz

```

```
SCENARIO 2: When you Only  provide one of the argument `Podname` & `Namespace` to the script

$ sudo ./eks-pod-information-collector.sh -p test -n                                        ✔

USAGE: ./aws-eks-pod-information-collector-script.sh -p <Podname> -n <Namespace of the pod> are Mandatory Flags

MANDATORY FLAGS NEEDS TO BE PROVIDED IN THE SAME ORDER

   -p  Pass this flag to provide the EKS pod name

   -n  Pass this flag to provide the Namespace in which above specified pod is running

OPTIONAL:
   -h Or -help to Show this help message.

```

```
SCENARIO 3: When you provide both `Podname` & `Namespace` to the script

$ sudo ./eks-pod-information-collector.sh <Provide Complete POD name> <Provide Complete Namespace Name In which that POD is running>

EX:
$ sudo ./eks-pod-information-collector.sh example-test-pod-123xxxx  test

RESULT:

Collecting information in Directory: /<User current directoy name>/<EKS-Cluster-Name>_2023-04-17_2314-UTC
Collecting Cluster Details, Review File: Cluster_Info.json
Collecting Default resources in KUBE-SYSTEM, Review Files ConfigMaps.yaml, DaemonSets.yaml, Deployments.yaml
Collecting Resource related to example-test-pod-123xxxx, Review Files in Directory: example-test-pod-123xxxx
******** NOTE ********
Please Enter yes Or y if you want to collect the logs of Pod "example-test-pod-123xxxx"
**********************
y
**********************
Collecting logs of Pod

###
Done Collecting Information
###
#### Bundling the file ####

	Done... your bundled logs are located in /~/Desktop/<EKS-Cluster-Name>_2023-04-17_2314-UTC.tar.gz

```
