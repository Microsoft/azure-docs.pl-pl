---
title: Przesyłanie strumieniowe alertów z Azure Security Center do systemów zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) oraz innych rozwiązań do monitorowania
description: Dowiedz się, jak przesyłać alerty zabezpieczeń do usługi Azure rozwiązań Siem, rozwiązań innych firm, o lub narzędzia ITSM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 3ddc385b9d489e0c2ab4abf35a6ade011970342b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572944"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Przesyłanie strumieniowe alertów do rozwiązania SIEM, o lub zarządzania usługami IT

Usługa Azure Security Center może przesyłać strumieniowo alerty zabezpieczeń do najpopularniejszych rozwiązań dotyczących zabezpieczeń i zarządzania zdarzeniami (SIEM), o (Security Orchestration) i zarządzania usługami IT (narzędzia ITSM).

Dostępne są narzędzia oparte na platformie Azure umożliwiające zagwarantowanie, że dane dotyczące alertów są dostępne we wszystkich najpopularniejszych rozwiązaniach używanych dzisiaj, w tym:

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **QRadar firmy IBM**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Przesyłanie strumieniowe alertów do platformy Azure — wskaźnik 

Security Center natywnie integruje się z platformą Azure, w chmurze platformy Azure — natywne rozwiązanie SIEM i o. 

[Dowiedz się więcej na temat platformy Azure — wskaźnik](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Łączniki usługi Azure wskaźnikowego dla Security Center

Wskaźnik platformy Azure obejmuje wbudowane łączniki do Azure Security Center na poziomie subskrypcji i dzierżawy:

- [Przesyłaj strumieniowo alerty do wskaźnikowego platformy Azure na poziomie subskrypcji](../sentinel/connect-azure-security-center.md)
- [Połącz wszystkie subskrypcje w dzierżawie z platformą Azure — wskaźnikiem](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Konfigurowanie pozyskiwania wszystkich dzienników inspekcji na platformie Azure — wskaźnik 

Kolejną alternatywą dla badania Security Center alertów na platformie Azure wskaźnikowej jest przesyłanie strumieniowe dzienników inspekcji do platformy Azure — wskaźnik:
    - [Łączenie ze zdarzeniami zabezpieczeń systemu Windows](../sentinel/connect-windows-security-events.md)
    - [Zbieranie danych z źródeł opartych na systemie Linux przy użyciu dziennika systemowego](../sentinel/connect-syslog.md)
    - [Połącz dane z dziennika aktywności platformy Azure](../sentinel/connect-azure-activity.md)

> [!TIP]
> Na platformie Azure jest naliczana stawka na podstawie ilości danych pozyskiwanych do analizy na platformie Azure i przechowywanych w obszarze roboczym Azure Monitor Log Analytics. Wskaźnik platformy Azure obejmuje elastyczny i przewidywalny model cen. [Dowiedz się więcej na stronie cennika usługi Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Alerty strumieniowe za pomocą interfejsu API zabezpieczeń Microsoft Graph

Security Center ma wbudowaną integrację z interfejsem API zabezpieczeń Microsoft Graph. Nie jest wymagana żadna konfiguracja i nie ma dodatkowych kosztów. 

Tego interfejsu API można używać do przesyłania strumieniowego alertów z **całej dzierżawy** (i danych z wielu innych produktów zabezpieczeń firmy Microsoft) do rozwiązań Siem i innych popularnych platform:

- Chmura Splunk Enterprise **i Splunk**  -  [Użyj Add-On interfejsu API zabezpieczeń Microsoft Graph dla Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power BI**  -  [Nawiązywanie połączenia z interfejsem API zabezpieczeń Microsoft Graph w programie Power BI Desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **Usługi ServiceNow**  -  [Postępuj zgodnie z instrukcjami, aby zainstalować i skonfigurować aplikację interfejsu API zabezpieczeń Microsoft Graph ze sklepu usługi ServiceNow](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  -  [Moduł pomocy technicznej urządzenia firmy IBM dla Azure Security Center za pośrednictwem interfejsu API Microsoft Graph](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **anomali**, **Lookout**, **Inspark** i innych — [interfejs API zabezpieczeń Microsoft Graph](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Dowiedz się więcej o interfejsie API zabezpieczeń Microsoft Graph](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Alerty strumieniowe za pomocą Azure Monitor 

Aby przesłać strumieniowo alerty do **ArcSight**, **Splunk**, **SumoLogic**, serwerów dziennika systemowego, **LogRhythm**, platformy do wglądu w **chmurę** i innych rozwiązań do monitorowania. Połącz Security Center z usługą Azure monitor za pośrednictwem platformy Azure Event Hubs:

1. Włącz [ciągły eksport](continuous-export.md) , aby przesyłać strumieniowo alerty Security Center do dedykowanego centrum zdarzeń platformy Azure na poziomie subskrypcji. 
    > [!TIP]
    > Aby to zrobić na poziomie grupy zarządzania przy użyciu Azure Policy, zobacz [Tworzenie konfiguracji automatyzacji ciągłego eksportu na dużą skalę](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)

1. [Połącz centrum zdarzeń platformy Azure z preferowanym rozwiązaniem przy użyciu wbudowanych łączników Azure monitor](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. Opcjonalnie można przesyłać strumieniowo dzienniki pierwotne do centrum zdarzeń platformy Azure i łączyć się z preferowanym rozwiązaniem. Dowiedz się więcej o [dostępnych danych monitorowania](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Aby wyświetlić schematy zdarzeń wyeksportowanych typów danych, odwiedź [schematy zdarzeń centrum zdarzeń](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Następne kroki

Na tej stronie wyjaśniono, jak upewnić się, że Azure Security Center dane alertu są dostępne w wybranym narzędziu SIEM, o lub narzędzia ITSM. W przypadku pokrewnego materiału Zobacz:

- [Co to jest Azure Sentinel?](../sentinel/overview.md)
- [Sprawdzanie poprawności alertu w Azure Security Center](security-center-alert-validation.md) — Sprawdź, czy alerty są poprawnie skonfigurowane
- [Ciągłe eksportowanie danych Security Center](continuous-export.md)