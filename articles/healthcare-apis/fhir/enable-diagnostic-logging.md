---
title: Włączanie rejestrowania diagnostycznego w interfejsie API platformy Azure dla usługi FHIR
description: W tym artykule wyjaśniono, jak włączyć rejestrowanie diagnostyczne w interfejsie API platformy Azure dla FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: d8f7a2b2f31fb192147c1950866cff77064a3671
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020317"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Włączanie rejestrowania diagnostycznego w interfejsie API platformy Azure dla usługi FHIR

W tym artykule dowiesz się, jak włączyć rejestrowanie diagnostyczne w interfejsie API platformy Azure dla usługi FHIR i móc przeglądać przykładowe zapytania dotyczące tych dzienników. Dostęp do dzienników diagnostycznych jest istotny dla każdej usługi opieki zdrowotnej, w której zgodność z wymaganiami prawnymi (takimi jak HIPAA) jest wymagana. Funkcja interfejsu API platformy Azure dla usługi FHIR, która umożliwia logowanie dzienników diagnostycznych, to [**Ustawienia diagnostyczne**](../../azure-monitor/essentials/diagnostic-settings.md) w Azure Portal. 

## <a name="view-and-download-fhir-metrics-data"></a>Wyświetlanie i pobieranie danych metryk FHIR

Możesz wyświetlić metryki w obszarze monitorowanie | Metryki z portalu. Metryki obejmują liczbę żądań, średnie opóźnienia, liczbę błędów, rozmiar danych, jednostek ru, liczbę żądań, które przekroczyły pojemność i dostępność (w%). Zrzut ekranu poniżej przedstawia jednostek ru używane dla przykładowego środowiska z niewielkimi działaniami w ciągu ostatnich 7 dni. Dane można pobrać w formacie JSON.

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="Azure API for FHIR — metryki z portalu" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>Włączanie dzienników inspekcji
1. Aby włączyć rejestrowanie diagnostyczne w interfejsie API platformy Azure dla FHIR, wybierz usługę Azure API for FHIR w Azure Portal 
2. Przejdź do **ustawień diagnostycznych** 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="Dodaj ustawienia diagnostyki usługi Azure FHIR." lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. Wybierz **+ Dodaj ustawienie diagnostyczne**

4. Wprowadź nazwę dla ustawienia

5. Wybierz metodę, która ma być używana do uzyskiwania dostępu do dzienników diagnostycznych:

    1. **Archiwizuj na koncie magazynu** na potrzeby inspekcji lub inspekcji ręcznej. Konto magazynu, które ma być używane, musi być już utworzone.
    2. **Przesyłaj strumieniowo do centrum zdarzeń** w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie analityczne. Aby można było skonfigurować ten krok, należy utworzyć przestrzeń nazw centrum zdarzeń i zasady centrum zdarzeń.
    3. **Przesyłaj strumieniowo do** obszaru roboczego Log Analytics w Azure monitor. Aby można było wybrać tę opcję, musisz utworzyć obszar roboczy usługi Dzienniki analizy.

6. Wybierz pozycję **AuditLogs** i/lub **AllMetrics**. Metryki obejmują nazwę usługi, dostępność, rozmiar danych, łączny czas oczekiwania, całkowitą liczbę żądań, łączne błędy i sygnaturę czasową. Więcej szczegółów na temat [obsługiwanych metryk](https://docs.microsoft.com/azure/azure-monitor/essentials/metrics-supported#microsofthealthcareapisservices)można znaleźć. 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Ustawienia diagnostyczne usługi Azure FHIR. Wybierz pozycję AuditLogs i/lub AllMetrics." lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. Wybierz pozycję **Zapisz**


> [!Note] 
> Aby pierwsze dzienniki były wyświetlane w Log Analytics, może upłynąć do 15 minut. Ponadto jeśli usługa Azure API for FHIR jest przenoszona z jednej grupy zasobów lub subskrypcji do innej, zaktualizuj ustawienie po zakończeniu przenoszenia. 
 
Aby uzyskać więcej informacji na temat pracy z dziennikami diagnostycznymi, zapoznaj się z [dokumentacją dotyczącą dzienników zasobów platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md) .

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

## <a name="conclusion"></a>Podsumowanie 
Posiadanie dostępu do dzienników diagnostycznych jest niezbędne do monitorowania usługi i zapewniania raportów zgodności. Interfejs API platformy Azure dla usługi FHIR umożliwia wykonywanie tych działań za pomocą dzienników diagnostycznych. 
 
FHIR to zastrzeżony znak towarowy firmy HL7 i jest używany za jej pozwoleniem.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób włączania dzienników inspekcji dla interfejsu API platformy Azure dla FHIR. Następnie Dowiedz się więcej na temat innych dodatkowych ustawień, które można skonfigurować w interfejsie API platformy Azure dla usługi FHIR
 
>[!div class="nextstepaction"]
>[Ustawienia dodatkowe](azure-api-for-fhir-additional-settings.md)
