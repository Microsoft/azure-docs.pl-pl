---
title: Wprowadzenie do usługi Azure Automation
description: Ten artykuł zawiera informacje o tym, co Azure Automation jest i jak używać go do automatyzowania cyklu życia infrastruktury i aplikacji.
services: automation
ms.subservice: process-automation
keywords: Azure Automation, DSC, PowerShell, konfiguracja stanu, zarządzanie aktualizacjami, śledzenie zmian, DSC, spis, elementy Runbook, Python, grafika
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: e3986b7e8fc70f8662bed40b076897caa6165744
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182822"
---
# <a name="an-introduction-to-azure-automation"></a>Wprowadzenie do usługi Azure Automation

Azure Automation oferuje chmurową usługę automatyzacji i konfiguracji, która obsługuje spójne zarządzanie w środowiskach platformy Azure i innych niż platformy Azure. Obejmuje automatyzację procesów, zarządzanie konfiguracją, zarządzanie aktualizacjami, udostępnione możliwości i funkcje heterogeniczne. Automatyzacja zapewnia pełną kontrolę podczas wdrażania, działania i likwidowania obciążeń i zasobów.

![Możliwości automatyzacji](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Automatyzacja procesów

Automatyzacja procesów w Azure Automation pozwala zautomatyzować częste, czasochłonne i podatne na błędy zadania zarządzania chmurą. Ta usługa ułatwia skoncentrowanie się na pracy, która zwiększa wartość biznesową. Poprzez zmniejszenie liczby błędów i zwiększenie wydajności pozwala ona również zmniejszyć koszty operacyjne. Środowisko operacyjne automatyzacji procesów zostało szczegółowo opisane w [Azure Automation wykonywania elementu Runbook](automation-runbook-execution.md).

Automatyzacja procesów obsługuje integrację usług platformy Azure i innych systemów publicznych wymaganych do wdrażania i konfigurowania kompleksowych procesów oraz zarządzania nimi. Usługa umożliwia tworzenie [elementów Runbook](automation-runbook-types.md) graficznie, w programie PowerShell lub przy użyciu języka Python. Przy użyciu [hybrydowego procesu roboczego elementu Runbook](automation-hybrid-runbook-worker.md)można ujednolicić zarządzanie przez organizację w środowiskach lokalnych. Elementy [webhook](automation-webhooks.md) pozwalają zrealizować żądania i zapewnić ciągłą dostawę i operacje, wyzwalając automatyzację od narzędzia ITSM, DevOps i systemów monitorowania. 

## <a name="configuration-management"></a>Zarządzanie konfiguracją

Zarządzanie konfiguracją w programie Azure Automation umożliwia dostęp do dwóch funkcji:

* Śledzenie zmian i spis
* Usługa State Configuration w usłudze Azure Automation

### <a name="change-tracking-and-inventory"></a>Śledzenie zmian i spis

Change Tracking i spis łączy funkcje śledzenia zmian i spisu, aby umożliwić śledzenie zmian infrastruktury maszyny wirtualnej i serwera. Usługa obsługuje śledzenie zmian w usługach, demonach, oprogramowaniu, rejestrze i plikach w środowisku, aby ułatwić diagnozowanie niepożądanych zmian i zgłaszanie alertów. Obsługa spisu umożliwia wykonywanie zapytań dotyczących zasobów gościa w celu wglądu w zainstalowane aplikacje i inne elementy konfiguracji. Aby uzyskać szczegółowe informacje o tej funkcji, zobacz [Change Tracking i spis](change-tracking/overview.md).

### <a name="azure-automation-state-configuration"></a>Usługa State Configuration w usłudze Azure Automation

[Konfiguracja stanu Azure Automation](automation-dsc-overview.md) to oparta na chmurze funkcja konfiguracji żądanego stanu (DSC) programu PowerShell, która udostępnia usługi dla środowisk przedsiębiorstwa. Korzystając z tej funkcji, można zarządzać zasobami DSC w Azure Automation i stosować konfiguracje na maszynach wirtualnych lub fizycznych z serwera ściągania DSC w chmurze platformy Azure. 

## <a name="update-management"></a>Zarządzanie aktualizacjami

Azure Automation zawiera funkcję [Update Management](./update-management/overview.md) dla systemów Windows i Linux w środowiskach hybrydowych. Update Management zapewnia wgląd w zgodność aktualizacji na platformie Azure oraz w innych chmurach i lokalnych. Funkcja ta umożliwia tworzenie zaplanowanych wdrożeń, które organizują instalację aktualizacji w ramach zdefiniowanego okna obsługi. Jeśli aktualizacja nie powinna być zainstalowana na komputerze, można użyć funkcji Update Management, aby wykluczyć ją z wdrożenia.

## <a name="shared-capabilities"></a>Współdzielone możliwości

Azure Automation oferuje wiele udostępnionych możliwości, w tym udostępnionych zasobów, kontroli dostępu opartej na rolach, elastycznego planowania, integracji kontroli źródła, inspekcji i tagowania.

### <a name="shared-resources"></a><a name="shared-resources"></a>Udostępnione zasoby

Usługa Azure Automation zawiera zestaw współdzielonych zasobów, które ułatwiają automatyzowanie i konfigurowanie środowiska odpowiednio do skali.

* **[Harmonogramy](./shared-resources/schedules.md)** — wyzwalanie operacji automatyzacji we wstępnie zdefiniowanym czasie.
* **[Moduły](./shared-resources/modules.md)** — zarządzanie platformą Azure i innymi systemami. Moduły można importować do konta usługi Automation dla programu Microsoft, innych firm, społeczności i zasobów DSC zdefiniowanych przez użytkownika.
* **[Galeria modułów](automation-runbook-gallery.md)** — obsługuje natywną integrację z Galeria programu PowerShell, aby umożliwić wyświetlanie elementów Runbook i importowanie ich do konta usługi Automation. Galeria pozwala szybko rozpocząć Integrowanie i tworzenie procesów z poziomu galerii programu PowerShell i centrum skryptów Microsoft.
* **[Pakiety Python 2](python-packages.md)** — Obsługa elementów Runbook języka Python 2 dla konta usługi Automation.
* **[Poświadczenia](./shared-resources/credentials.md)** — bezpiecznie Przechowuj informacje poufne, których elementy Runbook i konfiguracje mogą używać w środowisku uruchomieniowym.
* **[Połączenia](automation-connections.md)** — umożliwia przechowywanie par nazwa-wartość wspólnych informacji dotyczących połączeń z systemami. Autor modułu definiuje połączenia w elementach Runbook i konfiguracjach do użycia w czasie wykonywania.
* **[Certyfikaty](./shared-resources/certificates.md)** — Zdefiniuj informacje, które mają być używane podczas uwierzytelniania i zabezpieczania wdrożonych zasobów w przypadku dostępu do elementów Runbook lub konfiguracji DSC w czasie wykonywania. 
* **[Zmienne](./shared-resources/variables.md)** — Przechowuj zawartość, której można używać w elementach Runbook i konfiguracjach. Można zmienić wartości zmiennych bez konieczności modyfikowania elementów Runbook lub konfiguracji, które odwołują się do nich.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Azure Automation obsługuje kontrolę dostępu opartą na rolach (Azure RBAC) na platformie Azure w celu regulowania dostępu do konta usługi Automation i jego zasobów. Aby dowiedzieć się więcej o konfigurowaniu usługi Azure RBAC na koncie usługi Automation, elementach Runbook i zadaniach, zobacz [Kontrola dostępu oparta na rolach dla Azure Automation](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integracja kontroli źródła

Azure Automation obsługuje [integrację kontroli źródła](source-control-integration.md). Ta funkcja promuje konfigurację jako kod, w którym elementy Runbook lub konfiguracje mogą być sprawdzane w systemie kontroli źródła.

## <a name="heterogeneous-support-windows-and-linux"></a>Obsługa heterogeniczna (systemy Windows i Linux)

Automatyzacja jest przeznaczona do pracy w środowisku chmury hybrydowej oraz w systemach Windows i Linux. Zapewnia spójny sposób automatyzacji i konfigurowania wdrożonych obciążeń oraz systemów operacyjnych, w których są uruchamiane.

## <a name="common-scenarios-for-automation"></a>Typowe scenariusze dotyczące usługi Automation

Azure Automation obsługuje zarządzanie przez cały cykl życia infrastruktury i aplikacji. Typowe scenariusze obejmują:

* **Zapisuj elementy Runbook** — Twórz elementy Runbook programu PowerShell, przepływu pracy programu PowerShell, graficznego, języka Python 2 i funkcji konfiguracji DSC w typowych językach. 
* **Kompilowanie i wdrażanie zasobów** — wdrażanie maszyn wirtualnych w środowisku hybrydowym przy użyciu elementów Runbook i szablonów Azure Resource Manager. Integruj się z narzędziami programistycznymi, takimi jak Jenkins i Azure DevOps.
* **Skonfiguruj maszyny wirtualne** — Oceń i skonfiguruj maszyny z systemami Windows i Linux przy użyciu konfiguracji infrastruktury i aplikacji.
* **Udostępniaj** wiedzę na temat transferu wiedzy do systemu, w jaki sposób Twoja organizacja dostarcza i utrzymuje obciążenia. 
* **Pobierz spis** — Uzyskaj pełny spis wdrożonych zasobów na potrzeby określania wartości docelowej, raportowania i zgodności. 
* **Znajdź zmiany** — Zidentyfikuj zmiany, które mogą spowodować błędy konfiguracji i poprawić zgodność operacyjną.
* **Monitoruj** — Izoluj zmiany maszyn, które powodują problemy i Koryguj lub eskalować je do systemów zarządzania.
* **Ochrona** maszyn kwarantanny w przypadku zgłoszenia alertów zabezpieczeń. Ustaw wymagania dotyczące gościa.
* **Zarządzanie** — skonfigurowanie RBAC na platformie Azure dla zespołów. Odzyskaj nieużywane zasoby.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Cennik Azure Automation

Ceny skojarzone z Azure Automation można sprawdzić na stronie [cennika](https://azure.microsoft.com/pricing/details/automation/) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie konta usługi Automation](automation-quickstart-create-account.md)