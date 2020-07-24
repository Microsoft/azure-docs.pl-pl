---
title: Podstawa zabezpieczeń platformy Azure dla Azure DevTest Labs
description: Podstawa zabezpieczeń platformy Azure dla Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098517"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Podstawa zabezpieczeń platformy Azure dla Azure DevTest Labs

Podstawą zabezpieczeń platformy Azure dla Azure DevTest Labs są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](https://docs.microsoft.com/azure/security/benchmarks/overview), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu
**Wskazówki:** Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure. Można jednak zarządzać ustawieniami synchronizacji czasu dla zasobów obliczeniowych.

Zobacz następujący artykuł, aby dowiedzieć się więcej o konfigurowaniu synchronizacji czasu dla zasobów obliczeniowych platformy Azure: [synchronizacja czasu dla maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync). 

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Programu

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń
**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane na Azure DevTest Labs wystąpieniach na poziomie płaszczyzny zarządzania. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny zarządzania dla wystąpień usługi DevTest Labs.

Aby uzyskać więcej informacji, zobacz [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](../azure-monitor/platform/diagnostic-settings.md).

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure
**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Dzienniki aktywności zapewniają wgląd w operacje, które zostały wykonane na Azure DevTest Labs wystąpieniach na poziomie płaszczyzny zarządzania. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny zarządzania dla wystąpień usługi DevTest Labs.

Aby uzyskać więcej informacji, zobacz [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](../azure-monitor/platform/diagnostic-settings.md).

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych
**Wskazówki:** Azure DevTest Labs maszyny wirtualne są tworzone i własnością klienta. W związku z tym organizacja jest odpowiedzialna za monitorowanie. Aby monitorować system operacyjny obliczeń, można użyć Azure Security Center. Dane zbierane przez Security Center z systemu operacyjnego obejmują typ i wersję systemu operacyjnego, system operacyjny (dzienniki zdarzeń systemu Windows), uruchomione procesy, nazwę komputera, adresy IP i zalogowanego użytkownika. Agent Log Analytics również zbiera pliki zrzutu awaryjnego.

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

- [Jak zbierać dzienniki wewnętrznego hosta maszyny wirtualnej platformy Azure z Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Omówienie zbierania danych Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń
***Wskazówki:** W Azure Monitor Ustaw okres przechowywania dziennika dla Log Analytics obszarów roboczych skojarzonych z wystąpieniami Azure DevTest Labs zgodnie z regulacjami zgodności w organizacji.

Aby uzyskać więcej informacji, zobacz następujący artykuł: [jak ustawić parametry przechowywania dziennika](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania
**Wskazówki:** Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego Log Analytics. Uruchom zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie danych dziennika aktywności, które mogły zostać zebrane dla Azure DevTest Labs.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/diagnostic-settings.md)
- [Jak zbierać i analizować dzienniki aktywności platformy Azure w obszarze roboczym Log Analytics w Azure Monitor](../azure-monitor/platform/activity-log.md)

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania
**Wskazówki:** Za pomocą obszaru roboczego usługi Azure Log Analytics można monitorować i generować alerty o nietypowych działaniach w dziennikach zabezpieczeń oraz zdarzeniach związanych z Azure DevTest Labs.

Aby uzyskać więcej informacji, zobacz następujący artykuł: [jak wysyłać alerty dotyczące danych dziennika usługi log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitorowanie Azure Security Center:** Obecnie niedostępne

**Odpowiedzialność:** Dział

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem
**Wskazówki:** Nie dotyczy. Azure DevTest Labs nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS
**Wskazówki:** Nie dotyczy. Azure DevTest Labs nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie Azure Security Center:** Nie dotyczy

**Odpowiedzialność:** Dział

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia
**Wskazówki:** Azure DevTest Labs tworzy maszyny obliczeniowe platformy Azure, które są własnością klienta i są przez niego zarządzane. Użyj Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych z systemem Windows Azure, aby zarejestrować zdarzenie tworzenia procesu i pole wiersza polecenia. W przypadku obsługiwanych maszyn wirtualnych z systemem Linux na platformie Azure można ręcznie skonfigurować rejestrowanie konsoli dla poszczególnych węzłów i użyć dziennika systemowego do przechowywania danych. Należy również użyć obszaru roboczego Log Analytics Azure Monitor, aby przejrzeć dzienniki i uruchamiać zapytania dotyczące zarejestrowanych danych z usługi Azure Virtual Machines.

- [Zbieranie danych w usłudze Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Jak uruchamiać niestandardowe zapytania w Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
- [Syslog data sources in Azure Monitor (Źródła danych usługi Syslog w usłudze Azure Monitor)](../azure-monitor/platform/data-sources-syslog.md)

**Monitorowanie Azure Security Center:** Opcję

**Odpowiedzialność:** Dział

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem:

- [Alerty zabezpieczeń dla środowisk w Azure DevTest Labs](environment-security-alerts.md)