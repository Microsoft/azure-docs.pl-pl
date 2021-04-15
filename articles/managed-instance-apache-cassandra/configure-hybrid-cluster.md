---
title: Szybki start — konfigurowanie klastra hybrydowego za pomocą wystąpienia zarządzanego platformy Azure dla systemu Apache Cassandra
description: W tym przewodniku Szybki start pokazano, jak skonfigurować klaster hybrydowy przy użyciu wystąpienia zarządzanego platformy Azure dla systemu Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 9f3ad2a5d5b275ff611653855eff73bd36afda9f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379421"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Szybki start: konfigurowanie klastra hybrydowego przy użyciu wystąpienia zarządzanego platformy Azure dla usługi Apache Cassandra (wersja zapoznawcza)

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra zapewnia zautomatyzowane operacje wdrażania i skalowania dla zarządzanych centrów danych Apache Cassandra typu open source. Ta usługa pomaga przyspieszyć scenariusze hybrydowe i ograniczyć bieżącą konserwację.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla serwera Apache Cassandra jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku Szybki start pokazano, jak skonfigurować klaster hybrydowy za pomocą poleceń interfejsu wiersza polecenia platformy Azure. Jeśli masz istniejące centra danych w środowisku lokalnym lub własnym, możesz użyć wystąpienia zarządzanego platformy Azure dla bazy danych Apache Cassandra, aby dodać inne centra danych do tego klastra i je konserwować.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.12.1 lub wyższej. Jeśli używasz usługi Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

* [Usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md) z łącznością ze środowiskiem własnym lub lokalnym. Aby uzyskać więcej informacji na temat łączenia środowisk lokalnych z platformą Azure, zobacz artykuł [Connect an on-premises network to Azure (Łączenie](/azure/architecture/reference-architectures/hybrid-networking/) sieci lokalnej z platformą Azure).

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Konfigurowanie klastra hybrydowego

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do swojego Virtual Network zasobu.

1. Otwórz **kartę Podsieci** i utwórz nową podsieć. Aby dowiedzieć się więcej na temat pól w formularzu **Dodawanie podsieci,** zobacz [Virtual Network](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) artykułu:

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Dodaj nową podsieć do Virtual Network." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

    > [!NOTE]
    > Wdrożenie wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra wymaga dostępu do Internetu. Wdrożenie kończy się niepowodzeniem w środowiskach, w których dostęp do Internetu jest ograniczony. Upewnij się, że nie blokujesz dostępu w sieci wirtualnej do następujących istotnych usług platformy Azure, które są niezbędne do prawidłowego działania zarządzanego rozwiązania Cassandra:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Virtual Machine Scale Sets
    > - Monitorowanie platformy Azure
    > - Azure Active Directory
    > - Zabezpieczenia platformy Azure

1. Teraz zastosujemy pewne specjalne uprawnienia do sieci wirtualnej i podsieci, których wymaga wystąpienie zarządzane Cassandra, przy użyciu interfejsu wiersza polecenia platformy Azure. Użyj polecenia `az role assignment create` , zastępując wartości , i odpowiednimi `<subscription ID>` `<resource group name>` `<VNet name>` wartościami:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Wartości `assignee` i w poprzednim `role` poleceniu są odpowiednio stałą jednostką usługi i identyfikatorami ról.

