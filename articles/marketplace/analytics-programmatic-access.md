---
title: Model dostępu programistycznego
description: Zapoznaj się z ogólnym przepływem wzorca wywołań interfejsu API na potrzeby analizy programowanej. Są również omówione interfejsy API służące do uzyskiwania dostępu do raportów analitycznych w portalu komercyjnym firmy Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584088"
---
# <a name="programmatic-access-paradigm"></a>Model dostępu programistycznego

Ten diagram przedstawia wzorzec wywołania interfejsu API służący do tworzenia nowego szablonu raportu, planowania raportu niestandardowego i pobierania danych o niepowodzeniu.

[ ![ Ilustruje wzorzec wywołania interfejsu API służący do tworzenia nowego szablonu raportu, planowania raportu niestandardowego i pobierania danych o niepowodzeniu.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***Rysunek 1. przepływ wysokiego poziomu wzorca wywołania interfejsu API***

Ta lista zawiera więcej szczegółów na temat rysunku 1.

1. Aplikacja kliencka może zdefiniować niestandardowy schemat/szablon raportu, wywołując [interfejs API tworzenia zapytań raportu](#create-report-query-api). Alternatywnie można użyć szablonu raportu ( `QueryId` ) z [listy zapytań systemowych](analytics-system-queries.md).
1. Po pomyślnym zakończeniu interfejs API tworzenia szablonu raportu zwraca wartość `QueryId` .
1. Aplikacja kliencka następnie wywołuje [interfejs API tworzenia raportu](#create-report-api) , korzystając `QueryID` z programu wraz z datą rozpoczęcia raportu, interwałem powtarzania, cyklem i opcjonalnym identyfikatorem URI wywołania zwrotnego.
1. Po pomyślnym zakończeniu [interfejs API tworzenia raportu](#create-report-api) zwraca wartość `ReportID` .
1. Aplikacja kliencka otrzymuje powiadomienie o identyfikatorze URI wywołania zwrotnego, gdy tylko dane raportu będą gotowe do pobrania.
1. Aplikacja kliencka następnie używa [interfejsu API pobierania raportów do wykonywania](#get-report-executions-api) zapytań dotyczących stanu raportu z `Report ID` zakresem dat i.
1. Po pomyślnym zakończeniu zostanie zwrócone łącze pobierania raportu, a aplikacja będzie mogła inicjować pobieranie danych.

## <a name="report-query-language-specification"></a>Specyfikacja języka zapytania raportu

Dostarczając [zapytania systemowe](analytics-system-queries.md) , których można użyć do tworzenia raportów, możesz również utworzyć własne zapytania w zależności od potrzeb firmy. Aby dowiedzieć się więcej o niestandardowych zapytaniach, zobacz [niestandardowe specyfikacje zapytań](analytics-custom-query-specification.md).

## <a name="create-report-query-api"></a>Utwórz interfejs API zapytań raportu

Ten interfejs API pomaga utworzyć niestandardowe zapytania definiujące zestaw danych, z którego należy wyeksportować kolumny i metryki. Interfejs API zapewnia elastyczność tworzenia nowego szablonu raportowania w zależności od potrzeb firmy.

Możesz również użyć dostarczanych [zapytań systemowych](analytics-system-queries.md) . Gdy niestandardowe szablony raportów nie są potrzebne, można wywołać [interfejs API tworzenia raportu](#create-report-api) bezpośrednio przy użyciu [QueryIds](analytics-system-queries.md) z dostarczanymi przez nas kwerendami systemowymi.

Poniższy przykład pokazuje, jak utworzyć niestandardowe zapytanie, aby uzyskać _znormalizowane użycie i szacowane opłaty finansowe za płatne jednostki SKU_ z zestawu danych [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) w ciągu ostatniego miesiąca.

*Składnia żądania*

| Metoda | Identyfikator URI żądania |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*Nagłówek żądania*

| Nagłówek | Typ | Opis |
| ------------- | ------------- | ------------- |
| Autoryzacja | ciąg | Wymagane. Token dostępu Azure Active Directory (Azure AD). Format to `Bearer <token>` . |
| Content-Type | `string` | `application/JSON` |
||||

*Parametr ścieżki*

Brak

*Parametr zapytania*

Brak

*Przykład ładunku żądania*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*Słownik*

Ta tabela zawiera definicje kluczy elementów w ładunku żądania.

| Parametr | Wymagane | Opis | Dozwolone wartości |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Tak | Przyjazna nazwa zapytania | ciąg |
| `Description` | Nie | Opis zwracanych przez zapytanie | ciąg |
| `Query` | Tak | Ciąg zapytania raportu | Typ danych: ciąg<br>[Zapytanie niestandardowe](analytics-sample-queries.md) na podstawie potrzeb firmy |
|||||

> [!NOTE]
> Aby uzyskać niestandardowe przykłady zapytań, zobacz [przykłady przykładowych zapytań](analytics-sample-queries.md).

*Przykładowa odpowiedź*

Ładunek odpowiedzi jest następujący:

Kody odpowiedzi: 200, 400, 401, 403, 500

Przykład ładunku odpowiedzi:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*Słownik*

Ta tabela zawiera definicje kluczy elementów w odpowiedzi.

| Parametr | Opis |
| ------------ | ------------- |
| `QueryId` | Unikatowy identyfikator uniwersalny (UUID) utworzonego zapytania |
| `Name` | Przyjazna nazwa nadana kwerendzie w ładunku żądania |
| `Description` | Opis podanym podczas tworzenia zapytania |
| `Query` | Zapytanie raportu zostało przesłane jako dane wejściowe podczas tworzenia zapytania |
| `Type` | Ustaw wartość `userDefined` |
| `User` | Identyfikator użytkownika użyty do utworzenia zapytania |
| `CreatedTime` | Czas UTC utworzenia zapytania w tym formacie: RRRR-MM-DDTgg: mm: SSS |
| `TotalCount` | Liczba zestawów danych w tablicy wartości |
| `StatusCode` | Kod wyniku<br>Możliwe wartości to 200, 400, 401, 403, 500 |
| `message` | Komunikat o stanie z wykonania interfejsu API |
|||

## <a name="create-report-api"></a>Utwórz interfejs API raportów

Po pomyślnym utworzeniu niestandardowego szablonu raportu i odebraniu `QueryID` jako części odpowiedzi na [zapytanie o utworzenie raportu](#create-report-query-api) ten interfejs API można wywołać, aby zaplanować wykonywanie zapytania w regularnych odstępach czasu. Możesz ustawić częstotliwość i harmonogram, aby raport został dostarczony. W przypadku dostarczanych zapytań systemowych interfejs API tworzenia raportu może być również wywoływany z [QueryId](analytics-sample-queries.md).

*Składnia żądania*

| Metoda | Identyfikator URI żądania |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*Nagłówek żądania*

| Nagłówek | Typ | Opis |
| ------ | ---- | ----------- |
| Autoryzacja | ciąg | Wymagane. Token dostępu Azure Active Directory (Azure AD). Format to `Bearer <token>` . |
| Typ zawartości | ciąg | `application/JSON` |
||||

*Parametr ścieżki*

Brak

*Parametr zapytania*

Brak

*Przykład ładunku żądania*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*Słownik*

Ta tabela zawiera definicje kluczy elementów w ładunku żądania.

| Parametr | Wymagane | Opis | Dozwolone wartości |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Tak | Nazwa przypisana do raportu | ciąg |
| `Description` | Nie | Opis utworzonego raportu | ciąg |
| `QueryId` | Tak | Identyfikator zapytania raportu | ciąg |
| `StartTime` | Tak | Sygnatura czasowa UTC, w której rozpocznie się generowanie raportu.<br>Powinien to być format: RRRR-MM-DDTgg: mm: SSS | ciąg |
| `RecurrenceInterval` | Tak | Częstotliwość w godzinach, w której ma zostać wygenerowany raport.<br>Wartość minimalna to 4, a wartość maksymalna to 90. | liczba całkowita |
| `RecurrenceCount` | Nie | Liczba raportów do wygenerowania. | liczba całkowita |
| `Format` | Nie | Format pliku wyeksportowanego pliku.<br>Formatem domyślnym jest. CSV. | WOLUMINY CSV/TSV |
| `CallbackUrl` | Nie | Publicznie osiągalny adres URL, który można opcjonalnie skonfigurować jako miejsce docelowe wywołania zwrotnego. | Ciąg (adres URL http) |
| `ExecuteNow` | Nie | Ten parametr powinien służyć do tworzenia raportu, który zostanie wykonany tylko raz. `StartTime`, `RecurrenceInterval` , i `RecurrenceCount` są ignorowane, jeśli jest ustawiona na `true` . Raport jest wykonywany natychmiast w sposób asynchroniczny. | PRAWDA/FAŁSZ |
| `QueryStartTime` | Nie | Opcjonalnie określa czas rozpoczęcia zapytania podczas wyodrębniania danych. Ten parametr ma zastosowanie tylko w przypadku jednorazowego raportu wykonawczego, który ma `ExecuteNow` ustawioną wartość `true` . Format powinien być RRRR-MM-DDTgg: mm: SSS | Sygnatura czasowa jako ciąg |
| `QueryEndTime` | Nie | Opcjonalnie określa czas zakończenia kwerendy wyodrębniania danych. Ten parametr ma zastosowanie tylko w przypadku jednorazowego raportu wykonawczego, który ma `ExecuteNow` ustawioną wartość `true` . Format powinien być RRRR-MM-DDTgg: mm: SSS | Sygnatura czasowa jako ciąg |
|||||

*Przykładowa odpowiedź*

Ładunek odpowiedzi jest następujący:

Kod odpowiedzi: 200, 400, 401, 403, 404, 500

Ładunek odpowiedzi:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*Słownik*

Ta tabela zawiera definicje kluczy elementów w odpowiedzi.

| Parametr | Opis |
| ------------ | ------------- |
| `ReportId` | Unikatowy identyfikator uniwersalny (UUID) utworzonego raportu |
| `ReportName` | Nazwa nadana raportowi w ładunku żądania |
| `Description` | Opis udzielony podczas tworzenia raportu |
| `QueryId` | Identyfikator zapytania przesłany w momencie utworzenia raportu |
| `Query` | Tekst zapytania, który zostanie wykonany dla tego raportu |
| `User` | Identyfikator użytkownika użyty do utworzenia raportu |
| `CreatedTime` | Czas UTC, gdy raport został utworzony w tym formacie: RRRR-MM-DDTgg: mm: SSS |
| `ModifiedTime` | Czas UTC ostatniej modyfikacji raportu w tym formacie: RRRR-MM-DDTgg: mm: SSS |
| `StartTime` | Czas UTC rozpoczęcia wykonywania raportu w tym formacie: RRRR-MM-DDTgg: mm: SSS |
| `ReportStatus` | Stan wykonywania raportu. Możliwe wartości są **wstrzymane**, **aktywne** i **nieaktywne**. |
| `RecurrenceInterval` | Interwał cyklu podany podczas tworzenia raportu |
| `RecurrenceCount` | Liczba cykli podana podczas tworzenia raportu |
| `CallbackUrl` | Adres URL wywołania zwrotnego podany w żądaniu |
| `Format` | Format plików raportu. Możliwe wartości to CSV lub TSV. |
| `TotalCount` | Liczba zestawów danych w tablicy wartości |
| `StatusCode` | Kod wyniku<br>Możliwe wartości to 200, 400, 401, 403, 500 |
| `message` | Komunikat o stanie z wykonania interfejsu API |
|||

## <a name="get-report-executions-api"></a>Pobierz interfejs API wykonywania raportów

Za pomocą tej metody można wysyłać zapytania o stan wykonywania raportu za pomocą `ReportId` otrzymanego z [interfejsu API tworzenia raportów](#create-report-api). Metoda zwraca łącze pobierania raportu, jeśli raport jest gotowy do pobrania. W przeciwnym razie metoda zwraca stan. Tego interfejsu API można także użyć do pobrania wszystkich wykonań, które wystąpiły dla danego raportu.

> [!IMPORTANT]
> Ten interfejs API ma domyślne parametry zapytania ustawione dla `executionStatus=Completed` i  `getLatestExecution=true` . W związku z tym wywołanie interfejsu API przed pierwszym pomyślnym wykonaniem raportu zwróci wartość 404. Oczekujące wykonania można uzyskać przez ustawienie `executionStatus=Pending` .

*Składnia żądania*

| Metoda | Identyfikator URI żądania |
| ------------ | ------------- |
| Get | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*Nagłówek żądania*

| Nagłówek | Typ | Opis |
| ------ | ------ | ------ |
| Autoryzacja | ciąg | Wymagane. Token dostępu Azure Active Directory (Azure AD). Format to `Bearer <token>` . |
| Typ zawartości | ciąg | `application/json` |
||||

*Parametr ścieżki*

Brak

*Parametr zapytania*

| Nazwa parametru | Wymagany | Typ | Opis |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Tak | ciąg | Filtr, aby uzyskać szczegóły wykonania tylko dla raportów, które zostały `reportId` określone w tym argumencie. `reportIds`Można określić wiele, rozdzielając je średnikami ";". |
| `executionId` | Nie | ciąg | Filtruj, aby uzyskać szczegółowe informacje o raportach, które są `executionId` określone w tym argumencie. `executionIds`Można określić wiele, rozdzielając je średnikami ";". |
| `executionStatus` | Nie | ciąg/Wyliczenie | Filtruj, aby uzyskać szczegółowe informacje o raportach, które są `executionStatus` określone w tym argumencie.<br>Prawidłowe wartości to: `Pending` , `Running` , `Paused` i `Completed` <br>Wartość domyślna to `Completed`. Wiele stanów można określić, rozdzielając je średnikami ";". |
| `getLatestExecution` | Nie | boolean | Interfejs API zwróci szczegóły najnowszego wykonania raportu.<br>Domyślnie ten parametr jest ustawiony na `true` . Jeśli zdecydujesz się przekazać wartość tego parametru jako `false` , interfejs API zwróci ostatnie wystąpienia wykonywania (90 dni). |
|||||

*Ładunek żądania*

Brak

*Przykładowa odpowiedź*

Ładunek odpowiedzi jest następujący:

Kody odpowiedzi: 200, 400, 401, 403, 404, 500

Przykład ładunku odpowiedzi:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

Po zakończeniu wykonywania raportu `Completed` zostanie wyświetlony stan wykonania. Możesz pobrać raport, wybierając adres URL w `reportAccessSecureLink` .

*Słownik*

Definicje kluczy elementów w odpowiedzi.

| Parametr | Opis |
| ------------ | ------------- |
| `ExecutionId` | Unikatowy identyfikator uniwersalny (UUID) wystąpienia wykonywania |
| `ReportId` | Identyfikator raportu skojarzony z wystąpieniem wykonywania |
| `RecurrenceInterval` | Interwał cyklu podany podczas tworzenia raportu |
| `RecurrenceCount` | Liczba cykli podana podczas tworzenia raportu |
| `CallbackUrl` | Adres URL wywołania zwrotnego skojarzony z wystąpieniem wykonywania |
| `Format` | Format wygenerowanego pliku na końcu wykonywania |
| `ExecutionStatus` | Stan wystąpienia wykonywania raportu.<br>Prawidłowe wartości to: `Pending` , `Running` , `Paused` i `Completed` |
| `ReportAccessSecureLink` | Link, za pomocą którego można bezpiecznie uzyskać dostęp do raportu |
| `ReportExpiryTime` | Czas UTC, po upływie którego link do raportu wygaśnie w tym formacie: RRRR-MM-DDTgg: mm: SSS |
| `ReportGeneratedTime` | Czas UTC, w którym raport został wygenerowany w tym formacie: RRRR-MM-DDTgg: mm: SSS |
| `TotalCount` | Liczba zestawów danych w tablicy wartości |
| `StatusCode` | Kod wyniku <br>Możliwe wartości to 200, 400, 401, 403, 404 i 500 |
| `message` | Komunikat o stanie z wykonania interfejsu API |
|||

## <a name="next-steps"></a>Następne kroki
- Interfejsy API można wypróbować za pomocą [adresu URL interfejsu API struktury Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)
- Rozpoczynanie pracy z dostępem programistycznym do danych analitycznych
