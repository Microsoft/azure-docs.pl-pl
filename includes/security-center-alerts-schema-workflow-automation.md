---
title: dołączanie pliku
description: dołączanie pliku
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91401102"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>Model danych schematu

|Pole|Typ danych|Opis|
|----|----|----|
|**AlertDisplayName**|String|Nazwa wyświetlana alertu.|
|**AlertType**|String|Typ alertu. Alerty tego samego typu powinny mieć taką samą wartość. To pole jest podkluczowym ciągiem reprezentującym typ alertu, a nie wystąpienia alertu. Wszystkie wystąpienia alertów z tej samej logiki wykrywania/analitycznej powinny mieć taką samą wartość dla typu alertu.|
|**CompromisedEntity**|String|Nazwa wyświetlana zasobu najbardziej powiązanego z tym alertem.|
|**Opis**|String|Opis alertu.|
|**EndTimeUtc**|Data i godzina|Godzina ostatniego zdarzenia lub działania zawartego w alercie.  Pole powinno być ciągiem, który jest zgodny z formatem ISO8601, w tym informacje o strefie czasowej UTC.|
|**Jednostki**|IEnumerable (IEntity)|Lista jednostek związanych z alertem. Ta lista może przechowywać różne jednostki różnych typów. Typ jednostek może być dowolnym z typów zdefiniowanych w sekcji jednostek. Jednostki, które nie znajdują się na poniższej liście, można również wysłać, ale nie jest to gwarantowane, że zostaną przetworzone (alert nie powiedzie się w przypadku niepowodzenia weryfikacji przy użyciu nowych typów jednostek).|
|**Właściwości ExtendedProperties**|Dictionary (String, String)|Dostawcy mogą (opcjonalnie) dołączyć pola niestandardowe tutaj.|
|**Zamiar**|Wyliczenie|Cel łańcucha kasowania związany z tym alertem. Aby zapoznać się z listą obsługiwanych wartości oraz wyjaśnieniami, które są obsługiwane przez Azure Security Center, zobacz [intencje](../articles/security-center/alerts-reference.md#intentions).<br/>To pole może zawierać wiele wartości (rozdzielonych przecinkami).|
|**Iszdarzenie**|Wartość logiczna|To pole określa, czy alert jest zdarzeniem (złożonym grupowaniem kilku alertów) czy pojedynczym alertem. Wartość domyślna dla pola to "false" (oznacza to, że jest to pojedynczy Alert).|
|**ProcessingEndTime**|Data i godzina|Czas dostępności alertu dla użytkownika końcowego w oryginalnym produkcie zawierającym alert.|
|**ProductName**|String|Nazwa produktu, który opublikował ten alert (Azure Security Center, Azure ATP, Microsoft Defender ATP, MCAS itd.).|
|**RemediationSteps**|Staw<String>|Ręczne elementy akcji, które należy wykonać w celu skorygowania alertu.|
|**ResourceIdentifiers**|Lista (identyfikatory zasobów)|Identyfikatory zasobów dla tego alertu, których można użyć do skierowania alertu do odpowiedniej grupy zagrożeń produktu (dzierżawca, obszar roboczy, subskrypcja itp.). Dla każdego alertu może istnieć wiele identyfikatorów różnych typów.|
|**Ważność**|Wyliczenie|Ważność alertu zgłoszonego przez dostawcę. Możliwe wartości: informacyjny, niski, średni i wysoki.|
|**StartTimeUtc**|Data i godzina|Godzina pierwszego zdarzenia lub działania zawartego w alercie. Pole powinno być ciągiem, który jest zgodny z formatem ISO8601, w tym informacje o strefie czasowej UTC.|
|**Stan**|Wyliczenie|Stan cyklu życia alertu.<br/>Obsługiwane są następujące stany: nowe, rozwiązane, odrzucone, nieznane.<br/>Alert określający wartość inną niż obsługiwane opcje ma przypisany stan "nieznany".<br/>Do alertu, który nie określa wartości, jest przypisany stan "New".|
|**SystemAlertId**|String|Identyfikator alertu.|
|**TimeGenerated**|Data i godzina|Godzina, o której alert został wygenerowany przez dostawcę alertów. W przypadku braku zgłoszenia przez wewnętrznych dostawców alertów produkt może zdecydować się na przypisanie czasu otrzymanego do przetwarzania przez produkt.  Pole powinno być ciągiem, który jest zgodny z formatem ISO8601, w tym informacje o strefie czasowej UTC.|
|**NazwaDostawcy**|String|Nazwa dostawcy, który wygeneruje alert.|
|||
