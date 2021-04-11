---
title: Szybki Start — używanie interfejsu wiersza polecenia do tworzenia wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra
description: Skorzystaj z tego przewodnika Szybki Start, aby utworzyć wystąpienie zarządzane platformy Azure dla klastra Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: b719310a331044df363efcc6b79be323faf49247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562107"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Szybki Start: Tworzenie wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra zapewnia zautomatyzowane operacje wdrażania i skalowania dla centrów danych w zarządzanych źródłach oprogramowania Apache Cassandra. Ta usługa ułatwia przyspieszenie scenariuszy hybrydowych i zmniejszenie trwającej konserwacji.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku szybki start pokazano, jak za pomocą poleceń interfejsu wiersza polecenia platformy Azure utworzyć klaster z wystąpieniem zarządzanym platformy Azure dla systemu Apache Cassandra. Pokazuje również, jak utworzyć centrum danych i skalować węzły w górę lub w dół w centrum danych.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Platforma Azure Virtual Network](../virtual-network/virtual-networks-overview.md) z łącznością z własnym środowiskiem hostowanym lub lokalnym. Aby uzyskać więcej informacji na temat łączenia środowisk lokalnych z platformą Azure, zobacz artykuł [łączenie sieci lokalnej z platformą Azure](/azure/architecture/reference-architectures/hybrid-networking/) .

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.17.1 lub nowszej. Jeśli używasz Azure Cloud Shell, Najnowsza wersja jest już zainstalowana.

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
   > `assignee`Wartości i `role` w poprzednim poleceniu są stałymi wartościami, wprowadź te wartości dokładnie tak, jak wspomniano w poleceniu. Wykonanie tej czynności spowoduje błędy podczas tworzenia klastra. Jeśli wystąpią błędy podczas wykonywania tego polecenia, możesz nie mieć uprawnień do jego uruchamiania, skontaktuj się z administratorem w celu uzyskania uprawnień.

1. Następnie utwórz klaster na nowo utworzonym Virtual Network za pomocą polecenia [AZ Managed-Cassandra Cluster Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create) . Uruchom następujące polecenie jako wartość `delegatedManagementSubnetId` zmiennej:

   > [!NOTE]
   > Wartość `delegatedManagementSubnetId` zmiennej, która zostanie podana poniżej, jest dokładnie taka sama jak wartość `--scope` podana w powyższym poleceniu:

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

1. Na koniec Utwórz centrum danych dla klastra z trzema węzłami przy użyciu polecenia [AZ Managed-Cassandra Datacenter Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create) :

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

1. Po utworzeniu centrum danych, jeśli chcesz skalować w górę lub skalować węzły w centrum danych, uruchom polecenie [AZ Managed-Cassandra Datacenter Update](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update) . Zmień wartość `node-count` parametru na pożądaną wartość:

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

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi błąd podczas stosowania uprawnień do Virtual Network, takich jak *nie można znaleźć użytkownika lub nazwy głównej usługi w bazie danych grafu dla "e5007d2c-4b13-4a74-9b6a-605d99f03501"*, można ręcznie zastosować to samo uprawnienie z Azure Portal. Aby zastosować uprawnienia z portalu, przejdź do okienka **Kontrola dostępu (IAM)** istniejącej sieci wirtualnej i Dodaj przypisanie roli dla "Azure Cosmos DB" do roli "administrator sieci". Jeśli dwa wpisy są wyświetlane podczas wyszukiwania "Azure Cosmos DB", Dodaj zarówno wpisy, jak pokazano na poniższej ilustracji: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Zastosuj uprawnienia" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Przypisanie roli Azure Cosmos DB jest używane tylko do celów wdrożeniowych. Usługa Azure Managed instanced dla platformy Apache Cassandra nie ma zależności zaplecza na Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, możesz użyć `az group delete` polecenia, aby usunąć grupę zasobów, wystąpienie zarządzane i wszystkie powiązane zasoby:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure. Teraz można rozpocząć pracę z klastrem:

> [!div class="nextstepaction"]
> [Wdróż zarządzany Klaster Apache Spark z Azure Databricks](deploy-cluster-databricks.md)