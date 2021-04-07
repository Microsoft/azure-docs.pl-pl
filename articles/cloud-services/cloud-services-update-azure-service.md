---
title: Jak zaktualizować usługę w chmurze (klasyczną) | Microsoft Docs
description: Dowiedz się, jak zaktualizować usługi w chmurze na platformie Azure. Dowiedz się, jak działa aktualizacja usługi w chmurze w celu zapewnienia dostępności.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5d85003ca7b4307c308914484502ae03269f66ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741115"
---
# <a name="how-to-update-an-azure-cloud-service-classic"></a>Jak zaktualizować usługę w chmurze platformy Azure (klasyczną)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Proces aktualizowania usługi w chmurze, w tym jej ról i systemu operacyjnego gościa, to trzy czynności. Najpierw musi zostać przekazany plik binarny i pliki konfiguracyjne nowej usługi w chmurze lub wersji systemu operacyjnego. Następnie platforma Azure rezerwuje zasoby obliczeniowe i sieciowe dla usługi w chmurze na podstawie wymagań nowej wersji usługi w chmurze. Na koniec platforma Azure przeprowadza uaktualnienie stopniowe, aby stopniowo aktualizować dzierżawcę do nowej wersji lub systemu operacyjnego gościa, zachowując jednocześnie dostępność. W tym artykule omówiono szczegóły tego ostatniego kroku — uaktualnienie stopniowe.

## <a name="update-an-azure-service"></a>Aktualizowanie usługi platformy Azure
Platforma Azure organizuje wystąpienia roli w logiczne grupy o nazwie domeny uaktualnienia (UD). Domeny uaktualnienia (UD) to logiczne zestawy wystąpień ról, które są aktualizowane jako Grupa.  Platforma Azure aktualizuje jedną UD usługi w chmurze, dzięki czemu wystąpienia w innym miejscu mogą nadal obsługiwać ruch.

Domyślna liczba domen uaktualnienia to 5. Możesz określić inną liczbę domen uaktualnienia, dołączając atrybut upgradeDomainCount w pliku definicji usługi (. csdef). Aby uzyskać więcej informacji o atrybucie upgradeDomainCount, zobacz [schemat definicji usługi Azure Cloud Services (plik csdef)](./schema-csdef-file.md).

