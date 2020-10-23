---
title: Zbierz dzienniki niestandardowe z agentem Log Analytics w programie Azure Monitor
description: Azure Monitor może zbierać zdarzenia z plików tekstowych zarówno na komputerach z systemem Windows, jak i Linux.  W tym artykule opisano sposób definiowania nowego dziennika niestandardowego i szczegółów rekordów tworzonych w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 406371325ddf8b555ede481582e19635b85abe49
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461570"
---
# <a name="collect-custom-logs-with-log-analytics-agent-in-azure-monitor"></a>Zbierz dzienniki niestandardowe z agentem Log Analytics w programie Azure Monitor

Źródło danych dzienników niestandardowych dla agenta Log Analytics w programie Azure Monitor umożliwia zbieranie zdarzeń z plików tekstowych na komputerach z systemem Windows i Linux. Wiele aplikacji rejestruje informacje w plikach tekstowych zamiast standardowych usługach rejestrowania, takich jak dziennik zdarzeń systemu Windows lub system plików dziennika. Po zebraniu można przeanalizować dane do poszczególnych pól w zapytaniach lub wyodrębnić dane podczas zbierania do poszczególnych pól.

> [!IMPORTANT]
> W tym artykule opisano zbieranie niestandardowych dzienników z [agentem log Analytics](log-analytics-agent.md) , który jest jednym z agentów używanych przez Azure monitor. Inni Agenci zbierają różne dane i są skonfigurowani inaczej. Zobacz [Omówienie agentów Azure monitor](agents-overview.md) , aby uzyskać listę dostępnych agentów oraz dane, które mogą zbierać.

![Niestandardowa kolekcja dzienników](media/data-sources-custom-logs/overview.png)

Pliki dziennika do zebrania muszą być zgodne z następującymi kryteriami.

- Dziennik musi mieć pojedynczy wpis na wiersz lub użyć sygnatury czasowej pasującej do jednego z następujących formatów na początku każdego wpisu.

    RRRR-MM-DD GG: MM: SS<br>M/D/RRRR HH: MM: SS AM/PM<br>PN DD, rrrr HH: MM: SS<br />yyMMdd HH: mm: SS<br />ddMMyy HH: mm: SS<br />MMM d hh: mm: SS<br />DD/MMM/rrrr: gg: mm: SS ZZZ<br />RRRR-MM-DDTgg: mm: ssK

- Plik dziennika nie może zezwalać na cykliczne rejestrowanie ani obracanie dzienników, w którym plik jest zastępowany nowymi wpisami.
- Plik dziennika musi używać kodowania ASCII lub UTF-8.  Inne formaty, takie jak UTF-16, nie są obsługiwane.

>[!NOTE]
> Jeśli w pliku dziennika znajdują się zduplikowane wpisy, Azure Monitor będą zbierać te dane. Jednak wyniki zapytania będą niespójne, gdzie wyniki filtrowania pokazują więcej zdarzeń niż liczba wyników. Ważne jest, aby sprawdzić poprawność dziennika w celu ustalenia, czy aplikacja, która tworzy ten problem, jest przyczyną tego zachowania, a jeśli jest to możliwe, przed utworzeniem definicji kolekcji dzienników niestandardowych.  
>

>[!NOTE]
> Obszar roboczy Log Analytics obsługuje następujące limity:
> 
> * Można utworzyć tylko 500 dzienników niestandardowych.
> * Tabela obsługuje tylko do 500 kolumn. 
> * Maksymalna liczba znaków w nazwie kolumny to 500. 
>

>[!IMPORTANT]
>Niestandardowa kolekcja dzienników wymaga, aby aplikacja, która piszą plik dziennika, opróżnia zawartość dziennika na dysk okresowo. Wynika to z faktu, że niestandardowe kolekcje dzienników opierają się na powiadomieniach o zmianach systemu plików dla śledzonego pliku dziennika.

## <a name="defining-a-custom-log"></a>Definiowanie dziennika niestandardowego
Aby zdefiniować niestandardowy plik dziennika, należy wykonać czynności opisane w poniższej procedurze.  Przewiń do końca tego artykułu, aby zapoznać się z przykładem dodawania dziennika niestandardowego.

