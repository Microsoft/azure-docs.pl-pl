---
title: Zabezpieczenia sieci w usłudze Azure Data Lake Storage Gen1 | Microsoft Docs
description: Opis integracji z siecią wirtualną w usłudze Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 716e3766fdd7c1999efa12456346862a9902d7a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108715"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Integracja z siecią wirtualną w usłudze Azure Data Lake Storage Gen1

W tym artykule przedstawiono integrację z siecią wirtualną w usłudze Azure Data Lake Storage Gen1. Integracja z siecią wirtualną umożliwia konfigurowanie kont tak, aby przyjmowały ruch tylko z określonych sieci wirtualnych i podsieci. 

Ta funkcja umożliwia zabezpieczenie konta usługi Data Lake Storage przed zagrożeniami zewnętrznymi.

Integracja z siecią wirtualną w usłudze Data Lake Storage Gen1 wykorzystuje zabezpieczenia punktu końcowego usługi dla sieci wirtualnej pomiędzy siecią wirtualną a usługą Azure Active Directory (Azure AD) do wygenerowania dodatkowych oświadczeń zabezpieczeń w tokenie dostępu. Te oświadczenia są następnie używane do uwierzytelniania sieci wirtualnej na koncie usługi Data Lake Storage Gen1 i uzyskania dostępu.

