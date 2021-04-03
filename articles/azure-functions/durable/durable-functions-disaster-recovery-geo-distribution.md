---
title: Odzyskiwanie po awarii i dystrybucja geograficzna Durable Functions platformy Azure
description: Informacje na temat odzyskiwania po awarii i dystrybucji geograficznej w Durable Functions.
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89071214"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Odzyskiwanie po awarii i dystrybucja geograficzna na platformie Azure Durable Functions

Firma Microsoft dąży do zapewnienia, że usługi platformy Azure są zawsze dostępne. Może jednak wystąpić nieplanowana awaria usługi. Jeśli aplikacja wymaga odporności, firma Microsoft zaleca skonfigurowanie aplikacji pod kątem nadmiarowości geograficznej. Ponadto klienci powinni mieć plan odzyskiwania po awarii na potrzeby obsługi regionalnej awarii usługi. Ważna część planu odzyskiwania po awarii jest przygotowywana do przełączenia w tryb failover do pomocniczej repliki aplikacji i magazynu w przypadku, gdy replika podstawowa będzie niedostępna.

W Durable Functions wszystkie stany są domyślnie utrwalane w usłudze Azure Storage. [Centrum zadań](durable-functions-task-hubs.md) jest kontenerem logicznym dla zasobów usługi Azure Storage, które są używane dla [aranżacji](durable-functions-types-features-overview.md#orchestrator-functions) i [jednostek](durable-functions-types-features-overview.md#entity-functions). Funkcje programu Orchestrator, Activity i Entity mogą współdziałać ze sobą tylko wtedy, gdy należą do tego samego centrum zadań. Ten dokument odnosi się do centrów zadań w przypadku opisywania scenariuszy dotyczących wysokiej dostępności tych zasobów usługi Azure Storage.

Aranżacje i jednostki mogą być wyzwalane za pomocą [funkcji klienta](durable-functions-types-features-overview.md#client-functions) , które są wywoływane za pośrednictwem protokołu HTTP lub jednego z innych obsługiwanych typów wyzwalaczy Azure Functions. Mogą być również wyzwalane przy użyciu [wbudowanych interfejsów API protokołu HTTP](durable-functions-http-features.md#built-in-http-apis). Dla uproszczenia ten artykuł koncentruje się na scenariuszach obejmujących wyzwalacze usługi Azure Storage i funkcji opartych na protokole HTTP oraz o opcjach zwiększania dostępności i minimalizowania przestojów podczas działania odzyskiwania po awarii. Inne typy wyzwalaczy, takie jak Service Bus lub wyzwalacze Cosmos DB, nie zostaną jawnie omówione.

Poniższe scenariusze opierają się na konfiguracjach Active-Passive, ponieważ są one objęte użyciem usługi Azure Storage. Ten wzorzec składa się z wdrażania aplikacji funkcji Backup (pasywnej) w innym regionie. Traffic Manager będzie monitorować podstawową (aktywną) aplikację funkcji dla dostępności protokołu HTTP. Jeśli podstawowy zakończy się niepowodzeniem, nastąpi przełączenie w tryb failover do aplikacji funkcji Backup. Aby uzyskać więcej informacji, zobacz [Traffic-Routing metody priorytetu](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) [Traffic Manager platformy Azure](https://azure.microsoft.com/services/traffic-manager/).

> [!NOTE]
> - Proponowana konfiguracja Active-Passive zapewnia, że klient zawsze może wyzwolić nowe aranżacje za pośrednictwem protokołu HTTP. Jednak w związku z tym, że dwie aplikacje funkcji współużytkują ten sam centrum zadań w magazynie, niektóre transakcje magazynu w tle będą dystrybuowane między nimi. W związku z tym ta konfiguracja wiąże się z dodaniem kosztów ruchu wychodzącego dla aplikacji funkcji pomocniczej.
> - Podstawowe konto magazynu i centrum zadań są tworzone w regionie podstawowym i są współużytkowane przez obie aplikacje funkcji.
> - Wszystkie aplikacje funkcji, które są wdrożone nadmiarowo, muszą współużytkować te same klucze dostępu do funkcji w przypadku aktywowania za pośrednictwem protokołu HTTP. Środowisko uruchomieniowe funkcji udostępnia [interfejs API zarządzania](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) , który umożliwia konsumentom programistyczne Dodawanie, usuwanie i aktualizowanie kluczy funkcji. Zarządzanie kluczami jest również możliwe przy użyciu [Azure Resource Manager interfejsów API](https://www.markheath.net/post/managing-azure-functions-keys-2).

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenariusz 1 — Równoważenie obciążenia z magazynem udostępnionym

Jeśli infrastruktura obliczeniowa na platformie Azure ulegnie awarii, aplikacja funkcji może stać się niedostępna. Aby zminimalizować prawdopodobieństwo takiego przestoju, w tym scenariuszu są stosowane dwie aplikacje funkcji wdrożone w różnych regionach.
Traffic Manager jest skonfigurowany do wykrywania problemów w podstawowej aplikacji funkcji i automatycznego przekierowania ruchu do aplikacji funkcji w regionie pomocniczym. Ta aplikacja funkcji udostępnia to samo konto usługi Azure Storage i centrum zadań. W związku z tym stan aplikacji funkcji nie zostanie utracony, a działanie można wznowić w zwykły sposób. Gdy kondycja zostanie przywrócona do regionu podstawowego, usługa Azure Traffic Manager będzie automatycznie uruchamiać żądania routingu do tej aplikacji funkcji.

![Diagram przedstawiający scenariusz 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Korzystanie z tego scenariusza wdrażania wiąże się z kilkoma korzyściami:

- W przypadku niepowodzenia infrastruktury obliczeniowej można wznowić pracę w regionie trybu failover bez utraty danych.
- Traffic Manager automatycznie obsługuje automatyczne przejście w tryb failover do aplikacji funkcji w dobrej kondycji.
- Traffic Manager automatycznie przyłączyć ruch do podstawowej aplikacji funkcji po rozwiązaniu awarii.

Jednak w tym scenariuszu należy wziąć pod uwagę następujące zagadnienia:

- Jeśli aplikacja funkcji jest wdrażana przy użyciu dedykowanego planu App Service, replikowanie infrastruktury obliczeniowej w centrum danych trybu failover zwiększy koszty.
- Ten scenariusz obejmuje awarię infrastruktury obliczeniowej, ale konto magazynu jest nadal single point of failure dla aplikacji funkcji. Jeśli wystąpi awaria magazynu, aplikacja ponosi przestoje.
- Jeśli aplikacja funkcji zostanie przełączona w tryb failover, nastąpi zwiększenie opóźnienia, ponieważ będzie miało dostęp do konta magazynu w różnych regionach.
- Uzyskiwanie dostępu do usługi magazynu z innego regionu, w którym się znajduje, jest w większym zakresie ze względu na ruch wychodzący z sieci.
- Ten scenariusz zależy od Traffic Manager. Biorąc pod uwagę [sposób działania Traffic Manager](../../traffic-manager/traffic-manager-how-it-works.md), może to być czasochłonne, dopóki aplikacja kliencka korzystająca z funkcji trwałej nie musi ponownie wykonać zapytania dotyczącego adresu aplikacji funkcji z Traffic Manager.

> [!NOTE]
> Począwszy od **2.3.0** rozszerzenia Durable Functions, dwie aplikacje funkcji mogą być uruchamiane w tym samym czasie z tym samym kontem magazynu i konfiguracją centrum zadań. Pierwsza aplikacja do uruchomienia uzyska dzierżawę obiektów BLOB na poziomie aplikacji, która uniemożliwia innym aplikacjom kradzież komunikatów z kolejek centrum zadań. Jeśli ta pierwsza aplikacja przestanie działać, jej dzierżawa wygaśnie i będzie mogła zostać pobrana przez drugą aplikację, która następnie będzie przetwarzać komunikaty centrum zadań.
> 
> W systemach starszych niż v 2.3.0 aplikacje funkcji, które są skonfigurowane do korzystania z tego samego konta magazynu, będą przetwarzać komunikaty i aktualizować artefakty magazynu współbieżnie, co skutkuje znacznie wyższymi ogólnymi opóźnieniami i kosztami ruchu wychodzącego. Jeśli aplikacje podstawowe i repliki kiedykolwiek wcześniej zostały wdrożone różnego kodu, to nawet czasowo można przeprowadzić niepowodzeniem aranżacji z powodu niespójności funkcji programu Orchestrator w obu aplikacjach. W związku z tym zaleca się, aby wszystkie aplikacje, które wymagają dystrybucji geograficznej na potrzeby odzyskiwania po awarii, korzystały z 2.3.0 w wersji v lub nowszej.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenariusz 2 — zrównoważone obciążenie obliczeniowe z magazynem regionalnym

Poprzedni scenariusz dotyczy tylko przypadku awarii w infrastrukturze obliczeniowej. Jeśli usługa magazynu ulegnie awarii, spowoduje to awarię aplikacji funkcji.
Aby zapewnić ciągłą eksploatację trwałych funkcji, w tym scenariuszu jest stosowane konto magazynu lokalnego w każdym regionie, w którym są wdrażane aplikacje funkcji.

![Diagram przedstawiający scenariusz 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Takie podejście dodaje ulepszenia w poprzednim scenariuszu:

- Jeśli aplikacja funkcji ulegnie awarii, Traffic Manager zajmie przechodzenie w tryb failover do regionu pomocniczego. Jednak ponieważ aplikacja funkcji korzysta z własnego konta magazynu, funkcje trwałe nadal działają.
- Podczas pracy w trybie failover nie ma dodatkowego opóźnienia w regionie trybu failover, ponieważ aplikacja funkcji i konto magazynu są współzlokalizowane.
- Awaria warstwy magazynu spowoduje błędy w funkcjach trwałych, co z kolei spowoduje wyzwolenie przekierowania do regionu trybu failover. Ponownie, ponieważ aplikacja i magazyn funkcji są izolowane dla każdego regionu, funkcje trwałe będą nadal działać.

Ważne zagadnienia dotyczące tego scenariusza:

- Jeśli aplikacja funkcji jest wdrażana przy użyciu dedykowanego planu App Service, replikowanie infrastruktury obliczeniowej w centrum danych trybu failover zwiększy koszty.
- Bieżący stan nie przejdzie w tryb failover, co oznacza, że istniejące aranżacje i jednostki będą efektywnie wstrzymywane i niedostępne do momentu odzyskania regionu podstawowego.

Podsumowując, kompromis między pierwszym i drugim scenariuszem jest zachowaniem opóźnienia, a koszty ruchu wychodzącego są zminimalizowane, ale istniejące aranżacje i jednostki będą niedostępne podczas przestoju. Czy te kompromisy są akceptowalne, zależy od wymagań aplikacji.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenariusz 3 — przetwarzanie z równoważeniem obciążenia z magazynem udostępnionym GRS

Ten scenariusz jest modyfikacją w pierwszym scenariuszu, implementując konto magazynu udostępnionego. Główną różnicą jest to, że konto magazynu jest tworzone z włączoną replikacją geograficzną.
Funkcjonalnie ten scenariusz zapewnia takie same zalety jak scenariusz 1, ale zapewnia dodatkowe korzyści z odzyskiwania danych:

- Magazyn Geograficznie nadmiarowy (GRS) oraz dostęp do odczytu (RA-GRS) dla konta magazynu.
- W przypadku wystąpienia regionalnej awarii usługi magazynu można [ręcznie zainicjować pracę w trybie failover w replice pomocniczej](../../storage/common/storage-initiate-account-failover.md). W skrajnych okolicznościach, gdy region zostanie utracony ze względu na znaczną awarię, firma Microsoft może zainicjować regionalną pracę w trybie failover. W takim przypadku nie jest wymagana żadna akcja z Twojej strony.
- W przypadku przełączenia w tryb failover stan trwałych funkcji zostanie zachowany do ostatniej replikacji konta magazynu, co zwykle odbywa się co kilka minut.

Podobnie jak w przypadku innych scenariuszy, istnieją ważne zagadnienia:

- Przejście w tryb failover do repliki może zająć trochę czasu. Do momentu zaktualizowania trybu failover i rekordów DNS usługi Azure Storage aplikacja funkcji pozostanie nieprzerwana.
- Istnieje zwiększony koszt korzystania z kont magazynu replikowanych geograficznie.
- Replikacja GRS powoduje, że dane są kopiowane asynchronicznie. Niektóre z najnowszych transakcji mogą zostać utracone z powodu opóźnienia procesu replikacji.

![Diagram przedstawiający Scenariusz 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> Zgodnie z opisem w scenariuszu 1 zdecydowanie zaleca się, aby aplikacje funkcji wdrożone z tą strategią korzystały z 2.3.0 Durable Functions w wersji **v** lub nowszej.

Aby uzyskać więcej informacji, zobacz dokumentację [trybu failover odzyskiwania po awarii i konta magazynu usługi Azure Storage](../../storage/common/storage-disaster-recovery-guidance.md) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat projektowania aplikacji o wysokiej dostępności w usłudze Azure Storage](../../storage/common/geo-redundant-design.md)