### <a name="step-1-open-the-custom-log-wizard"></a>Krok 1. Otwórz Kreatora dziennika niestandardowego
Kreator dziennika niestandardowego działa w Azure Portal i umożliwia zdefiniowanie nowego dziennika niestandardowego do zbierania.

1. W Azure Portal wybierz pozycję **log Analytics obszary robocze** > obszarze roboczym > **Ustawienia zaawansowane**.
2. Kliknij pozycję **Data**  >  **dzienniki niestandardowe**danych.
3. Domyślnie wszystkie zmiany konfiguracji są automatycznie wypychane do wszystkich agentów. W przypadku agentów systemu Linux plik konfiguracji jest wysyłany do programu zbierającego dane.
4. Kliknij przycisk **Dodaj +** , aby otworzyć Kreatora dziennika niestandardowego.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Krok 2. Przekazywanie i analizowanie przykładowego dziennika
Zacznij od przekazania przykładu dziennika niestandardowego.  Kreator przeanalizuje i wyświetli wpisy w tym pliku, aby sprawdzić poprawność.  Azure Monitor użyje ogranicznika, który określisz, aby zidentyfikować każdy rekord.

**Nowy wiersz** jest domyślnym ogranicznikiem i jest używany dla plików dziennika, które mają pojedynczy wpis na wiersz.  Jeśli wiersz rozpoczyna się od daty i godziny w jednym z dostępnych formatów, można określić ogranicznik **sygnatury czasowej** , który obsługuje wpisy obejmujące więcej niż jeden wiersz.

Jeśli jest używany ogranicznik sygnatury czasowej, właściwość TimeGenerated każdego rekordu przechowywanego w Azure Monitor zostanie wypełniona datą/godziną określoną dla tego wpisu w pliku dziennika.  Jeśli jest używany nowy ogranicznik wiersza, TimeGenerated jest wypełniany datą i godziną, która Azure Monitor zebrania wpisu.

1. Kliknij przycisk **Przeglądaj** i przejdź do pliku przykładowego.  Należy zauważyć, że może to być przycisk z etykietą **Wybierz plik** w niektórych przeglądarkach.
2. Kliknij przycisk **Dalej**.
3. Kreator dziennika niestandardowego przekaże plik i wyświetli listę rekordów, które identyfikuje.
4. Zmień ogranicznik używany do identyfikowania nowego rekordu i wybierz ogranicznik, który najlepiej identyfikuje rekordy w pliku dziennika.
5. Kliknij przycisk **Dalej**.

### <a name="step-3-add-log-collection-paths"></a>Krok 3. Dodaj ścieżki zbierania dzienników
Należy zdefiniować co najmniej jedną ścieżkę w agencie, w której można zlokalizować dziennik niestandardowy.  Możesz podać konkretną ścieżkę i nazwę pliku dziennika lub określić ścieżkę z symbolem wieloznacznym dla nazwy. Obsługuje to aplikacje, które tworzą nowy plik każdego dnia lub gdy jeden plik osiągnie określony rozmiar. Istnieje również możliwość udostępnienia wielu ścieżek dla jednego pliku dziennika.

Na przykład aplikacja może utworzyć plik dziennika codziennie o dacie zawartej w log20100316.txt. Wzorzec dla takiego dziennika może być *Dziennik \* . txt* , który powinien zostać zastosowany do każdego pliku dziennika, zgodnie ze schematem nazewnictwa aplikacji.

Poniższa tabela zawiera przykłady prawidłowych wzorców do określenia różnych plików dziennika.

| Opis | Ścieżka |
|:--- |:--- |
| Wszystkie pliki w *C:\LOGS* z rozszerzeniem txt w agencie systemu Windows |C:\Logs \\ \* . txt |
| Wszystkie pliki w *C:\LOGS* o nazwie rozpoczynającej się od nazwy log i. txt w agencie systemu Windows |C:\Logs\log \* . txt |
| Wszystkie pliki w */var/log/Audit* z rozszerzeniem txt w agencie systemu Linux |/var/log/Audit/*. txt |
| Wszystkie pliki w */var/log/Audit* o nazwie rozpoczynającej się od pliku log i rozszerzenia. txt w agencie systemu Linux |/var/log/Audit/log \* . txt |

