---
title: Łączenie się z zasobami obszaru roboczego w usłudze Azure Synapse Analytics Studio z sieci z ograniczeniami
description: W tym artykule przedstawiono sposób nawiązywania połączenia z zasobami obszaru roboczego z sieci z ograniczeniami
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 41403a59be0395a6d9874c7369bfe59c22f5ac17
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218369"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>Łączenie z zasobami obszaru roboczego z sieci z ograniczeniami

Załóżmy, że jesteś administratorem IT, który zarządza siecią z ograniczeniami w organizacji. Chcesz włączyć połączenie sieciowe między usługą Azure Synapse Analytics Studio a stacją roboczą w ramach tej sieci z ograniczeniami. W tym artykule pokazano, jak to zrobić.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Obszar roboczy analizy usługi Azure Synapse**: możesz utworzyć jedną z usługi Azure Synapse Analytics. Potrzebna jest nazwa obszaru roboczego w kroku 4.
* **Sieć z ograniczeniami**: Administrator IT utrzymuje sieci z ograniczeniami dla organizacji i ma uprawnienia do konfigurowania zasad sieciowych. W kroku 3 potrzebna jest nazwa sieci wirtualnej i jej podsieć.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Krok 1. Dodawanie sieci wychodzących reguł zabezpieczeń do sieci z ograniczeniami

Należy dodać cztery sieciowe reguły zabezpieczeń dla ruchu wychodzącego z czterema tagami usług. 
* AzureResourceManager
* AzureFrontDoor. frontonu
* Usługi azureactivedirectory
* AzureMonitor (ten typ reguły jest opcjonalny. Dodaj ją tylko wtedy, gdy chcesz udostępnić dane firmie Microsoft.)

Poniższy zrzut ekranu przedstawia szczegółowe informacje dotyczące Azure Resource Manager reguły ruchu wychodzącego.

