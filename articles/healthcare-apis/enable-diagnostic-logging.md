---
title: Włączanie rejestrowania diagnostycznego w interfejsie API platformy Azure dla usługi FHIR
description: W tym artykule wyjaśniono, jak włączyć rejestrowanie diagnostyczne w interfejsie API platformy Azure dla FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 54119585d4f1377b60b85fbad01fe90f097a304f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95905178"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Włączanie rejestrowania diagnostycznego w interfejsie API platformy Azure dla usługi FHIR

W tym artykule dowiesz się, jak włączyć rejestrowanie diagnostyczne w interfejsie API platformy Azure dla usługi FHIR i móc przeglądać przykładowe zapytania dotyczące tych dzienników. Dostęp do dzienników diagnostycznych jest istotny dla każdej usługi opieki zdrowotnej, w której zgodność z wymaganiami prawnymi (takimi jak HIPAA) jest wymagana. Funkcja interfejsu API platformy Azure dla usługi FHIR, która umożliwia logowanie dzienników diagnostycznych, to [**Ustawienia diagnostyczne**](../azure-monitor/platform/diagnostic-settings.md) w Azure Portal. 

## <a name="enable-audit-logs"></a>Włączanie dzienników inspekcji
1. Aby włączyć rejestrowanie diagnostyczne w interfejsie API platformy Azure dla FHIR, wybierz usługę Azure API for FHIR w Azure Portal 
2. Przejdź do **ustawień diagnostycznych**  
 ![ ustawień diagnostycznych](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. Wybierz **+ Dodaj ustawienie diagnostyczne**

4. Wprowadź nazwę dla ustawienia

5. Wybierz metodę, która ma być używana do uzyskiwania dostępu do dzienników diagnostycznych:

    1. **Archiwizuj na koncie magazynu** na potrzeby inspekcji lub inspekcji ręcznej. Konto magazynu, które ma być używane, musi być już utworzone.
    2. **Przesyłaj strumieniowo do centrum zdarzeń** w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie analityczne. Aby można było skonfigurować ten krok, należy utworzyć przestrzeń nazw centrum zdarzeń i zasady centrum zdarzeń.
    3. **Przesyłaj strumieniowo do** obszaru roboczego Log Analytics w Azure monitor. Aby można było wybrać tę opcję, musisz utworzyć obszar roboczy usługi Dzienniki analizy.

6. Wybierz pozycję **AuditLogs** i wszystkie metryki, które chcesz przechwycić. Jeśli używasz łącznika usługi Azure IoT dla programu FHIR, upewnij się, że wybrano **błędy, ruch i czas oczekiwania** dla metryk. 

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

7. Wybierz pozycję **Zapisz**


> [!Note] 
> Aby pierwsze dzienniki były wyświetlane w Log Analytics, może upłynąć do 15 minut.  
 
Aby uzyskać więcej informacji na temat pracy z dziennikami diagnostycznymi, zapoznaj się z [dokumentacją dotyczącą dzienników zasobów platformy Azure](../azure-monitor/platform/platform-logs-overview.md) .

## <a name="audit-log-details"></a>Szczegóły dziennika inspekcji
W tej chwili usługa Azure API for FHIR zwraca następujące pola w dzienniku inspekcji: 

|Nazwa pola  |Typ  |Uwagi  |
|---------|---------|---------|
|CallerIdentity|Dynamiczny|Ogólny zbiór właściwości zawierający informacje o tożsamości
|CallerIdentityIssuer|Ciąg|Wystawca 
|CallerIdentityObjectId|Ciąg|Object_Id 
|CallerIPAddress|Ciąg|Adres IP wywołującego 
|CorrelationId|Ciąg| Identyfikator korelacji
|FhirResourceType|Ciąg|Typ zasobu, dla którego wykonano operację
|LogCategory|Ciąg|Kategoria dziennika (obecnie zwracamy "AuditLogs" LogCategory)
|Lokalizacja|Ciąg|Lokalizacja serwera, który przetworzył żądanie (np. Południowo-środkowe stany USA)
|OperationDuration|int|Czas, jaki zajęło wykonanie tego żądania w ciągu kilku sekund
|OperationName|Ciąg| Opisuje typ operacji (np. Update, Search-Type)
|RequestUri|Ciąg|Identyfikator URI żądania 
|ResultType|Ciąg|Dostępne wartości są obecnie **uruchomione**, **zakończone powodzeniem** lub **niepowodzeniem** .
|Stanu|int|Kod stanu HTTP. (np. 200) 
|TimeGenerated|DateTime|Data i godzina zdarzenia|
|Właściwości|Ciąg| Opisuje właściwości fhirResourceType
|SourceSystem|Ciąg| System źródłowy (zawsze platforma Azure w tym przypadku)
|TenantId|Ciąg|Identyfikator dzierżawy
|Typ|Ciąg|Typ dziennika (zawsze MicrosoftHealthcareApisAuditLog w tym przypadku)
|_ResourceId|Ciąg|Szczegóły zasobu

## <a name="sample-queries"></a>Przykładowe zapytania

Poniżej przedstawiono kilka podstawowych zapytań Application Insights, których można użyć do eksplorowania danych dziennika.

Uruchom to zapytanie, aby zobaczyć **100** najnowszych dzienników:

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Uruchom to zapytanie, aby grupować operacje według **typu zasobu FHIR**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

Uruchom to zapytanie, aby uzyskać wszystkie **wyniki zakończone niepowodzeniem**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Wniosek 
Posiadanie dostępu do dzienników diagnostycznych jest niezbędne do monitorowania usługi i zapewniania raportów zgodności. Interfejs API platformy Azure dla usługi FHIR umożliwia wykonywanie tych działań za pomocą dzienników diagnostycznych. 
 
FHIR to zastrzeżony znak towarowy firmy HL7 i jest używany za jej pozwoleniem.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób włączania dzienników inspekcji dla interfejsu API platformy Azure dla FHIR. Następnie Dowiedz się więcej na temat innych dodatkowych ustawień, które można skonfigurować w interfejsie API platformy Azure dla usługi FHIR
 
>[!div class="nextstepaction"]
>[Ustawienia dodatkowe](azure-api-for-fhir-additional-settings.md)