1. Wybierz pozycję Windows lub Linux, aby określić format ścieżki, który chcesz dodać.
2. Wpisz ścieżkę i kliknij **+** przycisk.
3. Powtórz ten proces dla każdej dodatkowej ścieżki.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Krok 4. Podaj nazwę i opis dziennika
Określona nazwa zostanie użyta dla typu dziennika zgodnie z powyższym opisem.  Będzie ona zawsze kończyć się _CL, aby odróżnić ją jako dziennik niestandardowy.

1. Wpisz nazwę dziennika.  Sufiks ** \_ CL** jest dostarczany automatycznie.
2. Dodaj opcjonalny **Opis**.
3. Kliknij przycisk **dalej** , aby zapisać definicję dziennika niestandardowego.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Krok 5. Sprawdź, czy dzienniki niestandardowe są zbierane
Początkowe dane z nowego dziennika niestandardowego mogą pojawić się w Azure Monitor.  Rozpocznie się zbieranie wpisów z dzienników znalezionych w określonej ścieżce od punktu, w którym został zdefiniowany dziennik niestandardowy.  Nie zachowuje on wpisów przekazanych podczas tworzenia dziennika niestandardowego, ale będzie zbierać już istniejące wpisy w plikach dziennika, które lokalizuje.

Gdy Azure Monitor rozpoczyna zbieranie danych z dziennika niestandardowego, jego rekordy będą dostępne z zapytaniem dziennika.  Użyj nazwy, która została przewidziana dla dziennika niestandardowego jako **typu** w zapytaniu.

> [!NOTE]
> Jeśli w zapytaniu brakuje właściwości RawData, może być konieczne zamknięcie i ponowne otwarcie przeglądarki.

### <a name="step-6-parse-the-custom-log-entries"></a>Krok 6. Analizowanie niestandardowych wpisów dziennika
Cały wpis dziennika zostanie zapisany w pojedynczej właściwości o nazwie **rawData**.  Najprawdopodobniej chcesz oddzielić różne fragmenty informacji w każdym wpisie do poszczególnych właściwości dla każdego rekordu. Zapoznaj się z tematem analizowanie [danych tekstowych w Azure monitor](../log-query/parse-text.md) , aby uzyskać opcje analizowania **rawData** na wiele właściwości.

## <a name="removing-a-custom-log"></a>Usuwanie dziennika niestandardowego
Użyj następującego procesu w Azure Portal, aby usunąć wcześniej zdefiniowany dziennik niestandardowy.

1. Z menu **dane** w obszarze **Ustawienia zaawansowane** dla obszaru roboczego wybierz pozycję **dzienniki niestandardowe** , aby wyświetlić listę wszystkich dzienników niestandardowych.
2. Kliknij przycisk **Usuń** obok dziennika niestandardowego do usunięcia.

## <a name="data-collection"></a>Zbieranie danych
Azure Monitor będzie zbierać nowe wpisy z każdego dziennika niestandardowego co 5 minut.  Agent będzie rejestrował swoje miejsce w każdym pliku dziennika, z którego zbiera dane.  Jeśli Agent przejdzie w tryb offline przez pewien czas, Azure Monitor będzie zbierać wpisy z miejsca, w którym został on ostatnio pozostawiony, nawet jeśli te wpisy zostały utworzone, gdy agent był w trybie offline.

Cała zawartość wpisu dziennika jest zapisywana w pojedynczej właściwości o nazwie **rawData**.  Zobacz [Analizowanie danych tekstowych w Azure monitor](../log-query/parse-text.md) , aby poznać metody analizowania każdego zaimportowanego wpisu dziennika do wielu właściwości.

