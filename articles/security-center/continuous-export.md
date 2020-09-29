---
title: Eksportuj alerty Azure Security Center i zalecenia do rozwiązań Siem | Microsoft Docs
description: W tym artykule wyjaśniono, jak skonfigurować ciągły eksport alertów zabezpieczeń i zaleceń do rozwiązań Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/13/2020
ms.author: memildin
ms.openlocfilehash: d0ada1b615d4673f696c6f1b003288f3e7aa02e4
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449157"
---
# <a name="export-security-alerts-and-recommendations"></a>Eksportowanie alertów zabezpieczeń i zaleceń

Azure Security Center generuje szczegółowe alerty zabezpieczeń i zalecenia. Można je wyświetlać w portalu lub za pomocą narzędzi programistycznych. Może być również konieczne wyeksportowanie tych informacji lub wysłanie ich do innych narzędzi do monitorowania w danym środowisku. 

W tym artykule opisano zestaw narzędzi umożliwiających eksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły.

Za pomocą tych narzędzi możesz:

* Ciągłe Eksportowanie do Log Analytics obszarów roboczych
* Ciągle Eksportuj do Event Hubs platformy Azure (w przypadku integracji z usługą rozwiązań Siem innej firmy)
* Eksportuj do pliku CSV (jednorazowo)



## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Bezpłatna|
|Wymagane role i uprawnienia:|**Rola administratora zabezpieczeń** w grupie zasobów (lub **właściciela**)<br>Musi mieć również uprawnienia do zapisu dla zasobu docelowego|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Tak](./media/icons/yes-icon.png) Chiny gov (do centrum zdarzeń), inne gov|
|||



## <a name="set-up-a-continuous-export"></a>Konfigurowanie eksportu ciągłego

Poniższe kroki są niezbędne, niezależnie od tego, czy konfigurujesz ciągły eksport do Log Analytics obszaru roboczego czy Event Hubs platformy Azure.

1. Na pasku bocznym Security Center wybierz pozycję **cennik & ustawienia**.

1. Wybierz określoną subskrypcję, dla której chcesz skonfigurować eksportowanie danych.
    
