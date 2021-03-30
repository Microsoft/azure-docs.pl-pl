---
title: Tworzenie zaawansowanych harmonogramów zadań i cykli
description: Dowiedz się, jak tworzyć zaawansowane harmonogramy i cykle dla zadań w usłudze Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 5a74240e3f116121c0aaddd11c186e6e674ea26a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368183"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Tworzenie zaawansowanych harmonogramów i cyklów dla zadań w usłudze Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która jest [wycofywana](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Aby kontynuować pracę z zadaniami skonfigurowanymi w usłudze Scheduler, [Przeprowadź migrację do Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) najszybciej, jak to możliwe. 
>
> Usługa Scheduler nie jest już dostępna w Azure Portal, ale polecenia cmdlet programu PowerShell dla [interfejsu API REST](/rest/api/scheduler) i [usługi Azure Scheduler](scheduler-powershell-reference.md) pozostają dostępne w tym momencie, aby można było zarządzać zadaniami i kolekcjami zadań.

W ramach zadania [usługi Azure Scheduler](../scheduler/scheduler-intro.md) harmonogram jest rdzeniem, który określa, kiedy i w jaki sposób usługa harmonogramu ma uruchamiać zadanie. Można skonfigurować wiele jednorazowych i cyklicznych harmonogramów dla zadania z harmonogramem. Harmonogramy jednorazowe są uruchamiane tylko raz w określonym czasie i są to zasadniczo cykliczne harmonogramy, które są uruchamiane tylko raz. Harmonogramy cykliczne są uruchamiane zgodnie z określoną częstotliwością. Dzięki tej elastyczności można używać usługi Scheduler dla różnych scenariuszy biznesowych, na przykład:

* **Regularnie oczyszczaj dane**: Utwórz codzienne zadanie, które usuwa wszystkie tweety starsze niż trzy miesiące.

* **Dane archiwalne**: Utwórz comiesięczne zadanie, które wypycha historię faktur do usługi kopii zapasowej.

* **Zażądaj danych zewnętrznych**: Utwórz zadanie, które jest uruchamiane co 15 minut i ściąga nowy raport pogody z NOAA.

* **Obrazy procesów**: Utwórz zadanie w dniu tygodnia, które jest uruchamiane w godzinach poza godzinami szczytu i korzysta z chmury obliczeniowej do kompresowania obrazów przekazanych w ciągu dnia.

W tym artykule opisano przykładowe zadania, które można utworzyć przy użyciu usługi Scheduler i [interfejsu API REST w usłudze Azure Scheduler](/rest/api/scheduler), a także zawiera definicję JavaScript Object Notation (JSON) dla każdego harmonogramu. 

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

W tych przykładach przedstawiono zakres scenariuszy obsługiwanych przez usługę Azure Scheduler oraz sposób tworzenia harmonogramów dla różnych wzorców zachowania, na przykład:

* Uruchamiaj raz w określonym dniu i o określonej godzinie.
* Uruchamiaj i Powtarzaj określoną liczbę razy.
* Uruchamiaj natychmiast i Powtarzaj.
* Uruchamiaj i Powtarzaj co *n* minut, godzin, dni, tygodnie lub miesiące, rozpoczynając od określonego czasu.
* Uruchamiaj i Powtarzaj co tydzień lub co miesiąc, ale tylko w określonych dniach tygodnia lub w określonych dniach miesiąca.
* Uruchom i cyklicznie więcej niż jeden raz przez określony okres. Na przykład każdy miesiąc w ostatnim piątek i poniedziałek lub codziennie o godzinie 5:15 i 5:15 PM.

W tym artykule opisano te scenariusze bardziej szczegółowo.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Tworzenie harmonogramu za pomocą interfejsu API REST

Aby utworzyć harmonogram podstawowy przy użyciu [interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler), wykonaj następujące kroki:

1. Zarejestruj swoją subskrypcję platformy Azure za pomocą dostawcy zasobów przy użyciu [interfejsu API REST operacji rejestrowania Menedżer zasobów](/rest/api/resources/providers). Nazwa dostawcy usługi Azure Scheduler to **Microsoft. Scheduler**. 

