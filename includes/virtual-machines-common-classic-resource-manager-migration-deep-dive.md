---
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: tagore
ms.openlocfilehash: b86e0d784d26e9e483dd12e20d45189ae8bfb9bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866184"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrowanie zasobów IaaS z klasycznego modelu wdrażania do usługi Azure Resource Manager
Po pierwsze ważne jest, aby zrozumieć różnicę między płaszczyzną danych a operacjami płaszczyzny zarządzania w zasobach infrastruktury jako usługi (IaaS).

* *Płaszczyzna zarządzania/kontroli* opisuje wywołania, które wchodzą do płaszczyzny zarządzania/kontroli lub interfejsu API do modyfikowania zasobów. Operacje, na przykład takie jak utworzenie maszyny wirtualnej, ponowne uruchomienie maszyny wirtualnej i zaktualizowanie maszyny wirtualnej przy użyciu nowej podsieci, powodują wykonanie działań zarządzania uruchomionymi zasobami. Nie mają one bezpośredniego wpływu na łączenie się z maszynami wirtualnymi.
* *Płaszczyzna danych* (aplikacja) opisuje środowisko uruchomieniowe samej aplikacji i obejmuje interakcję z wystąpieniami, które nie przechodzą za pośrednictwem interfejsu API platformy Azure. Na przykład dostęp do witryny sieci Web lub ściąganie danych z uruchomionego wystąpienia programu SQL Server lub serwera MongoDB to interakcje płaszczyzny danych lub aplikacji. Inne przykłady obejmują kopiowanie obiektu blob z konta magazynu i uzyskiwanie dostępu do publicznego adresu IP w celu użycia protokołu RDP (Remote Desktop Protocol) lub Secure Shell (SSH) do maszyny wirtualnej. Te operacje utrzymują działanie aplikacji w ramach usług obliczeniowych, sieciowych i magazynowych.

Płaszczyzna danych jest taka sama między klasycznym modelem wdrażania a stosami Menedżera zasobów. Różnica polega na tym, że podczas procesu migracji firma Microsoft tłumaczy reprezentację zasobów z klasycznego modelu wdrażania na ten w stosie Menedżera zasobów. W związku z tym należy użyć nowych narzędzi, interfejsów API i zestawów SDK do zarządzania zasobami w stosie Menedżera zasobów.