> [!NOTE]
> Korzystanie z tych funkcji nie wiąże się z dodatkowymi opłatami. Konto jest rozliczane według stawki standardowej dla usługi Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [cennik](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable). Zapoznaj się z [cennikiem](https://azure.microsoft.com/pricing/#product-picker) pozostałych usług platformy Azure, z których korzystasz.

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Scenariusze dotyczące integracji z siecią wirtualną w usłudze Data Lake Storage Gen1

Integracja z siecią wirtualną w usłudze Data Lake Storage Gen1 umożliwia ograniczenie dostępu do konta usługi Data Lake Storage Gen1 z określnych sieci wirtualnych i podsieci. Po ograniczeniu Twojego konta do określonej podsieci sieci wirtualnej inne sieci wirtualne/maszyny wirtualne nie mają do niego dostępu. Pod względem funkcjonalności integracja z siecią wirtualną w usłudze Data Lake Storage Gen1 umożliwia realizację takiego samego scenariusza, jak w przypadku [punktów końcowych usługi dla sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md). W poniższych sekcjach opisano szczegółowo kilka kluczowych różnic. 

![Diagram scenariusza integracji z siecią wirtualną w usłudze Data Lake Storage Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> Oprócz reguł sieci wirtualnej można stosować istniejące reguły zapory bazujące na adresach IP w celu zezwalania na dostęp również z sieci lokalnych. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Optymalny routing przy użyciu integracji z siecią wirtualną w usłudze Data Lake Storage Gen1

Kluczową korzyścią ze stosowania punktów końcowych usługi dla sieci wirtualnej jest [optymalny routing](../virtual-network/virtual-network-service-endpoints-overview.md#key-benefits) z sieci wirtualnej. Możesz zastosować taką samą optymalizację tras w przypadku kont usługi Data Lake Storage Gen1. Użyj następujących [tras zdefiniowanych przez użytkownika](../virtual-network/virtual-networks-udr-overview.md#user-defined) z sieci wirtualnej do konta usługi Data Lake Storage Gen1.

**Publiczny adres IP usługi Data Lake Storage** — użyj publicznego adresu IP docelowych kont usługi Data Lake Storage Gen1. Adresy IP kont usługi Data Lake Storage Gen1 można zidentyfikować przez [rozpoznawanie nazw DNS](./data-lake-store-connectivity-from-vnets.md#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) tych kont. Utwórz oddzielny wpis dla każdego adresu.

```azurecli
# Create a route table for your resource group.
az network route-table create --resource-group $RgName --name $RouteTableName

# Create route table rules for Data Lake Storage public IP addresses.
# There's one rule per Data Lake Storage public IP address. 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the virtual network, and apply the newly created route table to it.
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Wyciek danych z sieci wirtualnej klienta

Oprócz zabezpieczania dostępu do kont usługi Data Lake Storage z poziomu sieci wirtualnej można również zabezpieczyć usługę przed wyciekiem danych na nieautoryzowane konto.

Użyj rozwiązania zapory w sieci wirtualnej do filtrowania ruchu wychodzącego na podstawie adresu URL konta docelowego. Zezwalaj na dostęp tylko przez zatwierdzone konta usługi Data Lake Storage Gen1.

Dostępne opcje to między innymi:
- Usługa [Azure Firewall](../firewall/overview.md): [wdróż i skonfiguruj usługę Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md) dla sieci wirtualnej. Zabezpiecz ruch wychodzący usługi Data Lake Storage i ogranicz go do adresów URL znanych i zatwierdzonych kont.
- Zapora [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/): administrator może zezwolić na użycie tylko określonych dostawców komercyjnych zapór. Skorzystaj z rozwiązania zapory urządzenia sieci wirtualnej, które jest dostępne w witrynie Azure Marketplace, do realizacji tych samych funkcji.

> [!NOTE]
> Użycie zapory w ramach ścieżki danych wprowadza dodatkowy przeskok na tej ścieżce. Może to wpłynąć na wydajność sieci w całym procesie wymiany danych. Może mieć to również wpływ na dostępną przepływność oraz opóźnienie połączenia. 

## <a name="limitations"></a>Ograniczenia

- Klastry usługi HDInsight utworzone przed udostępnieniem integracji z siecią wirtualną w usłudze Data Lake Storage Gen1 należy utworzyć ponownie, aby umożliwić obsługę tej nowej funkcji.
 
- Gdy utworzysz nowy klaster usługi HDInsight i wybierzesz konto usługi Data Lake Storage Gen1 z włączoną funkcją integracji z siecią wirtualną, proces zakończy się niepowodzeniem. Należy najpierw wyłączyć regułę sieci wirtualnej. Możesz też wybrać opcję **Zezwalaj na dostęp ze wszystkich sieci i usług** w bloku **Zapory i sieci wirtualne** na koncie usługi Data Lake Storage. Następnie utwórz klaster usługi HDInsight i na koniec ponownie włącz regułę sieci wirtualnej lub wyczyść zaznaczenie opcji **Zezwalaj na dostęp ze wszystkich sieci i usług**. Zobacz sekcję [Wyjątki](#exceptions), aby uzyskać więcej informacji.

- Integracja z siecią wirtualną w usłudze Data Lake Storage Gen1 nie działa z [tożsamościami zarządzanymi zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).
  
- Dane plików i folderów na koncie usługi Data Lake Storage Gen1 z włączoną integracją z siecią wirtualną nie są dostępne z poziomu portalu. To ograniczenie dotyczy również dostępu z poziomu maszyny wirtualnej znajdującej się w tej sieci wirtualnej oraz czynności takich jak korzystanie z Eksploratora danych. Czynności związane z zarządzaniem kontem będą nadal działać. Dane plików i folderów na koncie usługi Data Lake Storage z włączoną integracją z siecią wirtualną są dostępne z poziomu wszystkich zasobów poza portalem. Te zasoby obejmują dostęp za pomocą zestawów SDK, skryptów programu PowerShell i innych usług platformy Azure, gdy nie pochodzą z portalu. 

## <a name="configuration"></a>Konfigurowanie

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>Krok 1. Konfigurowanie sieci wirtualnej pod kątem korzystania z punktu końcowego usługi Azure AD

1.  Przejdź do witryny Azure Portal i zaloguj się na swoim koncie.
 
2.  [Utwórz nową sieć wirtualną ](../virtual-network/quick-create-portal.md)w ramach swojej subskrypcji. Możesz też przejść do istniejącej sieci wirtualnej. Sieć wirtualna musi znajdować się w tym samym regionie co konto usługi Data Lake Storage Gen1.
 
3.  W bloku **Sieć wirtualna** wybierz pozycję **Punkty końcowe usługi**.
 
4.  Wybierz polecenie **Dodaj**, aby dodać nowy punkt końcowy usługi.

    ![Dodawanie punktu końcowego usługi dla sieci wirtualnej](media/data-lake-store-network-security/config-vnet-1.png)

5.  Jako usługę dla punktu końcowego wybierz **Microsoft.AzureActiveDirectory**.

     ![Wybieranie punktu końcowego usługi Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)

6.  Wybierz podsieci, w przypadku których chcesz zezwolić na łączność. Wybierz pozycję **Dodaj**.

    ![Wybieranie podsieci](media/data-lake-store-network-security/config-vnet-3.png)

7.  Dodanie punktu końcowego usługi może potrwać do 15 minut. Po dodaniu punkt zostanie wyświetlony na liście. Sprawdź, czy znajduje się on na liście, i czy wszystkie szczegóły są zgodne z konfiguracją.
 
    ![Pomyślne dodanie punktu końcowego usługi](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>Krok 2. Konfigurowanie dozwolonej sieci wirtualnej lub podsieci dla konta usługi Data Lake Storage Gen1

1.  Po skonfigurowaniu sieci wirtualnej [utwórz nowe konto usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) w ramach subskrypcji. Możesz też przejść do istniejącego konta usługi Data Lake Storage Gen1. Konto usługi Data Lake Storage Gen1 musi znajdować się w tym samym regionie co sieć wirtualna.
 
2.  Wybierz pozycję **Zapory i sieci wirtualne**.

    > [!NOTE]
    > Jeśli nie widzisz pozycji **Zapory i sieci wirtualne** w ustawieniach, wyloguj się z portalu. Zamknij przeglądarkę i wyczyść pamięć podręczną przeglądarki. Uruchom ponownie maszynę i spróbuj ponownie.

       ![Dodawanie reguły sieci wirtualnej do konta usługi Data Lake Storage](media/data-lake-store-network-security/config-adls-1.png)

3.  Wybierz pozycję **Wybrane sieci**.
 
4.  Wybierz pozycję **Dodaj istniejącą sieć wirtualną**.

    ![Dodawanie istniejącej sieci wirtualnej](media/data-lake-store-network-security/config-adls-2.png)

5.  Wybierz sieci wirtualne i podsieci, w przypadku których chcesz zezwolić na łączność. Wybierz pozycję **Dodaj**.

    ![Wybieranie sieci wirtualnej i podsieci](media/data-lake-store-network-security/config-adls-3.png)

6.  Upewnij się, że wybrane sieci wirtualne i podsieci są wyświetlane prawidłowo na liście. Wybierz pozycję **Zapisz**.

    ![Zapisywanie nowej reguły](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > Po zapisaniu ustawień zaczną one obowiązywać po upływie maksymalnie 5 minut.

7.  [Opcjonalnie] Możesz zezwolić na łączność z określonych adresów IP na stronie **Zapory i sieci wirtualne** w sekcji **Zapory**. 

## <a name="exceptions"></a>Wyjątki
Możesz włączyć łączność z usługami platformy Azure oraz maszynami wirtualnymi poza wybranymi sieciami wirtualnymi. W bloku **Zapory i sieci wirtualne** w obszarze **Wyjątki** wybierz jedną z dwóch opcji:
 
- **Zezwalaj wszystkim usługom platformy Azure na dostęp do tego konta usługi Data Lake Storage Gen1**. Ta opcja umożliwia wszystkim usługom platformy Azure, w tym usługom Azure Data Factory, Azure Event Hubs i wszystkim maszynom wirtualnym platformy Azure na nawiązywanie komunikacji z kontem usługi Data Lake Storage.

- **Zezwalaj usłudze Azure Data Lake Analytics na dostęp do tego konta usługi Data Lake Storage Gen1**. Ta opcja umożliwia nawiązywanie łączności pomiędzy usługą Data Lake Analytics a tym kontem usługi Data Lake Storage. 

  ![Wyjątki w bloku Zapory i sieci wirtualne](media/data-lake-store-network-security/firewall-exceptions.png)

Firma Microsoft zaleca, aby te wyjątki były wyłączone. Należy je włączyć tylko, jeśli potrzebujesz łączności z tymi innymi usługami spoza Twojej sieci wirtualnej.