W przypadku wykonywania aktualizacji w miejscu co najmniej jednej roli w usłudze platforma Azure aktualizuje zestawy wystąpień ról zgodnie z domeną uaktualnienia, do której należą. Platforma Azure aktualizuje wszystkie wystąpienia w danej domenie uaktualnienia — je zatrzymują, aktualizując je, a następnie przełączając je do następnej domeny. Zatrzymując tylko wystąpienia uruchomione w bieżącej domenie uaktualnienia, platforma Azure sprawdza, czy aktualizacja ma najmniejszy możliwy wpływ na uruchomioną usługę. Aby uzyskać więcej informacji, zobacz [jak działa aktualizacja](#howanupgradeproceeds) w dalszej części tego artykułu.

> [!NOTE]
> Terminy **aktualizacji** i **uaktualniania** mają nieco inne znaczenie w kontekście platformy Azure, ale mogą być używane zamiennie w zakresie procesów i opisów funkcji w tym dokumencie.
>
>

Usługa musi definiować co najmniej dwa wystąpienia roli dla tej roli, aby można je było aktualizować w miejscu bez przestoju. Jeśli usługa składa się tylko z jednego wystąpienia jednej roli, usługa będzie niedostępna do momentu zakończenia aktualizacji w miejscu.

W tym temacie omówiono następujące informacje dotyczące aktualizacji platformy Azure:

* [Dozwolone zmiany usługi podczas aktualizacji](#AllowedChanges)
* [Jak jest przeprowadzane uaktualnienie](#howanupgradeproceeds)
* [Wycofywanie aktualizacji](#RollbackofanUpdate)
* [Inicjowanie wielu operacji mutacji na trwającym wdrożeniu](#multiplemutatingoperations)
* [Dystrybucja ról w domenach uaktualnienia](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Dozwolone zmiany usługi podczas aktualizacji
W poniższej tabeli przedstawiono dozwolone zmiany usługi w trakcie aktualizacji:

| Zmiany dozwolone w hostingu, usługach i rolach | Aktualizacja w miejscu | Przemieszczanie (wymiana adresów VIP) | Usuń i ponownie Wdróż |
| --- | --- | --- | --- |
| Wersja systemu operacyjnego |Tak |Tak |Tak |
| Poziom zaufania platformy .NET |Tak |Tak |Tak |
| Rozmiar maszyny wirtualnej<sup>1</sup> |Tak<sup>2</sup> |Tak |Tak |
| Ustawienia magazynu lokalnego |Zwiększ tylko<sup>2</sup> |Tak |Tak |
| Dodawanie lub usuwanie ról w usłudze |Tak |Tak |Tak |
| Liczba wystąpień określonej roli |Tak |Tak |Tak |
| Liczba lub typ punktów końcowych dla usługi |Tak<sup>2</sup> |Nie |Tak |
| Nazwy i wartości ustawień konfiguracji |Tak |Tak |Tak |
| Wartości (ale nie nazwy) ustawień konfiguracji |Tak |Tak |Tak |
| Dodaj nowe certyfikaty |Tak |Tak |Tak |
| Zmień istniejące certyfikaty |Tak |Tak |Tak |
| Wdróż nowy kod |Tak |Tak |Tak |

<sup>1</sup> zmiana rozmiaru ograniczona do podzbioru rozmiarów dostępnych dla usługi w chmurze.

<sup>2</sup> wymaga zestawu Azure SDK w wersji 1,5 lub nowszej.

> [!WARNING]
> Zmiana rozmiaru maszyny wirtualnej spowoduje zniszczenie danych lokalnych.
>
>

Następujące elementy nie są obsługiwane w trakcie aktualizacji:

* Zmiana nazwy roli. Usuń, a następnie dodaj rolę z nową nazwą.
* Zmiana liczby domen uaktualnienia.
* Zmniejszenie rozmiaru zasobów lokalnych.

Jeśli wprowadzasz inne aktualizacje definicji usługi, takie jak zmniejszenie rozmiaru zasobu lokalnego, zamiast tego należy wykonać aktualizację zamiany adresu VIP. Aby uzyskać więcej informacji, zobacz [swap Deployment](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Jak jest przeprowadzane uaktualnienie
Możesz zdecydować, czy chcesz zaktualizować wszystkie role usługi lub pojedynczą rolę w usłudze. W obu przypadkach wszystkie wystąpienia każdej roli, która jest uaktualniana i należą do pierwszej domeny uaktualnienia, są zatrzymane, uaktualnione i przywrócone do trybu online. Po powrocie do trybu online wystąpienia w drugiej domenie uaktualnienia zostaną zatrzymane, uaktualnione i przywrócone do trybu online. Usługa w chmurze może mieć co najwyżej jedno uaktualnienie aktywne w danym momencie. Uaktualnienie jest zawsze wykonywane względem najnowszej wersji usługi w chmurze.

Na poniższym diagramie przedstawiono, w jaki sposób uaktualnienie jest wykonywane, jeśli uaktualniane są wszystkie role w usłudze:

![Usługa uaktualnienia](media/cloud-services-update-azure-service/IC345879.png "Usługa uaktualnienia")

Ten następny diagram ilustruje, w jaki sposób aktualizacja jest przeprowadzana, Jeśli uaktualniana jest tylko jedna rola:

![Uaktualnij rolę](media/cloud-services-update-azure-service/IC345880.png "Uaktualnij rolę")  

Podczas automatycznej aktualizacji kontroler sieci szkieletowej platformy Azure okresowo szacuje kondycję usługi w chmurze, aby określić, kiedy jest bezpiecznie, aby przeprowadzić kolejne UD. Ta ocena kondycji jest przeprowadzana na poszczególnych rolach i uwzględnia tylko wystąpienia w najnowszej wersji (tj. wystąpienia z tej pory, które zostały już przetworzone). Sprawdza, czy minimalna liczba wystąpień roli dla każdej roli osiągnęła zadowalający stan terminalu.

### <a name="role-instance-start-timeout"></a>Limit czasu uruchamiania wystąpienia roli
Kontroler sieci szkieletowej czeka 30 minut, aż każde wystąpienie roli osiągnie stan uruchomienia. W przypadku upływu limitu czasu, Kontroler sieci szkieletowej będzie kontynuował pracę z następnym wystąpieniem roli.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Wpływ na dane na dysku podczas uaktualniania usług w chmurze

Podczas uaktualniania usługi z jednego wystąpienia do wielu wystąpień usługa zostanie przełączona w tryb, gdy uaktualnienie zostanie przeprowadzone ze względu na sposób uaktualniania usług platformy Azure. Umowa dotycząca poziomu usług, która gwarantuje dostępność usługi, ma zastosowanie tylko do usług wdrożonych z więcej niż jednym wystąpieniem. Na poniższej liście opisano, w jaki sposób dane na poszczególnych dyskach mają wpływ na każdy scenariusz uaktualniania usługi platformy Azure:

|Scenariusz|Dysk C|Stacja D|Stacja E|
|--------|-------|-------|-------|
|Ponowne uruchomienie maszyny wirtualnej|Naruszon|Naruszon|Naruszon|
|Ponowny rozruch portalu|Naruszon|Naruszon|Zniszcz|
|Odobrazowanie portalu|Naruszon|Zniszcz|Zniszcz|
|Uaktualnianie In-Place|Naruszon|Naruszon|Zniszcz|
|Migracja węzłów|Zniszcz|Zniszcz|Zniszcz|

Należy pamiętać, że na powyższej liście dysk E: reprezentuje dysk główny roli i nie powinien być zakodowany na stałe. Zamiast tego należy użyć zmiennej środowiskowej **% RoleRoot%** do reprezentowania dysku.

Aby zminimalizować czas przestoju podczas uaktualniania usługi pojedynczego wystąpienia, należy wdrożyć nową usługę wiele wystąpień na serwerze tymczasowym i przeprowadzić zamianę adresu VIP.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Wycofywanie aktualizacji
Platforma Azure zapewnia elastyczność zarządzania usługami w trakcie aktualizacji, umożliwiając zainicjowanie dodatkowych operacji w ramach usługi po zaakceptowaniu pierwotnego żądania aktualizacji przez kontroler sieci szkieletowej platformy Azure. Wycofywanie można wykonać tylko wtedy, gdy aktualizacja (zmiana konfiguracji) lub uaktualnienie jest w stanie **w toku** wdrożenia. Aktualizacja lub uaktualnienie jest uznawane za w toku, o ile istnieje co najmniej jedno wystąpienie usługi, które nie zostało jeszcze zaktualizowane do nowej wersji. Aby sprawdzić, czy jest dozwolone wycofywanie, sprawdź wartość flagi RollbackAllowed zwróconej przez polecenie [Pobierz wdrożenie](/previous-versions/azure/reference/ee460804(v=azure.100)) i [Pobierz właściwości usługi w chmurze](/previous-versions/azure/reference/ee460806(v=azure.100)) , która jest ustawiona na wartość true.

> [!NOTE]
> Zaleca się wywołaj funkcję wycofywania na potrzeby aktualizacji lub uaktualnienia **w miejscu** , ponieważ uaktualnienia wymiany wirtualnych adresów VIP obejmują wymianę jednego całego działającego wystąpienia usługi z inną.
>
>

Wycofanie aktualizacji w toku ma następujące skutki dla wdrożenia:

* Wszystkie wystąpienia roli, które nie zostały jeszcze zaktualizowane lub uaktualnione do nowej wersji, nie są aktualizowane ani uaktualniane, ponieważ te wystąpienia mają już uruchomioną wersję docelową usługi.
* Wszystkie wystąpienia roli, które zostały już zaktualizowane lub uaktualnione do nowej wersji pliku pakietu usługi ( \* . cspkg) lub pliku konfiguracji usługi ( \* . cscfg), są przywracane do wersji sprzed uaktualnienia tych plików.

Ta funkcja jest zapewniana przez następujące funkcje:

* Operacja [wycofania aktualizacji lub uaktualnienia](/previous-versions/azure/reference/hh403977(v=azure.100)) , która może zostać wywołana na aktualizacji konfiguracji (wyzwolona przez wywołanie [konfiguracji zmiany wdrożenia](/previous-versions/azure/reference/ee460809(v=azure.100))) lub uaktualnienie (wyzwolone przez wywołanie [wdrożenia uaktualnienia](/previous-versions/azure/reference/ee460793(v=azure.100))), o ile w usłudze istnieje co najmniej jedno wystąpienie, które nie zostało jeszcze zaktualizowane do nowej wersji.
* Element zablokowany i element RollbackAllowed, które są zwracane jako część treści odpowiedzi operacji [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) i [Get Cloud Service Properties](/previous-versions/azure/reference/ee460806(v=azure.100)) :

  1. Zablokowany element pozwala wykryć, kiedy można wywołać operację mutacji w danym wdrożeniu.
  2. Element RollbackAllowed umożliwia wykrycie, kiedy operacja [wycofywania aktualizacji lub uaktualnienia](/previous-versions/azure/reference/hh403977(v=azure.100)) może zostać wywołana dla danego wdrożenia.

  Aby można było wykonać wycofywanie, nie trzeba sprawdzać zarówno elementów zablokowanych, jak i RollbackAllowed. Wystarczy potwierdzić, że RollbackAllowed ma wartość true. Te elementy są zwracane tylko wtedy, gdy te metody są wywoływane przy użyciu nagłówka żądania ustawionego na wartość "x-MS-Version: 2011-10-01" lub nowszą. Aby uzyskać więcej informacji na temat nagłówków wersji, zobacz [przechowywanie wersji usługi Service Management](/previous-versions/azure/gg592580(v=azure.100)).

Istnieją sytuacje, w których wycofanie aktualizacji lub uaktualnienia nie jest obsługiwane, są następujące:

* Zmniejszenie zasobów lokalnych — Jeśli aktualizacja spowoduje zwiększenie zasobów lokalnych dla roli, platforma Azure nie zezwala na wycofywanie.
* Ograniczenia przydziałów — Jeśli aktualizacja była operacją skalowania w dół, nie ma już wystarczającego limitu przydziału obliczeń, aby ukończyć operację wycofywania. Z każdą subskrypcją platformy Azure jest skojarzony przydział, który określa maksymalną liczbę rdzeni, które mogą być używane przez wszystkie usługi hostowane należące do tej subskrypcji. Jeśli wykonanie wycofania danej aktualizacji spowodowałoby przekroczenie limitu przydziału, wówczas wycofanie nie zostanie włączone.
* Sytuacja wyścigu — jeśli początkowa aktualizacja została ukończona, wycofywanie nie jest możliwe.

Przykładem sytuacji, w której wycofywanie aktualizacji może być przydatne w przypadku korzystania z operacji uaktualniania wdrożenia w trybie ręcznym w celu kontrolowania szybkości, z jaką zostanie [wdrożone](/previous-versions/azure/reference/ee460793(v=azure.100)) główne uaktualnienie w miejscu do usługi hostowanej na platformie Azure.

Podczas [wdrażania wdrożenia](/previous-versions/azure/reference/ee460793(v=azure.100)) uaktualnienia należy wykonać w trybie ręcznym i rozpocząć sprawdzanie domen uaktualnienia. Jeśli w pewnym momencie jest monitorowane uaktualnienie, należy zauważyć, że niektóre wystąpienia roli w pierwszej domenie uaktualnienia przestaną reagować, można wywołać operację [wycofywania aktualizacji lub uaktualnienia](/previous-versions/azure/reference/hh403977(v=azure.100)) do wdrożenia, co spowoduje pozostawienie niezmienionej instancji, które nie zostały jeszcze uaktualnione i wycofał wystąpienia, które zostały uaktualnione do poprzedniego pakietu usługi i konfiguracji.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Inicjowanie wielu operacji mutacji na trwającym wdrożeniu
W niektórych przypadkach może być konieczne zainicjowanie wielu jednoczesnych operacji mutacji na trwającym wdrożeniu. Na przykład możesz wykonać aktualizację usługi i, podczas gdy ta aktualizacja jest wdrażana w ramach usługi, trzeba wprowadzić pewne zmiany, np. w celu wycofania aktualizacji, zastosowania innej aktualizacji lub nawet usunięcia wdrożenia. Przypadek, w którym może być to konieczne, ma zastosowanie w przypadku, gdy uaktualnienie usługi zawiera kod debugowany, który powoduje powtarzanie awarii wystąpienia roli uaktualnionej. W takim przypadku Kontroler sieci szkieletowej platformy Azure nie będzie mógł postępować zgodnie z uaktualnieniem, ponieważ niewystarczająca liczba wystąpień w uaktualnionej domenie jest w dobrej kondycji. Ten stan jest określany jako *zablokowane wdrożenie*. Możesz odwrócić wdrożenie, wycofując aktualizację lub stosując nową aktualizację zamiast jednego z nich.

Po odebraniu wstępnego żądania zaktualizowania lub uaktualnienia usługi przez kontroler sieci szkieletowej platformy Azure można rozpocząć kolejne mutacje operacji. Oznacza to, że nie musisz czekać na ukończenie początkowej operacji przed rozpoczęciem kolejnej operacji mutacji.

Zainicjowanie drugiej operacji aktualizacji, gdy pierwsza aktualizacja jest w toku, będzie działać podobnie jak operacja wycofywania. Jeśli druga aktualizacja działa w trybie automatycznym, pierwsza domena uaktualnienia zostanie uaktualniona natychmiast, prawdopodobnie prowadząc do wystąpienia z wielu domen uaktualnienia w tym samym punkcie w czasie.

Operacje zmieniania są następujące: [Zmień konfigurację wdrożenia](/previous-versions/azure/reference/ee460809(v=azure.100)), [wdrożenie uaktualnienia](/previous-versions/azure/reference/ee460793(v=azure.100)), [stan wdrożenia aktualizacji](/previous-versions/azure/reference/ee460808(v=azure.100)), [Usuń wdrożenie](/previous-versions/azure/reference/ee460815(v=azure.100))i [Wycofaj aktualizację lub uaktualnienie](/previous-versions/azure/reference/hh403977(v=azure.100)).

Dwie operacje, [Uzyskiwanie wdrożenia](/previous-versions/azure/reference/ee460804(v=azure.100)) i [pobieranie właściwości usługi w chmurze](/previous-versions/azure/reference/ee460806(v=azure.100))zwracają zablokowaną flagę, która może zostać zbadana w celu ustalenia, czy można wywołać operację mutacji w danym wdrożeniu.

Aby wywołać wersję tych metod, która zwraca flagę zablokowaną, należy ustawić nagłówek żądania na wartość "x-MS-Version: 2011-10-01" lub "nowsza". Aby uzyskać więcej informacji na temat nagłówków wersji, zobacz [przechowywanie wersji usługi Service Management](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Dystrybucja ról w domenach uaktualnienia
Na platformie Azure wystąpienia roli są dystrybuowane równomiernie przez określoną liczbę domen uaktualnienia, które można skonfigurować w ramach pliku definicji usługi (. csdef). Maksymalna liczba domen uaktualnienia to 20, a wartość domyślna to 5. Aby uzyskać więcej informacji na temat modyfikowania pliku definicji usługi, zobacz [schemat definicji usługi platformy Azure (plik csdef)](cloud-services-model-and-package.md#csdef).

Na przykład jeśli rola ma dziesięć wystąpień, domyślnie Każda domena uaktualnienia zawiera dwa wystąpienia. Jeśli Twoja rola ma 14 wystąpień, cztery domeny uaktualnienia zawierają trzy wystąpienia, a piąta domena zawiera dwa.

Domeny uaktualnień są identyfikowane za pomocą indeksu od zera: pierwsza domena uaktualnienia ma identyfikator 0, a druga domena uaktualnienia ma identyfikator 1 i tak dalej.

Na poniższym diagramie przedstawiono sposób, w jaki usługa programu, która zawiera dwie role, jest dystrybuowana, gdy usługa definiuje dwie domeny uaktualnienia. Usługa działa w ośmiu wystąpieniach roli sieci Web i dziewięciu wystąpieniach roli proces roboczy.

![Dystrybucja domen uaktualnienia](media/cloud-services-update-azure-service/IC345533.png "Dystrybucja domen uaktualnienia")

> [!NOTE]
> Należy zauważyć, że platforma Azure kontroluje sposób przydzielenia wystąpień między domenami uaktualniania. Nie można określić, które wystąpienia są przyłączone do tej domeny.
>
>

## <a name="next-steps"></a>Następne kroki
[Jak zarządzać Cloud Services](cloud-services-how-to-manage-portal.md)  
[Jak monitorować Cloud Services](cloud-services-how-to-monitor.md)  
[Jak skonfigurować Cloud Services](cloud-services-how-to-configure-portal.md)