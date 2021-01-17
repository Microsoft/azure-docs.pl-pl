---
title: Jak używać usługi Azure Defender dla programu SQL
description: Dowiedz się, jak używać opcjonalnego planu usługi Azure Defender dla programu SQL Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/23/2020
ms.author: memildin
ms.openlocfilehash: c9d11f1ddc3e1dd2239bdfce9c41f58fe9e20497
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539959"
---
# <a name="azure-defender-for-sql-servers-on-machines"></a>Usługa Azure Defender dla serwerów SQL na maszynach 

Ten plan usługi Azure Defender wykrywa nietypowe działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Zobaczysz alerty w przypadku podejrzanych działań bazy danych, potencjalnych luk w zabezpieczeniach lub ataków wstrzykiwania kodu SQL oraz nietypowego dostępu do bazy danych i wzorców zapytań.

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Opłaty za **usługi Azure Defender dla serwerów SQL na maszynach** są rozliczane zgodnie z [cennikiem](security-center-pricing.md) .|
|Chronione wersje programu SQL:|Azure SQL Server (wszystkie wersje objęte pomocą techniczną firmy Microsoft)|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Nie](./media/icons/no-icon.png) Chiny gov, inne gov|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Konfigurowanie usługi Azure Defender dla serwerów SQL na maszynach

Aby włączyć ten plan:

* Zainicjuj obsługę agenta Log Analytics na hoście programu SQL Server. Zapewnia to połączenie z platformą Azure.

* Włącz opcjonalny plan na stronie cennika i ustawienia Security Center.

Oba te elementy zostały opisane poniżej.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Krok 1. Zainicjuj obsługę agenta Log Analytics na hoście programu SQL Server:

- **SQL Server na maszynie wirtualnej platformy Azure** — Jeśli maszyna SQL jest hostowana na maszynie wirtualnej platformy Azure, możesz [włączyć funkcję autoaprowizacji agenta <a name="auto-provision-mma"></a> log Analytics](security-center-enable-data-collection.md#auto-provision-mma). Alternatywnie można wykonać procedurę ręczną, aby [dołączyć Azure Stack maszyn wirtualnych](quickstart-onboard-machines.md#onboard-your-azure-stack-vms).
- **SQL Server w usłudze Azure Arc** — Jeśli SQL Server jest zarządzany przez serwery z obsługą [usługi Azure Arc](../azure-arc/index.yml) , można wdrożyć agenta Log Analytics przy użyciu zalecenia Security Center "log Analytics Agent należy zainstalować na maszynach usługi Azure Arc opartych na systemie Windows (wersja zapoznawcza)". Alternatywnie możesz skorzystać z metod instalacji opisanych w [dokumentacji usługi Azure Arc](../azure-arc/servers/manage-vm-extensions.md).

- **SQL Server Premium** — Jeśli SQL Server jest hostowana na lokalnym komputerze z systemem Windows bez usługi Azure ARC, dostępne są dwie opcje połączenia z platformą Azure:
    
    - **Wdrażanie usługi Azure Arc** — można podłączyć dowolną maszynę z systemem Windows do Security Center. Natomiast usługa Azure Arc zapewnia lepszą integrację ze *wszystkimi* środowiskami platformy Azure. W przypadku skonfigurowania usługi Azure Arc zostanie wyświetlona strona **SQL Server — usługa Azure Arc** w portalu, a na tej stronie zostaną wyświetlone **alerty zabezpieczeń.** Dlatego pierwszą i zalecaną opcją jest [skonfigurowanie usługi Azure Arc na hoście](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) i postępuj zgodnie z instrukcjami dotyczącymi **SQL Server w usłudze Azure Arc** powyżej.
        
    - **Połącz maszynę z systemem Windows bez usługi Azure Arc** — w przypadku wybrania opcji łączenia SQL Server uruchomionego na komputerze z systemem Windows bez korzystania z usługi Azure Arc postępuj zgodnie z instrukcjami w temacie [łączenie komputerów z systemem windows, aby Azure monitor](../azure-monitor/platform/agent-windows.md).


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>Krok 2. Włącz opcjonalny plan na stronie cennika i ustawienia Security Center:

1. W menu Security Center Otwórz stronę **ustawienia & cennika** .

    - Jeśli używasz **domyślnego obszaru roboczego Azure Security Center** (o nazwie "defaultworkspace-[Identyfikator subskrypcji] — [region]"), wybierz odpowiednią **subskrypcję**.

    - Jeśli używasz **obszaru roboczego innego niż domyślny**, wybierz odpowiedni **obszar roboczy** (w razie potrzeby wprowadź nazwę obszaru roboczego w filtrze):

        ![Znajdowanie obszaru roboczego innego niż domyślny według tytułu](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. Ustaw opcję **usługi Azure Defender for SQL Server na maszynach** na **komputerze**. 

    ![Strona cennika Security Center z opcjonalnymi planami](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)

    Plan zostanie włączony na wszystkich serwerach SQL połączonych z wybranym obszarem roboczym. Ochrona zostanie w pełni aktywna po pierwszym ponownym uruchomieniu wystąpienia SQL Server.

    >[!TIP] 
    > Aby utworzyć nowy obszar roboczy, postępuj zgodnie z instrukcjami w temacie [tworzenie log Analytics obszaru roboczego](../azure-monitor/learn/quick-create-workspace.md).


1. Opcjonalnie możesz skonfigurować powiadomienia e-mail o alertach zabezpieczeń. 
    Po wygenerowaniu alertów Security Center można ustawić listę adresatów, którzy otrzymają powiadomienie e-mail. Wiadomość e-mail zawiera bezpośredni link do alertu w Azure Security Center ze wszystkimi odpowiednimi szczegółami. Aby uzyskać więcej informacji, zobacz [Konfigurowanie powiadomień e-mail dla alertów zabezpieczeń](security-center-provide-security-contact-details.md).



## <a name="explore-vulnerability-assessment-reports"></a>Poznaj raporty oceny luk w zabezpieczeniach

Usługa oceny luk w zabezpieczeniach skanuje bazy danych raz w tygodniu. Skany są uruchamiane w tym samym dniu tygodnia, w którym włączono usługę.

Pulpit nawigacyjny oceny luk w zabezpieczeniach zawiera przegląd wyników oceny dla wszystkich baz danych oraz podsumowanie kondycji i złej jakości baz danych oraz ogólne podsumowanie niepowodzenia kontroli zgodnie z dystrybucją ryzyka.

Wyniki oceny luk w zabezpieczeniach można wyświetlić bezpośrednio w Security Center.

1. Na pasku bocznym Security Center Otwórz stronę **zalecenia** i wybierz **luki w zabezpieczeniach serwerów SQL na maszynach, które należy skorygować (wersja zapoznawcza)**. Aby uzyskać więcej informacji, zobacz [Security Center zalecenia](security-center-recommendations.md). 

    :::image type="content" source="./media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png" alt-text="Oceny luk w zabezpieczeniach na serwerach SQL na maszynach należy skorygować (wersja zapoznawcza)":::

    Zostanie wyświetlony szczegółowy widok tego zalecenia.

    :::image type="content" source="./media/security-center-advanced-iaas-data/all-servers-view.png" alt-text="Szczegółowy widok rekomendacji":::

1. Aby uzyskać więcej informacji, przechodzenie do szczegółów:

    * Aby zapoznać się z omówieniem skanowanych zasobów (bazy danych) i listę testowanych testów zabezpieczeń, wybierz odpowiedni serwer.

    * Aby zapoznać się z omówieniem luk w zabezpieczeniach pogrupowanych według konkretnej bazy danych SQL, wybierz interesującą bazę danych.

    W każdym widoku sprawdzanie zabezpieczeń jest sortowane według **ważności**. Kliknij określone sprawdzanie zabezpieczeń, aby wyświetlić okienko szczegółów z **opisem**, jak **rozwiązać ten** problem, oraz inne powiązane informacje, takie jak **wpływ** lub **test porównawczy**.

## <a name="azure-defender-for-sql-alerts"></a>Alerty usługi Azure Defender dla programu SQL
Alerty są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do maszyn SQL lub korzystania z nich. Zdarzenia te mogą wyzwalać alerty wyświetlane na [stronie odniesienia alertów](alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-and-investigate-security-alerts"></a>Eksplorowanie i badanie alertów zabezpieczeń

Alerty usługi Azure Defender dla programu SQL są dostępne na stronie Alerty Security Center, na karcie Zabezpieczenia zasobu, na [pulpicie nawigacyjnym usługi Azure Defender](azure-defender-dashboard.md)lub za pośrednictwem bezpośredniego linku w wiadomościach e-mail z alertami.

1. Aby wyświetlić alerty, wybierz pozycję **alerty zabezpieczeń** z menu Security Center i wybierz alert.

1. Alerty zostały zaprojektowane jako samodzielne, z szczegółowymi krokami korygującymi i informacjami o badaniu w każdej z nich. Można dokładniej badać przy użyciu innych Azure Security Center i możliwości funkcji badania platformy Azure w szerszym widoku:

    * Włącz funkcję inspekcji SQL Server na potrzeby dalszych badań. Jeśli jesteś użytkownikiem ze wskaźnikiem wydajności platformy Azure, możesz przekazać dzienniki inspekcji SQL z zdarzeń dziennika zabezpieczeń systemu Windows do badania wskaźnikowego i cieszyć się bogatym doświadczeniem. [Dowiedz się więcej na temat inspekcji SQL Server](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * Aby ulepszyć stan zabezpieczeń, użyj zaleceń Security Center dla maszyny hosta wskazanej w każdym alercie. Pozwoli to zmniejszyć ryzyko ataków na ataki w przyszłości. 

    [Dowiedz się więcej o tym, jak zarządzać alertami i odpowiadać na](security-center-managing-and-responding-alerts.md)nie.


## <a name="next-steps"></a>Następne kroki

W przypadku pokrewnego materiału zapoznaj się z następującym artykułem:

- [Alerty zabezpieczeń dotyczące SQL Database i usługi Azure Synapse Analytics](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Konfigurowanie powiadomień e-mail na potrzeby alertów zabezpieczeń](security-center-provide-security-contact-details.md)
- [Dowiedz się więcej na temat platformy Azure — Wskaźnikowanie](../sentinel/index.yml)