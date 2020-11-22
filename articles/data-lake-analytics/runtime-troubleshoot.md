---
title: Jak rozwiązywać problemy z błędami środowiska uruchomieniowego U-SQL Azure Data Lake Analytics
description: Dowiedz się, jak rozwiązywać błędy środowiska uruchomieniowego U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 41b7c80c85331f288343351749e6b2e5292b30c6
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241611"
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

* Odwoływanie się do Newtonsoft.Jsw wersji pliku 12.0.3 lub nowszej w skrypcie USQL spowoduje wystąpienie następującego błędu kompilacji:

    *"Niestety; zadania uruchomione na koncie Data Lake Analytics prawdopodobnie będą działać wolniej lub niepowodzenie. Nieoczekiwany problem uniemożliwia automatyczne przywrócenie tej funkcji na koncie Azure Data Lake Analytics. Azure Data Lake inżynierów skontaktowali się z Tobą w celu zbadania ".*  

    Gdzie stos wywołań będzie zawierać:  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **Rozwiązanie**: Użyj Newtonsoft.Jsw pliku 12.0.2 lub niższym.


## <a name="see-also"></a>Zobacz także

- [Przegląd Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Zarządzanie Azure Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-manage-use-portal.md)
- [Monitoruj zadania w Azure Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
