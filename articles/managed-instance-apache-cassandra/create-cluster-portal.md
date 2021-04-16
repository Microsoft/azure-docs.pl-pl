---
title: Szybki start — tworzenie wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra z Azure Portal
description: W tym przewodniku Szybki start pokazano, jak utworzyć wystąpienie zarządzane platformy Azure dla klastra Apache Cassandra przy użyciu Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e42f85bb79dcb1bfe14cacbbfda3576888b841c9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481332"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Szybki start: tworzenie wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra z Azure Portal (wersja zapoznawcza)
 
Usługa Azure Managed Instance dla systemu Apache Cassandra zapewnia zautomatyzowane operacje wdrażania i skalowania dla zarządzanych centrów danych Apache Cassandra typu open source, przyspieszając scenariusze hybrydowe i zmniejszając bieżącą konserwację.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku Szybki start pokazano, jak za pomocą Azure Portal utworzyć wystąpienie zarządzane platformy Azure dla klastra Apache Cassandra.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Tworzenie klastra wystąpienia zarządzanego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Na pasku wyszukiwania wyszukaj wystąpienie zarządzane dla **rozwiązania Apache Cassandra** i wybierz wynik.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Wyszukaj wystąpienie zarządzane dla rozwiązania Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Wybierz **przycisk Utwórz wystąpienie zarządzane dla klastra Apache Cassandra.**

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Tworzenie klastra." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. W **okienku Create Managed Instance for Apache Cassandra** (Tworzenie wystąpienia zarządzanego dla systemu Apache Cassandra) wprowadź następujące informacje:

   * **Subskrypcja** — z listy rozwijanej wybierz swoją subskrypcję platformy Azure.
   * **Grupa zasobów**— określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej. Grupa zasobów to kontener zawierający powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł Omówienie grupy zasobów platformy [Azure.](../azure-resource-manager/management/overview.md)
   * **Nazwa klastra** — wprowadź nazwę klastra.
   * **Lokalizacja** — lokalizacja, w której zostanie wdrożony klaster.
   * **SKU** — typ SKU klastra.
   * **Nie. liczba węzłów**— liczba węzłów w klastrze. Te węzły działają jako repliki danych.
   * **Początkowe hasło administratora cassandra** — hasło używane do tworzenia klastra.
   * **Potwierdź hasło administratora cassandra** — ponownie wdaj swoje hasło.

    > [!NOTE]
    > W publicznej wersji zapoznawczej można utworzyć klaster wystąpień zarządzanych w regionach Wschodnie stany USA, Zachodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Europa *Północna,* Europa Zachodnia, Południowe Azja Wschodnia i Australia Wschodnia.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Wypełnij formularz tworzenia klastra." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Następnie wybierz **kartę** Sieć.

1. W **okienku** Sieć wybierz **nazwę** Virtual Network **podsieci**. Możesz wybrać istniejącą Virtual Network lub utworzyć nową.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Konfigurowanie szczegółów sieci." lightbox="./media/create-cluster-portal/networking.png" border="true":::

    > [!NOTE]
    > Wdrożenie wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra wymaga dostępu do Internetu. Wdrożenie kończy się niepowodzeniem w środowiskach, w których dostęp do Internetu jest ograniczony. Upewnij się, że nie blokujesz dostępu w sieci wirtualnej do następujących istotnych usług platformy Azure, które są niezbędne do prawidłowego działania zarządzanej platformy Cassandra:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Virtual Machine Scale Sets
    > - Monitorowanie platformy Azure
    > - Azure Active Directory
    > - Zabezpieczenia platformy Azure

1. Jeśli w ostatnim kroku utworzono nową sieć wirtualną, przejdź do kroku 8. Jeśli wybrano istniejącą sieć wirtualną, przed utworzeniem klastra musisz zastosować pewne specjalne uprawnienia do Virtual Network podsieci. Aby to zrobić, użyj polecenia , zastępując wartości `az role assignment create` `<subscription ID>` , i odpowiednimi `<resource group name>` `<VNet name>` wartościami:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Wartości `assignee` i w poprzednim poleceniu są wartościami stałymi. Wprowadź te wartości dokładnie tak, jak `role` wspomniano w poleceniu . Nie spowoduje to błędów podczas tworzenia klastra. Jeśli podczas wykonywania tego polecenia wystąpią błędy, być może nie masz uprawnień do jego uruchomienia. Skontaktuj się z administratorem, aby uzyskać uprawnienia.

1. Teraz, po zakończeniu pracy z siecią, kliknij pozycję **Przejrzyj i utwórz**  >  **utwórz**

    > [!NOTE]
    > Do utworzenia klastra może upłynieć do 15 minut.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Przejrzyj podsumowanie, aby utworzyć klaster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Po zakończeniu wdrażania sprawdź grupę zasobów, aby wyświetlić nowo utworzony klaster wystąpienia zarządzanego:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Strona Przegląd po utworzeniu klastra." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Aby przeglądać węzły klastra, przejdź do Virtual Network klastra użytego do utworzenia  klastra i otwórz okienko Przegląd, aby je wyświetlić:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Wyświetl zasoby klastra." lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>Nawiązywanie połączenia z klastrem

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra nie tworzy węzłów z publicznymi adresami IP, dlatego aby nawiązać połączenie z nowo utworzonym klastrem Cassandra, należy utworzyć inny zasób w sieci wirtualnej. Może to być aplikacja lub maszyna wirtualna z zainstalowanym narzędziem do zapytań typu open source [języka CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) firmy Apache. Do wdrożenia maszyny [wirtualnej z](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) systemem Ubuntu można użyć szablonu. Po wdrożeniu połącz się z maszyną za pomocą połączenia SSH, a następnie zainstaluj język CQLSH przy użyciu poniższych poleceń:

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

Jeśli nie zamierzasz nadal używać tego klastra wystąpień zarządzanych, usuń go, aby wykonać następujące czynności:

1. W menu po lewej stronie Azure Portal pozycję **Grupy zasobów.**
1. Z listy wybierz grupę zasobów utworzoną na podstawie tego przewodnika Szybki start.
1. W okienku **Przegląd grupy** zasobów wybierz pozycję Usuń **grupę zasobów.**
1. W następnym oknie wprowadź nazwę grupy zasobów do usunięcia, a następnie wybierz pozycję **Usuń.**

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra przy użyciu Azure Portal. Teraz możesz rozpocząć pracę z klastrem:

> [!div class="nextstepaction"]
> [Wdrażanie zarządzanego klastra Apache Spark za pomocą Azure Databricks](deploy-cluster-databricks.md)
