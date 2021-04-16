---
title: Azure Monitor dla rozwiązań SAP omówienie i architektura| Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Monitor dla rozwiązań SAP
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: c561a9a786765ccfdaf00abf4e0d9c8cc550cb9a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377212"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Usługa Azure Monitor dla rozwiązań SAP (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

Azure Monitor dla rozwiązań SAP to produkt do monitorowania natywny dla platformy Azure dla klientów, który działa w środowiskach SAP na platformie Azure. Produkt działa zarówno z [SAP na platformie Azure Virtual Machines,](./hana-get-started.md) [jak i SAP na platformie Azure dużymi wystąpieniami.](./hana-overview-architecture.md)
Dzięki Azure Monitor dla rozwiązań SAP klienci mogą zbierać dane telemetryczne z infrastruktury i baz danych platformy Azure w jednej centralnej lokalizacji oraz wizualnie korelować dane telemetryczne w celu szybszego rozwiązywania problemów.

Azure Monitor dla rozwiązań SAP jest oferowana za pośrednictwem Azure Marketplace. Zapewnia proste, intuicyjne środowisko konfiguracji i wystarczy kilka kliknięć, aby wdrożyć zasób dla usługi Azure Monitor dla rozwiązań SAP (znany jako **zasób monitora SAP).**

Klienci mogą monitorować różne składniki środowiska SAP, takie jak usługa Azure Virtual Machines, klaster o wysokiej dostępności, baza danych  SAP HANA, oprogramowanie SAP NetWeaver i tak dalej, dodając odpowiedniego dostawcę dla tego składnika.

Obsługiwana infrastruktura:

- Maszyna wirtualna platformy Azure
- Duże wystąpienie platformy Azure

Obsługiwane bazy danych:
- Baza danych SAP HANA
- Microsoft SQL Server

Azure Monitor dla rozwiązań SAP korzysta z możliwości istniejących funkcji [Azure Monitor,](../../../azure-monitor/overview.md) takich jak log Analytics i [skoroszyty,](../../../azure-monitor/visualize/workbooks-overview.md) w celu zapewnienia większej liczby możliwości monitorowania. Klienci mogą [](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) tworzyć wizualizacje niestandardowe, edytując domyślne skoroszyty dostarczane [](../../../azure-monitor/alerts/tutorial-response.md) przez usługę Azure Monitor dla rozwiązań SAP, zapisując zapytania [](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) niestandardowe i tworząc alerty niestandardowe przy użyciu obszaru roboczego usługi Azure Log Analytics, korzystając z elastycznego okresu przechowywania i połącz dane monitorowania z systemem biletów. [](../../../azure-monitor/logs/log-analytics-tutorial.md)

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Jakie dane są zbierane Azure Monitor rozwiązań SAP?

Zbieranie danych w Azure Monitor dla rozwiązań SAP zależy od dostawców skonfigurowanych przez klientów. W publicznej wersji zapoznawczej zbierane są następujące dane.

