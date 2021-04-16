---
title: Tworzenie kopii zapasowej aplikacji klastra Azure Red Hat OpenShift 4 przy użyciu narzędzia Velero
description: Dowiedz się, jak utworzyć kopię zapasową aplikacji klastra Azure Red Hat OpenShift przy użyciu narzędzia Velero
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c8bf722bd77372cd89e7c64757347b5fd07eb1ed
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481365"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Tworzenie kopii zapasowej Azure Red Hat OpenShift 4 klastrów

W tym artykule przygotujesz środowisko do utworzenia kopii zapasowej aplikacji klastra Azure Red Hat OpenShift 4. Omawiane tematy:

> [!div class="checklist"]
> * Konfigurowanie wymagań wstępnych i instalowanie niezbędnych narzędzi
> * Tworzenie kopii zapasowej Azure Red Hat OpenShift 4

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i używać go lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

### <a name="install-velero"></a>Instalowanie oprogramowania Velero

Aby [zainstalować](https://velero.io/docs/main/basic-install/) velero w systemie, postępuj zgodnie z zalecanym procesem dla swojego systemu operacyjnego.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Konfigurowanie konta magazynu platformy Azure i kontenera obiektów blob

Ten krok spowoduje utworzenie grupy zasobów poza grupą zasobów klastra ARO.  Ta grupa zasobów umożliwi utrwalanie kopii zapasowych i przywracanie aplikacji do nowych klastrów.

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

## <a name="set-permissions-for-velero"></a>Ustawianie uprawnień dla Velero

### <a name="create-service-principal"></a>Tworzenie jednostki usługi

Velero musi mieć uprawnienia do wykonywania kopii zapasowych i przywracania. Podczas tworzenia jednostki usługi udzielasz uprawnień Velero w celu uzyskania dostępu do grupy zasobów, która jest zdefiniowana w poprzednim kroku. Ten krok spowoduje pobrać grupę zasobów klastra:

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

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Instalowanie aplikacji Velero w Azure Red Hat OpenShift 4

Ten krok spowoduje zainstalowanie velero we [](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) własnym projekcie i niestandardowych definicjach zasobów niezbędnych do tworzenia kopii zapasowych i przywracania za pomocą programu Velero. Upewnij się, że pomyślnie zalogowano się do klastra Azure Red Hat OpenShift w wersji 4.


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

## <a name="create-a-backup-with-velero"></a>Tworzenie kopii zapasowej za pomocą usługi Velero

Aby utworzyć kopię zapasową aplikacji za pomocą usługi Velero, należy dołączyć przestrzeń nazw, w których znajduje się ta aplikacja.  Jeśli masz przestrzeń nazw i chcesz uwzględnić wszystkie zasoby w tej przestrzeni nazw w kopii zapasowej, uruchom następujące polecenie `nginx-example` w terminalu:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
Stan kopii zapasowej można sprawdzić, uruchamiając:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Pomyślna kopia zapasowa będzie tworzyć `phase:Completed` dane wyjściowe, a obiekty będą się żyć w kontenerze na koncie magazynu.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Tworzenie kopii zapasowej za pomocą programu Velero w celu dołączania migawek

Aby utworzyć kopię zapasową aplikacji za pomocą usługi Velero, aby uwzględnić trwałe woluminy aplikacji, należy dołączyć przestrzeń nazw, w którym znajduje się aplikacja, a także dołączyć flagę podczas tworzenia kopii `snapshot-volumes=true` zapasowej

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

Stan kopii zapasowej można sprawdzić, uruchamiając:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Pomyślnie tworzyć kopię `phase:Completed` zapasową z danych wyjściowych i obiekty będą się żyć w kontenerze na koncie magazynu.

Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania przy użyciu narzędzia Velero, zobacz Backup OpenShift resources the native way (Tworzenie kopii zapasowych [zasobów OpenShift w sposób natywny)](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Następne kroki

W tym artykule została Azure Red Hat OpenShift kopii zapasowej aplikacji klastra z 4 elementami. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie kopii zapasowej aplikacji klastra OpenShift v4 przy użyciu narzędzia Velero
> * Tworzenie kopii zapasowej aplikacji klastra OpenShift v4 z migawkami przy użyciu narzędzia Velero


W następnym artykule dowiesz się, jak utworzyć klaster z Azure Red Hat OpenShift przywracania aplikacji z 4 klasterami.

* [Tworzenie klastra Azure Red Hat OpenShift przywracania aplikacji z 4 klastrami](howto-create-a-restore.md)
* [Tworzenie aplikacji klastra Azure Red Hat OpenShift 4, w tym migawek](howto-create-a-restore.md)
