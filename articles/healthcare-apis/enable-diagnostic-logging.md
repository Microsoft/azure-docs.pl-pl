---
title: Włączanie rejestrowania diagnostycznego w usłudze Azure API for FHIR®
description: W tym artykule wyjaśniono, jak włączyć rejestrowanie diagnostyczne w interfejsie API platformy Azure dla FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 262509df98b93c7902d83f90756872a16d84198f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398134"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Włączanie rejestrowania diagnostycznego w usłudze Azure API for FHIR®

W tym artykule dowiesz się, jak włączyć rejestrowanie diagnostyczne w interfejsie API platformy Azure dla FHIR® i móc przeglądać przykładowe zapytania dotyczące tych dzienników. Dostęp do dzienników diagnostycznych jest istotny dla każdej usługi opieki zdrowotnej, w której zgodność z wymaganiami prawnymi (takimi jak HIPAA) jest wymagana. Funkcja w usłudze Azure API for FHIR®, która włącza dzienniki diagnostyczne, jest [**ustawieniami diagnostycznymi**](../azure-monitor/platform/diagnostic-settings.md) w Azure Portal. 

## <a name="enable-audit-logs"></a>Włączanie dzienników inspekcji
1. Aby włączyć rejestrowanie diagnostyczne w usłudze Azure API for FHIR®, wybierz swój interfejs API platformy Azure dla usługi FHIR® w Azure Portal 
2. Przejdź do **ustawień diagnostycznych**  
 ![ ustawień diagnostycznych](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. Wybierz **+ Dodaj ustawienie diagnostyczne**

4. Wprowadź nazwę dla ustawienia

5. Wybierz metodę, która ma być używana do uzyskiwania dostępu do dzienników diagnostycznych:

    1. **Archiwizuj na koncie magazynu** na potrzeby inspekcji lub inspekcji ręcznej. Konto magazynu, które ma być używane, musi być już utworzone.
    2. **Przesyłaj strumieniowo do centrum zdarzeń** w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie analityczne. Aby można było skonfigurować ten krok, należy utworzyć przestrzeń nazw centrum zdarzeń i zasady centrum zdarzeń.
    3. **Przesyłaj strumieniowo do** obszaru roboczego Log Analytics w Azure monitor. Aby można było wybrać tę opcję, musisz utworzyć obszar roboczy usługi Dzienniki analizy.

6. Wybierz **AuditLogs** i wszystkie metryki, które chcesz przechwycić

7. Klikanie pozycji Zapisz.

> [!Note] 
> Aby pierwsze dzienniki były wyświetlane w Log Analytics, może upłynąć do 15 minut.  
 
Aby uzyskać więcej informacji na temat pracy z dziennikami diagnostycznymi, zapoznaj się z [dokumentacją dotyczącą dzienników zasobów platformy Azure](../azure-monitor/platform/platform-logs-overview.md) .

## <a name="audit-log-details"></a>Szczegóły dziennika inspekcji
W tej chwili usługa Azure API for FHIR® zwraca następujące pola w dzienniku inspekcji: 

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
|ResultType|Ciąg|Dostępne wartości są obecnie **uruchomione** , **zakończone powodzeniem** lub **niepowodzeniem** .
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

Uruchom to zapytanie, aby grupować operacje według **typu zasobu FHIR** :

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
Posiadanie dostępu do dzienników diagnostycznych jest niezbędne do monitorowania usługi i zapewniania raportów zgodności. Usługa Azure API for FHIR® umożliwia wykonywanie tych działań za pomocą dzienników diagnostycznych. 
 
FHIR® jest zastrzeżonym znakiem towarowym HL7 i jest używany z uprawnieniem HL7.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób włączania dzienników inspekcji dla interfejsu API platformy Azure dla FHIR®. Następnie Dowiedz się więcej na temat innych dodatkowych ustawień, które można skonfigurować w interfejsie API platformy Azure dla usługi FHIR
 
>[!div class="nextstepaction"]
>[Ustawienia dodatkowe](azure-api-for-fhir-additional-settings.md)