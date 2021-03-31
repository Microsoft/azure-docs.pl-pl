---
title: Szybki Start — Tworzenie wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra z poziomu Azure Portal
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra przy użyciu Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: cb555eefb19b5db7ed7eb0792a813c295a4bf38b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588617"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Szybki Start: Tworzenie wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra z poziomu Azure Portal (wersja zapoznawcza)
 
Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra zapewnia zautomatyzowane operacje wdrażania i skalowania dla zarządzanych centrów danych programu Apache Cassandra w środowiskach open source, przyspieszanie scenariuszy hybrydowych i zmniejszanie trwającej konserwacji.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku szybki start pokazano, jak za pomocą Azure Portal utworzyć wystąpienie zarządzane platformy Azure dla klastra Apache Cassandra.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Tworzenie klastra wystąpienia zarządzanego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Na pasku wyszukiwania Wyszukaj **wystąpienie zarządzane dla platformy Apache Cassandra** i wybierz wynik.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Wyszukaj wystąpienie zarządzane dla oprogramowania Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Przycisk **Utwórz wystąpienie zarządzane dla klastra Apache Cassandra** .

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Tworzenie klastra." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. W okienku **Utwórz wystąpienie zarządzane dla Apache Cassandra** wprowadź następujące szczegóły:

   * **Subskrypcja** — z listy rozwijanej wybierz subskrypcję platformy Azure.
   * **Grupa zasobów**— Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej. Grupa zasobów to kontener zawierający powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł Omówienie [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md) .
   * **Nazwa klastra** — wprowadź nazwę klastra.
   * **Lokalizacja lokalizacji** , w której zostanie wdrożony klaster.
   * **SKU** — typ jednostki SKU dla klastra.
   * **Nie. węzłów**— liczba węzłów w klastrze. Te węzły pełnią rolę replik dla danych.
   * **Początkowe hasło administratora Cassandra** — hasło, które jest używane do tworzenia klastra.
   * **Potwierdź hasło administratora Cassandra** — ponownie wprowadź hasło.

    > [!NOTE]
    > W publicznej wersji zapoznawczej można utworzyć klaster wystąpienia zarządzanego w regionach *Wschodnie stany USA, zachodnie stany USA, Wschodnie stany USA 2, zachodnie stany USA 2, środkowe stany USA, Południowo-środkowe stany USA, Europa Północna, Europa Zachodnia, południowe Azja Wschodnia i Australia Wschodnia* .

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Wypełnij formularz Utwórz klaster." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Następnie wybierz kartę **Sieć** .

1. W okienku **Sieć** wybierz nazwę **Virtual Network** i **podsieć**. Możesz wybrać istniejący Virtual Network lub utworzyć nowy.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Skonfiguruj szczegóły sieci." lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. Jeśli w ostatnim kroku utworzono nową sieć wirtualną, przejdź do kroku 8. W przypadku wybrania istniejącej sieci wirtualnej przed utworzeniem klastra należy zastosować specjalne uprawnienia do Virtual Network i podsieci. Aby to zrobić, użyj `az role assignment create` polecenia, zastępując `<subscription ID>` ,, `<resource group name>` `<VNet name>` i `<subnet name>` z odpowiednimi wartościami:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > `assignee`Wartości i `role` w poprzednim poleceniu są stałymi wartościami, wprowadź te wartości dokładnie tak, jak wspomniano w poleceniu. Wykonanie tej czynności spowoduje błędy podczas tworzenia klastra. Jeśli wystąpią błędy podczas wykonywania tego polecenia, możesz nie mieć uprawnień do jego uruchamiania, skontaktuj się z administratorem w celu uzyskania uprawnień.

1. Teraz po zakończeniu pracy z siecią kliknij kolejno pozycje **Przegląd + Utwórz**  >  **Utwórz** .

    > [!NOTE]
    > Utworzenie klastra może potrwać do 15 minut.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Przejrzyj podsumowanie, aby utworzyć klaster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Po zakończeniu wdrożenia Sprawdź grupę zasobów, aby wyświetlić nowo utworzony klaster wystąpienia zarządzanego:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Strona przegląd po utworzeniu klastra." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Aby przeglądać węzły klastra, przejdź do okienka Virtual Network, które zostało użyte do utworzenia klastra, i Otwórz okienko **przeglądu** , aby je wyświetlić:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Wyświetl zasoby klastra." lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>Nawiązywanie połączenia z klastrem

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra nie tworzy węzłów z publicznymi adresami IP, dlatego w celu nawiązania połączenia z nowo utworzonym klastrem Cassandra należy utworzyć inny zasób wewnątrz sieci wirtualnej. Może to być aplikacja lub maszyna wirtualna z zainstalowanym narzędziem zapytania typu "open source" programu Apache [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) . Możesz użyć [szablonu](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) , aby wdrożyć maszynę wirtualną Ubuntu. Po wdrożeniu programu Użyj protokołu SSH, aby nawiązać połączenie z maszyną, i zainstaluj CQLSH przy użyciu poniższych poleceń:

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

Jeśli nie chcesz nadal korzystać z tego klastra wystąpienia zarządzanego, usuń go z następujących kroków:

1. W menu po lewej stronie Azure Portal wybierz pozycję **grupy zasobów**.
1. Z listy wybierz grupę zasobów utworzoną dla tego przewodnika Szybki Start.
1. W okienku **Przegląd** grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
1. W następnym oknie wprowadź nazwę grupy zasobów do usunięcia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra przy użyciu Azure Portal. Teraz można rozpocząć pracę z klastrem:

> [!div class="nextstepaction"]
> [Wdróż zarządzany Klaster Apache Spark z Azure Databricks](deploy-cluster-databricks.md)
