---
title: Konfigurowanie rejestrowania do monitorowania aplikacji logiki w Azure Security Center
description: Monitoruj kondycję zasobów Logic Apps w Azure Security Center przez skonfigurowanie rejestrowania diagnostycznego.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: ed1fe2885b1be28a03251bcfcecd08bdbd35adcf
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790076"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Konfigurowanie rejestrowania do monitorowania aplikacji logiki w Azure Security Center

W przypadku monitorowania zasobów Logic Apps w [Microsoft Azure Security Center](../security-center/security-center-introduction.md)można [sprawdzić, czy aplikacje logiki są zgodne z zasadami domyślnymi](#view-logic-apps-health-status). Na platformie Azure jest wyświetlany stan kondycji zasobu Logic Apps po włączeniu rejestrowania i poprawnym skonfigurowaniu miejsca docelowego dzienników. W tym artykule wyjaśniono, jak skonfigurować rejestrowanie diagnostyczne i upewnić się, że wszystkie aplikacje logiki są w dobrej kondycji.

> [!TIP]
> Aby znaleźć bieżący stan usługi Logic Apps, przejrzyj [stronę stanu platformy Azure](https://status.azure.com/), która zawiera informacje o stanie różnych produktów i usług w każdym z dostępnych regionów.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* Istniejące aplikacje logiki z [włączonym rejestrowaniem diagnostycznym](#enable-diagnostic-logging).
* Obszar roboczy Log Analytics, który jest wymagany do włączenia rejestrowania dla aplikacji logiki. Jeśli nie masz obszaru roboczego, najpierw [Utwórz obszar roboczy](../azure-monitor/learn/quick-create-workspace.md).

## <a name="enable-diagnostic-logging"></a>Włączanie rejestrowania diagnostycznego

Aby można było wyświetlić stan kondycji zasobów dla aplikacji logiki, należy najpierw [skonfigurować rejestrowanie diagnostyczne](monitor-logic-apps-log-analytics.md). Jeśli masz już obszar roboczy Log Analytics, możesz włączyć rejestrowanie podczas tworzenia aplikacji logiki lub istniejących aplikacji logiki.

> [!TIP]
> Zalecane jest włączenie dzienników diagnostycznych dla Logic Apps. Można jednak kontrolować to ustawienie dla aplikacji logiki. Po włączeniu dzienników diagnostycznych dla aplikacji logiki można użyć tych informacji, aby pomóc analizować zdarzenia związane z zabezpieczeniami.

### <a name="check-diagnostic-logging-setting"></a>Sprawdź ustawienia rejestrowania diagnostycznego

Jeśli nie masz pewności, czy Twoje aplikacje logiki mają włączone rejestrowanie diagnostyczne, możesz zaewidencjonować Security Center:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku wyszukiwania wprowadź i wybierz **Security Center**.
1. W menu Security Center pulpitu nawigacyjnego w obszarze **Ogólne** wybierz pozycję **zalecenia**.
1. W tabeli sugestii dotyczących zabezpieczeń Znajdź i wybierz opcję **Włącz inspekcję i rejestrowanie** &gt; **dzienników diagnostycznych w Logic Apps należy włączyć** w tabeli formantów zabezpieczeń.
1. Na stronie zalecenie rozwiń sekcję **kroki korygowania** i przejrzyj opcje. Można włączyć diagnostykę Logic Apps, wybierając **szybkie rozwiązanie!** lub zgodnie z ręcznymi instrukcjami korygowania.

## <a name="view-logic-apps-health-status"></a>Wyświetl stan kondycji aplikacji logiki

Po [włączeniu rejestrowania diagnostycznego](#enable-diagnostic-logging)można sprawdzić stan kondycji aplikacji logiki w Security Center.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku wyszukiwania wprowadź i wybierz **Security Center**.
1. W menu Security Center pulpitu nawigacyjnego w obszarze **Ogólne** wybierz pozycję **spis**.
1. Na stronie spis odfiltruj listę zasobów, aby wyświetlić tylko zasoby Logic Apps. W menu Strona Wybierz pozycję **typy zasobów** &gt; **Aplikacje logiki**.

   Licznik **zasoby w złej kondycji** przedstawia liczbę aplikacji logiki, które Security Center traktują w złej kondycji.
1.  Na liście zasobów aplikacji logiki Przejrzyj kolumnę **zalecenia** . Aby przejrzeć szczegóły kondycji konkretnej aplikacji logiki, wybierz nazwę zasobu lub przycisk wielokropka (**...**) &gt; **Wyświetl zasób**.
1.  Aby skorygować potencjalne problemy z kondycją zasobów, wykonaj czynności opisane w sekcji Aplikacje logiki.

Jeśli rejestrowanie diagnostyczne jest już włączone, może wystąpić problem z miejscem docelowym dzienników. Zapoznaj [się z tematem rozwiązywania problemów z różnymi lokalizacjami docelowymi rejestrowania diagnostycznego](#fix-diagnostic-logging-for-logic-apps).

## <a name="fix-diagnostic-logging-for-logic-apps"></a>Naprawianie rejestrowania diagnostycznego dla aplikacji logiki

Jeśli [Aplikacje logiki są wyświetlane jako w złej kondycji w Security Center](#view-logic-apps-health-status), Otwórz aplikację logiki w widoku kodu w Azure Portal lub za pomocą interfejsu wiersza polecenia platformy Azure. Następnie sprawdź konfigurację docelową dzienników diagnostycznych: [azure log Analytics](#log-analytics-and-event-hubs-destinations), [Azure Event Hubs](#log-analytics-and-event-hubs-destinations)lub [konto usługi Azure Storage](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics i Event Hubs miejsc docelowych

Jeśli używasz Log Analytics lub Event Hubs jako miejsca docelowego dla dzienników diagnostycznych Logic Apps, sprawdź poniższe ustawienia. 

1. Aby potwierdzić włączenie dzienników diagnostycznych, sprawdź, czy pole ustawienia diagnostyczne `logs.enabled` ma ustawioną wartość `true` . 
1. Aby potwierdzić, że jako miejsce docelowe nie zostało ustawione konto magazynu, sprawdź, czy `storageAccountId` pole jest ustawione na `false` .

Na przykład:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Miejsce docelowe konta magazynu

Jeśli używasz konta magazynu jako miejsca docelowego dla dzienników diagnostycznych Logic Apps, sprawdź poniższe ustawienia.

1. Aby potwierdzić włączenie dzienników diagnostycznych, sprawdź, czy pole ustawienia diagnostyki `logs.enabled` ma ustawioną wartość `true` .
1. Aby upewnić się, że zostały włączone zasady przechowywania dzienników diagnostycznych, sprawdź, czy `retentionPolicy.enabled` pole jest ustawione na `true` .
1. Aby potwierdzić ustawienie czasu przechowywania 0-365 dni, sprawdź, czy `retentionPolicy.days` pole jest ustawione na liczbę z zakresu od 0 do 365.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```