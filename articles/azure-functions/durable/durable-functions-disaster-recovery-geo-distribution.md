---
title: Odzyskiwanie po awarii i dystrybucja geograficzna Durable Functions platformy Azure
description: Informacje na temat odzyskiwania po awarii i dystrybucji geograficznej w Durable Functions.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 7951f216143bef0d48a6b751beff3f8f4316b9bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75433333"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Odzyskiwanie po awarii i dystrybucja geograficzna na platformie Azure Durable Functions

W Durable Functions wszystkie stany są utrwalane w usłudze Azure Storage. [Centrum zadań](durable-functions-task-hubs.md) jest kontenerem logicznym dla zasobów usługi Azure Storage, które są używane dla aranżacji. Funkcje programu Orchestrator i Activity mogą współdziałać ze sobą tylko wtedy, gdy należą do tego samego centrum zadań.
Opisane scenariusze umożliwiają zaproponowanie opcji wdrożenia w celu zwiększenia dostępności i zminimalizowania przestojów podczas działania odzyskiwania po awarii.

Należy zauważyć, że te scenariusze są oparte na konfiguracjach pasywnych, ponieważ są one objęte użyciem usługi Azure Storage. Ten wzorzec składa się z wdrażania aplikacji funkcji Backup (pasywnej) w innym regionie. Traffic Manager będzie monitorować podstawową (aktywną) aplikację funkcji pod kątem dostępności. Jeśli podstawowy zakończy się niepowodzeniem, nastąpi przełączenie w tryb failover do aplikacji funkcji Backup. Aby uzyskać więcej informacji, [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)Zobacz Traffic Manager [priorytetowa Metoda routingu.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - Proponowana konfiguracja aktywna-pasywna gwarantuje, że klient będzie zawsze mógł wyzwolić nowe aranżacje za pośrednictwem protokołu HTTP. Jednak w związku z tym, że dwie aplikacje funkcji współużytkują ten sam magazyn, przetwarzanie w tle będzie dystrybuowane między nimi, konkurując w przypadku komunikatów w tych samych kolejkach. Ta konfiguracja wiąże się z dodatkowymi kosztami ruchu wychodzącego dla aplikacji funkcji pomocniczej.
> - Podstawowe konto magazynu i centrum zadań są tworzone w regionie podstawowym i są współużytkowane przez obie aplikacje funkcji.
> - Wszystkie aplikacje funkcji, które są wdrażane nadmiarowo, muszą współużytkować te same klucze dostępu do funkcji w przypadku aktywowania za pośrednictwem protokołu HTTP. Środowisko uruchomieniowe funkcji udostępnia [interfejs API zarządzania](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) , który umożliwia konsumentom programistyczne Dodawanie, usuwanie i aktualizowanie kluczy funkcji.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenariusz 1 — Równoważenie obciążenia z magazynem udostępnionym

Jeśli infrastruktura obliczeniowa na platformie Azure ulegnie awarii, aplikacja funkcji może stać się niedostępna. Aby zminimalizować prawdopodobieństwo takiego przestoju, w tym scenariuszu są stosowane dwie aplikacje funkcji wdrożone w różnych regionach.
Traffic Manager jest skonfigurowany do wykrywania problemów w podstawowej aplikacji funkcji i automatycznego przekierowania ruchu do aplikacji funkcji w regionie pomocniczym. Ta aplikacja funkcji udostępnia to samo konto usługi Azure Storage i centrum zadań. W związku z tym stan aplikacji funkcji nie zostanie utracony, a działanie można wznowić w zwykły sposób. Gdy kondycja zostanie przywrócona do regionu podstawowego, usługa Azure Traffic Manager będzie automatycznie uruchamiać żądania routingu do tej aplikacji funkcji.

![Diagram przedstawiający scenariusz 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Korzystanie z tego scenariusza wdrażania wiąże się z kilkoma korzyściami:

- W przypadku niepowodzenia infrastruktury obliczeniowej można wznowić pracę w regionie pracy awaryjnej bez utraty stanu.
- Traffic Manager automatycznie działa w trybie failover w aplikacji funkcji w dobrej kondycji.
- Traffic Manager automatycznie przyłączyć ruch do podstawowej aplikacji funkcji po rozwiązaniu awarii.

Jednak w tym scenariuszu należy wziąć pod uwagę następujące zagadnienia:

- Jeśli aplikacja funkcji jest wdrażana przy użyciu dedykowanego planu App Service, replikowanie infrastruktury obliczeniowej w centrum danych w trybie failover zwiększy koszty.
- Ten scenariusz obejmuje awarię infrastruktury obliczeniowej, ale konto magazynu jest nadal single point of failure dla aplikacji funkcji. Jeśli wystąpi awaria magazynu, aplikacja ponosi przestoje.
- Jeśli aplikacja funkcji zostanie przełączona w tryb failover, nastąpi zwiększenie opóźnienia, ponieważ będzie miało dostęp do konta magazynu w różnych regionach.
- Uzyskiwanie dostępu do usługi magazynu z innego regionu, w którym się znajduje, jest w większym zakresie ze względu na ruch wychodzący z sieci.
- Ten scenariusz zależy od Traffic Manager. Biorąc pod uwagę [sposób działania Traffic Manager](../../traffic-manager/traffic-manager-how-it-works.md), może to być czasochłonne, dopóki aplikacja kliencka korzystająca z funkcji trwałej nie musi ponownie wykonać zapytania dotyczącego adresu aplikacji funkcji z Traffic Manager.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenariusz 2 — zrównoważone obciążenie obliczeniowe z magazynem regionalnym

Poprzedni scenariusz dotyczy tylko przypadku awarii w infrastrukturze obliczeniowej. Jeśli usługa magazynu ulegnie awarii, spowoduje to awarię aplikacji funkcji.
Aby zapewnić ciągłą eksploatację trwałych funkcji, w tym scenariuszu jest stosowane konto magazynu lokalnego w każdym regionie, w którym są wdrażane aplikacje funkcji.

![Diagram przedstawiający scenariusz 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Takie podejście dodaje ulepszenia w poprzednim scenariuszu:

- Jeśli aplikacja funkcji ulegnie awarii, Traffic Manager zajmie przechodzenie w tryb failover do regionu pomocniczego. Jednak ponieważ aplikacja funkcji korzysta z własnego konta magazynu, funkcje trwałe nadal działają.
- W przypadku przełączenia w tryb failover nie ma dodatkowych opóźnień w regionie przełączenia w tryb failover, ponieważ aplikacja funkcji i konto magazynu znajdują się na tym samym miejscu.
- Awaria warstwy magazynu spowoduje błędy w funkcjach trwałych, co z kolei spowoduje wyzwolenie przekierowania do regionu trybu failover w trybie failover. Ponownie, ponieważ aplikacja i magazyn funkcji są izolowane dla każdego regionu, funkcje trwałe będą nadal działać.

Ważne zagadnienia dotyczące tego scenariusza:

- Jeśli aplikacja funkcji jest wdrażana przy użyciu dedykowanego planu AppService, replikowanie infrastruktury obliczeniowej w centrum danych w trybie failover zwiększy koszty.
- Bieżący stan nie przejdzie w tryb failover, co oznacza, że wykonywanie i funkcje z punktami kontrolnymi zakończą się niepowodzeniem. Do aplikacji klienckiej można ponowić próbę wykonania operacji lub uruchomić ją ponownie.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenariusz 3 — przetwarzanie z równoważeniem obciążenia z magazynem udostępnionym GRS

Ten scenariusz jest modyfikacją w pierwszym scenariuszu, implementując konto magazynu udostępnionego. Główna różnica polega na tym, że konto magazynu jest tworzone z włączoną replikacją geograficzną.
Funkcjonalnie ten scenariusz zapewnia takie same zalety jak scenariusz 1, ale zapewnia dodatkowe korzyści z odzyskiwania danych:

- Magazyn Geograficznie nadmiarowy (GRS) oraz dostęp do odczytu (RA-GRS) dla konta magazynu.
- W przypadku awarii regionu usługi magazynu jedna z możliwości polega na tym, że operacje w centrum danych określają, że magazyn musi być przełączona w tryb failover do regionu pomocniczego. W takim przypadku dostęp do konta magazynu zostanie przekierowany w sposób niewidoczny do replikowanej geograficznie kopii konta magazynu bez interwencji użytkownika.
- W takim przypadku stan funkcji trwałych zostanie zachowany do ostatniej replikacji konta magazynu, co kilka minut.

Podobnie jak w przypadku innych scenariuszy, istnieją ważne zagadnienia:

- Przełączenie w tryb failover do repliki odbywa się za pomocą operatorów centrów danych. może to zająć trochę czasu. Do tego czasu aplikacja funkcji pozostanie nieprzerwana.
- Istnieje zwiększony koszt korzystania z kont magazynu replikowanych geograficznie.
- GRS odbywa się asynchronicznie. Niektóre z najnowszych transakcji mogą zostać utracone z powodu opóźnienia procesu replikacji.

![Diagram przedstawiający Scenariusz 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat [projektowania aplikacji o wysokiej dostępności przy użyciu usługi RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
