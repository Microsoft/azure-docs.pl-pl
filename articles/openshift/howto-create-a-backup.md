---
title: Tworzenie kopii zapasowej aplikacji klastra usługi Azure Red Hat OpenShift 4 przy użyciu Velero
description: Dowiedz się, jak utworzyć kopię zapasową aplikacji klastra Red Hat OpenShift na platformie Azure przy użyciu Velero
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: a05003bfeceb46059ad0301f99befcdceb456e89
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633295"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Tworzenie kopii zapasowej aplikacji klastra usługi Azure Red Hat OpenShift 4

W tym artykule opisano przygotowanie środowiska do utworzenia kopii zapasowej aplikacji klastra Red Hat OpenShift 4. Omawiane tematy:

> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne i Zainstaluj niezbędne narzędzia
> * Tworzenie kopii zapasowej aplikacji usługi Azure Red Hat OpenShift 4

> [!NOTE] 
> Velero nie tworzy kopii zapasowych danych klucza OpenShift etcd usługi Azure Red Hat. Jeśli potrzebujesz kopii zapasowej etcd, zobacz [Tworzenie kopii](https://docs.openshift.com/container-platform/4.5/backup_and_restore/backing-up-etcd.html)zapasowych etcd.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

### <a name="install-velero"></a>Zainstaluj Velero

Aby [zainstalować](https://velero.io/docs/main/basic-install/) program Velero w systemie, postępuj zgodnie z zalecanym procesem dla danego systemu operacyjnego.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Konfigurowanie konta usługi Azure Storage i kontenera obiektów BLOB

Ten krok spowoduje utworzenie grupy zasobów poza grupą zasobów klastra ARO.  Ta grupa zasobów umożliwi utrwalenie kopii zapasowych i przywrócenie aplikacji do nowych klastrów.

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Ustaw uprawnienia dla Velero

### <a name="create-service-principal"></a>Tworzenie jednostki usługi

Velero potrzebuje uprawnień do wykonywania kopii zapasowych i przywracania. Podczas tworzenia nazwy głównej usługi nadajesz Velero uprawnienia dostępu do grupy zasobów zdefiniowanej w poprzednim kroku. Ten krok spowoduje uzyskanie grupy zasobów klastra:

```bash
export AZURE_RESOURCE_GROUP=$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r .clusterProfile.resourceGroupId | cut -d '/' -f 5,5)
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Zainstaluj Velero na platformie Azure Red Hat OpenShift 4

Ten krok spowoduje zainstalowanie Velero we własnym projekcie oraz [niestandardowych definicji zasobów](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) potrzebnych do wykonywania kopii zapasowych i przywracania za pomocą Velero. Upewnij się, że pomyślnie zalogowano się w klastrze usługi Azure Red Hat OpenShift v4.


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Tworzenie kopii zapasowej za pomocą Velero

Aby utworzyć kopię zapasową aplikacji za pomocą Velero, należy uwzględnić przestrzeń nazw, w której znajduje się ta aplikacja.  Jeśli masz `nginx-example` przestrzeń nazw i chcesz uwzględnić wszystkie zasoby w tej przestrzeni nazw w kopii zapasowej, uruchom następujące polecenie w terminalu:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
Stan kopii zapasowej można sprawdzić, uruchamiając:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Pomyślna kopia zapasowa zostanie wyprodukowana `phase:Completed` i obiekty będą przechowywane w kontenerze na koncie magazynu.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Tworzenie kopii zapasowej z Velero w celu uwzględnienia migawek

Aby utworzyć kopię zapasową aplikacji za pomocą Velero w celu uwzględnienia trwałych woluminów aplikacji, należy uwzględnić przestrzeń nazw, w której aplikacja jest, oraz dodać `snapshot-volumes=true` flagę podczas tworzenia kopii zapasowej.

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

Stan kopii zapasowej można sprawdzić, uruchamiając:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Pomyślna kopia zapasowa z danymi wyjściowymi `phase:Completed` i obiekty będą przechowywane w kontenerze na koncie magazynu.

Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania przy użyciu programu Velero [, zobacz Backup OpenShift Resources w sposób natywny](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono kopię zapasową aplikacji klastra Red Hat OpenShift 4 platformy Azure. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie kopii zapasowej aplikacji klastra OpenShift v4 przy użyciu Velero
> * Tworzenie kopii zapasowej aplikacji klastra OpenShift v4 z użyciem migawek przy użyciu Velero


Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć Przywracanie aplikacji klastra usługi Azure Red Hat OpenShift 4.

* [Tworzenie aplikacji klastra usługi Azure Red Hat OpenShift 4](howto-create-a-restore.md)
* [Tworzenie aplikacji klastra usługi Azure Red Hat OpenShift 4 z uwzględnieniem migawek](howto-create-a-restore.md)
