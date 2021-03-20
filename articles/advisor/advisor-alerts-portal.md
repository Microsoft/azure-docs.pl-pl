---
title: Utwórz alerty Azure Advisor dla nowych zaleceń przy użyciu Azure Portal
description: Utwórz alerty Azure Advisor dla nowego zalecenia
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 3c51479821914ef34edcd13d8708344169f17aae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590105"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations-using-the-azure-portal"></a>Utwórz Azure Advisor alerty dotyczące nowych zaleceń przy użyciu Azure Portal 

W tym artykule opisano sposób konfigurowania alertu dotyczącego nowych zaleceń z Azure Advisor przy użyciu Azure Portal. 

Za każdym razem, gdy Azure Advisor wykryje nowe zalecenie dla jednego z zasobów, zdarzenie jest przechowywane w [dzienniku aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md). Alerty dotyczące tych zdarzeń można skonfigurować w Azure Advisor przy użyciu funkcji tworzenia alertów specyficznych dla zalecenia. Możesz wybrać subskrypcję i opcjonalnie grupę zasobów, aby określić zasoby, dla których chcesz otrzymywać alerty. 

Możesz również określić typy zaleceń, korzystając z następujących właściwości:

* Kategoria
* Poziom wpływu
* Typ zalecenia

Możesz również skonfigurować akcję, która będzie wykonywana po wyzwoleniu alertu przez:  

* Wybieranie istniejącej grupy akcji
* Tworzenie nowej grupy akcji

Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/alerts/action-groups.md).

> [!NOTE] 
> Alerty usługi Advisor są obecnie dostępne tylko w celu uzyskania wysokiej dostępności, wydajności i zaleceń dotyczących kosztów. Zalecenia dotyczące zabezpieczeń nie są obsługiwane. 

## <a name="create-alert-rule"></a>Tworzenie reguły alertu
1. W **portalu** wybierz pozycję **Azure Advisor**.

    ![Azure Advisor w portalu](./media/advisor-alerts/create1.png)

2. W sekcji **monitorowanie** w menu po lewej stronie wybierz pozycję **alerty**. 

    ![Alerty w usłudze Advisor](./media/advisor-alerts/create2.png)

3. Wybierz pozycję **Nowy alert usługi Advisor**.

    ![Alert dotyczący nowego klasyfikatora](./media/advisor-alerts/create3.png)

4. W sekcji **zakres** wybierz subskrypcję i opcjonalnie grupę zasobów, w której chcesz otrzymywać alerty. 

    ![Zakres alertów klasyfikatora](./media/advisor-alerts/create4.png)

5. W sekcji **warunek** wybierz metodę, która ma być używana do konfigurowania alertu. Jeśli chcesz otrzymywać alerty dotyczące wszystkich zaleceń dla konkretnej kategorii i/lub poziomu wpływu, wybierz **kategorię i poziom wpływu**. Jeśli chcesz otrzymywać alerty dotyczące wszystkich zaleceń określonego typu, wybierz pozycję **typ rekomendacji**.

    ![Azure Advisor warunek alertu](./media/advisor-alerts/create5.png)

6. W zależności od wybranej opcji konfigurowania przez użytkownika będzie można określić kryteria. Jeśli chcesz uzyskać wszystkie zalecenia, po prostu pozostaw pozostałe pola puste. 

    ![Grupa akcji alertu klasyfikatora](./media/advisor-alerts/create6.png)

7. W sekcji **grupy akcji** wybierz pozycję **Dodaj istniejący** , aby użyć grupy akcji, która została już utworzona, lub wybierz pozycję **Utwórz nową** , aby skonfigurować nową [grupę akcji](../azure-monitor/alerts/action-groups.md). 

    ![Alert klasyfikatora — Dodawanie istniejących](./media/advisor-alerts/create7.png)

8. W sekcji Szczegóły alertu nadaj Alertowi nazwę i Krótki opis. Jeśli chcesz, aby alert był włączony, pozostaw **regułę Włącz po** wybraniu pozycji **tak**. Następnie wybierz grupę zasobów, w której ma zostać zapisany alert. Nie wpłynie to na zakres docelowy zalecenia. 

    :::image type="content" source="./media/advisor-alerts/create8.png" alt-text="Zrzut ekranu przedstawiający sekcję Szczegóły alertu.":::


## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Konfigurowanie alertów rekomendacji do używania elementu webhook
W tej sekcji przedstawiono sposób konfigurowania alertów Azure Advisor w celu wysyłania danych rekomendacji za poorednictwem elementów webhook do istniejących systemów. 

Możesz skonfigurować alerty, które będą powiadamiane, gdy masz nowe zalecenie klasyfikatora dotyczące jednego z zasobów. Te alerty mogą powiadamiać użytkownika za pośrednictwem poczty e-mail lub wiadomości SMS, ale mogą być również używane do integracji z istniejącymi systemami za pośrednictwem elementu webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Korzystanie z ładunku alertu rekomendacji klasyfikatora
Jeśli chcesz zintegrować alerty usługi Advisor z własnymi systemami przy użyciu elementu webhook, musisz przeanalizować ładunek JSON, który jest wysyłany z powiadomienia. 

Po skonfigurowaniu grupy akcji dla tego alertu należy wybrać, czy chcesz użyć typowego schematu alertu. Jeśli wybierzesz Schemat typowego alertu, Twój ładunek będzie wyglądać następująco: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Jeśli nie używasz wspólnego schematu, ładunek będzie wyglądać następująco: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

W obu schematach można zidentyfikować zdarzenia rekomendacji klasyfikatora, szukając elementu **EventSource** `Recommendation` , a **OperationName**  jest `Microsoft.Advisor/recommendations/available/action` .

Oto niektóre z innych ważnych pól, których warto użyć: 

* *alertTargetIDs* (we wspólnym schemacie) lub *ResourceID* (starszy schemat)
* *Rekomendacja*
* *Rekomendacja*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>Zarządzanie alertami 

W Azure Advisor można edytować, usuwać lub wyłączać i włączać alerty dotyczące zaleceń. 

1. W **portalu** wybierz pozycję **Azure Advisor**.

    :::image type="content" source="./media/advisor-alerts/create1.png" alt-text="Zrzut ekranu przedstawiający menu Azure Portal, w którym zaznaczono Azure Advisor.":::

2. W sekcji **monitorowanie** w menu po lewej stronie wybierz pozycję **alerty**.

    :::image type="content" source="./media/advisor-alerts/create2.png" alt-text="Zrzut ekranu przedstawiający menu Azure Portal, w którym są zaznaczone alerty.":::

3. Aby edytować alert, kliknij nazwę alertu, aby otworzyć alert i edytować pola, które chcesz edytować.

4. Aby usunąć, włączyć lub wyłączyć alert, kliknij wielokropek na końcu wiersza, a następnie wybierz akcję, którą chcesz wykonać.
 

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z [omówieniem alertów dziennika aktywności](../azure-monitor/alerts/alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.
- Dowiedz się więcej na temat [grup akcji](../azure-monitor/alerts/action-groups.md).
