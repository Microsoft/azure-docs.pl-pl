---
title: Rozwiązywanie problemów z błędami alokacji usługi w chmurze (klasycznej) | Microsoft Docs
description: Rozwiązywanie problemów z niepowodzeniem alokacji podczas wdrażania usługi Azure Cloud Services. Dowiedz się, jak działa przydział i dlaczego Alokacja może zakończyć się niepowodzeniem.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 95fe4a8e1f6c6ee5f519311f8e756be89a09acf8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738314"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-classic-in-azure"></a>Rozwiązywanie problemów z niepowodzeniem alokacji podczas wdrażania Cloud Services (klasyczny) na platformie Azure

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).


## <a name="summary"></a>Podsumowanie

W przypadku wdrażania wystąpień do usługi w chmurze lub dodawania nowych wystąpień roli sieci Web lub procesu roboczego Microsoft Azure przydziela zasoby obliczeniowe. Czasami mogą wystąpić błędy podczas wykonywania tych operacji nawet przed osiągnięciem limitów subskrypcji platformy Azure. W tym artykule wyjaśniono przyczyny niektórych typowych błędów alokacji i zaproponowano możliwe korygowanie. Te informacje mogą być również przydatne podczas planowania wdrożenia usług.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Tło — sposób działania alokacji

Serwery w centrach danych platformy Azure są partycjonowane na klastry. Podjęto próbę wykonania nowego żądania alokacji usługi w chmurze w wielu klastrach. Po wdrożeniu pierwszego wystąpienia w usłudze w chmurze (w ramach przemieszczania lub produkcji) usługa w chmurze jest przypięta do klastra. Wszelkie dalsze wdrożenia usługi w chmurze będą wykonywane w tym samym klastrze. W tym artykule odnosimy się do "przypięty do klastra". Diagram 1 poniżej ilustruje przypadek normalnej alokacji, która została podjęta w wielu klastrach. Diagram 2 przedstawia wielkość przydziału przypiętego do klastra 2, ponieważ w tym miejscu jest hostowana istniejąca usługa w chmurze CS_1.

