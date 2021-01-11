---
title: Azure Monitor dzienniki rozwiązywania problemów (wersja zapoznawcza)
description: Korzystaj z Azure Monitor, aby szybko lub okresowo badać problemy, rozwiązywać problemy związane z kodem lub konfiguracją lub w przypadku obsługi adresów, które często polegają na przeszukiwaniu dużej ilości danych w celu uzyskania szczegółowych informacji.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: 816cdddc1f3d0a9bc9ebc3f277bc223a688cba31
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98067370"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>Azure Monitor dzienniki rozwiązywania problemów (wersja zapoznawcza)
Korzystaj z Azure Monitor, aby szybko i/lub okresowo badać problemy, rozwiązywać problemy związane z kodem lub konfiguracją lub w przypadku obsługi adresów, które często polegają na przeszukiwaniu dużej ilości danych w celu uzyskania szczegółowych informacji.

>[!NOTE]
> * Rozwiązywanie problemów z dziennikami jest w trybie podglądu.
>* Skontaktuj się z [zespołem log Analytics](mailto:orens@microsoft.com) z innymi pytaniami lub, aby zastosować tę funkcję.
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>Rozwiązywanie problemów z kodem lub konfiguracją i wykonywanie względem nich zapytań
Użyj Azure Monitor dzienników rozwiązywania problemów, aby pobrać swoje rekordy i zbadać problemy i problemy w prostszy i tańszy sposób przy użyciu KQL.
Rozwiązywanie problemów z dziennikami pozwala uzyskać podstawowe możliwości rozwiązywania problemów.

> [!NOTE]
>* Decyzję dotyczącą trybu rozwiązywania problemów można skonfigurować.
>* Dzienniki rozwiązywania problemów można zastosować do określonych tabel, obecnie w tabelach "dzienniki kontenerów" i "ślady aplikacji".
>* Dostępny jest 4-dniowy okres przechowywania danych. można go dodatkowo wydłużyć.
>* Domyślnie tabele dziedziczą przechowywanie obszaru roboczego. Aby uniknąć dodatkowych opłat, zaleca się zmianę przechowywania tych tabel. [Kliknij tutaj, aby dowiedzieć się, jak zmienić przechowywanie tabeli](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage).

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>Włącz Rozwiązywanie problemów z dziennikami w tabelach

Aby włączyć Rozwiązywanie problemów z dziennikami w obszarze roboczym, należy użyć następującego wywołania interfejsu API.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>Sprawdź, czy dla danej tabeli włączono funkcję dzienników rozwiązywania problemów.
Aby sprawdzić, czy dziennik rozwiązywania problemów dla danej tabeli jest włączony, można użyć następującego wywołania interfejsu API.

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>Sprawdź, czy funkcja dzienniki rozwiązywania problemów jest włączona dla wszystkich tabel w obszarze roboczym
Aby sprawdzić, które tabele mają włączony dziennik rozwiązywania problemów, można użyć następującego wywołania interfejsu API.

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>Wyłączanie dzienników rozwiązywania problemów w tabelach

Aby wyłączyć dzienniki rozwiązywania problemów w obszarze roboczym, należy użyć następującego wywołania interfejsu API.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
>[!TIP]
>* Aby uruchomić polecenia, można użyć dowolnego narzędzia interfejsu API REST. [Przeczytaj więcej](https://docs.microsoft.com/rest/api/azure/)
>* Do uwierzytelniania należy użyć tokenu okaziciela. [Przeczytaj więcej](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* Flaga "isTroubleshootingAllowed" — opisuje, czy tabela jest dozwolona w usłudze
>* "IsTroubleshootEnabled" wskazuje, czy funkcja jest włączona dla tabeli — można ją włączyć lub wyłączyć (wartość true lub false).
>* Podczas wyłączania flagi "isTroubleshootEnabled" dla konkretnej tabeli, ponowne włączenie tego elementu jest możliwe tylko jeden tydzień po poprzedniej dacie włączenia.
>* Obecnie jest to obsługiwane tylko dla tabel w obszarze (niektóre inne jednostki SKU również będą obsługiwane w przyszłości) — [Przeczytaj więcej na temat cen](https://docs.microsoft.com/services-hub/health/azure_pricing).

## <a name="query-limitations-for-troubleshooting"></a>Ograniczenia zapytania dotyczące rozwiązywania problemów
Istnieje kilka ograniczeń dotyczących tabeli oznaczonej jako "Rozwiązywanie problemów z dziennikami":
*   Program będzie miał mniej zasobów przetwarzania i dlatego nie będzie odpowiedni dla dużych pulpitów nawigacyjnych, złożonej analizy ani wielu współbieżnych wywołań interfejsu API.
*   Zapytania są ograniczone do zakresu czasu wynoszącego dwa dni.
* Przeczyszczanie nie będzie działało — [Przeczytaj więcej na temat przeczyszczania](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge).
* Alerty nie są obsługiwane przez tę usługę.
## <a name="next-steps"></a>Następne kroki
* [Pisanie zapytań](https://docs.microsoft.com/azure/data-explorer/write-queries)