Dane telemetryczne klastra Pacemaker o wysokiej dostępności:
- Stan węzła, zasobu i urządzenia SBD
- Ograniczenia lokalizacji pacemaker
- Głosy kworum i stan pierścienia
- [Inne](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA telemetrii:
- Wykorzystanie procesora CPU, pamięci, dysku i sieci
- Replikacja systemu HANA (HSR)
- Kopia zapasowa platformy HANA
- Stan hosta HANA
- Serwer indeksów i role serwera nazw

Telemetria programu Microsoft SQL Server:
- Procesor CPU, pamięć, wykorzystanie dysku
- Nazwa hosta, nazwa wystąpienia SQL, identyfikator systemu SAP
- Żądania wsadowe, kompilacje i oczekiwana długość życia strony w czasie
- 10 najdroższych instrukcji SQL w czasie
- 12 największych tabel w systemie SAP
- Problemy zarejestrowane w dzienniku SQL Server błędów
- Blokowanie procesów i statystyk oczekiwania SQL w czasie

Telemetria systemu operacyjnego (Linux) 
- Użycie procesora CPU, liczba wideł, uruchomione i zablokowane procesy. 
- Wykorzystanie i dystrybucja pamięci wśród wykorzystywanych, buforowanych i buforowanych. 
- Wykorzystanie, stronicowanie i szybkość zamiany. 
- Wykorzystanie systemów plików, liczba bajtów odczytanych i zapisywanych na urządzenie blokowe. 
- Opóźnienie odczytu/zapisu na urządzenie blokowe. 
- Bieżąca liczba we/wy, liczba bajtów odczytu/zapisu w pamięci trwałej. 
- Liczba przychodzących/wychodzących pakietów sieciowych, liczba bajtów sieciowych przychodzących/wychodząych 

Telemetria sap NetWeaver:

- Dostępność systemu SAP i serwera aplikacji, w tym dostępność procesu wystąpienia dyspozytora, ICM, bramy, serwera komunikatów, serwera enqueue, usługi IGS Watchdog
- Statystyki i trendy dotyczące wykorzystania procesów pracy
- Enqueue Lock statistics and trends (Statystyki i trendy dotyczące blokady kolejkowania)
- Statystyki i trendy dotyczące wykorzystania kolejek

## <a name="data-sharing-with-microsoft"></a>Udostępnianie danych firmie Microsoft

Azure Monitor dla rozwiązań SAP zbiera metadane systemu, aby zapewnić ulepszoną obsługę SAP na platformie Azure klientów. Nie są zbierane żadne dane osobowe/EUII.
Klienci mogą włączyć udostępnianie danych firmie Microsoft w momencie tworzenia Azure Monitor dla rozwiązań SAP zasobów, *wybierając* z listy rozwijanej opcję Udostępnij.
Zdecydowanie zaleca się, aby klienci włączyli udostępnianie danych, ponieważ zapewnia on zespołom pomocy technicznej i inżynieryjnemu firmy Microsoft więcej informacji na temat środowiska klienta oraz zapewnia ulepszoną obsługę naszych kluczowych SAP na platformie Azure klientów.

## <a name="architecture-overview"></a>Omówienie architektury

Na wysokim poziomie na poniższym diagramie wyjaśniono, jak program Azure Monitor dla rozwiązań SAP dane telemetryczne z SAP HANA bazy danych. Architektura jest niezależnie od tego, czy SAP HANA na platformie Azure Virtual Machines czy azure large instances.

![Azure Monitor architektury rozwiązań SAP](./media/azure-monitor-sap/azure-monitor-architecture.png)

Najważniejsze składniki architektury to:
- Azure Portal — punkt wyjścia dla klientów. Klienci mogą przechodzić do witryny Marketplace w Azure Portal i odnajdywać Azure Monitor dla rozwiązań SAP
- Azure Monitor dla rozwiązań SAP — miejsce docelowe dla klientów na potrzeby wyświetlania telemetrii monitorowania
- Zarządzana grupa zasobów — wdrażana automatycznie w ramach Azure Monitor dla rozwiązań SAP zasobów. Zasoby wdrożone w ramach zarządzanej grupy zasobów ułatwiają zbieranie danych telemetrycznych. Kluczowe wdrożone zasoby i ich przeznaczenie to:
   - Maszyna wirtualna platformy Azure: znana również jako maszyna *wirtualna modułu zbierającego.* Jest to maszyna Standard_B2ms wirtualna. Głównym celem tej maszyny wirtualnej jest hostnie *ładunku monitorowania*. Ładunek monitorowania odnosi się do logiki zbierania danych telemetrycznych z systemów źródłowych i przesyłania zebranych danych do struktury monitorowania. Na powyższym diagramie ładunek monitorowania zawiera logikę nawiązywania połączenia SAP HANA bazą danych za pośrednictwem portu SQL.
   - [Azure Key Vault:](../../../key-vault/general/basic-concepts.md)ten zasób jest wdrażany do bezpiecznego przechowywania poświadczeń SAP HANA bazy danych oraz do przechowywania informacji o [dostawcach.](./azure-monitor-providers.md)
   - Obszar roboczy usługi Log Analytics: miejsce docelowe, w którym znajdują się dane telemetryczne.
      - Wizualizacja jest zbudowana na podstawie telemetrii w usłudze Log Analytics przy [użyciu skoroszytów platformy Azure.](../../../azure-monitor/visualize/workbooks-overview.md) Klienci mogą dostosowywać wizualizacje. Klienci mogą również przypiąć swoje skoroszyty lub określoną wizualizację w ramach skoroszytów do pulpitu nawigacyjnego platformy Azure, aby automatycznie korzystać z funkcji automatycznego wykonywania o najniższym stopniu szczegółowości co 30 minut.
      - Klienci mogą korzystać z istniejącego obszaru roboczego w ramach tej samej subskrypcji co zasób monitora SAP, wybierając tę opcję w czasie wdrażania.
      - Klienci mogą używać języka zapytań Kusto (KQL), aby uruchamiać zapytania względem nieprzetworzonych tabel w obszarze roboczym usługi Log Analytics. [](../../../azure-monitor/logs/log-query-overview.md) Przyjrzyj się *dziennikom niestandardowym.*

> [!Note]
> Klienci są odpowiedzialni za stosowanie poprawek i konserwację maszyny wirtualnej wdrożonej w zarządzanej grupie zasobów.

> [!Tip]
> Klienci mogą używać istniejącego obszaru roboczego usługi Log Analytics do zbierania danych telemetrycznych, jeśli jest on wdrażany w ramach tej samej subskrypcji platformy Azure co zasób dla Azure Monitor dla rozwiązań SAP.

### <a name="architecture-highlights"></a>Najważniejsze informacje o architekturze

Poniżej przedstawiono najważniejsze informacje o architekturze:
 -  Wiele wystąpień — klienci mogą tworzyć monitory dla wielu wystąpień danego typu składnika (na przykład HANA DB, klaster HA, serwer Microsoft SQL Server, SAP NetWeaver) w wielu identyfikatorach SID SAP w sieci wirtualnej z jednym zasobem Azure Monitor dla rozwiązań SAP.
 - **Wielu dostawców —** na powyższym diagramie architektury przedstawiono SAP HANA jako przykład. Podobnie klienci mogą skonfigurować większej liczby dostawców dla odpowiednich składników (na przykład HANA DB, klastra HA, programu Microsoft SQL Server, SAP NetWeaver) w celu zbierania danych z tych składników.
 - **Open source** — kod źródłowy aplikacji Azure Monitor dla rozwiązań SAP jest dostępny w witrynie [GitHub.](https://github.com/Azure/AzureMonitorForSAPSolutions) Klienci mogą odwoływać się do kodu dostawcy i dowiedzieć się więcej na temat produktu, współtwomentować lub udostępniać opinie.
 - **Rozszerzalna baza zapytań —** zapytania SQL służące do zbierania danych telemetrycznych są zapisywane w [języku JSON.](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) Więcej zapytań SQL w celu zbierania większej ilości danych telemetrycznych można łatwo dodać. Klienci mogą zażądać dodania określonych danych telemetrycznych do usługi Azure Monitor dla rozwiązań SAP, pozostawiając opinię za pośrednictwem linku na końcu tego dokumentu lub kontaktując się z zespołem kont.

## <a name="pricing"></a>Ceny
Azure Monitor dla rozwiązań SAP jest produktem bezpłatnym (bez opłaty licencyjnej). Klienci są odpowiedzialni za poniesienie kosztów podstawowych składników w zarządzanej grupie zasobów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dostawcach i utwórz pierwszy Azure Monitor dla rozwiązań SAP zasobów.
 - Dowiedz się więcej o [dostawcach](./azure-monitor-providers.md)
 - [Wdrażanie Azure Monitor rozwiązań SAP przy użyciu Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Czy masz pytania dotyczące Azure Monitor dla rozwiązań SAP? Zapoznaj się z [sekcją często zadawanych](./azure-monitor-faq.md) pytań
