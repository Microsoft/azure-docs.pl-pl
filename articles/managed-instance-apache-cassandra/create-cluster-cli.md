---
title: Szybki Start — używanie interfejsu wiersza polecenia do tworzenia wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra
description: Skorzystaj z tego przewodnika Szybki Start, aby utworzyć wystąpienie zarządzane platformy Azure dla klastra Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 86fa7e2e45dacb86b6601b699dca46b1b909fd08
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424703"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Szybki Start: Tworzenie wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra zapewnia zautomatyzowane operacje wdrażania i skalowania dla centrów danych w zarządzanych źródłach oprogramowania Apache Cassandra. Ta usługa ułatwia przyspieszenie scenariuszy hybrydowych i zmniejszenie trwającej konserwacji.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku szybki start pokazano, jak za pomocą poleceń interfejsu wiersza polecenia platformy Azure utworzyć klaster z wystąpieniem zarządzanym platformy Azure dla systemu Apache Cassandra. Pokazuje również, jak utworzyć centrum danych i skalować węzły w górę lub w dół w centrum danych.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.12.1 lub nowszej. Jeśli używasz Azure Cloud Shell, Najnowsza wersja jest już zainstalowana.

* [Platforma Azure Virtual Network](../virtual-network/virtual-networks-overview.md) z łącznością z własnym środowiskiem hostowanym lub lokalnym. Aby uzyskać więcej informacji na temat łączenia środowisk lokalnych z platformą Azure, zobacz artykuł [łączenie sieci lokalnej z platformą Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) .

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Tworzenie klastra wystąpienia zarządzanego

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

1. Ustaw identyfikator subskrypcji w interfejsie wiersza polecenia platformy Azure:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Następnie utwórz Virtual Network z dedykowaną podsiecią w grupie zasobów:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. Zastosuj specjalne uprawnienia do Virtual Network i podsieci, które są wymagane przez wystąpienie zarządzane. Użyj `az role assignment create` polecenia, zastępując `<subscription ID>` ,, `<resource group name>` `<VNet name>` , i `<subnet name>` z odpowiednimi wartościami:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > `assignee`Wartości i `role` w poprzednim poleceniu są odpowiednio stałymi zasadami usługi i identyfikatorami ról.

1. Następnie utwórz klaster na nowo utworzonym Virtual Network. Uruchom następujące polecenie i upewnij się, że `Resource ID` w poprzednim poleceniu użyto wartości pobranej jako wartość `delegatedManagementSubnetId` zmiennej:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='<Resource_ID>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Na koniec Utwórz centrum danych dla klastra z trzema węzłami:

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId='<Resource_ID>'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedSubnetId \
      --node-count 3 
   ```

1. Po utworzeniu centrum danych, jeśli chcesz skalować w górę lub skalować węzły w centrum danych, uruchom następujące polecenie. Zmień wartość `node-count` parametru na pożądaną wartość:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource_ID>'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Nawiązywanie połączenia z klastrem

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra nie tworzy węzłów z publicznymi adresami IP. Aby nawiązać połączenie z nowo utworzonym klastrem Cassandra, należy utworzyć inny zasób wewnątrz sieci wirtualnej. Ten zasób może być aplikacją lub maszyną wirtualną, na której zainstalowano narzędzie do [CQLSHego](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) zapytania typu "open source" platformy Apache. Za pomocą [szablonu Menedżer zasobów](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) można wdrożyć maszynę wirtualną Ubuntu. Po wdrożeniu programu Użyj protokołu SSH, aby nawiązać połączenie z maszyną i zainstalować CQLSH, jak pokazano w następujących poleceniach:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, możesz użyć `az group delete` polecenia, aby usunąć grupę zasobów, wystąpienie zarządzane i wszystkie powiązane zasoby:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure. Teraz można rozpocząć pracę z klastrem:

> [!div class="nextstepaction"]
> [Wdróż zarządzany Klaster Apache Spark z Azure Databricks](deploy-cluster-databricks.md)
