---
title: Eksportuj alerty Azure Security Center i zalecenia do rozwiązań Siem | Microsoft Docs
description: W tym artykule wyjaśniono, jak skonfigurować ciągły eksport alertów zabezpieczeń i zaleceń do rozwiązań Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: c30fef5c82f831365a11bcb14559b0914a9eda4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84299948"
---
# <a name="export-security-alerts-and-recommendations"></a>Eksportowanie alertów zabezpieczeń i zaleceń

Azure Security Center generuje szczegółowe alerty zabezpieczeń i zalecenia. Można je wyświetlać w portalu lub za pomocą narzędzi programistycznych. Może być również konieczne wyeksportowanie tych informacji lub wysłanie ich do innych narzędzi do monitorowania w danym środowisku. 

W tym artykule opisano zestaw narzędzi umożliwiających eksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły.

Za pomocą tych narzędzi możesz:

* Ciągłe Eksportowanie do Log Analytics obszarów roboczych
* Ciągle Eksportuj do Event Hubs platformy Azure (w przypadku integracji z usługą rozwiązań Siem innej firmy)
* Eksportuj do pliku CSV (jednorazowo)




## <a name="availability"></a>Dostępność

- Stan wydania: **ogólnie dostępny**
- Wymagane role i uprawnienia:
    - **Czytelnik** w subskrypcji zawierającej konfigurację eksportu
    - **Rola administratora zabezpieczeń** w grupie zasobów (lub **właściciela**)
    - Musi mieć również uprawnienia do zapisu dla zasobu docelowego
- Połączeń 
    - ✔ Chmury komercyjne
    - ✔ US Gov
    - ✘ Chiny gov, inne gov


## <a name="setting-up-a-continuous-export"></a>Konfigurowanie eksportu ciągłego

Poniższe kroki są niezbędne, niezależnie od tego, czy konfigurujesz ciągły eksport do Log Analytics obszaru roboczego czy Event Hubs platformy Azure.

1. Na pasku bocznym Security Center wybierz pozycję **cennik & ustawienia**.

1. Wybierz określoną subskrypcję, dla której chcesz skonfigurować eksportowanie danych.
    
1. Na pasku bocznym strony Ustawienia dla tej subskrypcji wybierz pozycję **eksport ciągły**.

    [ ![ Opcje eksportowania w Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) tym miejscu są wyświetlane opcje eksportowania. Dla każdego dostępnego elementu docelowego eksportu istnieje karta. 

1. Wybierz typ danych, który chcesz wyeksportować, i wybierz spośród filtrów dla każdego typu (na przykład wyeksportuj tylko alerty o wysokiej ważności).

1. W obszarze "Eksportuj element docelowy" Wybierz miejsce, w którym chcesz zapisać dane. Dane można zapisywać w miejscu docelowym w innej subskrypcji (na przykład w centralnym wystąpieniu centrum zdarzeń lub w centralnym obszarze roboczym Log Analytics).

1. Kliknij pozycję **Zapisz**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Konfigurowanie integracji SIEM za pomocą usługi Azure Event Hubs

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

Aby wyeksportować do obszaru roboczego Log Analytics, musisz mieć Security Center Log Analytics rozwiązania w Twoim obszarze roboczym. W przypadku korzystania z Azure Portal rozwiązanie warstwy Bezpłatna Security Center jest automatycznie włączane po włączeniu eksportu ciągłego. Jeśli jednak konfigurujesz ustawienia eksportu ciągłego programowo, musisz ręcznie wybrać warstwę cenową dla wymaganego obszaru roboczego z poziomu **ustawień & cenowych**.  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabele i schematy

Alerty zabezpieczeń i zalecenia są przechowywane odpowiednio w tabelach *SecurityAlert* i *SecurityRecommendations* . Nazwa rozwiązania Log Analytics zawierającego te tabele zależy od tego, czy korzystasz z warstwy Bezpłatna, czy standardowa (zobacz [Cennik](security-center-pricing.md)): zabezpieczenia ("Security and Audit") lub SecurityCenterFree.

![Tabela * SecurityAlert * w Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Aby wyświetlić schematy zdarzeń wyeksportowanych typów danych, odwiedź [log Analytics schematy tabel](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Wyświetlanie wyeksportowanych alertów zabezpieczeń i zaleceń w Azure Monitor

W niektórych przypadkach można wyświetlić wyeksportowane alerty zabezpieczeń i/lub zalecenia w [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor zapewnia ujednolicone środowisko alertów dla różnych alertów platformy Azure, w tym dzienników diagnostycznych, alertów metryk i alertów niestandardowych opartych na zapytaniach Log Analytics obszaru roboczego.

Aby wyświetlić alerty i zalecenia z Security Center w Azure Monitor, skonfiguruj regułę alertu na podstawie zapytań Log Analytics (alert dziennika):

1. Na stronie **alerty** Azure Monitor kliknij pozycję **Nowa reguła alertu**.

    ![Strona alertów Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na stronie Tworzenie reguły Skonfiguruj nową regułę (w taki sam sposób jak w przypadku konfigurowania [reguły alertu dziennika w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * W obszarze **zasób**wybierz obszar roboczy log Analytics, do którego wyeksportowano alerty zabezpieczeń i zalecenia.

    * W obszarze **warunek**wybierz opcję **Wyszukiwanie w dzienniku niestandardowym**. Na wyświetlonej stronie Skonfiguruj zapytanie, okres lookback i okres częstotliwości. W zapytaniu wyszukiwania można wpisać *SecurityAlert* lub *SecurityRecommendation* , aby wykonać zapytanie dotyczące typów danych, które Security Center ciągle eksportować w miarę włączania eksportu ciągłego do log Analytics funkcji. 
    
    * Opcjonalnie Skonfiguruj [grupę akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) , którą chcesz wyzwolić. Grupy akcji mogą wyzwalać wysyłanie wiadomości e-mail, bilety narzędzia ITSM, elementy webhook i inne elementy.
    ![Reguła alertu Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

Zobaczysz teraz nowe alerty i zalecenia dotyczące Azure Security Center (w zależności od konfiguracji) w Azure Monitor alertach z automatycznym wyzwalaniem grupy akcji (jeśli została podana).

## <a name="manual-one-time-export-of-security-alerts"></a>Ręczne eksportowanie alertów zabezpieczeń jednorazowe

Aby pobrać raport CSV dotyczący alertów lub zaleceń, Otwórz stronę **alerty zabezpieczeń** lub **zalecenia** , a następnie kliknij przycisk **Pobierz raport CSV** .

[![Pobierz dane alertów jako plik CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Te raporty zawierają alerty i zalecenia dotyczące zasobów z aktualnie wybranych subskrypcji.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania ciągłego eksportowania zaleceń i alertów. Wiesz również, jak pobrać dane alertów jako plik CSV. 

W przypadku pokrewnego materiału zapoznaj się z następującą dokumentacją: 

- [Dokumentacja usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentacja usługi Azure wskaźnikowego](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentacja Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Schematy automatyzacji przepływu pracy i typy danych eksportu ciągłego](https://aka.ms/ASCAutomationSchemas)