1. Utwórz kolekcję zadań przy użyciu [operacji tworzenia lub aktualizacji kolekcji zadań](/rest/api/scheduler/jobcollections) w interfejsie API REST usługi Scheduler. 

1. Utwórz zadanie przy użyciu [operacji tworzenia lub aktualizacji zadań](/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Elementy schematu zadania

Ta tabela zawiera ogólne omówienie głównych elementów JSON, których można użyć podczas konfigurowania cykli i harmonogramów zadań. 

| Element | Wymagane | Opis | 
|---------|----------|-------------|
| **Rozpoczęcia** | Nie | Wartość ciągu DateTime w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) , która określa, kiedy zadanie zaczyna się najpierw w harmonogramie podstawowym. <p>W przypadku złożonych harmonogramów zadanie nie jest uruchamiane wcześniej niż **godzina rozpoczęcia**. | 
| **wystąpieniu** | Nie | Reguły cyklu dla momentu uruchomienia zadania. Obiekt **cykl** obsługuje te elementy: **częstotliwość**, **Interwał**, **harmonogram**, **Liczba** i **Endtime**. <p>W przypadku użycia elementu **cykl** należy również użyć elementu **częstotliwość** , podczas gdy inne elementy **cyklu** są opcjonalne. |
| **jaką** | Tak, w przypadku użycia **cyklu** | Jednostka czasu między wystąpieniami i obsługuje te wartości: "minuta", "godzina", "dzień", "tydzień", "miesiąc" i "Year" | 
| **dat** | Nie | Dodatnia liczba całkowita, która określa liczbę jednostek czasu między wystąpieniami na podstawie **częstotliwości**. <p>Jeśli na przykład **Interwał** wynosi 10, a **częstotliwość** to "tydzień", zadanie powtarza się co 10 tygodni. <p>Poniżej przedstawiono największą liczbę interwałów dla każdej częstotliwości: <p>– 18 miesięcy <br>– 78 tyg. <br>– 548 dni <br>— W godzinach i minutach zakres wynosi 1 <= <*interwał*> <= 1000. | 
| **rozkład** | Nie | Definiuje zmiany w cyklu na podstawie określonych minut, znaków godzin, dni tygodnia i dni miesiąca. | 
| **count** | Nie | Dodatnia liczba całkowita, która określa, ile razy zadanie zostanie uruchomione przed zakończeniem. <p>Na przykład, gdy dzienne zadanie ma **ustawioną** wartość 7, a Data rozpoczęcia to poniedziałek, zadanie zakończy się w niedzielę. Jeśli data rozpoczęcia została już przeniesiona, pierwsze uruchomienie jest obliczane na podstawie czasu utworzenia. <p>Bez **Endtime** lub **Count**, zadanie działa nieskończonie. Nie można użyć jednocześnie **Count** i **Endtime** w tym samym zadaniu, ale zasada, która kończy się pierwszym jest Honora. | 
| **endTime** | Nie | Wartość ciągu daty lub godziny w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) , która określa, kiedy zadanie przestaje działać. Można ustawić wartość **Endtime** , która znajduje się w przeszłości. <p>Bez **Endtime** lub **Count**, zadanie działa nieskończonie. Nie można użyć jednocześnie **Count** i **Endtime** w tym samym zadaniu, ale zasada, która kończy się pierwszym jest Honora. |
|||| 

Na przykład w tym schemacie JSON opisano podstawowy harmonogram i cykl dla zadania: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Daty i wartości DateTime*

