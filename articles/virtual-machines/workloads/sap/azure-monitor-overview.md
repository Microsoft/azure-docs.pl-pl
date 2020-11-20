---
title: Azure Monitor omówienie i Architektura rozwiązań SAP | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure monitor dla rozwiązań SAP
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 5bb40ec930731b58d457500d83b66c7a187b44d8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957439"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure monitor dla rozwiązań SAP (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

Azure Monitor for SAP Solutions to natywny produkt monitorowania platformy Azure dla klientów korzystających z oprogramowania SAP Landscapes na platformie Azure. Produkt współpracuje z programem [SAP na platformie azure Virtual Machines](./hana-get-started.md) i [SAP w dużych wystąpieniach platformy Azure](./hana-overview-architecture.md).
Dzięki Azure Monitor dla rozwiązań SAP klienci mogą zbierać dane telemetryczne z infrastruktury i baz danych platformy Azure w jednej centralnej lokalizacji i wizualnie skorelowanych danych telemetrycznych w celu szybszego rozwiązywania problemów.

Azure Monitor dla rozwiązań SAP jest oferowana w portalu Azure Marketplace. Zapewnia proste, intuicyjne środowisko instalacji i klika kliknięcia, aby wdrożyć zasób dla Azure Monitor dla rozwiązań SAP (nazywanych **zasobem monitorowania SAP**).

Klienci mogą monitorować różne składniki oprogramowania SAP, takie jak Azure Virtual Machines, klaster o wysokiej dostępności, baza danych SAP HANA i tak dalej, przez dodanie odpowiedniego **dostawcy** dla tego składnika.

Obsługiwana infrastruktura:

- Maszyna wirtualna platformy Azure
- Duże wystąpienie platformy Azure

Obsługiwane bazy danych:
- Baza danych SAP HANA
- Program Microsoft SQL Server