![Diagram alokacji](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Dlaczego występuje niepowodzenie alokacji

Gdy żądanie alokacji jest przypięte do klastra, nie można znaleźć bezpłatnych zasobów, ponieważ dostępna Pula zasobów jest ograniczona do klastra. Ponadto jeśli żądanie alokacji jest przypięte do klastra, ale żądany typ zasobu nie jest obsługiwany przez ten klaster, żądanie zakończy się niepowodzeniem, nawet jeśli klaster ma bezpłatny zasób. Diagram 3 poniżej ilustruje przypadek, w którym przypięta alokacja nie powiedzie się, ponieważ jedyny klaster kandydujący nie ma bezpłatnych zasobów. Diagram 4 ilustruje przypadek, w którym przypięta alokacja nie powiedzie się, ponieważ jedyny klaster kandydujący nie obsługuje żądanego rozmiaru maszyny wirtualnej, nawet jeśli klaster ma bezpłatne zasoby.

![Niepowodzenie przypiętej alokacji](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Rozwiązywanie problemów z niepowodzeniem alokacji dla usług Cloud Services

### <a name="error-message"></a>Komunikat o błędzie

W Azure Portal przejdź do usługi w chmurze, a następnie na pasku bocznym wybierz pozycję *dzienniki operacji (klasyczny)* , aby wyświetlić dzienniki.

Zapoznaj się z kolejnymi rozwiązaniami dotyczącymi poniższych wyjątków:

|Typ wyjątku  |Komunikat o błędzie  |Rozwiązanie  |
|---------|---------|---------|
|FabricInternalServerError     |Operacja nie powiodła się z kodem błędu "InternalError" i errorMessage "Serwer napotkał błąd wewnętrzny. Spróbuj ponownie wykonać żądanie. ".|[Rozwiązywanie problemów z FabricInternalServerError](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|ServiceAllocationFailure     |Operacja nie powiodła się z kodem błędu "InternalError" i errorMessage "Serwer napotkał błąd wewnętrzny. Spróbuj ponownie wykonać żądanie. ".|[Rozwiązywanie problemów z ServiceAllocationFailure](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|LocationNotFoundForRoleSize     |Operacja " `{Operation ID}` " nie powiodła się: "żądana warstwa maszyny wirtualnej jest obecnie niedostępna w regionie ( `{Region ID}` ) dla tej subskrypcji. Wypróbuj inną warstwę lub Wdróż ją w innej lokalizacji.|[Rozwiązywanie problemów z LocationNotFoundForRoleSize](cloud-services-troubleshoot-location-not-found-for-role-size.md)|
|ConstrainedAllocationFailed     |Operacja platformy Azure " `{Operation ID}` " nie powiodła się z kodem COMPUTE. ConstrainedAllocationFailed. Szczegóły: Alokacja nie powiodła się; nie można spełnić ograniczeń w żądaniu. Zażądane nowe wdrożenie usługi jest związane z grupą koligacji, dotyczy sieci wirtualnej albo w ramach tej usługi hostowanej występuje już wdrożenie. Każdy z tych warunków ogranicza nowe wdrożenie do określonych zasobów platformy Azure. Spróbuj ponownie później albo ogranicz rozmiar maszyny wirtualnej lub liczbę wystąpień roli. Ewentualnie usuń w miarę możliwości wspomniane ograniczenia lub spróbuj wdrożyć w innym regionie.|[Rozwiązywanie problemów z ConstrainedAllocationFailed](cloud-services-troubleshoot-constrained-allocation-failed.md)|
|OverconstrainedAllocationRequest     |Nie można zainicjować obsługi rozmiaru maszyny wirtualnej (lub kombinacji rozmiarów maszyn wirtualnych) wymaganej przez to wdrożenie z powodu ograniczeń żądania wdrożenia. Jeśli to możliwe, spróbuj użyć ograniczeń złagodzeniu wymagań dotyczących, takich jak powiązania sieci wirtualnej, wdrożyć w hostowanej usłudze bez innych wdrożeń oraz do innej grupy koligacji lub bez żadnej grupy koligacji lub spróbuj przeprowadzić wdrożenie w innym regionie.|[Rozwiązywanie problemów z OverconstrainedAllocationRequest](cloud-services-troubleshoot-overconstrained-allocation-request.md)|

Przykładowy komunikat o błędzie:

> "Operacja platformy Azure" {OperationName} "nie powiodła się z kodem COMPUTE. ConstrainedAllocationFailed. Szczegóły: Alokacja nie powiodła się; nie można spełnić ograniczeń w żądaniu. Zażądane nowe wdrożenie usługi jest związane z grupą koligacji, dotyczy sieci wirtualnej albo w ramach tej usługi hostowanej występuje już wdrożenie. Każdy z tych warunków ogranicza nowe wdrożenie do określonych zasobów platformy Azure. Spróbuj ponownie później albo ogranicz rozmiar maszyny wirtualnej lub liczbę wystąpień roli. Alternatywnie, jeśli to możliwe, Usuń wspomniane ograniczenia lub spróbuj wykonać wdrożenie w innym regionie.

### <a name="common-issues"></a>Typowe problemy

Poniżej przedstawiono typowe scenariusze alokacji, które powodują Przypinanie żądania alokacji do jednego klastra.

* Wdrażanie do miejsca przejściowego — Jeśli usługa w chmurze ma wdrożenie w dowolnym miejscu, cała usługa w chmurze jest przypięta do określonego klastra.  Oznacza to, że jeśli wdrożenie już istnieje w miejscu produkcyjnym, nowe wdrożenie przejściowe może zostać przydzielone tylko w tym samym klastrze co używany dla miejsca produkcyjnego. Jeśli klaster zbliża się do pojemności, żądanie może zakończyć się niepowodzeniem.
* Skalowanie — dodawanie nowych wystąpień do istniejącej usługi w chmurze wymaga alokacji w tym samym klastrze.  Alokacja dla niewielkich żądań skalowania zwykle jest możliwa, ale nie zawsze. Jeśli klaster zbliża się do pojemności, żądanie może zakończyć się niepowodzeniem.
* Grupa koligacji — nowe wdrożenie do pustej usługi w chmurze może być przydzielone przez sieć szkieletową w dowolnym klastrze w tym regionie, chyba że usługa w chmurze jest przypięta do grupy koligacji. W tym samym klastrze podjęto próbę wdrożenia w tej samej grupie koligacji. Jeśli klaster zbliża się do pojemności, żądanie może zakończyć się niepowodzeniem.
* Sieć wirtualna z grupą koligacji — starsze sieci wirtualne były powiązane z grupami koligacji zamiast regionów, a usługi Cloud Services w tych sieciach wirtualnych byłyby przypięte do klastra grupy koligacji. Podjęto próbę wdrożenia tego typu sieci wirtualnej w przypiętym klastrze. Jeśli klaster zbliża się do pojemności, żądanie może zakończyć się niepowodzeniem.

## <a name="solutions"></a>Rozwiązania

1. Ponowne wdrożenie do nowej usługi w chmurze — to rozwiązanie prawdopodobnie zakończy się najbardziej pomyślnie, ponieważ pozwala na wybór platformy ze wszystkich klastrów w tym regionie.

   * Wdróż obciążenie w nowej usłudze w chmurze  
   * Zaktualizuj rekord CNAME lub A, aby wskazywał ruch do nowej usługi w chmurze
   * Gdy zerowy ruch przechodzi do starej lokacji, można usunąć starą usługę w chmurze. To rozwiązanie powinno mieć zero przestojów.
2. Usuwanie zarówno miejsc produkcyjnych, jak i przejściowych — to rozwiązanie będzie zachować istniejącą nazwę DNS, ale spowoduje przestoje aplikacji.

   * Usuń miejsca produkcyjne i przejściowe istniejącej usługi w chmurze, aby usługa w chmurze była pusta, a następnie
   * Utwórz nowe wdrożenie w istniejącej usłudze w chmurze. Spowoduje to ponowną próbę alokacji wszystkich klastrów w regionie. Upewnij się, że usługa w chmurze nie jest powiązana z grupą koligacji.
3. Zastrzeżony adres IP — to rozwiązanie zachowa istniejący adres IP, ale spowoduje przestoje aplikacji.  

   * Tworzenie zastrzeżonego adresu IP dla istniejącego wdrożenia przy użyciu programu PowerShell

     ```azurepowershell
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```

   * Postępuj zgodnie z powyższymi #2, aby określić nowy adres IP w pliku CSCFG usługi.
4. Usuń grupę koligacji dla nowych wdrożeń — grupy koligacji nie są już zalecane. Wykonaj czynności opisane w punkcie 1 powyżej, aby wdrożyć nową usługę w chmurze. Upewnij się, że usługa w chmurze nie znajduje się w grupie koligacji.
5. Konwertuj na Virtual Network regionalną — Zobacz, [jak migrować z grup koligacji do Virtual Network regionalnej (VNET)](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet).
