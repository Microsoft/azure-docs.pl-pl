---
title: Wdrażanie modułu równoważenia obciążenia dostępnego z Internetu przy użyciu szablonu IPv6 — Azure
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak wdrożyć obsługę protokołu IPv6 dla Azure Load Balancer i maszyn wirtualnych o zrównoważonym obciążeniu przy użyciu szablonu platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, moduł równoważenia obciążenia platformy Azure, podwójny stos, publiczny adres IP, natywny protokół IPv6, urządzenia przenośne, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 415c95a441ac0cc6ed2dbf2d6a37f57d7a9e7341
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90562523"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Wdrażanie rozwiązania równoważenia obciążenia dostępnego z Internetu za pomocą protokołu IPv6 przy użyciu szablonu

> [!div class="op_single_selector"]
> * [Program PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)
> * [Szablon](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>W tym artykule opisano wstępną funkcję IPv6 umożliwiającą podstawowym usługom równoważenia obciążenia zapewnienie łączności protokołów IPv4 i IPv6. Kompleksowa łączność z protokołem IPv6 jest teraz dostępna przy użyciu [protokołu IPv6 dla usługi Azure sieci wirtualnych](../virtual-network/ipv6-overview.md) , która integruje łączność IPv6 z sieciami wirtualnymi i zawiera najważniejsze funkcje, takie jak reguły sieciowej grupy zabezpieczeń IPv6, routing zdefiniowany przez użytkownika IPv6, podstawowe i standardowe Równoważenie obciążenia.  Protokół IPv6 dla usługi Azure sieci wirtualnych jest zalecanym standardem dla aplikacji IPv6 na platformie Azure. Zobacz [IPv6 dla wdrożenia programu PowerShell sieci wirtualnej platformy Azure](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Usługa Azure Load Balancer to moduł równoważenia obciążenia w warstwie 4 (TCP, UDP). Moduł równoważenia obciążenia zapewnia wysoką dostępność, dystrybuując ruch przychodzący w wystąpieniach usług o dobrej kondycji w usługach w chmurze lub na maszynach wirtualnych w zestawie modułu równoważenia obciążenia. Usługa Azure Load Balancer może także prezentować te usługi na wielu portach i/lub wielu adresach IP.

## <a name="example-deployment-scenario"></a>Przykładowy scenariusz wdrażania

Na poniższym diagramie przedstawiono wdrożone rozwiązanie równoważenia obciążenia przy użyciu przykładowego szablonu opisanego w tym artykule.

![Diagram przedstawia przykładowy scenariusz używany w tym artykule wraz z klientem stacji roboczej podłączonym do Azure Load Balancer za pośrednictwem Internetu, podłączonym z kolei do dwóch maszyn wirtualnych.](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

W tym scenariuszu utworzysz następujące zasoby platformy Azure:

* Interfejs sieci wirtualnej dla każdej maszyny wirtualnej z przypisanymi adresami IPv4 i IPv6
* Load Balancer dostępne z Internetu przy użyciu adresu IPv4 i publicznego protokołu IPv6
* dwie reguły równoważenia obciążenia służące do mapowania publicznych adresów VIP na prywatne punkty końcowe
* Zestaw dostępności zawierający dwie maszyny wirtualne
* dwie maszyny wirtualne

## <a name="deploying-the-template-using-the-azure-portal"></a>Wdrażanie szablonu przy użyciu Azure Portal

Ten artykuł zawiera odwołania do szablonu opublikowanego w galerii [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) . Możesz pobrać szablon z galerii lub uruchomić wdrożenie na platformie Azure bezpośrednio z galerii. W tym artykule przyjęto założenie, że szablon został pobrany na komputer lokalny.

1. Otwórz Azure Portal i zaloguj się przy użyciu konta z uprawnieniami do tworzenia maszyn wirtualnych i zasobów sieciowych w ramach subskrypcji platformy Azure. Ponadto, jeśli nie korzystasz z istniejących zasobów, konto musi mieć uprawnienia do tworzenia grupy zasobów i konta magazynu.
2. Kliknij pozycję "+ nowy" w menu, a następnie w polu wyszukiwania wpisz ciąg "template". Wybierz pozycję "Template deployment" z wyników wyszukiwania.

    ![Zrzut ekranu przedstawia Azure Portal z wybranymi nowymi i Template deployment.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. W bloku wszystkiego kliknij pozycję "Template deployment".

    ![Zrzut ekranu przedstawia Template deployment w portalu Marketplace.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Kliknij pozycję "Utwórz".

    ![Zrzut ekranu przedstawia opis Template deployment w portalu Marketplace.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Kliknij pozycję "Edytuj szablon". Usuń istniejącą zawartość i skopiuj/wklej zawartość do całej zawartości pliku szablonu (aby dołączyć początek i koniec {}), a następnie kliknij pozycję "Zapisz".

    > [!NOTE]
    > W przypadku korzystania z programu Microsoft Internet Explorer po wklejeniu zostanie wyświetlone okno dialogowe z prośbą o zezwolenie na dostęp do Schowka systemu Windows. Kliknij pozycję "Zezwalaj na dostęp".

    ![Zrzut ekranu przedstawia krok gaśnicy wdrożenia niestandardowego, który jest szablonem edycji.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Kliknij pozycję "Edytuj parametry". W bloku parametry Określ wartości zgodnie ze wskazówkami w sekcji Parametry szablonu, a następnie kliknij przycisk "Zapisz", aby zamknąć Blok parametrów. W bloku wdrożenie niestandardowe wybierz swoją subskrypcję, istniejącą grupę zasobów lub utwórz ją. W przypadku tworzenia grupy zasobów wybierz lokalizację grupy zasobów. Następnie kliknij pozycję **postanowienia prawne**, a następnie kliknij pozycję **Kup** w przypadku postanowień prawnych. Platforma Azure rozpocznie wdrażanie zasobów. Wdrożenie wszystkich zasobów trwa kilka minut.

    ![Zrzut ekranu przedstawia kroki związane z wdrożeniem niestandardowym, rozpoczynając od wprowadzenia wartości parametrów szablonu.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Aby uzyskać więcej informacji na temat tych parametrów, zobacz sekcję [Parametry szablonu i zmienne](#template-parameters-and-variables) w dalszej części tego artykułu.

7. Aby wyświetlić zasoby utworzone przez szablon, kliknij przycisk Przeglądaj, przewiń w dół listę do momentu wyświetlenia okna "grupy zasobów", a następnie kliknij je.

    ![Zrzut ekranu przedstawia Azure Portal z wybranymi przyciskami Przeglądaj i grupy zasobów.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. W bloku grupy zasobów kliknij nazwę grupy zasobów, która została określona w kroku 6. Zostanie wyświetlona lista wszystkich wdrożonych zasobów. Jeśli wszystko zostało wykonane prawidłowo, powinien powiedzieć "powodzenie" w "ostatnim wdrożeniu". Jeśli nie, upewnij się, że konto, którego używasz, ma uprawnienia do tworzenia niezbędnych zasobów.

    ![Zrzut ekranu przedstawia stan ostatniego wdrożenia grupy zasobów, w tym przykładzie powiodło się.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > W przypadku przeglądania grup zasobów natychmiast po wykonaniu kroku 6 "ostatnie wdrożenie" spowoduje wyświetlenie stanu "wdrażanie" podczas wdrażania zasobów.

9. Na liście zasobów kliknij pozycję "myIPv6PublicIP". Zobaczysz, że ma on adres IPv6 w obszarze adres IP i że jego nazwa DNS jest wartością określoną dla parametru dnsNameforIPv6LbIP w kroku 6. Ten zasób to publiczny adres IPv6 i nazwa hosta, które są dostępne dla klientów internetowych.

    ![Zrzut ekranu przedstawia publiczny adres IPv6.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Po pomyślnym wdrożeniu szablonu można sprawdzić poprawność łączności, wykonując następujące zadania:

1. Zaloguj się do Azure Portal i Połącz się z wszystkimi maszynami wirtualnymi utworzonymi w ramach wdrożenia szablonu. W przypadku wdrożenia maszyny wirtualnej z systemem Windows Server uruchom polecenie ipconfig/all w wierszu polecenia. Zobaczysz, że maszyny wirtualne mają zarówno adresy IPv4, jak i IPv6. W przypadku wdrożenia maszyn wirtualnych z systemem Linux należy skonfigurować system operacyjny Linux do odbierania dynamicznych adresów IPv6 przy użyciu instrukcji dostarczonych dla dystrybucji systemu Linux.
2. Z klienta podłączonego do Internetu IPv6 należy zainicjować połączenie z publicznym adresem IPv6 modułu równoważenia obciążenia. Aby upewnić się, że moduł równoważenia obciążenia ma równoważenie między dwiema maszynami wirtualnymi, możesz zainstalować serwer sieci Web, taki jak Microsoft Internet Information Services (IIS) na każdej z maszyn wirtualnych. Domyślna strona sieci Web na każdym serwerze może zawierać tekst "Server0" lub "serwer1" w celu jego jednoznacznej identyfikacji. Następnie otwórz przeglądarkę internetową na kliencie podłączonym do Internetu IPv6 i przejdź do nazwy hosta określonej dla parametru dnsNameforIPv6LbIP modułu równoważenia obciążenia, aby potwierdzić, że połączenie z każdą maszyną wirtualną jest nawiązywane na trasie IPv6. Jeśli widzisz tylko stronę sieci Web tylko z jednego serwera, może być konieczne wyczyszczenie pamięci podręcznej przeglądarki. Otwórz wiele sesji przeglądania prywatnego. Powinna zostać wyświetlona odpowiedź z każdego serwera.
3. Z klienta połączonego z Internetem IPv4, należy zainicjować połączenie z publicznym adresem IPv4 modułu równoważenia obciążenia. Aby upewnić się, że moduł równoważenia obciążenia ma Równoważenie obciążenia dwóch maszyn wirtualnych, można testować za pomocą usług IIS, zgodnie z opisem w kroku 2.
4. Z każdej maszyny wirtualnej Zainicjuj połączenie wychodzące z urządzeniem internetowym połączonym z protokołem IPv6 lub IPv4. W obu przypadkach źródłowy adres IP widziany przez urządzenie docelowe jest publicznym adresem IPv4 lub IPv6 modułu równoważenia obciążenia.

> [!NOTE]
> Protokół ICMP dla protokołów IPv4 i IPv6 jest blokowany w sieci platformy Azure. W efekcie narzędzia ICMP, takie jak ping, zawsze kończą się niepowodzeniem. Aby przetestować łączność, użyj alternatywnej metody TCP, takiej jak TCPing, lub polecenia cmdlet programu PowerShell Test-NetConnection. Należy zauważyć, że adresy IP wyświetlane na diagramie są przykładami wartości, które mogą zostać wyświetlone. Adresy IPv6 są przypisywane dynamicznie, a otrzymane adresy różnią się w zależności od regionu. Ponadto jest często stosowany dla publicznego adresu IPv6 w module równoważenia obciążenia, aby można było rozpocząć od innego prefiksu niż prywatne adresy IPv6 w puli zaplecza.

## <a name="template-parameters-and-variables"></a>Parametry szablonu i zmienne

Szablon Azure Resource Manager zawiera wiele zmiennych i parametrów, które można dostosować do własnych potrzeb. Zmienne są używane dla stałych wartości, które nie powinny być zmieniane przez użytkownika. Parametry są używane dla wartości, które użytkownik ma podawać podczas wdrażania szablonu. Przykładowy szablon został skonfigurowany dla scenariusza opisanego w tym artykule. Można dostosować ten sposób do potrzeb środowiska.

Przykładowy szablon używany w tym artykule zawiera następujące zmienne i parametry:

| Parametr/zmienna | Uwagi |
| --- | --- |
| adminUsername |Określ nazwę konta administratora używanego do logowania się do maszyn wirtualnych za pomocą programu. |
| adminPassword |Określ hasło dla konta administratora używanego do logowania się do maszyn wirtualnych za pomocą programu. |
| dnsNameforIPv4LbIP |Określ nazwę hosta DNS, która ma zostać przypisana jako Nazwa publiczna modułu równoważenia obciążenia. Ta nazwa jest rozpoznawana jako publiczny adres IPv4 modułu równoważenia obciążenia. Nazwa musi być małą literą i być zgodna z wyrażeniem regularnym: ^ [a-z] [a-Z0-9-] {1,61} [a-Z0-9] $. |
| dnsNameforIPv6LbIP |Określ nazwę hosta DNS, która ma zostać przypisana jako Nazwa publiczna modułu równoważenia obciążenia. Ta nazwa jest rozpoznawana jako publiczny adres IPv6 modułu równoważenia obciążenia. Nazwa musi być małą literą i być zgodna z wyrażeniem regularnym: ^ [a-z] [a-Z0-9-] {1,61} [a-Z0-9] $. Może to być taka sama nazwa, jak adres IPv4. Gdy klient wysyła zapytanie DNS dla tej nazwy, platforma Azure zwróci rekordy A i AAAA, gdy nazwa jest udostępniona. |
| vmNamePrefix |Określ prefiks nazwy maszyny wirtualnej. Szablon dołącza liczbę (0, 1, itd.) do nazwy podczas tworzenia maszyn wirtualnych. |
| nicNamePrefix |Określ prefiks nazwy interfejsu sieciowego. Po utworzeniu interfejsów sieciowych szablon dołącza do nazwy liczbę (0, 1, itp.). |
| storageAccountName |Wprowadź nazwę istniejącego konta magazynu lub określ nazwę nowej nazwy, która ma zostać utworzona przez szablon. |
| availabilitySetName |Wprowadź nazwę zestawu dostępności, który ma być używany z maszynami wirtualnymi |
| addressPrefix |Prefiks adresu używany do definiowania zakresu adresów Virtual Network |
| subnetName |Nazwa podsieci utworzonej dla sieci wirtualnej |
| subnetPrefix |Prefiks adresu używany do definiowania zakresu adresów podsieci |
| vnetName |Określ nazwę sieci wirtualnej używanej przez maszyny wirtualne. |
| ipv4PrivateIPAddressType |Metoda alokacji używana dla prywatnego adresu IP (statyczna lub dynamiczna) |
| ipv6PrivateIPAddressType |Metoda alokacji używana dla prywatnego adresu IP (dynamiczny). Protokół IPv6 obsługuje tylko dynamiczną alokację. |
| numberOfInstances |Liczba wystąpień o zrównoważonym obciążeniu wdrożonych przez szablon |
| ipv4PublicIPAddressName |Określ nazwę DNS, która ma być używana do komunikacji z publicznym adresem IPv4 modułu równoważenia obciążenia. |
| ipv4PublicIPAddressType |Metoda alokacji używana dla publicznego adresu IP (statyczna lub dynamiczna) |
| Ipv6PublicIPAddressName |Określ nazwę DNS, która ma być używana do komunikacji z publicznym adresem IPv6 modułu równoważenia obciążenia. |
| ipv6PublicIPAddressType |Metoda alokacji używana dla publicznego adresu IP (dynamiczny). Protokół IPv6 obsługuje tylko dynamiczną alokację. |
| lbName |Określ nazwę modułu równoważenia obciążenia. Ta nazwa jest wyświetlana w portalu lub używana w przypadku odwoływania się do niej przy użyciu interfejsu wiersza polecenia lub programu PowerShell. |

Pozostałe zmienne w szablonie zawierają wartości pochodne, które są przypisywane podczas tworzenia zasobów przez platformę Azure. Nie należy zmieniać tych zmiennych.

## <a name="next-steps"></a>Następne kroki

Aby poznać składnię i właściwości JSON modułu równoważenia obciążenia w szablonie, zobacz [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