Azure Monitor dla rozwiązań SAP wykorzystuje moc istniejących możliwości [Azure monitor](../../../azure-monitor/overview.md) , takich jak log Analytics i [skoroszyty](../../../azure-monitor/platform/workbooks-overview.md) , aby zapewnić dodatkowe możliwości monitorowania. Klienci mogą tworzyć [niestandardowe wizualizacje](../../../azure-monitor/platform/workbooks-overview.md#getting-started) , edytując domyślne skoroszyty udostępniane przez Azure monitor dla rozwiązań SAP, pisać [zapytania niestandardowe](../../../azure-monitor/log-query/get-started-portal.md) i twórz [niestandardowe alerty](../../../azure-monitor/learn/tutorial-response.md) przy użyciu obszaru roboczego usługi Azure log Analytics, korzystaj z [elastycznego okresu przechowywania](../../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) i łącz dane monitorowania z systemem biletów.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Jakie dane są Azure Monitor na potrzeby zbierania rozwiązań SAP?

Zbieranie danych w Azure Monitor dla rozwiązań SAP zależy od dostawców skonfigurowanych przez klientów. W publicznej wersji zapoznawczej są zbierane następujące dane.

Telemetrię klastra Pacemaker o wysokiej dostępności:
- Stan urządzenia węzeł, zasób i SBD
- Ograniczenia lokalizacji Pacemaker
- Głosy kworum i stan pierścienia
- [Inne](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA telemetrii:
- Użycie procesora CPU, pamięci, dysku i sieci
- Replikacja systemu HANA (HSR)
- Kopia zapasowa HANA
- Stan hosta HANA
- Role serwera indeksowania i serwera nazw

Dane telemetryczne programu Microsoft SQL Server:
- Procesor CPU, pamięć, użycie dysku
- Hostname, nazwa wystąpienia SQL, identyfikator systemu SAP
- Żądania wsadowe, kompilacje i życia stron stron w czasie
- 10 najpopularniejszych instrukcji SQL w czasie
- Najwyżej 12 największych tabel w systemie SAP
- Problemy zarejestrowane w dziennikach błędów SQL Server
- Blokowanie procesów i statystyk oczekiwania SQL w czasie

## <a name="data-sharing-with-microsoft"></a>Udostępnianie danych w firmie Microsoft

Azure Monitor dla rozwiązań SAP zbiera metadane systemu, aby zapewnić lepszą obsługę oprogramowania SAP w przypadku klientów platformy Azure. Nie są zbierane dane OSOBowe/EUII.
Klienci mogą włączać udostępnianie danych w firmie Microsoft w momencie tworzenia Azure Monitor dla zasobów rozwiązań SAP, wybierając pozycję *Udostępnij* z listy rozwijanej.
Zdecydowanie zaleca się, aby klienci mogli korzystać z udostępniania danych, ponieważ udostępniają one zespołom pomocy technicznej i inżynierów inżynieryjnych firmy Microsoft więcej informacji na temat środowiska klienta i zapewniają ulepszoną pomoc techniczną dla naszych krytycznych rozwiązań SAP w przypadku klientów platformy Azure.

## <a name="architecture-overview"></a>Omówienie architektury

Na poniższym diagramie wyjaśniono, jak Azure Monitor dla rozwiązań SAP zbiera dane telemetryczne z SAP HANA Database. Architektura to niezależny od, czy SAP HANA jest wdrażana na platformie Azure Virtual Machines czy w dużych wystąpieniach platformy Azure.

![Azure Monitor dla architektury rozwiązań SAP](./media/azure-monitor-sap/azure-monitor-architecture.png)

Kluczowe składniki architektury są następujące:
- Azure Portal — punkt początkowy dla klientów. Klienci mogą przechodzić do witryny Marketplace w Azure Portal i odnajdywać Azure Monitor dla rozwiązań SAP
- Azure Monitor dla zasobu rozwiązań SAP — miejsce wyładunku dla klientów, aby wyświetlić dane telemetryczne monitorowania
- Zarządzana Grupa zasobów — wdrożona automatycznie w ramach Azure Monitor dla wdrożenia zasobów rozwiązań SAP. Zasoby wdrożone w ramach zarządzanej grupy zasobów ułatwiają zbieranie danych telemetrycznych. Najważniejsze zasoby zostały wdrożone i ich celem są:
   - Maszyna wirtualna platformy Azure: znana także jako *maszyna wirtualna modułu zbierającego*. To jest maszyna wirtualna Standard_B2ms. Głównym celem tej maszyny wirtualnej jest hostowanie *ładunku monitorowania*. Ładunek monitorowania odnosi się do logiki zbierania danych telemetrycznych z systemów źródłowych i przesyłania zebranych dane do struktury monitorowania. Na powyższym diagramie ładunek monitorowania zawiera logikę do łączenia się z bazą danych SAP HANA za pośrednictwem portu SQL.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): ten zasób został wdrożony w celu bezpiecznego przechowywania poświadczeń bazy danych SAP HANA i przechowywania informacji o [dostawcach](./azure-monitor-providers.md).
   - Obszar roboczy Log Analytics: miejsce docelowe, w którym znajdują się dane telemetryczne.
      - Wizualizacja jest oparta na danych telemetrycznych w Log Analytics przy użyciu [skoroszytów platformy Azure](../../../azure-monitor/platform/workbooks-overview.md). Klienci mogą dostosować wizualizację. Klienci mogą także przypinać swoje skoroszyty lub konkretne wizualizacje w obrębie skoroszytów do pulpitu nawigacyjnego platformy Azure w celu zapewnienia możliwości autoodświeżania o najniższym poziomie szczegółowości 30 minut.
      - Klienci mogą korzystać z istniejącego obszaru roboczego w ramach tej samej subskrypcji co zasób monitora SAP przez wybranie tej opcji w czasie wdrażania.
      - Klienci mogą używać języka Kusto Query Language (KQL) do uruchamiania [zapytań](../../../azure-monitor/log-query/log-query-overview.md) względem nieprzetworzonych tabel w obszarze roboczym log Analytics. Sprawdź *dzienniki niestandardowe*.

> [!Note]
> Klienci są odpowiedzialni za stosowanie poprawek i konserwacji maszyn wirtualnych wdrożonych w zarządzanej grupie zasobów.

> [!Tip]
> Klienci mogą zdecydować się na korzystanie z istniejącego obszaru roboczego Log Analytics na potrzeby zbierania danych telemetrycznych, jeśli są one wdrożone w ramach tej samej subskrypcji platformy Azure jako zasób Azure Monitor dla rozwiązań SAP.

### <a name="architecture-highlights"></a>Najważniejsze informacje o architekturze

Poniżej przedstawiono najważniejsze informacje o architekturze:
 - **Wiele wystąpień** — klienci mogą tworzyć monitor dla wielu wystąpień danego typu składnika (na przykład baza danych Hana, klaster ha, program Microsoft SQL Server) dla wielu identyfikatorów SID SAP w sieci wirtualnej z pojedynczym zasobem Azure monitor dla rozwiązań SAP.
 - **Wiele dostawców** — powyższy diagram architektury przedstawia dostawcę SAP HANA jako przykład. Podobnie klienci mogą konfigurować dodatkowych dostawców dla odpowiednich składników (na przykład bazy danych HANA, klastra HA, programu Microsoft SQL Server) do zbierania danych z tych składników.
 - **Open Source** — kod źródłowy Azure monitor dla rozwiązań SAP jest dostępny w serwisie [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). Klienci mogą odwoływać się do kodu dostawcy i dowiedzieć się więcej na temat produktu, współtworzenia lub udostępniania opinii.
 - **Extensible Query Framework** — zapytania SQL służące do zbierania danych telemetrycznych są zapisywane w formacie [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Dodatkowe zapytania SQL służące do zbierania większej ilości danych telemetrycznych mogą być łatwo dodawane. Klienci mogą zażądać, aby dane telemetryczne były dodawane do Azure Monitor dla rozwiązań SAP, pozostawiając opinię za pomocą linku na końcu tego dokumentu lub kontaktując się z zespołem kont.

## <a name="pricing"></a>Cennik
Azure Monitor dla rozwiązań SAP to bezpłatny produkt (bez opłat licencyjnych). Klienci są zobowiązani do uiszczenia kosztów związanych z podstawowymi składnikami w zarządzanej grupie zasobów.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dostawcami i Utwórz pierwszy Azure Monitor dla zasobu rozwiązań SAP.
 - Dowiedz się więcej o [dostawcach](./azure-monitor-providers.md)
 - [Wdrażanie Azure Monitor dla rozwiązań SAP z Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Masz pytania dotyczące Azure Monitor dla rozwiązań SAP? Zapoznaj się z sekcją [często zadawane pytania](./azure-monitor-faq.md)