1. Na pasku bocznym strony Ustawienia dla tej subskrypcji wybierz pozycję **eksport ciągły**.

    [ ![ Opcje eksportowania w Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) tym miejscu są wyświetlane opcje eksportowania. Dla każdego dostępnego elementu docelowego eksportu istnieje karta. 

1. Wybierz typ danych, który chcesz wyeksportować, i wybierz spośród filtrów dla każdego typu (na przykład wyeksportuj tylko alerty o wysokiej ważności).

1. Opcjonalnie, jeśli wybór obejmuje jedno z czterech zaleceń, można dołączyć do nich wyniki oceny luk w zabezpieczeniach:

    - Oceny luk w zabezpieczeniach baz danych SQL należy skorygować
    - Oceny luk w zabezpieczeniach na serwerach SQL na maszynach należy skorygować (wersja zapoznawcza)
    - Luki w zabezpieczeniach Azure Container Registry obrazów powinny być skorygowane (obsługiwane przez Qualys)
    - Luki w zabezpieczeniach maszyn wirtualnych należy skorygować

    Aby uwzględnić ustalenia z tymi zaleceniami, Włącz opcję **Uwzględnij wnioski dotyczące zabezpieczeń** .

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Uwzględnij w konfiguracji eksportu ciągłego funkcję przełączania zabezpieczeń&quot; :::


1. W obszarze &quot;Eksportuj element docelowy" Wybierz miejsce, w którym chcesz zapisać dane. Dane można zapisywać w miejscu docelowym w innej subskrypcji (na przykład w centralnym wystąpieniu centrum zdarzeń lub w centralnym obszarze roboczym Log Analytics).

1. Wybierz pozycję **Zapisz**.


## <a name="set-up-continuous-export-via-the-rest-api"></a>Konfigurowanie eksportu ciągłego za pomocą interfejsu API REST

Funkcję eksportu ciągłego można skonfigurować i zarządzać nią za pośrednictwem [interfejsu API Azure Security Center automations](https://docs.microsoft.com/rest/api/securitycenter/automations). Ten interfejs API służy do tworzenia lub aktualizowania automatyzacji do eksportowania do dowolnego z następujących możliwych miejsc docelowych:

- Centrum zdarzeń Azure
- Obszar roboczy usługi Log Analytics
- Azure Logic Apps 

Interfejs API zapewnia dodatkowe funkcje niedostępne w Azure Portal, na przykład:

* **Większy wolumin** — interfejs API umożliwia tworzenie wielu konfiguracji eksportu w ramach jednej subskrypcji. Strona **eksportu ciągłego** w interfejsie użytkownika portalu Security Center obsługuje tylko jedną konfigurację eksportu na subskrypcję.

* **Dodatkowe funkcje** — interfejs API oferuje dodatkowe parametry, które nie są wyświetlane w interfejsie użytkownika. Na przykład możesz dodać tagi do zasobu usługi Automation, a także zdefiniować eksport na podstawie szerszego zestawu właściwości alertu i rekomendacji niż te oferowane na stronie **eksportu ciągłego** w interfejsie użytkownika portalu Security Center.

* **Bardziej ukierunkowany zakres** — interfejs API zapewnia bardziej szczegółowy poziom konfiguracji eksportu. Podczas definiowania eksportu za pomocą interfejsu API można to zrobić na poziomie grupy zasobów. Jeśli używasz strony **eksportu ciągłego** w interfejsie użytkownika portalu Security Center, musisz ją zdefiniować na poziomie subskrypcji.

    > [!TIP]
    > Jeśli skonfigurowano wiele konfiguracji eksportu przy użyciu interfejsu API lub jeśli użyto parametrów tylko do API, te dodatkowe funkcje nie będą wyświetlane w interfejsie użytkownika Security Center. Zamiast tego zostanie wyświetlony transparent informujący o tym, że istnieją inne konfiguracje.

Więcej informacji na temat interfejsu API usługi automations znajduje się w [dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/securitycenter/automations).



## <a name="configure-siem-integration-via-azure-event-hubs"></a>Konfigurowanie integracji SIEM za pomocą usługi Azure Event Hubs

Usługa Azure Event Hubs to doskonałe rozwiązanie do programowoego wykorzystywania danych przesyłanych strumieniowo. W przypadku alertów i zaleceń dotyczących Azure Security Center jest to preferowany sposób integrowania z SIEMem innej firmy.

> [!NOTE]
> Najbardziej efektywną metodą przesyłania strumieniowego danych monitorowania do zewnętrznych narzędzi w większości przypadków jest użycie usługi Azure Event Hubs. [Ten artykuł](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) zawiera krótki opis sposobu przesyłania strumieniowego danych monitorowania z różnych źródeł do centrum zdarzeń oraz linki do szczegółowych wskazówek.

> [!NOTE]
> Jeśli wcześniej wyeksportowano Security Center alerty do SIEM przy użyciu dziennika aktywności platformy Azure, Poniższa procedura zastępuje tę metodologię.

Aby wyświetlić schematy zdarzeń wyeksportowanych typów danych, odwiedź [schematy zdarzeń centrum zdarzeń](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Aby zintegrować z usługą SIEM 

Po skonfigurowaniu ciągłego eksportowania wybranych Security Center danych do usługi Azure Event Hubs można skonfigurować odpowiedni łącznik dla SIEM:

* **Azure — wskaźnikowanie** — Użyj macierzystego [łącznika danych](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) alertów Azure Security Center.
* **Splunk** — użyj [dodatku Azure monitor dla Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** — Użyj [ręcznie skonfigurowanego źródła dziennika](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** — Użyj [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Ponadto jeśli chcesz automatycznie przenieść ciągłe wyeksportowane dane ze skonfigurowanego centrum zdarzeń na platformę Azure Eksplorator danych, Skorzystaj z instrukcji w temacie pozyskiwanie [danych z centrum zdarzeń w usłudze azure Eksplorator danych](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Eksport ciągły do obszaru roboczego Log Analytics

Jeśli chcesz analizować Azure Security Center dane w obszarze roboczym Log Analytics lub użyć alertów platformy Azure razem z Security Center, skonfiguruj eksport ciągły do obszaru roboczego Log Analytics.

Aby wyeksportować do obszaru roboczego Log Analytics, musisz mieć Security Center Log Analytics rozwiązania w Twoim obszarze roboczym. Jeśli używasz Azure Portal, bezpłatny rozwiązanie Security Center jest automatycznie włączane po włączeniu eksportu ciągłego. Jeśli jednak konfigurujesz ustawienia eksportu ciągłego programowo, musisz ręcznie włączyć lub wyłączyć usługę Azure Defender na stronie **ustawień & cenowej** .

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabele i schematy

Alerty zabezpieczeń i zalecenia są przechowywane odpowiednio w tabelach *SecurityAlert* i *SecurityRecommendations* . Nazwa rozwiązania Log Analytics zawierającego te tabele zależy od tego, czy usługa Azure Defender jest włączona: zabezpieczenia ("Security and Audit") czy SecurityCenterFree.

![Tabela * SecurityAlert * w Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Aby wyświetlić schematy zdarzeń wyeksportowanych typów danych, odwiedź [log Analytics schematy tabel](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Wyświetlanie wyeksportowanych alertów zabezpieczeń i zaleceń w Azure Monitor

W niektórych przypadkach można wyświetlić wyeksportowane alerty zabezpieczeń i/lub zalecenia w [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor zapewnia ujednolicone środowisko alertów dla różnych alertów platformy Azure, w tym dzienników diagnostycznych, alertów metryk i alertów niestandardowych opartych na zapytaniach Log Analytics obszaru roboczego.

Aby wyświetlić alerty i zalecenia z Security Center w Azure Monitor, skonfiguruj regułę alertu na podstawie zapytań Log Analytics (alert dziennika):

1. Na stronie **alerty** Azure Monitor wybierz pozycję **Nowa reguła alertu**.

    ![Strona alertów Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na stronie Tworzenie reguły Skonfiguruj nową regułę (w taki sam sposób jak w przypadku konfigurowania [reguły alertu dziennika w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * W obszarze **zasób**wybierz obszar roboczy log Analytics, do którego wyeksportowano alerty zabezpieczeń i zalecenia.

    * W obszarze **warunek**wybierz opcję **Wyszukiwanie w dzienniku niestandardowym**. Na wyświetlonej stronie Skonfiguruj zapytanie, okres lookback i okres częstotliwości. W zapytaniu wyszukiwania można wpisać *SecurityAlert* lub *SecurityRecommendation* , aby wykonać zapytanie dotyczące typów danych, które Security Center ciągle eksportować w miarę włączania eksportu ciągłego do log Analytics funkcji. 
    
    * Opcjonalnie Skonfiguruj [grupę akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) , którą chcesz wyzwolić. Grupy akcji mogą wyzwalać wysyłanie wiadomości e-mail, bilety narzędzia ITSM, elementy webhook i inne elementy.
    ![Reguła alertu Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

Zobaczysz teraz nowe alerty i zalecenia dotyczące Azure Security Center (w zależności od konfiguracji) w Azure Monitor alertach z automatycznym wyzwalaniem grupy akcji (jeśli została podana).

## <a name="manual-one-time-export-of-security-alerts"></a>Ręczne eksportowanie alertów zabezpieczeń jednorazowe

Aby pobrać raport CSV dotyczący alertów lub zaleceń, Otwórz stronę **alerty zabezpieczeń** lub **zalecenia** i wybierz przycisk **Pobierz raport CSV** .

[![Pobierz dane alertów jako plik CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Te raporty zawierają alerty i zalecenia dotyczące zasobów z aktualnie wybranych subskrypcji.



## <a name="faq---continuous-export"></a>Często zadawane pytania — eksport ciągły

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Jakie są koszty związane z eksportowaniem danych?

Włączenie eksportu ciągłego nie ma kosztu. Mogą być naliczane koszty pozyskiwania i przechowywania danych w obszarze roboczym Log Analytics, w zależności od konfiguracji. 

Dowiedz się więcej o [cenniku obszaru roboczego log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Dowiedz się więcej na temat [cennika usługi Azure Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/).


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania ciągłego eksportowania zaleceń i alertów. Wiesz również, jak pobrać dane alertów jako plik CSV. 

W przypadku pokrewnego materiału zapoznaj się z następującą dokumentacją: 

- [Dokumentacja usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentacja usługi Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentacja usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Schematy automatyzacji przepływu pracy i typy danych eksportu ciągłego](https://aka.ms/ASCAutomationSchemas)
