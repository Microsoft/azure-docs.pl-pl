---
title: Zaawansowane zabezpieczenia danych dla IaaS w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak włączyć zaawansowane zabezpieczenia danych dla maszyn SQL w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 1c269ad13072ae4dcff9caba892ccc0643647e5c
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254233"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>Zaawansowane zabezpieczenia danych dla maszyn SQL (wersja zapoznawcza)

Zaawansowane zabezpieczenia danych Azure Security Center dla maszyn SQL chronią serwery SQL hostowane na platformie Azure, w innych środowiskach w chmurze, a nawet na maszynach lokalnych. Rozszerza to ochronę natywnych serwerów SQL platformy Azure, aby w pełni obsługiwać środowiska hybrydowe.

Ta funkcja w wersji zapoznawczej obejmuje funkcje identyfikacji i łagodzenia potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywania nietypowych działań, które mogą wskazywać na zagrożenia dla bazy danych: 

* **Ocena luk w zabezpieczeniach** — Usługa skanowania w celu odnajdywania, śledzenia i rozwiązywania problemów dotyczących potencjalnych luk w zabezpieczeniach bazy danych. Przeglądy oceny zawierają omówienie stanu zabezpieczeń maszyn SQL i szczegółowe informacje o wszelkich wynikach związanych z bezpieczeństwem.

* [Zaawansowana ochrona przed zagrożeniami](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) — usługa wykrywania, która stale monitoruje serwery SQL pod kątem zagrożeń, takich jak iniekcja SQL, ataki z wymuszeniem i nadużycie uprawnień. Ta usługa udostępnia alerty zabezpieczeń zorientowane na działania w Azure Security Center z szczegółowymi informacjami o podejrzanych działaniach, wskazówkami dotyczącymi sposobu łagodzenia zagrożeń oraz opcjami do kontynuowania badań za pomocą platformy Azure.

>[!TIP]
> Zaawansowane zabezpieczenia danych dla maszyn programu SQL to rozszerzenie [zaawansowanego pakietu zabezpieczeń danych](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)Azure Security Center, które jest już dostępne dla usług Azure SQL Database, Synapse i SQL Managed Instances.


## <a name="set-up-advanced-data-security-for-sql-machines"></a>Konfigurowanie zaawansowanych zabezpieczeń danych dla maszyn SQL 

Konfigurowanie zaawansowanych zabezpieczeń danych Azure Security Center dla maszyn SQL obejmuje dwa kroki:

* Zainicjuj obsługę agenta Log Analytics na hoście programu SQL Server. Zapewnia to połączenie z platformą Azure.

* Włącz opcjonalny pakiet na stronie ceny i ustawienia w Security Center.

Oba te elementy zostały opisane poniżej.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Krok 1. Zainicjuj obsługę agenta Log Analytics na hoście programu SQL Server:

- **SQL Server na maszynie wirtualnej platformy Azure** — Jeśli maszyna SQL jest hostowana na maszynie wirtualnej platformy Azure, można [zainicjować obsługę administracyjną log Analytics agenta](security-center-enable-data-collection.md#workspace-configuration). Alternatywnie można wykonać procedurę ręczną, aby [dodać maszynę wirtualną platformy Azure](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines).

- **SQL Server w usłudze Azure Arc** — Jeśli SQL Server jest hostowana na maszynie [usługi Azure Arc](https://docs.microsoft.com/azure/azure-arc/) , można wdrożyć agenta Log Analytics przy użyciu zalecenia Security Center "log Analytics Agent należy zainstalować na maszynach usługi Azure Arc opartych na systemie Windows (wersja zapoznawcza)". Alternatywnie można wykonać procedurę ręczną w [dokumentacji usługi Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).

- **SQL Server Premium** — Jeśli SQL Server jest hostowana na lokalnym komputerze z systemem Windows bez usługi Azure ARC, dostępne są dwie opcje połączenia z platformą Azure:
    
    - **Wdrażanie usługi Azure Arc** — można podłączyć dowolną maszynę z systemem Windows do Security Center. Natomiast usługa Azure Arc zapewnia lepszą integrację ze *wszystkimi* środowiskami platformy Azure. W przypadku skonfigurowania usługi Azure Arc zostanie wyświetlona strona **SQL Server — usługa Azure Arc** w portalu, a na tej stronie zostaną wyświetlone **alerty zabezpieczeń.** Dlatego pierwszą i zalecaną opcją jest [skonfigurowanie usługi Azure Arc na hoście](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) i postępuj zgodnie z instrukcjami dotyczącymi **SQL Server w usłudze Azure Arc**powyżej.
        
    - **Połącz maszynę z systemem Windows bez usługi Azure Arc** — w przypadku wybrania opcji łączenia SQL Server uruchomionego na komputerze z systemem Windows bez korzystania z usługi Azure Arc postępuj zgodnie z instrukcjami w temacie [łączenie komputerów z systemem windows, aby Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>Krok 2. Włącz opcjonalny pakiet na stronie cennika i ustawienia Security Center:

1. Na pasku bocznym Security Center Otwórz stronę **ustawienia & cennika** .

    - Jeśli używasz **domyślnego obszaru roboczego Azure Security Center** (o nazwie "defaultworkspace-[Identyfikator subskrypcji] — [region]"), wybierz odpowiednią **subskrypcję**.

    - Jeśli używasz **obszaru roboczego innego niż domyślny**, wybierz odpowiedni **obszar roboczy** (w razie potrzeby wprowadź nazwę obszaru roboczego w filtrze):

        ![tytuł](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. Przełącz opcję dla **serwerów SQL na maszynach (wersja zapoznawcza)** na wartość włączone. 

    [![Strona cennika Security Center z opcjonalnymi pakietami](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Zaawansowane zabezpieczenia danych dla serwerów SQL na maszynach zostaną włączone na wszystkich serwerach SQL połączonych z wybranym obszarem roboczym. Ochrona zostanie w pełni aktywna po pierwszym ponownym uruchomieniu SQL Server. 

    >[!TIP] 
    > Aby utworzyć nowy obszar roboczy, postępuj zgodnie z instrukcjami w temacie [tworzenie log Analytics obszaru roboczego](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).


1. Opcjonalnie możesz skonfigurować powiadomienia e-mail o alertach zabezpieczeń. 
    Po wygenerowaniu alertów Security Center można ustawić listę adresatów, którzy otrzymają powiadomienie e-mail. Wiadomość e-mail zawiera bezpośredni link do alertu w Azure Security Center ze wszystkimi odpowiednimi szczegółami. Aby uzyskać więcej informacji, zobacz [Konfigurowanie powiadomień e-mail dla alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).



## <a name="explore-vulnerability-assessment-reports"></a>Poznaj raporty oceny luk w zabezpieczeniach

Usługa oceny luk w zabezpieczeniach skanuje bazy danych raz w tygodniu. Skany są uruchamiane w tym samym dniu tygodnia, w którym włączono usługę.

Pulpit nawigacyjny oceny luk w zabezpieczeniach zawiera przegląd wyników oceny dla wszystkich baz danych oraz podsumowanie kondycji i złej jakości baz danych oraz ogólne podsumowanie niepowodzenia kontroli zgodnie z dystrybucją ryzyka.

Wyniki oceny luk w zabezpieczeniach można wyświetlić bezpośrednio w Security Center.

1. Na pasku bocznym Security Center Otwórz stronę **zalecenia** i wybierz **luki w zabezpieczeniach serwerów usługi SQL Database na maszynach, które należy skorygować (wersja zapoznawcza)**. Aby uzyskać więcej informacji, zobacz [Security Center zalecenia](security-center-recommendations.md). 


    [![* * Luki w zabezpieczeniach baz danych SQL na maszynach powinny być skorygowane (wersja zapoznawcza) * * zalecenie](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Zostanie wyświetlony szczegółowy widok tego zalecenia.

    [![Szczegółowy widok dla * * luk w zabezpieczeniach baz danych SQL na maszynach powinien zostać skorygowany (wersja zapoznawcza) * * zalecenie](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Aby uzyskać więcej informacji, przechodzenie do szczegółów:

    * Aby zapoznać się z omówieniem skanowanych zasobów (bazy danych) i listę testowanych testów zabezpieczeń, wybierz odpowiedni serwer.

    * Aby zapoznać się z omówieniem luk w zabezpieczeniach pogrupowanych według konkretnej bazy danych SQL, wybierz interesującą bazę danych.

    W każdym widoku sprawdzanie zabezpieczeń jest sortowane według **ważności**. Kliknij określone sprawdzanie zabezpieczeń, aby wyświetlić okienko szczegółów z **opisem**, jak **rozwiązać ten** problem, oraz inne powiązane informacje, takie jak **wpływ** lub **test porównawczy**.

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>Zaawansowana ochrona przed zagrożeniami dla serwerów SQL na komputerach
Alerty są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do maszyn SQL lub korzystania z nich. Zdarzenia te mogą wyzwalać alerty wyświetlane w [sekcji alerty dla SQL Database i SQL Data Warehouse na stronie informacje o alertach](alerts-reference.md#alerts-sql-db-and-warehouse).



## <a name="explore-and-investigate-security-alerts"></a>Eksplorowanie i badanie alertów zabezpieczeń

Alerty zabezpieczeń są dostępne na stronie Alerty Security Center, na karcie Zabezpieczenia zasobu lub za pośrednictwem linku bezpośredniego w wiadomościach e-mail z alertami.

1. Aby wyświetlić alerty, wybierz pozycję **alerty zabezpieczeń** z paska bocznego Security Center i wybierz alert.

1. Alerty zostały zaprojektowane jako samodzielne, z szczegółowymi krokami korygującymi i informacjami o badaniu w każdej z nich. Można dokładniej badać przy użyciu innych Azure Security Center i możliwości funkcji badania platformy Azure w szerszym widoku:

    * Włącz funkcję inspekcji SQL Server na potrzeby dalszych badań. Jeśli jesteś użytkownikiem ze wskaźnikiem wydajności platformy Azure, możesz przekazać dzienniki inspekcji SQL z zdarzeń dziennika zabezpieczeń systemu Windows do badania wskaźnikowego i cieszyć się bogatym doświadczeniem. [Dowiedz się więcej na temat inspekcji SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15).
    * Aby ulepszyć stan zabezpieczeń, użyj zaleceń Security Center dla maszyny hosta wskazanej w każdym alercie. Pozwoli to zmniejszyć ryzyko ataków na ataki w przyszłości. 

    [Dowiedz się więcej o tym, jak zarządzać alertami i odpowiadać na](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)nie.


## <a name="next-steps"></a>Następne kroki

W przypadku pokrewnego materiału zapoznaj się z następującym artykułem:

- [Alerty zabezpieczeń dla SQL Database i SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Konfigurowanie powiadomień e-mail dla alertów zabezpieczeń](security-center-provide-security-contact-details.md)
- [Dowiedz się więcej na temat platformy Azure — Wskaźnikowanie](https://docs.microsoft.com/azure/sentinel/)
- [Pakiet zaawansowanej ochrony danych Azure Security Center](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)