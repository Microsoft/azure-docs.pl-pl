---
title: Tworzenie klasy Azure Files StorageClass w usłudze Azure Red Hat OpenShift 4
description: Dowiedz się, jak utworzyć Azure Files StorageClass na platformie Azure Red Hat OpenShift
ms.service: container-service
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI, plik platformy Azure
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fe80698b71ae0ba808991d79b423d49abfacdf7c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825930"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Tworzenie klasy Azure Files StorageClass w usłudze Azure Red Hat OpenShift 4

W tym artykule utworzysz StorageClass dla platformy Azure Red Hat OpenShift 4, który dynamicznie obsługuje magazyn ReadWriteMany (RWX) przy użyciu Azure Files. Omawiane tematy:

> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne i Zainstaluj niezbędne narzędzia
> * Tworzenie usługi Azure Red Hat OpenShift 4 StorageClass przy użyciu usługi Azure File Provisioning

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wdróż klaster usługi Azure Red Hat OpenShift 4 w ramach subskrypcji, zobacz [Tworzenie klastra usługi Azure Red Hat OpenShift 4](tutorial-create-cluster.md)


### <a name="set-up-azure-storage-account"></a>Konfigurowanie konta usługi Azure Storage

Ten krok spowoduje utworzenie grupy zasobów poza grupą zasobów klastra Red Hat OpenShift (ARO) platformy Azure. Ta grupa zasobów będzie zawierać udziały Azure Files, które są tworzone przez dynamiczną prowizję usługi Azure Red Hat OpenShift.

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>Ustawianie uprawnień
### <a name="set-resource-group-permissions"></a>Ustawianie uprawnień grupy zasobów

Jednostka usługi ARO musi mieć uprawnienie "listKeys" w nowej grupie zasobów konta usługi Azure Storage. Przypisz rolę "Współautor", aby to osiągnąć.

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER --query servicePrincipalProfile.clientId -o tsv)

az role assignment create --role Contributor -–assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>Ustawianie uprawnień klastra ARO

Konto usługi trwałego segregatora woluminu OpenShift będzie wymagało możliwości odczytywania wpisów tajnych. Utwórz i przypisz rolę klastra OpenShift, aby to osiągnąć.
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>Utwórz StorageClass z obsługą administracyjną Azure Files

Ten krok spowoduje utworzenie StorageClass Azure Files z obsługą administracyjną. W manifeście StorageClass są wymagane szczegóły konta magazynu, dzięki czemu klaster ARO wie o wyszukiwaniu konta magazynu poza bieżącą grupą zasobów.

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  skuName: Standard_LRS
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>Zmień domyślny StorageClass (opcjonalnie)

Domyślna StorageClass w wersji ARO jest nazywana Managed-Premium i używa aprowizacji Azure-Disk. Należy to zmienić przez wygenerowanie poleceń poprawek względem manifestów StorageClass.

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Weryfikowanie usługi Azure File StorageClass (opcjonalnie)

Utwórz nową aplikację i przypisz do niej magazyn.

```bash
oc new-project azfiletest
oc new-app –template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
Plik test.txt będzie również widoczny za pośrednictwem Eksplorator usługi Storage w Azure Portal.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono dynamiczny magazyn trwały przy użyciu plików Microsoft Azure i Azure Red Hat OpenShift 4. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie konta usługi Storage
> * Konfigurowanie StorageClass w klastrze Red Hat OpenShift 4 platformy Azure przy użyciu aprowizacji Azure Files

Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat obsługiwanych zasobów usługi Azure Red Hat OpenShift 4.

* [Zasady pomocy technicznej usługi Azure Red Hat OpenShift](support-policies-v4.md)
