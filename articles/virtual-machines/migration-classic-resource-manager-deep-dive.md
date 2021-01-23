---
title: Narzędzie migracji obsługiwane przez platformę.
description: Szczegółowee techniczne głębokiej migracji zasobów z klasycznego modelu wdrażania do Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: tagore
ms.openlocfilehash: bc12d626d8a331981cbbad015b376b826c617209
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735136"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager

> [!IMPORTANT]
> Dzisiaj około 90% maszyn wirtualnych IaaS korzysta z [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28 lutego 2020, klasyczne maszyny wirtualne są przestarzałe i zostaną w pełni wycofane z 1 marca 2023. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym zaniechaniu i [sposobach jego działania](./classic-vm-deprecation.md#how-does-this-affect-me).

Przechodźmy głębokie szczegółowe na Migrowanie z klasycznego modelu wdrażania platformy Azure do modelu wdrażania Azure Resource Manager. Zapoznaj się z zasobami na poziomie zasobów i funkcji, aby ułatwić zrozumienie, w jaki sposób platforma Azure migruje zasoby między dwoma modelami wdrażania. Aby uzyskać więcej informacji, przeczytaj artykuł dotyczący anonsu dotyczącego usługi: [obsługiwana przez platform migrację zasobów IaaS z klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md).


## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migruj zasoby IaaS z klasycznego modelu wdrażania do Azure Resource Manager
Najpierw należy zrozumieć różnicę między operacjami płaszczyzny danych i płaszczyzny zarządzania w ramach zasobów infrastruktury jako usługi (IaaS).

* *Płaszczyzna zarządzania/kontroli* opisuje wywołania, które są dołączone do płaszczyzny zarządzania/kontroli lub interfejsu API w celu modyfikowania zasobów. Operacje, na przykład takie jak utworzenie maszyny wirtualnej, ponowne uruchomienie maszyny wirtualnej i zaktualizowanie maszyny wirtualnej przy użyciu nowej podsieci, powodują wykonanie działań zarządzania uruchomionymi zasobami. Nie wpływają one bezpośrednio na łączenie się z maszynami wirtualnymi.
* *Płaszczyzna danych* (aplikacja) opisuje środowisko uruchomieniowe samej aplikacji i obejmuje interakcje z wystąpieniami, które nie przechodzą przez interfejs API platformy Azure. Na przykład uzyskiwanie dostępu do witryny sieci Web lub ściąganie danych z działającego wystąpienia SQL Server lub serwera MongoDB jest interakcją między płaszczyznami danych lub aplikacjami. Inne przykłady obejmują Kopiowanie obiektu BLOB z konta magazynu oraz uzyskiwanie dostępu do publicznego adresu IP w celu używania Remote Desktop Protocol (RDP) lub Secure Shell (SSH) z maszyną wirtualną. Te operacje utrzymują działanie aplikacji w ramach usług obliczeniowych, sieciowych i magazynowych.

Płaszczyzna danych jest taka sama między klasycznym modelem wdrażania a stosami Menedżer zasobów. Różnica polega na tym, że podczas procesu migracji firma Microsoft tłumaczy reprezentację zasobów z klasycznego modelu wdrażania do tego w stosie Menedżer zasobów. W związku z tym należy użyć nowych narzędzi, interfejsów API i zestawów SDK do zarządzania zasobami w stosie Menedżer zasobów.

![Diagram, który pokazuje różnicę między płaszczyzną zarządzania a warstwą kontroli i płaszczyzną danych](./media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> W przypadku niektórych scenariuszy migracji platforma Azure zatrzymuje maszyny wirtualne, cofa ich przydział i uruchamia je ponownie. Powoduje to krótkotrwałe przestoje płaszczyzny danych.
>

## <a name="the-migration-experience"></a>Środowisko migracji
Przed rozpoczęciem migracji:

* Upewnij się, że zasoby do zmigrowania nie używają żadnych nieobsługiwanych funkcji ani konfiguracji. Zazwyczaj platforma wykrywa te problemy i zgłasza błąd.
* Jeśli masz maszyny wirtualne, które nie znajdują się w sieci wirtualnej, są one zatrzymane i cofnięte w ramach operacji przygotowania. Jeśli nie chcesz utracić publicznego adresu IP, rozważ zarezerwowanie adresu IP przed wyzwoleniem operacji przygotowania. Jeśli maszyny wirtualne znajdują się w sieci wirtualnej, nie są one zatrzymane ani cofnięte.
* Zaplanuj przeprowadzenie migracji poza godzinami pracy, aby uwzględnić możliwość wystąpienia nieoczekiwanych awarii podczas migracji.
* Pobierz bieżącą konfigurację maszyn wirtualnych przy użyciu programu PowerShell, poleceń interfejsu wiersza polecenia (CLI) lub interfejsów API REST, aby ułatwić walidację po zakończeniu kroku przygotowania.
* Przed rozpoczęciem migracji zaktualizuj skrypty automatyzacji i operacjonalizacji, aby obsługiwały model wdrażania Menedżer zasobów. Opcjonalnie możesz wykonać operacje GET, gdy zasoby są w stanie przygotowania.
* Oceń zasady kontroli dostępu opartej na rolach (Azure RBAC), które są skonfigurowane dla zasobów IaaS w klasycznym modelu wdrażania, i Zaplanuj po zakończeniu migracji.

Przepływ pracy migracji jest następujący:

![Diagram przedstawiający przepływ pracy migracji](./media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Wszystkie operacje opisane w poniższych sekcjach dotyczą wszystkich idempotentne. Jeśli masz problem inny niż nieobsługiwana funkcja lub błąd konfiguracji, ponów próbę wykonania operacji przygotowywania, przerwania lub zatwierdzania. Platforma Azure próbuje wykonać akcję ponownie.
>
>

### <a name="validate"></a>Walidacja
Operacja walidacji to pierwszy krok w procesie migracji. Celem tego kroku jest analizowanie stanu zasobów, które mają zostać zmigrowane w klasycznym modelu wdrażania. Operacja oblicza, czy zasoby mogą przeprowadzić migrację (powodzenie lub niepowodzenie).

Należy wybrać sieć wirtualną lub usługę w chmurze (jeśli nie znajduje się ona w sieci wirtualnej), która ma zostać zweryfikowana pod kątem migracji. Jeśli zasób nie może przeprowadzić migracji, platforma Azure wyświetla listę przyczyn.

#### <a name="checks-not-done-in-the-validate-operation"></a>Nie wykonano testów w operacji walidacji

Operacja walidacji analizuje tylko stan zasobów w klasycznym modelu wdrażania. Może ona sprawdzać wszystkie błędy i nieobsługiwane scenariusze z powodu różnych konfiguracji w klasycznym modelu wdrażania. Nie można wyszukać wszystkich problemów, które mogą nałożyć stos Azure Resource Manager na zasoby podczas migracji. Te problemy są sprawdzane tylko wtedy, gdy zasoby poddawane transformacji w następnym kroku migracji (operacja przygotowania). W poniższej tabeli wymieniono wszystkie problemy, które nie są sprawdzane w operacji weryfikacji:


|Testy sieci nie są w operacji weryfikacji|
|-|
|Sieć wirtualna mająca zarówno bramy ER, jak i sieci VPN.|
|Połączenie bramy sieci wirtualnej w stanie odłączonym.|
|Wszystkie obwody usługi ER są wstępnie migrowane do Azure Resource Manager stosu.|
|Azure Resource Manager sprawdza limity przydziału zasobów sieciowych. Na przykład: statyczny publiczny adres IP, dynamiczne publiczne adresy IPv4, moduł równoważenia obciążenia, sieciowe grupy zabezpieczeń, tabele tras i interfejsy sieciowe. |
| Wszystkie reguły modułu równoważenia obciążenia są prawidłowe w ramach wdrożenia i sieci wirtualnej. |
| Konflikty prywatnych adresów IP między oddzielonymi maszynami wirtualnymi w tej samej sieci wirtualnej. |

### <a name="prepare"></a>Przygotowywanie
Operacja przygotowania to drugi krok w procesie migracji. Celem tego kroku jest symulowanie transformacji zasobów IaaS z klasycznego modelu wdrażania w celu Menedżer zasobów zasobów. Kolejna operacja przygotowywania przedstawia ten bok obok siebie, aby wizualizować.

> [!NOTE] 
> Twoje zasoby w klasycznym modelu wdrażania nie są modyfikowane w tym kroku. Jest to bezpieczny krok do uruchomienia, jeśli próbujesz przeprowadzić migrację. 

Wybierz sieć wirtualną lub usługę w chmurze (jeśli nie jest to sieć wirtualna), która ma zostać przygotowana do migracji.

* Jeśli zasób nie może przeprowadzić migracji, platforma Azure zatrzyma proces migracji i wyświetli przyczynę niepowodzenia operacji przygotowania.
* Jeśli zasób jest w stanie migracji, platforma Azure zablokuje operacje płaszczyzny zarządzania dla zasobów objętych migracją. Na przykład nie będzie można dodać dysku danych do migrowanej maszyny wirtualnej.

Na platformie Azure rozpocznie się migracja metadanych z klasycznego modelu wdrażania w celu Menedżer zasobów migracji zasobów.

Po zakończeniu operacji przygotowywania można wizualizować zasoby zarówno w klasycznym modelu wdrażania, jak i w Menedżer zasobów. Dla każdej usługi w chmurze w klasycznym modelu wdrażania platforma Azure tworzy nazwę grupy zasobów zgodnie ze wzorcem `cloud-service-name>-Migrated`.

> [!NOTE]
> Nie można wybrać nazwy grupy zasobów utworzonej dla zmigrowanych zasobów (czyli "-zmigrowany"). Po zakończeniu migracji można jednak użyć funkcji przenoszenia programu Azure Resource Manager, aby przenieść zasoby do dowolnej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../azure-resource-manager/management/move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

Poniższe dwa zrzuty ekranu pokazują wynik po pomyślnej operacji przygotowania. Pierwszy z nich zawiera grupę zasobów zawierającą oryginalną usługę w chmurze. Druga z nich zawiera nową grupę zasobów "-zmigrowany", która zawiera równoważne zasoby Azure Resource Manager.

![Zrzut ekranu przedstawiający oryginalną usługę w chmurze](./media/migration-classic-resource-manager/portal-classic.png)

![Zrzut ekranu przedstawiający zasoby Azure Resource Manager w operacji przygotowywania](./media/migration-classic-resource-manager/portal-arm.png)

W tym miejscu znajdują się w tle zasoby po zakończeniu fazy przygotowania. Należy pamiętać, że zasób w płaszczyźnie danych jest taki sam. Jest reprezentowana w płaszczyźnie zarządzania (klasyczny model wdrażania) i płaszczyzny kontroli (Menedżer zasobów).

![Diagram fazy przygotowania](./media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Maszyny wirtualne, które nie znajdują się w sieci wirtualnej w klasycznym modelu wdrażania, są zatrzymane i cofnięte w tej fazie migracji.
>

### <a name="check-manual-or-scripted"></a>Sprawdzenie (ręczne lub za pomocą skryptu)
W kroku wyboru można użyć pobranej wcześniej konfiguracji w celu sprawdzenia, czy migracja jest prawidłowa. Alternatywnie możesz zalogować się do portalu i sprawdzić właściwości i zasoby, aby sprawdzić, czy migracja metadanych jest dobra.

Jeśli migrujesz sieć wirtualną, większość konfiguracji maszyn wirtualnych nie zostanie uruchomiona ponownie. W przypadku aplikacji na tych maszynach wirtualnych możesz sprawdzić, czy aplikacja jest nadal uruchomiona.

Możesz przetestować skrypty monitorowania i działania, aby sprawdzić, czy maszyny wirtualne działają zgodnie z oczekiwaniami, oraz czy zaktualizowane skrypty działają prawidłowo. Tylko operacje GET są obsługiwane, jeśli zasoby są w stanie przygotowania.

Nie ma okna określonego czasu, przed upływem którego należy zatwierdzić migrację. Ten stan może trwać tak długo, jak chcesz. Jednak płaszczyzna zarządzania jest zablokowana dla tych zasobów do momentu przerwania lub zatwierdzenia.

Jeśli napotkasz jakiekolwiek problemy, zawsze możesz przerwać migrację i wrócić do klasycznego modelu wdrażania. Po przejściu na platformę Azure otwierają operacje płaszczyzny zarządzania na zasobach, dzięki czemu można wznowić normalne operacje na tych maszynach wirtualnych w klasycznym modelu wdrażania.

### <a name="abort"></a>Przerwanie
Jest to opcjonalny krok, jeśli chcesz cofnąć zmiany do klasycznego modelu wdrażania i zatrzymać migrację. Ta operacja usuwa metadane Menedżer zasobów (utworzone w kroku Prepare) dla zasobów. 

![Diagram kroku przerwania](media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Nie można wykonać tej operacji po wywołaniu operacji zatwierdzania.     
>

### <a name="commit"></a>Zatwierdzenie
Po zakończeniu walidacji możesz zatwierdzić migrację. Zasoby nie pojawiają się już w klasycznym modelu wdrażania i są dostępne tylko w modelu wdrażania Menedżer zasobów. Zmigrowanymi zasobami można zarządzać tylko w nowym portalu.

> [!NOTE]
> Jest to operacja idempotentna. Jeśli to się nie powiedzie, spróbuj ponownie wykonać operację. Jeśli to się nie powiedzie, Utwórz bilet pomocy technicznej lub Utwórz forum w witrynie [Microsoft Q&a](/answers/index.html)
>
>

![Diagram kroku zatwierdzania](media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Schemat blokowy migracji

Oto schemat blokowy pokazujący, jak kontynuować migrację:

![Zrzut ekranu przedstawiający kroki migracji](media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Tłumaczenie klasycznego modelu wdrażania na Menedżer zasobów zasobów
Możesz znaleźć klasyczny model wdrażania i Menedżer zasobów reprezentacje zasobów w poniższej tabeli. Inne funkcje i zasoby nie są obecnie obsługiwane.

| Reprezentacja klasyczna | Reprezentacja usługi Resource Manager | Uwagi |
| --- | --- | --- |
| Nazwa usługi w chmurze (nazwa usługi hostowanej) |Nazwa DNS |Podczas migracji jest tworzona nowa grupa zasobów dla każdej usługi w chmurze przy użyciu wzorca nazewnictwa `<cloudservicename>-migrated`. Ta grupa zasobów zawiera wszystkie zasoby. Nazwa usługi w chmurze stanie się nazwą DNS skojarzoną z publicznym adresem IP. |
| Maszyna wirtualna |Maszyna wirtualna |Właściwości specyficzne dla maszyny wirtualnej są migrowane bez zmian. Niektóre informacje osProfile, takie jak nazwa komputera, nie są przechowywane w klasycznym modelu wdrażania i pozostają puste po migracji. |
| Zasoby dysków dołączonych do maszyny wirtualnej |Niejawne dyski dołączone do maszyny wirtualnej |Dyski nie są modelowane jako zasoby najwyższego poziomu w modelu wdrażania usługi Resource Manager. Są one migrowane jako dyski niejawne w ramach maszyny wirtualnej. Tylko dyski dołączone do maszyny wirtualnej są obecnie obsługiwane. Maszyny wirtualne Menedżer zasobów mogą teraz używać kont magazynu w klasycznym modelu wdrażania, co umożliwia łatwe Migrowanie dysków bez żadnych aktualizacji. |
| Rozszerzenia maszyn wirtualnych |Rozszerzenia maszyn wirtualnych |Wszystkie rozszerzenia zasobów, z wyjątkiem rozszerzeń XML, są migrowane z klasycznego modelu wdrażania. |
| Certyfikaty maszyny wirtualnej |Certyfikaty w usłudze Azure Key Vault |Jeśli usługa w chmurze zawiera certyfikaty usług, proces migracji utworzy nowy magazyn kluczy platformy Azure dla każdej usługi w chmurze i przeniesie certyfikaty do magazynu kluczy. Maszyny wirtualne są aktualizowane, tak aby przywoływały certyfikaty z magazynu kluczy. <br><br> Nie usuwaj magazynu kluczy. Może to spowodować, że maszyna wirtualna przejdzie w stan niepowodzenia. |
| Konfiguracja usługi WinRM |Konfiguracja usługi WinRM w ramach elementu osProfile |Konfiguracja usługi Windows Remote Management jest przenoszona bez zmian w ramach migracji. |
| Właściwość Availability-set |Zasób Availability-set | Specyfikacja zestawu dostępności jest właściwością maszyny wirtualnej w klasycznym modelu wdrażania. Zestawy dostępności stają się zasobami najwyższego poziomu w ramach migracji. Następujące konfiguracje nie są obsługiwane: wiele zestawów dostępności dla usługi w chmurze, co najmniej jeden zestaw dostępności razem z maszyną wirtualną poza zestawem dostępności w ramach usługi w chmurze. |
| Konfiguracja sieci na maszynie wirtualnej |Podstawowy interfejs sieciowy |Konfiguracja sieci na maszynie wirtualnej jest reprezentowana jako zasób podstawowego interfejsu sieciowego po migracji. W przypadku maszyn wirtualnych poza siecią wirtualną wewnętrzny adres IP zmieni się podczas migracji. |
| Wiele interfejsów sieciowych na maszynie wirtualnej |Interfejsy sieciowe |Jeśli z maszyną wirtualną jest skojarzonych wiele interfejsów sieciowych, każdy interfejs sieciowy będzie zasobem najwyższego poziomu w ramach migracji wraz ze wszystkimi właściwościami. |
| Zestaw punktów końcowych z równoważeniem obciążenia |Moduł równoważenia obciążenia |W klasycznym modelu wdrażania do platformy jest przypisany niejawny moduł równoważenia obciążenia dla każdej usługi w chmurze. Podczas migracji jest tworzony nowy zasób modułu równoważenia obciążenia, a zestaw punktów końcowych równoważenia obciążenia staje się regułami modułu równoważenia obciążenia. |
| Reguły NAT dla ruchu przychodzącego |Reguły NAT dla ruchu przychodzącego |Wejściowe punkty końcowe zdefiniowane na maszynie wirtualnej są konwertowane na reguły translacji dla adresu sieciowego ruchu przychodzącego w module równoważenia obciążenia podczas migracji. |
| Adres VIP |Publiczny adres IP z nazwą DNS |Wirtualny adres IP będzie publicznym adresem IP i jest skojarzony z modułem równoważenia obciążenia. Wirtualny adres IP można zmigrować tylko pod warunkiem, że ma przypisany wejściowy punkt końcowy. |
| Sieć wirtualna |Sieć wirtualna |Sieć wirtualna jest migrowana razem ze wszystkimi właściwościami do modelu wdrażania usługi Resource Manager. Nowa grupa zasobów jest tworzona za pomocą sufiksu `-migrated`. |
| Zastrzeżone adresy IP |Publiczny adres IP z przydziałem statycznym |Zastrzeżone adresy IP skojarzone z modułem równoważenia obciążenia są migrowane w ramach migrowania usługi w chmurze lub maszyny wirtualnej. Nieskojarzone zastrzeżone adresy IP można migrować za pomocą polecenia [Move-AzureReservedIP](/powershell/module/servicemanagement/azure.service/move-azurereservedip).  |
| Publiczny adres IP dla maszyny wirtualnej |Publiczny adres IP z przydziałem dynamicznym |Publiczny adres IP skojarzony z maszyną wirtualną jest konwertowany na zasób publicznego adresu IP z przydziałem statycznym. |
| Sieciowe grupy zabezpieczeń |Sieciowe grupy zabezpieczeń |Sieciowa grupa zabezpieczeń skojarzona z podsiecią jest klonowana w ramach migracji do modelu wdrażania usługi Resource Manager. Sieciowa grupa zabezpieczeń w klasycznym modelu wdrażania nie jest usuwana podczas migracji. Jednak operacje płaszczyzny zarządzania dla sieciowej grupy zabezpieczeń są zablokowane, gdy trwa migracja. Nieskojarzone sieciowych grup zabezpieczeń można migrować za pomocą polecenia [Move-AzureNetworkSecurityGroup](/powershell/module/servicemanagement/azure.service/move-azurenetworksecuritygroup).|
| Serwery DNS |Serwery DNS |Serwery DNS skojarzone z siecią wirtualną lub maszyną wirtualną są migrowane w ramach migracji odpowiadających zasobów razem ze wszystkimi właściwościami. |
| Trasy zdefiniowane przez użytkownika |Trasy zdefiniowane przez użytkownika |Trasy zdefiniowane przez użytkownika skojarzone z podsiecią są klonowane w ramach migracji do modelu wdrażania usługi Resource Manager. Trasy zdefiniowane przez użytkownika w klasycznym modelu wdrażania nie są usuwane podczas migracji. Operacje płaszczyzny zarządzania dla tras zdefiniowanych przez użytkownika są zablokowane, gdy trwa migracja. Nieskojarzone UDR można migrować za pomocą polecenia [Move-AzureRouteTable](/powershell/module/servicemanagement/azure.service/Move-AzureRouteTable). |
| Właściwość przekazywania adresu IP w konfiguracji sieci maszyny wirtualnej |Właściwość przekazywania adresu IP dla karty sieciowej |Właściwość przekazywania adresu IP na maszynie wirtualnej jest konwertowana na właściwość interfejsu sieciowego podczas migracji. |
| Moduł równoważenia obciążenia z wieloma adresami IP |Moduł równoważenia obciążenia z wieloma zasobami publicznego adresu IP |Każdy publiczny adres IP skojarzony z modułem równoważenia obciążenia jest konwertowany na zasób publicznego adresu IP i skojarzony z modułem równoważenia obciążenia po migracji. |
| Wewnętrzne nazwy DNS na maszynie wirtualnej |Wewnętrzne nazwy DNS na karcie sieciowej |Podczas migracji wewnętrzne sufiksy DNS dla maszyn wirtualnych są migrowane do właściwości tylko do odczytu o nazwie „InternalDomainNameSuffix” na karcie sieciowej. Sufiks pozostaje niezmieniony po migracji, a rozdzielczość maszyny wirtualnej powinna być nadal działała jak wcześniej. |
| Brama sieci wirtualnej |Brama sieci wirtualnej |Właściwości bramy sieci wirtualnej są migrowane bez zmian. Adres VIP skojarzony z bramą także nie jest zmieniany. |
| Lokacja sieci lokalnej |Brama sieci lokalnej |Właściwości lokacji sieci lokalnej są migrowane bez zmian do nowego zasobu zwanego bramą sieci lokalnej. Reprezentuje prefiksy adresów lokalnych i adres IP bramy zdalnej. |
| Odwołania połączeń |Połączenie |Odwołania do łączności między bramą a lokacją sieci lokalnej w konfiguracji sieci są reprezentowane przez nowy zasób o nazwie połączenie. Wszystkie właściwości odwołania do łączności w plikach konfiguracji sieci są kopiowane bez zmian do zasobu połączenia. Łączność między sieciami wirtualnymi w klasycznym modelu wdrażania uzyskuje się przez utworzenie dwóch tuneli IPsec do lokacji sieci lokalnej reprezentujących sieci wirtualne. Jest on przekształcany na typ połączenia sieci wirtualnej z siecią-wirtualną w modelu Menedżer zasobów, bez konieczności używania bram sieci lokalnej. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Zmiany automatyzacji i narzędzi po migracji
W ramach migracji zasobów z klasycznego modelu wdrażania do modelu wdrażania Menedżer zasobów należy zaktualizować istniejącą automatyzację lub narzędzia, aby upewnić się, że nadal działa po migracji.


## <a name="next-steps"></a>Następne kroki

* [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md)
* [Użyj interfejsu wiersza polecenia do migrowania zasobów IaaS z klasycznego do Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [VPN Gateway klasyczny do Menedżer zasobów migracji](../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [Migruj obwody usługi ExpressRoute i skojarzone sieci wirtualne z klasycznego modelu wdrażania Menedżer zasobów](../expressroute/expressroute-migration-classic-resource-manager.md)
* [Narzędzia społecznościowe ułatwiające migrację zasobów IaaS z klasycznej do Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md)
* [Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi migrowania zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-faq.md)