* Daty w zadaniach harmonogramu zawierają tylko datę i są zgodne ze [specyfikacją ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

* Daty i godziny w zadaniach harmonogramu obejmują zarówno datę, jak i godzinę, należy postępować zgodnie ze [specyfikacją ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)i przyjmuje się, że wartość jest UTC, gdy nie określono przesunięcia czasu UTC. 

Aby uzyskać więcej informacji, zobacz [pojęcia, terminologia i jednostki](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Szczegóły: startTime

W tej tabeli opisano sposób, w jaki procedura **StartTime** kontroluje sposób uruchomienia zadania:

| startTime | Brak cyklu | Cykl, bez harmonogramu | Cykl z harmonogramem |
|-----------|---------------|-------------------------|--------------------------|
| **Brak czasu rozpoczęcia** | Uruchom jednokrotnie. | Uruchom jednokrotnie. Uruchamianie późniejszych wykonań obliczonych od czasu ostatniego wykonania. | Uruchom jednokrotnie. Uruchom późniejsze wykonania na podstawie harmonogramu cyklu. | 
| **Godzina rozpoczęcia w przeszłości** | Uruchom jednokrotnie. | Oblicz pierwszy przyszły czas wykonania po czasie rozpoczęcia i uruchom w tym czasie. <p>Uruchamianie późniejszych wykonań obliczonych od czasu ostatniego wykonania. <p>Zapoznaj się z przykładem poniżej tej tabeli. | Rozpocznij zadanie *no wcześniej niż* w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia. <p>Uruchom późniejsze wykonania na podstawie harmonogramu cyklu. | 
| **Czas rozpoczęcia w przyszłości lub bieżący czas** | Uruchamiany raz o określonej godzinie rozpoczęcia. | Uruchamiany raz o określonej godzinie rozpoczęcia. <p>Uruchamianie późniejszych wykonań obliczonych od czasu ostatniego wykonania. | Rozpocznij zadanie *no wcześniej niż* w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia. <p>Uruchom późniejsze wykonania na podstawie harmonogramu cyklu. |
||||| 

Załóżmy, że ten przykład zawiera następujące warunki: godzina rozpoczęcia w przeszłości z cyklem, ale bez harmonogramu.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Bieżąca data i godzina to 08 kwietnia, 2015 o 1:00 PM.

* Data i godzina rozpoczęcia to 07 kwietnia, 2015 o 2:00 PM, czyli przed bieżącą datą i godziną.

* Cykl jest co dwa dni.

1. W tych warunkach pierwsze wykonanie jest 9 kwietnia 2015 o 2:00 PM. 

   Usługa Scheduler oblicza wystąpienia wykonania na podstawie czasu rozpoczęcia, odrzuca wszystkie wystąpienia w przeszłości i używa następnego wystąpienia w przyszłości. 
   W takim przypadku czas **rozpoczęcia** wynosi 07 kwietnia 2015 o 2:00 PM, więc następne wystąpienie to dwa dni od tego czasu, co to jest 09 kwietnia 2015 o 2:00 PM.

   Pierwsze wykonanie jest takie samo, niezależnie od tego, czy **StartTime** jest 2015-04-05 14:00 czy 2015-04-01 14:00. Po pierwszym wykonaniu kolejne wykonania są obliczane na podstawie harmonogramu. 
   
1. Wykonanie przebiega w następującej kolejności: 
   
   1. 2015-04-11 o 2:00 PM
   1. 2015-04-13 o 2:00 PM 
   1. 2015-04-15 o 2:00 PM
   1. I tak dalej...

1. Na koniec, gdy zadanie ma harmonogram, ale nie podano określonych godzin i minut, te wartości domyślnie są podane w godzinach i minutach w pierwszym wykonaniu.

<a name="schedule"></a>

## <a name="details-schedule"></a>Szczegóły: harmonogram

Możesz użyć **harmonogramu** , aby *ograniczyć* liczbę wykonań zadań. Na przykład jeśli zadanie z **częstotliwością** "Month" ma harmonogram, który jest uruchamiany tylko w dniu 31, zadanie jest uruchamiane tylko w miesiącach, które mają 31 dni.

Można również użyć **harmonogramu** , aby *zwiększyć* liczbę wykonań zadań. Na przykład jeśli zadanie z **częstotliwością** "Month" ma harmonogram, który jest uruchamiany w ciągu miesiąca 1 i 2, to zadanie jest uruchamiane w ciągu pierwszego i drugiego dnia miesiąca, a nie tylko raz w miesiącu.

W przypadku określenia więcej niż jednego elementu harmonogramu kolejność obliczeń jest od największych do najmniejszych: numer tygodnia, dzień miesiąca, dzień tygodnia, godzina i minuta.

W poniższej tabeli opisano szczegółowo elementy harmonogramu:

| Nazwa JSON | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |
| **minut** |Minuty godziny, w których uruchamiane jest zadanie. |Tablica liczb całkowitych. |
| **liczb** |Godziny dnia, w którym działa zadanie. |Tablica liczb całkowitych. |
| **weekDays** |Dni tygodnia, w których zostanie uruchomione zadanie. Można określić tylko z częstotliwością tygodniową. |Tablica dowolnych z następujących wartości (maksymalny rozmiar tablicy to 7):<br />— "Poniedziałek"<br />-"Wtorek"<br />-"Środa"<br />-"Czwartek"<br />-"Piątek"<br />-"Sobota"<br />-"Niedziela"<br /><br />Bez uwzględniania wielkości liter. |
| **monthlyOccurrences** |Określa dni miesiąca, w których uruchamiane jest zadanie. Można określić tylko z częstotliwością miesięczną. |Tablica obiektów **monthlyOccurrences** :<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dzień** to dzień tygodnia, w którym jest uruchamiane zadanie. Na przykład, *{Niedziela}* jest każdą niedzielę miesiąca. Wymagane.<br /><br />**wystąpienie** jest wystąpieniem dnia w miesiącu. Na przykład  *{niedziela,-1}* to Ostatnia niedziela miesiąca. Opcjonalny. |
| **monthDays** |Dzień miesiąca, w którym jest uruchamiane zadanie. Można określić tylko z częstotliwością miesięczną. |Tablica następujących wartości:<br />- Dowolna wartość <= -1 i >= -31<br />- Dowolna wartość >= 1 i <= 31|

## <a name="examples-recurrence-schedules"></a>Przykłady: harmonogramy cykli

W poniższych przykładach przedstawiono różne harmonogramy cyklu. Przykłady koncentrują się na obiekcie Schedule i jego podelementach.

W tych harmonogramach przyjęto założenie, że **Interwał** jest ustawiony na 1\. W przykładach założono również poprawne wartości **częstotliwości** dla wartości w **harmonogramie**. Na przykład nie można użyć **częstotliwości** "Day" i mieć **monthDays** modyfikacje w **harmonogramie**. Firma Microsoft opisuje te ograniczenia wcześniej w tym artykule.

| Przykład | Opis |
|:--- |:--- |
| `{"hours":[5]}` |Uruchamiany codziennie o 5 godzin.<br /><br />Usługa Scheduler dopasowuje każdą wartość w ciągu "godzin" przy użyciu każdej wartości w ciągu "minuty", jeden według jednego, aby utworzyć listę wszystkich godzin uruchomienia zadania. |
| `{"minutes":[15], "hours":[5]}` |Uruchamiany o godz. 5:15 każdego dnia. |
| `{"minutes":[15], "hours":[5,17]}` |Uruchamiany o godz. 5:15 i 17:15 każdego dnia. |
| `{"minutes":[15,45], "hours":[5,17]}` |Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 każdego dnia. |
| `{"minutes":[0,15,30,45]}` |Uruchamiany co 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Uruchamiany co godzinę.<br /><br />To zadanie jest uruchamiane co godzinę. Minuta jest kontrolowana przez wartość parametru **StartTime**, jeśli została określona. Jeśli wartość **StartTime** nie zostanie określona, minuta jest kontrolowana przez czas utworzenia. Jeśli na przykład czas rozpoczęcia lub czas utworzenia (zależnie od zastosowania) to 12:25 PM, zadanie jest uruchamiane o godzinie 00:25, 01:25, 02:25,..., 23:25.<br /><br />Harmonogram jest taki sam jak zadanie z **częstotliwością** "godzina", **interwałem** 1 i bez wartości **harmonogramu** . Różnica polega na tym, że można użyć tego harmonogramu z różnymi wartościami **częstotliwości** i **interwałów** do tworzenia innych zadań. Na przykład jeśli **częstotliwość** to "miesiąc", harmonogram jest uruchamiany tylko raz w miesiącu, a nie codziennie (Jeśli **częstotliwość** to "dzień"). |
| `{minutes:[0]}` |Uruchamiany co godzinę o pełnej godzinie.<br /><br />To zadanie jest uruchamiane co godzinę, ale na godzinę (12 AM, 1 am, 2 itd.). Ten harmonogram jest taki sam jak zadanie o **częstotliwości** "godzina", wartości **StartTime** równej zero minut i bez **harmonogramu**, jeśli częstotliwość to "dzień". Jeśli jednak **częstotliwość** wynosi "tydzień" lub "miesiąc", harmonogram jest wykonywany odpowiednio tylko raz w tygodniu lub raz w miesiącu. |
| `{"minutes":[15]}` |Uruchamiany o 15 minut po godzinie co godzinę.<br /><br />Uruchamiany co godzinę, rozpoczynając od 00:15 AM, 1:15 AM, 2:15 AM i tak dalej. Skończy się o 11:15 PM. |
| `{"hours":[17], "weekDays":["saturday"]}` |Uruchamiany o godzinie 5 PM w sobotę co tydzień. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchamiany o godzinie 5 PM w poniedziałek, środę i piątek co tydzień. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchamiany o godz. 17:15 i 17:45 w każdy poniedziałek, środę i piątek. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchamiany o godzinie 5 AM i 5 PM w poniedziałek, środę i piątek co tydzień. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchamiany o godzinie 5:15 AM, 5:45 AM, 5:15 PM i 5:45 PM w poniedziałek, środę i piątek, co tydzień. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Uruchamiany co 15 minut w dni robocze. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Uruchamiany co 15 minut w dniach tygodnia, od 9 AM i 4:45 PM. |
| `{"weekDays":["sunday"]}` |Uruchamiany w niedziele w czasie rozpoczęcia. |
| `{"weekDays":["tuesday", "thursday"]}` |Uruchamiany we wtorki i czwartki w czasie rozpoczęcia. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Uruchamiany o godzinie 6 – 28 dzień każdego miesiąca (przy założeniu, że **częstotliwość** to "miesiąc"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Uruchamiany o godzinie 6 w ostatnim dniu miesiąca.<br /><br />Jeśli chcesz uruchomić zadanie w ostatnim dniu miesiąca, użyj wartości-1 zamiast 28, 29, 30 lub 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Uruchamiany o godzinie 6 AM pierwszego i ostatniego dnia każdego miesiąca. |
| `{monthDays":[1,-1]}` |Uruchamiany pierwszy i ostatni dzień każdego miesiąca o godzinie rozpoczęcia. |
| `{monthDays":[1,14]}` |Uruchamiany pierwszego i czternastego dnia każdego miesiąca w czasie rozpoczęcia. |
| `{monthDays":[2]}` |Uruchamiany w drugim dniu miesiąca o godzinie rozpoczęcia. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Uruchamiany w pierwszym piątek każdego miesiąca o godzinie 5 AM. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Uruchamiany w pierwszym piątek każdego miesiąca w czasie rozpoczęcia. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Uruchamiany w trzeci piątek od końca miesiąca, co miesiąc, w czasie rozpoczęcia. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Uruchamiany pierwszy i ostatni piątek każdego miesiąca w czasie rozpoczęcia. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Uruchamiany w piątym piątek każdego miesiąca w czasie rozpoczęcia.<br /><br />Jeśli w miesiącu nie ma piątego piątku, zadanie nie zostanie uruchomione. Można rozważyć użycie-1 zamiast 5 dla wystąpienia, jeśli zadanie ma być uruchamiane w ostatnim miesiącu miesiąca. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Uruchamiany co 15 minut w ostatni piątek miesiąca. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 w trzecią środę każdego miesiąca. |

## <a name="next-steps"></a>Następne kroki

* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler)
* [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)
* [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)