![Diagram przedstawiający różnicę między płaszczyzną zarządzania/sterowania a płaszczyzną danych](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> W przypadku niektórych scenariuszy migracji platforma Azure zatrzymuje maszyny wirtualne, cofa ich przydział i uruchamia je ponownie. Powoduje to krótki przestój płaszczyzny danych.
>

## <a name="the-migration-experience"></a>Środowisko migracji
Przed rozpoczęciem migracji:

* Upewnij się, że zasoby do zmigrowania nie używają żadnych nieobsługiwanych funkcji ani konfiguracji. Zazwyczaj platforma wykrywa te problemy i zgłasza błąd.
* Jeśli masz maszyny wirtualne, które nie są w sieci wirtualnej, są one zatrzymywane i cofnięte alokacji w ramach operacji przygotowania. Jeśli nie chcesz utracić publicznego adresu IP, przed wyzwoleniem operacji przygotowywania należy rozważyć zarezerwowanie adresu IP. Jeśli maszyny wirtualne znajdują się w sieci wirtualnej, nie są zatrzymywane i cofnięte.
* Zaplanuj przeprowadzenie migracji poza godzinami pracy, aby uwzględnić możliwość wystąpienia nieoczekiwanych awarii podczas migracji.
* Pobierz bieżącą konfigurację maszyn wirtualnych przy użyciu programu PowerShell, poleceń interfejsu wiersza polecenia (CLI) lub interfejsów API REST, aby ułatwić walidację po zakończeniu kroku przygotowania.
* Przed rozpoczęciem migracji należy zaktualizować skrypty automatyzacji i operacjonalizacji w celu obsługi modelu wdrażania Menedżera zasobów. Opcjonalnie możesz wykonać operacje GET, gdy zasoby są w stanie przygotowania.
* Oceń zasady kontroli dostępu opartej na rolach (RBAC), które są skonfigurowane w zasobach IaaS w klasycznym modelu wdrażania i zaplanuj po zakończeniu migracji.

Przepływ pracy migracji jest następujący:

![Diagram przedstawiający przepływ pracy migracji](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Operacje opisane w poniższych sekcjach są idempotentne. Jeśli masz problem inny niż nieobsługicona funkcja lub błąd konfiguracji, ponów próbę wykonania operacji przygotowania, przerwania lub zatwierdzenia. Platforma Azure próbuje ponownie akcji.
>
>

### <a name="validate"></a>Walidacja
Operacja walidacji to pierwszy krok w procesie migracji. Celem tego kroku jest analizowanie stanu zasobów, które mają zostać migrowane w klasycznym modelu wdrażania. Operacja ocenia, czy zasoby są zdolne do migracji (powodzenie lub niepowodzenie).

Wybierz sieć wirtualną lub usługę w chmurze (jeśli nie znajduje się w sieci wirtualnej), które chcesz sprawdzić dla migracji. Jeśli zasób nie jest w stanie migracji, platforma Azure zawiera listę przyczyn.

#### <a name="checks-not-done-in-the-validate-operation"></a>Kontrole nie zostały wykonane w operacji sprawdzania poprawności

Operacja sprawdzania poprawności tylko analizuje stan zasobów w klasycznym modelu wdrażania. Można sprawdzić, czy wszystkie błędy i nieobsługiwał scenariuszy z powodu różnych konfiguracji w klasycznym modelu wdrażania. Nie można sprawdzić, czy wszystkie problemy, które stos usługi Azure Resource Manager może nałożyć na zasoby podczas migracji. Te problemy są sprawdzane tylko wtedy, gdy zasoby przechodzą transformację w następnym kroku migracji (operacja przygotowania). W poniższej tabeli wymieniono wszystkie problemy, które nie zostały sprawdzone w operacji sprawdzania poprawności:


|Kontrole sieciowe, które nie są w operacji sprawdzania poprawności|
|-|
|Sieć wirtualna posiadająca bramy ER i VPN.|
|Połączenie bramy sieci wirtualnej w stanie rozłączonym.|
|Wszystkie obwody ER są wstępnie migrowane do stosu usługi Azure Resource Manager.|
|Sprawdzanie przydziałów usługi Azure Resource Manager pod kątem zasobów sieciowych. Na przykład: statyczny publiczny adres IP, dynamiczne publiczne adresy IP, moduł równoważenia obciążenia, sieciowe grupy zabezpieczeń, tabele tras i interfejsy sieciowe. |
| Wszystkie reguły modułu równoważenia obciążenia są prawidłowe we wszystkich wdrożeniach i sieci wirtualnej. |
| Konflikt prywatnych adresów IP między stop-deallocated maszyn wirtualnych w tej samej sieci wirtualnej. |

### <a name="prepare"></a>Przygotowanie
Operacja przygotowania to drugi krok w procesie migracji. Celem tego kroku jest symulacja transformacji zasobów IaaS z klasycznego modelu wdrażania do zasobów Menedżera zasobów. Ponadto operacja przygotowanie przedstawia to side-by-side do wizualizacji.

> [!NOTE] 
> Zasoby w klasycznym modelu wdrażania nie są modyfikowane w tym kroku. Jest to bezpieczny krok do uruchomienia, jeśli próbujesz migracji. 

Wybierz sieć wirtualną lub usługę w chmurze (jeśli nie jest to sieć wirtualna), którą chcesz przygotować do migracji.

* Jeśli zasób nie jest w stanie migracji, platforma Azure zatrzymuje proces migracji i wyświetla przyczynę niepowodzenia operacji przygotowania.
* Jeśli zasób jest zdolny do migracji, platforma Azure blokuje operacje płaszczyzny zarządzania dla zasobów w ramach migracji. Na przykład nie będzie można dodać dysku danych do migrowanej maszyny wirtualnej.

Platforma Azure następnie rozpoczyna migrację metadanych z klasycznego modelu wdrażania do Menedżera zasobów dla zasobów migrujących.

Po zakończeniu operacji przygotowania można wizualizować zasoby zarówno w klasycznym modelu wdrażania, jak i w Menedżerze zasobów. Dla każdej usługi w chmurze w klasycznym modelu wdrażania platforma Azure tworzy nazwę grupy zasobów zgodnie ze wzorcem `cloud-service-name>-Migrated`.

> [!NOTE]
> Nie można wybrać nazwy grupy zasobów utworzonej dla migrowanych zasobów (czyli "-Migrowane"). Po zakończeniu migracji można jednak użyć funkcji przenoszenia usługi Azure Resource Manager, aby przenieść zasoby do dowolnej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../articles/resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

Następujące dwa zrzuty ekranu pokazują wynik po pomyślnej operacji przygotowania. Pierwsza z nich pokazuje grupę zasobów, która zawiera oryginalną usługę w chmurze. Druga pokazuje nową grupę zasobów "-Migrated", która zawiera równoważne zasoby usługi Azure Resource Manager.

![Zrzut ekranu przedstawiający oryginalną usługę w chmurze](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Zrzut ekranu przedstawiający zasoby usługi Azure Resource Manager w operacji przygotowania](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Oto zakulisowe spojrzenie na twoje zasoby po zakończeniu fazy przygotowań. Należy zauważyć, że zasób na płaszczyźnie danych jest taki sam. Jest reprezentowana zarówno w płaszczyźnie zarządzania (klasyczny model wdrażania), jak i na płaszczyźnie sterowania (Menedżer zasobów).

![Schemat fazy przygotowania](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Maszyny wirtualne, które nie znajdują się w sieci wirtualnej w klasycznym modelu wdrażania są zatrzymywane i cofnięte alokacji w tej fazie migracji.
>

### <a name="check-manual-or-scripted"></a>Sprawdzenie (ręczne lub za pomocą skryptu)
W kroku wyboru można użyć konfiguracji pobranej wcześniej, aby sprawdzić, czy migracja wygląda poprawnie. Alternatywnie można zalogować się do portalu i na miejscu sprawdzić właściwości i zasoby, aby sprawdzić, czy migracja metadanych wygląda dobrze.

Jeśli migrujesz sieć wirtualną, większość konfiguracji maszyn wirtualnych nie zostanie uruchomiona ponownie. W przypadku aplikacji na tych maszynach wirtualnych można sprawdzić, czy aplikacja jest nadal uruchomiona.

Można przetestować monitorowania i skryptów operacyjnych, aby sprawdzić, czy maszyny wirtualne działają zgodnie z oczekiwaniami i czy zaktualizowane skrypty działają poprawnie. Tylko operacje GET są obsługiwane, jeśli zasoby są w stanie przygotowania.

Nie ma ustawionego przedziału czasu, przed którym należy zatwierdzić migrację. Ten stan może trwać tak długo, jak chcesz. Jednak płaszczyzna zarządzania jest zablokowana dla tych zasobów do momentu przerwania lub zatwierdzenia.

Jeśli napotkasz jakiekolwiek problemy, zawsze możesz przerwać migrację i wrócić do klasycznego modelu wdrażania. Po powrocie platforma Azure otwiera operacje płaszczyzny zarządzania na zasobach, dzięki czemu można wznowić normalne operacje na tych maszynach wirtualnych w klasycznym modelu wdrażania.

### <a name="abort"></a>Przerwanie
Jest to opcjonalny krok, jeśli chcesz przywrócić zmiany do klasycznego modelu wdrażania i zatrzymać migrację. Ta operacja usuwa metadane Menedżera zasobów (utworzone w kroku przygotowania) dla zasobów. 

![Diagram kroku przerwania](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Tej operacji nie można wykonać po wyzwoleniu operacji zatwierdzania.     
>

### <a name="commit"></a>Zatwierdzenie
Po zakończeniu walidacji możesz zatwierdzić migrację. Zasoby nie są już wyświetlane w klasycznym modelu wdrażania i są dostępne tylko w modelu wdrażania Menedżera zasobów. Zmigrowanymi zasobami można zarządzać tylko w nowym portalu.

> [!NOTE]
> Jest to operacja idempotentna. Jeśli to się nie powiedzie, spróbuj ponowić próbę wykonania operacji. Jeśli nadal się nie powiedzie, utwórz bilet pomocy technicznej lub utwórz forum w systemie [Microsoft Q&A](https://docs.microsoft.com/answers/index.html)
>
>

![Diagram kroku zatwierdzania](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Schemat blokowy migracji

Oto schemat blokowy, który pokazuje, jak kontynuować migrację:

![Zrzut ekranu przedstawiający kroki migracji](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Tłumaczenie klasycznego modelu wdrażania na zasoby Menedżera zasobów
Klasyczny model wdrażania i reprezentacje zasobów w Menedżerze zasobów można znaleźć w poniższej tabeli. Inne funkcje i zasoby nie są obecnie obsługiwane.

| Reprezentacja klasyczna | Reprezentacja usługi Resource Manager | Uwagi |
| --- | --- | --- |
| Nazwa usługi w chmurze |Nazwa DNS |Podczas migracji jest tworzona nowa grupa zasobów dla każdej usługi w chmurze przy użyciu wzorca nazewnictwa `<cloudservicename>-migrated`. Ta grupa zasobów zawiera wszystkie zasoby. Nazwa usługi w chmurze stanie się nazwą DNS skojarzoną z publicznym adresem IP. |
| Maszyna wirtualna |Maszyna wirtualna |Właściwości specyficzne dla maszyny wirtualnej są migrowane bez zmian. Niektóre informacje osProfile, takie jak nazwa komputera, nie są przechowywane w klasycznym modelu wdrażania i pozostają puste po migracji. |
| Zasoby dysków dołączonych do maszyny wirtualnej |Niejawne dyski dołączone do maszyny wirtualnej |Dyski nie są modelowane jako zasoby najwyższego poziomu w modelu wdrażania usługi Resource Manager. Są one migrowane jako dyski niejawne w ramach maszyny wirtualnej. Tylko dyski dołączone do maszyny wirtualnej są obecnie obsługiwane. Maszyny wirtualne Menedżera zasobów mogą teraz używać kont magazynu w klasycznym modelu wdrażania, co umożliwia łatwą migrację dysków bez żadnych aktualizacji. |
| Rozszerzenia maszyn wirtualnych |Rozszerzenia maszyn wirtualnych |Wszystkie rozszerzenia zasobów, z wyjątkiem rozszerzeń XML, są migrowane z klasycznego modelu wdrażania. |
| Certyfikaty maszyny wirtualnej |Certyfikaty w usłudze Azure Key Vault |Jeśli usługa w chmurze zawiera certyfikaty usług, migracja tworzy nowy magazyn kluczy platformy Azure na usługę w chmurze i przenosi certyfikaty do magazynu kluczy. Maszyny wirtualne są aktualizowane, tak aby przywoływały certyfikaty z magazynu kluczy. <br><br> Nie należy usuwać magazynu kluczy. Może to spowodować, że maszyna wirtualna przejdzie w stan awarii. |
| Konfiguracja usługi WinRM |Konfiguracja usługi WinRM w ramach elementu osProfile |Konfiguracja usługi Windows Remote Management jest przenoszona bez zmian w ramach migracji. |
| Właściwość Availability-set |Zasób Availability-set | Specyfikacja zestawu dostępności jest właściwością na maszynie Wirtualnej w klasycznym modelu wdrażania. Zestawy dostępności stają się zasobami najwyższego poziomu w ramach migracji. Następujące konfiguracje nie są obsługiwane: wiele zestawów dostępności dla usługi w chmurze, co najmniej jeden zestaw dostępności razem z maszyną wirtualną poza zestawem dostępności w ramach usługi w chmurze. |
| Konfiguracja sieci na maszynie wirtualnej |Podstawowy interfejs sieciowy |Konfiguracja sieci na maszynie wirtualnej jest reprezentowana jako zasób podstawowego interfejsu sieciowego po migracji. W przypadku maszyn wirtualnych poza siecią wirtualną wewnętrzny adres IP zmieni się podczas migracji. |
| Wiele interfejsów sieciowych na maszynie wirtualnej |Interfejsy sieciowe |Jeśli maszyna wirtualna ma wiele interfejsów sieciowych skojarzonych z nim, każdy interfejs sieciowy staje się zasobem najwyższego poziomu w ramach migracji, wraz ze wszystkimi właściwościami. |
| Zestaw punktów końcowych z równoważeniem obciążenia |Moduł równoważenia obciążenia |W klasycznym modelu wdrażania do platformy jest przypisany niejawny moduł równoważenia obciążenia dla każdej usługi w chmurze. Podczas migracji jest tworzony nowy zasób modułu równoważenia obciążenia, a zestaw punktów końcowych równoważenia obciążenia staje się regułami modułu równoważenia obciążenia. |
| Reguły NAT dla ruchu przychodzącego |Reguły NAT dla ruchu przychodzącego |Wejściowe punkty końcowe zdefiniowane na maszynie wirtualnej są konwertowane na reguły translacji dla adresu sieciowego ruchu przychodzącego w module równoważenia obciążenia podczas migracji. |
| Adres VIP |Publiczny adres IP z nazwą DNS |Wirtualny adres IP staje się publicznym adresem IP i jest skojarzony z modułem równoważenia obciążenia. Wirtualny adres IP można zmigrować tylko pod warunkiem, że ma przypisany wejściowy punkt końcowy. |
| Sieć wirtualna |Sieć wirtualna |Sieć wirtualna jest migrowana razem ze wszystkimi właściwościami do modelu wdrażania usługi Resource Manager. Nowa grupa zasobów jest tworzona za pomocą sufiksu `-migrated`. |
| Zastrzeżone adresy IP |Publiczny adres IP z przydziałem statycznym |Zastrzeżone adresy IP skojarzone z modułem równoważenia obciążenia są migrowane w ramach migrowania usługi w chmurze lub maszyny wirtualnej. Migracja nieskojarzonego zastrzeżonego adresu IP nie jest obecnie obsługiwana. |
| Publiczny adres IP dla maszyny wirtualnej |Publiczny adres IP z przydziałem dynamicznym |Publiczny adres IP skojarzony z maszyną wirtualną jest konwertowany na zasób publicznego adresu IP z przydziałem statycznym. |
| Sieciowe grupy zabezpieczeń |Sieciowe grupy zabezpieczeń |Sieciowa grupa zabezpieczeń skojarzona z podsiecią jest klonowana w ramach migracji do modelu wdrażania usługi Resource Manager. Sieciowa grupa zabezpieczeń w klasycznym modelu wdrażania nie jest usuwana podczas migracji. Jednak operacje płaszczyzny zarządzania dla sieciowej grupy zabezpieczeń są zablokowane, gdy trwa migracja. |
| Serwery DNS |Serwery DNS |Serwery DNS skojarzone z siecią wirtualną lub maszyną wirtualną są migrowane w ramach migracji odpowiadających zasobów razem ze wszystkimi właściwościami. |
| Trasy zdefiniowane przez użytkownika |Trasy zdefiniowane przez użytkownika |Trasy zdefiniowane przez użytkownika skojarzone z podsiecią są klonowane w ramach migracji do modelu wdrażania usługi Resource Manager. Trasy zdefiniowane przez użytkownika w klasycznym modelu wdrażania nie są usuwane podczas migracji. Operacje płaszczyzny zarządzania dla tras zdefiniowanych przez użytkownika są zablokowane, gdy trwa migracja. |
| Właściwość przekazywania adresu IP w konfiguracji sieci maszyny wirtualnej |Właściwość przekazywania adresu IP dla karty sieciowej |Właściwość przekazywania adresu IP na maszynie wirtualnej jest konwertowana na właściwość interfejsu sieciowego podczas migracji. |
| Moduł równoważenia obciążenia z wieloma adresami IP |Moduł równoważenia obciążenia z wieloma zasobami publicznego adresu IP |Każdy publiczny adres IP skojarzony z modułem równoważenia obciążenia jest konwertowany na publiczny zasób IP i skojarzony z modułem równoważenia obciążenia po migracji. |
| Wewnętrzne nazwy DNS na maszynie wirtualnej |Wewnętrzne nazwy DNS na karcie sieciowej |Podczas migracji wewnętrzne sufiksy DNS dla maszyn wirtualnych są migrowane do właściwości tylko do odczytu o nazwie „InternalDomainNameSuffix” na karcie sieciowej. Sufiks pozostaje niezmieniony po migracji, a rozdzielczość maszyny Wirtualnej powinna nadal działać tak jak poprzednio. |
| Brama sieci wirtualnej |Brama sieci wirtualnej |Właściwości bramy sieci wirtualnej są migrowane bez zmian. Adres VIP skojarzony z bramą także nie jest zmieniany. |
| Lokacja sieci lokalnej |Brama sieci lokalnej |Właściwości lokacji sieci lokalnej są migrowane bez zmian do nowego zasobu zwanego bramą sieci lokalnej. Reprezentuje to prefiksy adresów lokalnych i adres IP bramy zdalnej. |
| Odwołania połączeń |Połączenie |Odwołania do łączności między bramą a lokacją sieci lokalnej w konfiguracji sieci jest reprezentowana przez nowy zasób o nazwie Połączenie. Wszystkie właściwości odwołania do łączności w plikach konfiguracji sieci są kopiowane bez zmian do zasobu Połączenie. Łączność między sieciami wirtualnymi w klasycznym modelu wdrażania jest osiągana przez utworzenie dwóch tuneli IPsec do lokalnych lokacji sieciowych reprezentujących sieci wirtualne. Jest to przekształcane w typ połączenia sieć wirtualna z siecią wirtualną w modelu Menedżera zasobów, bez konieczności tworzenia bram sieci lokalnej. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Zmiany automatyzacji i narzędzi po migracji
W ramach migracji zasobów z klasycznego modelu wdrażania do modelu wdrażania Menedżera zasobów należy zaktualizować istniejącą automatyzację lub narzędzia, aby upewnić się, że będzie on nadal działać po migracji.