1. Następnie skonfigurujemy zasoby dla naszego klastra hybrydowego. Ponieważ masz już klaster, nazwa klastra w tym miejscu będzie tylko zasobem logicznym identyfikującym nazwę istniejącego klastra. Pamiętaj, aby użyć nazwy istniejącego klastra podczas definiowania zmiennych `clusterName` i `clusterNameOverride` w poniższym skrypcie. Potrzebne są również węzły iniekcyjna, publiczne certyfikaty klienta (jeśli w punkcie końcowym cassandra skonfigurowano klucz publiczny/prywatny) oraz certyfikaty plotkowania istniejącego klastra.

   > [!NOTE]
   > Wartość zmiennej, która zostanie dostarczona poniżej, jest dokładnie taka sama jak wartość zmiennej podanej `delegatedManagementSubnetId` `--scope` w poleceniu powyżej:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2,10.52.221.3,10.52.221.4
      --client-certificates 'BEGIN CERTIFICATE-----\n...PEM format..\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format...\n-----END CERTIFICATE-----' \
      --external-gossip-certificates 'BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format 2...\n-----END CERTIFICATE-----'
   ```

    > [!NOTE]
    > Należy wiedzieć, gdzie są przechowywane istniejące certyfikaty publiczne i/lub plotk. Jeśli nie masz pewności, powinno być możliwe uruchomienie, `keytool -list -keystore <keystore-path> -rfc -storepass <password>` aby wydrukować certyfikaty. 

1. Po utworzeniu zasobu klastra uruchom następujące polecenie, aby uzyskać szczegóły konfiguracji klastra:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. Poprzednie polecenie zwraca informacje o środowisku wystąpienia zarządzanego. Potrzebne będą certyfikaty plotkowe, aby można je było zainstalować w węzłach w istniejącym centrum danych. Poniższy zrzut ekranu przedstawia dane wyjściowe poprzedniego polecenia i format certyfikatów:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Pobierz szczegóły certyfikatu z klastra." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Następnie utwórz nowe centrum danych w klastrze hybrydowym. Pamiętaj, aby zastąpić wartości zmiennych szczegółami klastra:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 9 
   ```

1. Teraz, po utworzeniu nowego centrum danych, uruchom polecenie pokaż centrum danych, aby wyświetlić jego szczegóły:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Poprzednie polecenie wyprowadza węzły iniekcyjne nowego centrum danych. Dodaj węzły iniekcyjne nowego centrum danych do konfiguracji istniejącego centrum danych w pliku *cassandra.yaml.* Zainstaluj zebrane wcześniej certyfikaty plotkowe wystąpienia zarządzanego:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Pobierz szczegóły centrum danych." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Jeśli chcesz dodać więcej centrów danych, możesz powtórzyć powyższe kroki, ale potrzebne są tylko węzły iniekcyjna. 

1. Na koniec użyj następującego zapytania CQL, aby zaktualizować strategię replikacji w każdej przestrzeni kluczy, aby uwzględnić wszystkie centra danych w klastrze:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   Należy również zaktualizować tabele haseł:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli podczas stosowania uprawnień do usługi Virtual Network wystąpi błąd, taki jak Nie można znaleźć użytkownika lub jednostki usługi w grafowej bazie danych dla wartości *"e5007d2c-4b13-4a74-9b6a-605d99f03501",* możesz ręcznie zastosować to samo uprawnienie z poziomu Azure Portal. Aby zastosować uprawnienia z portalu, przejdź do okienka Kontrola dostępu **(IAM)** istniejącej sieci wirtualnej i dodaj przypisanie roli "Azure Cosmos DB" do roli "Administrator sieci". Jeśli podczas wyszukiwania ciągu "Azure Cosmos DB" są wyświetlane dwa wpisy, dodaj oba wpisy, jak pokazano na poniższej ilustracji: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Stosowanie uprawnień" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Przypisanie Azure Cosmos DB jest używane tylko do celów wdrażania. Wystąpienie zarządzane platformy Azure dla bazy danych Apache Cassandra nie ma zależności zaplecza od Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz nadal używać tego klastra wystąpień zarządzanych, usuń go, aby wykonać następujące czynności:

1. W menu po lewej stronie Azure Portal pozycję **Grupy zasobów.**
1. Z listy wybierz grupę zasobów utworzoną na podstawie tego przewodnika Szybki start.
1. W okienku **Przegląd grupy** zasobów wybierz pozycję Usuń **grupę zasobów.**
3. W następnym oknie wprowadź nazwę grupy zasobów do usunięcia, a następnie wybierz pozycję **Usuń.**

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia klastra hybrydowego przy użyciu interfejsu wiersza polecenia platformy Azure i wystąpienia zarządzanego platformy Azure dla systemu Apache Cassandra. Teraz możesz rozpocząć pracę z klastrem.

> [!div class="nextstepaction"]
> [Zarządzanie wystąpieniem zarządzanym platformy Azure dla zasobów Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md)