![Zrzut ekranu przedstawiający szczegóły tagu usługi Azure Resource Manager.](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

Podczas tworzenia innych trzech reguł Zastąp wartość **docelową tag usługi** wartościami **AzureFrontDoor. frontonu**, **usługi azureactivedirectory** lub **AzureMonitor** z listy.

Aby uzyskać więcej informacji, zobacz [Omówienie tagów usług](../../virtual-network/service-tags-overview.md).

## <a name="step-2-create-private-link-hubs"></a>Krok 2. Tworzenie centrów linków prywatnych

Następnie utwórz prywatne centra linków z Azure Portal. Aby znaleźć ten element w portalu, Wyszukaj pozycję *Azure Synapse Analytics (centra linków prywatnych)*, a następnie wprowadź wymagane informacje, aby je utworzyć. 

![Zrzut ekranu przedstawiający centrum tworzenia prywatnego łącza Synapse.](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>Krok 3. Tworzenie prywatnego punktu końcowego dla programu Synapse Studio

Aby uzyskać dostęp do usługi Azure Synapse Analytics Studio, należy utworzyć prywatny punkt końcowy na podstawie Azure Portal. Aby go znaleźć w portalu, Wyszukaj *łącze prywatne*. W **centrum linku prywatnego** wybierz pozycję **Utwórz prywatny punkt końcowy**, a następnie wprowadź wymagane informacje, aby je utworzyć. 

> [!Note]
> Upewnij się, że wartość **regionu** jest taka sama jak ta, w której znajduje się obszar roboczy usługi Azure Synapse Analytics.

![Zrzut ekranu przedstawiający tworzenie prywatnego punktu końcowego, karta podstawy.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Na karcie **zasób** wybierz prywatne centrum łączy, które zostało utworzone w kroku 2.

![Zrzut ekranu przedstawiający tworzenie prywatnego punktu końcowego, karty zasób.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

Na karcie **Konfiguracja** : 
* W obszarze **Sieć wirtualna** wybierz nazwę sieci wirtualnej z ograniczeniami.
* W obszarze **podsieć** wybierz podsieć sieci wirtualnej z ograniczeniami. 
* W **obszarze integracja z prywatną strefą DNS** wybierz pozycję **tak**.

![Zrzut ekranu przedstawiający tworzenie prywatnego punktu końcowego, karty konfiguracja.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Po utworzeniu prywatnego linku połączenia można uzyskać dostęp do strony logowania narzędzia sieci Web dla usługi Azure Synapse Analytics Studio. Nie można jednak uzyskać dostępu do zasobów w obszarze roboczym. W tym celu należy wykonać następny krok.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>Krok 4. tworzenie prywatnych punktów końcowych dla zasobu obszaru roboczego

Aby uzyskać dostęp do zasobów w ramach zasobu obszaru roboczego usługi Azure Synapse Analytics Studio, należy utworzyć następujące elementy:

- Co najmniej jeden prywatny punkt końcowy łącza z typem **dev** dla **docelowego zasobu podrzędnego**.
- Dwa inne opcjonalne prywatne punkty końcowe z typami **SQL** lub **SqlOnDemand**, w zależności od zasobów w obszarze roboczym, do których chcesz uzyskać dostęp.

Tworzenie tych metod jest podobne do sposobu tworzenia punktu końcowego w poprzednim kroku.  

Na karcie **zasób** :

* W obszarze **Typ zasobu** wybierz pozycję **Microsoft. Synapse/obszary robocze**.
* W polu **zasób** wybierz wcześniej utworzoną nazwę obszaru roboczego.
* W polu **docelowy zasób podrzędny** wybierz typ punktu końcowego:
  * **SQL** jest przeznaczony do wykonywania zapytań SQL w puli SQL.
  * **SqlOnDemand** jest dla wbudowanego wykonywania zapytań SQL.
  * **Dev** służy do uzyskiwania dostępu do wszystkiego innego w obszarach roboczych usługi Azure Synapse Analytics Studio. Należy utworzyć co najmniej jeden prywatny punkt końcowy linku tego typu.

![Zrzut ekranu przedstawiający tworzenie prywatnego punktu końcowego, karty zasobów i obszaru roboczego.](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>Krok 5. tworzenie prywatnych punktów końcowych dla połączonego magazynu obszaru roboczego

Aby uzyskać dostęp do połączonego magazynu za pomocą Eksploratora magazynu w obszarze roboczym usługi Azure Synapse Analytics Studio, należy utworzyć jeden prywatny punkt końcowy. Te kroki są podobne do tych, które opisano w kroku 3. 

Na karcie **zasób** :
* W obszarze **Typ zasobu** wybierz pozycję **Microsoft. Synapse/storageAccounts**.
* W polu **zasób** wybierz wcześniej utworzoną nazwę konta magazynu.
* W polu **docelowy zasób podrzędny** wybierz typ punktu końcowego:
  * **obiekt BLOB** jest przeznaczony dla BLOB Storage platformy Azure.
  * **system plików DFS** jest przeznaczony dla Azure Data Lake Storage Gen2.

![Zrzut ekranu przedstawiający tworzenie prywatnego punktu końcowego, karty zasobów i magazynu.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Teraz możesz uzyskać dostęp do zasobu połączonego magazynu. W ramach sieci wirtualnej w obszarze roboczym usługi Azure Synapse Analytics Studio można użyć Eksploratora magazynu, aby uzyskać dostęp do połączonego zasobu magazynu.

Możesz włączyć zarządzaną sieć wirtualną dla obszaru roboczego, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający obszar roboczy Tworzenie Synapse z wyróżnioną opcją Włącz zarządzane sieci wirtualne.](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Jeśli chcesz, aby Notes miał dostęp do połączonych zasobów magazynu w ramach określonego konta magazynu, Dodaj zarządzane prywatne punkty końcowe w usłudze Azure Synapse Analytics Studio. Nazwa konta magazynu powinna być tym, do którego notesu potrzebuje dostęp. Aby uzyskać więcej informacji, zobacz [Tworzenie zarządzanego prywatnego punktu końcowego w źródle danych](./how-to-create-managed-private-endpoints.md).

Po utworzeniu tego punktu końcowego stan zatwierdzenia wskazuje stan **oczekiwanie**. Zażądaj zatwierdzenia od właściciela tego konta magazynu, na karcie **połączenia prywatne punktów końcowych** tego konta magazynu w Azure Portal. Po jego zatwierdzeniu Notes może uzyskać dostęp do połączonych zasobów magazynu w ramach tego konta magazynu.

Teraz wszystkie zestawy. Możesz uzyskać dostęp do zasobu obszaru roboczego usługi Azure Synapse Analytics Studio.

## <a name="appendix-dns-registration-for-private-endpoint"></a>Dodatek: rejestracja DNS dla prywatnego punktu końcowego

Jeśli nie włączono "integracji z prywatną strefą DNS" podczas tworzenia prywatnego punktu końcowego jako zrzutu ekranu poniżej, należy utworzyć "**prywatna strefa DNS strefę**" dla każdego z prywatnych punktów końcowych.
![Zrzut ekranu przedstawiający Synapse prywatnej strefy DNS 1.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

Aby znaleźć **strefę prywatna strefa DNS** w portalu, wyszukaj pozycję *prywatna strefa DNS Zone*. W **obszarze prywatna strefa DNS** Wprowadź poniższe informacje, aby je utworzyć.

* W polu **Nazwa** wprowadź nazwę prywatnej strefy DNS dedykowaną dla określonego prywatnego punktu końcowego w następujący sposób:
  * **`privatelink.azuresynapse.net`** jest przeznaczony dla prywatnego punktu końcowego uzyskiwania dostępu do bramy usługi Azure Synapse Analytics Studio. Zobacz Tworzenie tego typu prywatnego punktu końcowego w kroku 3.
  * **`privatelink.sql.azuresynapse.net`** jest przeznaczony dla tego typu prywatnego punktu końcowego wykonywania zapytań SQL w puli SQL i wbudowanej puli. Zobacz Tworzenie punktów końcowych w kroku 4.
  * **`privatelink.dev.azuresynapse.net`** jest przeznaczony dla tego typu prywatnego punktu końcowego, który uzyskuje dostęp do wszystkich innych elementów w obszarze roboczym usługi Azure Synapse Analytics Studio. Zapoznaj się z tym typem prywatnego tworzenia punktów końcowych w kroku 4.
  * **`privatelink.dfs.core.windows.net`** jest przeznaczony dla prywatnego punktu końcowego dostępu do połączonej Azure Data Lake Storage Gen2 obszaru roboczego. Zapoznaj się z tym typem prywatnego tworzenia punktów końcowych w kroku 5.
  * **`privatelink.blob.core.windows.net`** jest przeznaczony dla prywatnego punktu końcowego dostępu do połączonego obszaru roboczego Blob Storage platformy Azure. Zapoznaj się z tym typem prywatnego tworzenia punktów końcowych w kroku 5.

![Zrzut ekranu przedstawiający Synapse prywatną strefę DNS 2.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

Po utworzeniu **strefy prywatna strefa DNS** wprowadź utworzoną prywatną strefę DNS i wybierz **linki sieci wirtualnej** , aby dodać łącze do sieci wirtualnej. 

![Zrzut ekranu przedstawiający Synapse prywatną strefę DNS 3.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

Wypełnij pola obowiązkowe w następujący sposób:
* W polu **Nazwa łącza** wprowadź nazwę łącza.
* W obszarze **Sieć wirtualna** wybierz sieć wirtualną.

![Zrzut ekranu przedstawiający Synapse prywatną strefę DNS 4.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

Po dodaniu łącza do sieci wirtualnej należy dodać zestaw rekordów DNS w wcześniej utworzonej **strefie prywatna strefa DNS** .

* W polu **Nazwa** wprowadź dedykowane ciągi nazw dla różnych prywatnych punktów końcowych: 
  * **Internet** jest przeznaczony dla prywatnego punktu końcowego dostępu do usługi Azure Synapse Analytics Studio.
  * "***YourWorkSpaceName * * _" jest przeznaczony dla prywatnego punktu końcowego wykonywania zapytań SQL w puli SQL, a także dla prywatnego punktu końcowego, który uzyskuje dostęp do wszystkich innych elementów w obszarach roboczych usługi Azure Synapse Analytics Studio. _ "*** YourWorkSpaceName *-OnDemand * *" jest przeznaczony dla prywatnego punktu końcowego wykonywania zapytań SQL w puli wbudowanej.
* W obszarze **Typ** wybierz pozycję tylko rekord DNS typ **A** . 
* W polu **adres IP** wprowadź odpowiedni adres IP każdego prywatnego punktu końcowego. Adres IP można uzyskać w **interfejsie sieciowym** z omówienia prywatnego punktu końcowego.

![Zrzut ekranu przedstawiający Synapse prywatnej strefy DNS 5.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [sieci wirtualnej zarządzanego obszaru roboczego](./synapse-workspace-managed-vnet.md).

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md).