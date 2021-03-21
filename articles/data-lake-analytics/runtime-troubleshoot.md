---
title: Jak rozwiązywać problemy z błędami środowiska uruchomieniowego U-SQL Azure Data Lake Analytics
description: Dowiedz się, jak rozwiązywać błędy środowiska uruchomieniowego U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 1236b83b410057e55015391772e37bd461a448d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030617"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Dowiedz się, jak rozwiązywać błędy środowiska uruchomieniowego U-SQL ze względu na zmiany w czasie wykonywania

Azure Data Lake środowisko uruchomieniowe U-SQL, w tym kompilator, optymalizator i Menedżer zadań, polega na tym, że przetwarza kod U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Wybieranie wersji środowiska uruchomieniowego U-SQL

Gdy przesyłane są zadania U-SQL z programu Visual Studio, zestawu ADL SDK lub portalu Azure Data Lake Analytics, zadanie będzie korzystać z obecnie dostępnego domyślnego środowiska uruchomieniowego. Nowe wersje środowiska uruchomieniowego U-SQL są regularnie publikowane i obejmują zarówno drobne aktualizacje, jak i poprawki zabezpieczeń.

Możesz również wybrać niestandardową wersję środowiska uruchomieniowego; ponieważ chcesz wypróbować nową aktualizację, musisz pozostać w starszej wersji środowiska uruchomieniowego lub została dostarczona z poprawką dla zgłoszonego problemu, gdzie nie można zaczekać na tę nową aktualizację.

> [!CAUTION]
> Wybranie środowiska uruchomieniowego, które różni się od domyślnego, ma możliwość podziału zadań U-SQL. Użyj tych innych wersji tylko do celów testowych.

W rzadkich przypadkach pomoc techniczna firmy Microsoft mogą przypiąć inną wersję środowiska uruchomieniowego jako domyślną dla Twojego konta. Upewnij się, że ten numer PIN został przywrócony najszybciej, jak to możliwe. Jeśli użytkownik pozostanie przypięty do tej wersji, wygaśnie w późniejszym terminie.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitorowanie wersji środowiska uruchomieniowego U-SQL

Możesz zobaczyć historię wersji środowiska uruchomieniowego, które przeszły zadania, które były używane w historii zadań Twojego konta za pośrednictwem przeglądarki zadań programu Visual Studio lub historii zadania Azure Portal.

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Wybierz pozycję **Wyświetl wszystkie zadania**. Zostanie wyświetlona lista wszystkich aktywnych i ostatnio zakończonych zadań w ramach konta.
3. Opcjonalnie kliknij przycisk **Filtruj** , aby pomóc znaleźć zadania według **zakresu czasu**, **nazwy zadania** i wartości **autora** .
4. Można zobaczyć środowisko uruchomieniowe używane w zakończonych zadaniach.

![Wyświetlanie wersji środowiska uruchomieniowego przeszłego zadania](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Dostępne wersje środowiska uruchomieniowego zmieniają się w miarę upływu czasu. Domyślne środowisko uruchomieniowe zawsze jest nazywane "domyślnym" i utrzymujemy, że co najmniej poprzednie środowisko uruchomieniowe jest dostępne przez jakiś czas, oraz że specjalne środowiska uruchomieniowe są dostępne z różnych powodów. Jawnie nazwane środowiska uruchomieniowe zwykle są zgodne z następującym formatem (kursywa są używane dla części zmiennych i [] oznacza części opcjonalne):

release_YYYYMMDD_adl_buildno [_modifier]

Na przykład release_20190318_adl_3394512_2 oznacza drugą wersję kompilacji 3394512 wydania w czasie wykonywania z marca 18 2019 i release_20190318_adl_3394512_private oznacza prywatną kompilację tej samej wersji. Uwaga: Data jest zależna od tego, kiedy ostatnie zaewidencjonowanie zostało wykonane dla tej wersji i nie musi być oficjalną datą wydania.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Rozwiązywanie problemów z wersją środowiska uruchomieniowego U-SQL

Istnieją dwie możliwe problemy z wersją środowiska uruchomieniowego, które mogą wystąpić:

1. Skrypt lub jakiś kod użytkownika zmienia zachowanie z jednej wersji na następną. Takie istotne zmiany są zwykle przekazywane wcześniej z publikacją informacji o wersji. W przypadku wystąpienia takiej zmiany należy skontaktować się z pomoc techniczna firmy Microsoft, aby zgłosić takie zachowanie przerwania (jeśli nie zostało to jeszcze udokumentowane) i przesłać zadania do starszej wersji środowiska uruchomieniowego.

2. Użyto niedomyślnego środowiska uruchomieniowego jawnie lub niejawnie, gdy został on przypięty do Twojego konta, i że środowisko uruchomieniowe zostało usunięte po pewnym czasie. Jeśli napotkasz brakujące środowiska uruchomieniowe, Uaktualnij skrypty, aby były uruchamiane przy użyciu bieżącego domyślnego środowiska uruchomieniowego. Jeśli potrzebujesz dodatkowego czasu, skontaktuj się z firmą pomoc techniczna firmy Microsoft

## <a name="known-issues"></a>Znane problemy

1. Odwoływanie się do Newtonsoft.Jsw wersji pliku 12.0.3 lub nowszej w skrypcie USQL spowoduje wystąpienie następującego błędu kompilacji:

    *"Niestety; zadania uruchomione na koncie Data Lake Analytics prawdopodobnie będą działać wolniej lub niepowodzenie. Nieoczekiwany problem uniemożliwia automatyczne przywrócenie tej funkcji na koncie Azure Data Lake Analytics. Azure Data Lake inżynierów skontaktowali się z Tobą w celu zbadania ".*  

    Gdzie stos wywołań będzie zawierać:  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **Rozwiązanie**: Użyj Newtonsoft.Jsw pliku 12.0.2 lub niższym.
2. Klienci mogą zobaczyć pliki tymczasowe i foldery w ich sklepie. Są one tworzone w ramach normalnego wykonywania zadania, ale są zwykle usuwane przed ich wyświetleniem przez klientów. W pewnych okolicznościach, które są rzadkie i losowe, mogą pozostawać widoczne przez pewien czas. Są one ostatecznie usuwane i nigdy nie są zliczane jako część magazynu użytkowników ani nie generują żadnych opłat. W zależności od logiki zadań klientów mogą powodować problemy. Na przykład, jeśli zadanie wylicza wszystkie pliki w folderze, a następnie porównuje listy plików, może się nie powieść z powodu braku nieoczekiwanych plików tymczasowych. Podobnie, jeśli zadanie podrzędne wylicza wszystkie pliki z danego folderu do dalszej obróbki, może także wyliczyć pliki tymczasowe.  

    **Rozwiązanie**: poprawka jest identyfikowana w środowisku uruchomieniowym, w którym pliki tymczasowe będą przechowywane w folderze tymczasowym na poziomie konta niż bieżący folder wyjściowy. Pliki tymczasowe będą zapisywane w nowym folderze tymczasowym i zostaną usunięte po zakończeniu wykonywania zadania.  
    Ponieważ ta poprawka obsługuje dane klienta, niezwykle ważne jest, aby ta poprawka była dobrze sprawdzona w wersji MSFT przed jej wydaniem. Oczekuje się, że ta poprawka jest dostępna jako środowisko uruchomieniowe beta w połowie roku 2021 i jako domyślne środowisko uruchomieniowe w drugiej połowie roku 2021. 


## <a name="see-also"></a>Zobacz też

- [Przegląd Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Zarządzanie Azure Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-manage-use-portal.md)
- [Monitoruj zadania w Azure Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
