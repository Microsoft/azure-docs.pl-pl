---
title: Szybki start — tworzenie wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra przy użyciu interfejsu wiersza polecenia
description: Skorzystaj z tego przewodnika Szybki start, aby utworzyć wystąpienie zarządzane platformy Azure dla klastra Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: 53fe53e1406bfcde1f2d8c7b2a1ce8369303426f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379370"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Szybki start: tworzenie wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra zapewnia zautomatyzowane operacje wdrażania i skalowania dla zarządzanych centrów danych Apache Cassandra typu open source. Ta usługa pomaga przyspieszyć scenariusze hybrydowe i ograniczyć bieżącą konserwację.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla serwera Apache Cassandra jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku Szybki start pokazano, jak za pomocą poleceń interfejsu wiersza polecenia platformy Azure utworzyć klaster przy użyciu wystąpienia zarządzanego platformy Azure dla systemu Apache Cassandra. Pokazuje również, jak utworzyć centrum danych i skalować węzły w górę lub w dół w centrum danych.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md) z łącznością ze środowiskiem własnym lub lokalnym. Aby uzyskać więcej informacji na temat łączenia środowisk lokalnych z platformą Azure, zobacz artykuł [Connect an on-premises network to Azure (Łączenie](/azure/architecture/reference-architectures/hybrid-networking/) sieci lokalnej z platformą Azure).

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.17.1 lub wyższej. Jeśli używasz usługi Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Tworzenie klastra wystąpienia zarządzanego

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

1. Ustaw identyfikator subskrypcji w interfejsie wiersza polecenia platformy Azure:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Następnie utwórz nową Virtual Network z dedykowaną podsiecią w grupie zasobów:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```
    > [!NOTE]
    > Wdrożenie wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra wymaga dostępu do Internetu. Wdrożenie kończy się niepowodzeniem w środowiskach, w których dostęp do Internetu jest ograniczony. Upewnij się, że nie blokujesz dostępu w sieci wirtualnej do następujących istotnych usług platformy Azure, które są niezbędne do prawidłowego działania zarządzanej platformy Cassandra:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Virtual Machine Scale Sets
    > - Monitorowanie platformy Azure
    > - Azure Active Directory
    > - Zabezpieczenia platformy Azure

1. Zastosuj pewne specjalne uprawnienia do Virtual Network, które są wymagane przez wystąpienie zarządzane. Użyj polecenia `az role assignment create` , zastępując wartości , i odpowiednimi `<subscription ID>` `<resource group name>` `<VNet name>` wartościami:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Wartości `assignee` i w poprzednim poleceniu są wartościami stałymi. Wprowadź te wartości dokładnie tak, jak `role` wspomniano w poleceniu . Nie spowoduje to błędów podczas tworzenia klastra. Jeśli podczas wykonywania tego polecenia wystąpią błędy, być może nie masz uprawnień do jego uruchomienia. Skontaktuj się z administratorem, aby uzyskać uprawnienia.

1. Następnie utwórz klaster w nowo utworzonym Virtual Network za pomocą polecenia [az managed-cassandra cluster create.](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create) Uruchom następujące polecenie jako wartość `delegatedManagementSubnetId` zmiennej:

   > [!NOTE]
   > Wartość zmiennej, która zostanie dostarczona poniżej, jest dokładnie taka sama jak wartość zmiennej podanej `delegatedManagementSubnetId` `--scope` w poleceniu powyżej:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Na koniec utwórz centrum danych dla klastra z trzema węzłami za pomocą [polecenia az managed-cassandra datacenter create:](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create)

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Po utworzeniu centrum danych, jeśli chcesz skalować w górę lub skalować w dół węzły w centrum danych, uruchom polecenie [az managed-cassandra datacenter update.](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update) Zmień wartość `node-count` parametru na żądaną wartość:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Nawiązywanie połączenia z klastrem

Wystąpienie zarządzane platformy Azure dla serwera Apache Cassandra nie tworzy węzłów z publicznymi adresami IP. Aby nawiązać połączenie z nowo utworzonym klastrem Cassandra, musisz utworzyć inny zasób w sieci wirtualnej. Ten zasób może być aplikacją lub maszyną wirtualną z zainstalowanym narzędziem do zapytań typu open source [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) firmy Apache. Do wdrożenia maszyny [wirtualnej z systemem](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) Ubuntu Resource Manager szablonu aplikacji. Po wdrożeniu połącz się z maszyną za pomocą połączenia SSH i zainstaluj język CQLSH, jak pokazano w następujących poleceniach:

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

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli podczas stosowania uprawnień do usługi Virtual Network wystąpi błąd, taki jak Nie można znaleźć użytkownika lub jednostki usługi w grafowej bazie danych dla wartości *"e5007d2c-4b13-4a74-9b6a-605d99f03501",* możesz ręcznie zastosować to samo uprawnienie z poziomu Azure Portal. Aby zastosować uprawnienia z portalu, przejdź do okienka Kontrola dostępu **(IAM)** istniejącej sieci wirtualnej i dodaj przypisanie roli "Azure Cosmos DB" do roli "Administrator sieci". Jeśli podczas wyszukiwania ciągu "Azure Cosmos DB" są wyświetlane dwa wpisy, dodaj oba wpisy, jak pokazano na poniższej ilustracji: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Stosowanie uprawnień" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Przypisanie Azure Cosmos DB jest używane tylko do celów wdrażania. Wystąpienie zarządzane platformy Azure dla bazy danych Apache Cassandra nie ma zależności zaplecza od Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, wystąpienie zarządzane i wszystkie pokrewne zasoby nie będą już potrzebne, możesz je usunąć za `az group delete` pomocą polecenia :

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure. Teraz możesz rozpocząć pracę z klastrem:

> [!div class="nextstepaction"]
> [Wdrażanie zarządzanego klastra Apache Spark za pomocą Azure Databricks](deploy-cluster-databricks.md)