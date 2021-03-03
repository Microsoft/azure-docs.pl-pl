---
title: Szybki Start — Konfigurowanie klastra hybrydowego przy użyciu wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra
description: Ten przewodnik Szybki Start przedstawia sposób konfigurowania klastra hybrydowego przy użyciu wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: dac59fb5262cc55acfbabedd304913fc7ac57751
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748756"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Szybki Start: Konfigurowanie klastra hybrydowego przy użyciu wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra (wersja zapoznawcza)

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra zapewnia zautomatyzowane operacje wdrażania i skalowania dla centrów danych w zarządzanych źródłach oprogramowania Apache Cassandra. Ta usługa ułatwia przyspieszenie scenariuszy hybrydowych i zmniejszenie trwającej konserwacji.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten przewodnik Szybki Start przedstawia sposób konfigurowania klastra hybrydowego przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Jeśli masz istniejące centra danych w środowisku lokalnym lub własnym, możesz użyć wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra, aby dodać do niego inne centra danych i je zachować.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.12.1 lub nowszej. Jeśli używasz Azure Cloud Shell, Najnowsza wersja jest już zainstalowana.

* [Platforma Azure Virtual Network](../virtual-network/virtual-networks-overview.md) z łącznością z własnym środowiskiem hostowanym lub lokalnym. Aby uzyskać więcej informacji na temat łączenia środowisk lokalnych z platformą Azure, zobacz artykuł [łączenie sieci lokalnej z platformą Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) .

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Konfigurowanie klastra hybrydowego

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do zasobu Virtual Network.

1. Otwórz kartę **podsieci** i Utwórz nową podsieć. Aby dowiedzieć się więcej na temat pól w formularzu **Dodawanie podsieci** , zobacz artykuł [Virtual Network](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) :

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Dodaj nową podsieć do Virtual Network." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

1. Teraz zastosujemy specjalne uprawnienia do sieci wirtualnej i podsieci, których wystąpienie zarządzane Cassandra wymaga, przy użyciu interfejsu wiersza polecenia platformy Azure. Najpierw musimy odnaleźć `Resource ID` dla istniejącej sieci wirtualnej. Skopiuj dane wyjściowe z tego polecenia do nowszej wersji `Resource ID` .

   ```azurecli-interactive
    # discover the vnet id
    az network vnet show -n <your VNet name> -g <Resource Group Name> --query "id" --output tsv
   ```

1. Teraz stosujemy specjalne uprawnienia, przekazując dane wyjściowe poprzedniego polecenia jako parametr zakresu:

   ```azurecli-interactive
    az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope <Resource ID>
   ```
    > [!NOTE]
    > `assignee` `role` Powyższe wartości i są odpowiednio stałymi zasadami usługi i identyfikatorami ról. 

1. Następnie skonfigurujemy zasoby dla naszego klastra hybrydowego. Ponieważ masz już klaster, nazwa klastra będzie tylko zasobem logicznym, aby zidentyfikować nazwę istniejącego klastra. Upewnij się, że używasz nazwy istniejącego klastra podczas definiowania `clusterName` `clusterNameOverride` zmiennych i w poniższym skrypcie.

   Potrzebne są również węzły inicjatora, publiczne certyfikaty klientów (Jeśli skonfigurowano klucz publiczny/prywatny w punkcie końcowym Cassandra) i Gossip certyfikaty istniejącego klastra. Należy również użyć wcześniej skopiowanego identyfikatora zasobu w celu zdefiniowania `delegatedManagementSubnetId` zmiennej.

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='<Resource ID>'
    
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
    > Należy wiedzieć, gdzie są przechowywane istniejące certyfikaty publiczne i/lub Gossip. Jeśli nie masz pewności, możesz uruchomić polecenie, `keytool -list -keystore <keystore-path> -rfc -storepass <password>` Aby wydrukować certyfikaty. 

1. Po utworzeniu zasobu klastra uruchom następujące polecenie, aby uzyskać szczegółowe informacje dotyczące konfiguracji klastra:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. Poprzednie polecenie zwraca informacje o środowisku wystąpienia zarządzanego. Będziesz potrzebować certyfikatów Gossip, aby można było je zainstalować na węzłach w istniejącym centrum danych. Poniższy zrzut ekranu przedstawia dane wyjściowe poprzedniego polecenia i format certyfikatów:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Pobierz szczegóły certyfikatu z klastra." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Następnie utwórz nowe centrum danych w klastrze hybrydowym. Upewnij się, że wartości zmiennych zostały zamienione na szczegóły klastra:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource ID>'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedSubnetId \
       --node-count 9 
   ```

1. Po utworzeniu nowego centrum danych Uruchom polecenie Pokaż centrum danych, aby wyświetlić jego szczegóły:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Poprzednie polecenie wyprowadza węzły inicjatora nowego centrum danych. Dodaj węzły inicjatora nowego centrum danych do konfiguracji istniejącego centrum danych w pliku *Cassandra. YAML* . I zainstaluj wcześniej zebrane certyfikaty Gossip wystąpienia zarządzanego:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Pobierz szczegóły centrum danych." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Jeśli chcesz dodać więcej centrów danych, możesz powtórzyć powyższe kroki, ale potrzebujesz tylko węzłów inicjatora. 

1. Na koniec użyj poniższego zapytania CQL, aby zaktualizować strategię replikacji w każdej przestrzeni kluczy w celu uwzględnienia wszystkich centrów danych w klastrze:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   Należy również zaktualizować tabele haseł:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tego klastra wystąpienia zarządzanego, usuń go z następujących kroków:

1. W menu po lewej stronie Azure Portal wybierz pozycję **grupy zasobów**.
1. Z listy wybierz grupę zasobów utworzoną dla tego przewodnika Szybki Start.
1. W okienku **Przegląd** grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
3. W następnym oknie wprowadź nazwę grupy zasobów do usunięcia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia klastra hybrydowego przy użyciu interfejsu wiersza polecenia platformy Azure i wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra. Teraz można rozpocząć pracę z klastrem.

> [!div class="nextstepaction"]
> [Zarządzanie wystąpieniem zarządzanym platformy Azure dla zasobów Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md)