## <a name="custom-log-record-properties"></a>Właściwości niestandardowego rekordu dziennika
Niestandardowe rekordy dziennika mają typ o podanej nazwie dziennika i właściwościach w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| TimeGenerated |Data i godzina zebrania rekordu przez Azure Monitor.  Jeśli w dzienniku jest wykorzystywany ogranicznik oparty na czasie, to jest to czas uzyskany od wpisu. |
| SourceSystem |Typ agenta, z którego zebrano rekord. <br> OpsManager — Agent systemu Windows, bezpośrednie połączenie lub System Center Operations Manager <br> Linux — Wszyscy agenci systemu Linux |
| RawData |Pełny tekst zebranego wpisu. Najprawdopodobniej chcesz [przeanalizować te dane do poszczególnych właściwości](../log-query/parse-text.md). |
| ManagementGroupName |Nazwa grupy zarządzania dla agentów programu System Center Operations Manager.  W przypadku innych agentów jest to AOI-\<workspace ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Przykładowe wskazówki dotyczące dodawania dziennika niestandardowego
W poniższej sekcji omówiono przykład tworzenia dziennika niestandardowego.  Zbierany dziennik przykładowy zawiera pojedynczy wpis w każdym wierszu, zaczynając od daty i godziny, a następnie pola rozdzielane przecinkami dla kodu, stanu i wiadomości.  Poniżej przedstawiono kilka przykładowych wpisów.

```output
2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2019-08-27 01:38:22 302,Error,Application could not connect to database
2019-08-27 01:31:34 303,Error,Application lost connection to database
```

### <a name="upload-and-parse-a-sample-log"></a>Przekazywanie i analizowanie przykładowego dziennika
Udostępniamy jeden z plików dziennika i widzisz zdarzenia, które będą zbierane.  W tym przypadku nowy wiersz jest wystarczającym ogranicznikiem.  Jeśli pojedynczy wpis w dzienniku może obejmować wiele wierszy, należy użyć ogranicznika sygnatury czasowej.

![Przekazywanie i analizowanie przykładowego dziennika](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Dodaj ścieżki zbierania dzienników
Pliki dziennika znajdują się w *C:\MyApp\Logs*.  Nowy plik zostanie utworzony każdego dnia o nazwie zawierającej datę w wzorcu *appYYYYMMDD. log*.  Wystarczającym wzorcem dla tego dziennika będzie *C:\MyApp\Logs \\ \* . log*.

![Ścieżka do kolekcji dzienników](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Podaj nazwę i opis dziennika
Używamy nazwy *MyApp_CL* i wpisz **Opis**.

![Nazwa dziennika](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Sprawdź, czy dzienniki niestandardowe są zbierane
Używamy prostego zapytania o *MyApp_CL* , aby zwrócić wszystkie rekordy z zebranych dzienników.

![Zapytanie dziennika bez pól niestandardowych](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternatywy dla dzienników niestandardowych
Chociaż dzienniki niestandardowe są przydatne, jeśli dane są zgodne z kryteriami wymienionymi powyżej, istnieją przypadki takie jak następujące, w których jest potrzebna inna strategia:

- Dane nie mieszczą się w wymaganej strukturze, takiej jak sygnatura czasowa w innym formacie.
- Plik dziennika nie jest zgodny z wymaganiami, takimi jak kodowanie plików lub nieobsługiwana struktura folderów.
- Dane wymagają przetwarzania wstępnego lub filtrowania przed kolekcją. 

W przypadkach, gdy dane nie mogą być zbierane z dziennikami niestandardowymi, należy wziąć pod uwagę następujące alternatywne strategie:

- Użyj skryptu niestandardowego lub innej metody, aby zapisać dane do [zdarzeń systemu Windows](data-sources-windows-events.md) lub [dziennika](data-sources-syslog.md) systemowego, które są zbierane przez Azure monitor. 
- Wyślij dane bezpośrednio do Azure Monitor przy użyciu [interfejsu API modułu zbierającego dane http](data-collector-api.md). 

## <a name="next-steps"></a>Następne kroki
* Zobacz [Analizowanie danych tekstowych w Azure monitor](../log-query/parse-text.md) , aby poznać metody analizowania każdego zaimportowanego wpisu dziennika do wielu właściwości.
* Informacje na temat [zapytań dzienników](../log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.
