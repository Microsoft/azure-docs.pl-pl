---
title: Informacje o metrykach dla chmury wiosennej platformy Azure
description: Dowiedz się, jak przejrzeć metryki w chmurze Azure wiosennej
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 8c375c3dc9f4c5fdb20270797eb55b3a20a6e214
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698284"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Informacje o metrykach dla chmury wiosennej platformy Azure

Eksplorator metryk platformy Azure to składnik Microsoft Azure Portal, który umożliwia Wykreślanie wykresów, wizualnie skorelowanych trendów oraz badanie skoków i wartości DIP w metrykach. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobów. 

W chmurze Azure wiosną istnieją dwa punkty widzenia dla metryk.
* Wykresy na każdej stronie przeglądu aplikacji
* Strona typowe metryki

 ![Wykresy metryk](media/metrics/metrics-1.png)

Wykresy w **przeglądzie** aplikacji zapewniają szybkie sprawdzanie stanu dla każdej aplikacji. Strona typowe **metryki** zawiera wszystkie metryki dostępne dla odwołania. Możesz utworzyć własne wykresy na stronie typowe metryki i przypiąć je na pulpicie nawigacyjnym.

## <a name="application-overview-page"></a>Strona przeglądu aplikacji
Wybierz aplikację w **aplikacjach** , aby znaleźć wykresy na stronie Przegląd.  

 ![Zarządzanie metrykami aplikacji](media/metrics/metrics-2.png)

Na stronie **przeglądu aplikacji** każdej aplikacji jest prezentowany wykres metryk, który umożliwia wykonywanie szybkiego sprawdzania stanu aplikacji.  

 ![Przegląd metryk aplikacji](media/metrics/metrics-3.png)

Chmura sprężynowa Azure udostępnia te pięć wykresów z metrykami, które są aktualizowane co minutę:

* **Błędy serwera http**: liczba błędów dla żądań HTTP do aplikacji
* **Dane w**: bajty odebrane przez aplikację
* **Dane wychodzące**: Bajty wysłane przez aplikację
* **Żądania**: żądania odebrane przez aplikację
* **Średni czas odpowiedzi**: Średni czas odpowiedzi z aplikacji

Dla wykresu można wybrać zakres czasu z przedziału od 1 do siedmiu dni.

## <a name="common-metrics-page"></a>Strona typowe metryki

**Metryki** w okienku nawigacji po lewej stronie łączą się ze wspólną metryką.

Najpierw wybierz metryki do wyświetlenia:

![Wybierz widok metryki](media/metrics/metrics-4.png)

Szczegóły wszystkich opcji metryk można znaleźć w [sekcji](#user-metrics-options) poniżej.

Następnie wybierz typ agregacji dla każdej metryki:

![Agregacja metryk](media/metrics/metrics-5.png)

Typ agregacji wskazuje sposób agregowania punktów metryk na wykresie według czasu. Istnieje jeden nieprzetworzony punkt metryki co minutę, a typ wstępnej agregacji w ramach minuty jest wstępnie zdefiniowany przez typ metryki.
* Sum: Sumuj wszystkie wartości jako docelowe dane wyjściowe.
* Średnia: Użyj średniej wartości w okresie jako docelowy wynik.
* Max/min: Użyj wartości max/min w okresie jako docelowego wyjścia.

Zakres czasu można również dostosować w ciągu ostatnich 30 minut do ostatnich 30 dni lub do niestandardowego zakresu czasu.

![Modyfikacja metryki](media/metrics/metrics-6.png)

Widok domyślny obejmuje wszystkie metryki application's usługi chmurowej platformy Azure ze sobą. Metryki jednej aplikacji lub wystąpienia można filtrować na ekranie.  Kliknij przycisk **Dodaj filtr**, ustaw właściwość na wartość **aplikacja** i wybierz aplikację docelową, którą chcesz monitorować, w polu tekstowym **wartości** . 

Można użyć dwóch rodzajów filtrów (właściwości):
* Aplikacja: Filtruj według nazwy aplikacji
* Wystąpienie: Filtruj według wystąpienia aplikacji

![Filtry metryk](media/metrics/metrics-7.png)

Można również użyć opcji **Zastosuj dzielenie** , która spowoduje narysowanie wielu wierszy dla jednej aplikacji:

![Podział metryk](media/metrics/metrics-8.png)

>[!TIP]
> Możesz tworzyć własne wykresy na stronie metryk i przypinać je do **pulpitu nawigacyjnego**. Zacznij od zmiany nazwy wykresu.  Następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego w prawym górnym rogu**. Teraz możesz sprawdzić swoją aplikację na **pulpicie nawigacyjnym** portalu.

## <a name="user-metrics-options"></a>Opcje metryk użytkownika

W poniższych tabelach przedstawiono dostępne metryki i szczegóły.

### <a name="error"></a>Błąd
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| Tomcat. Global. Error | Tomcat. Global. Error | Liczba | Liczba błędów, które wystąpiły w przetwarzanych żądaniach |

### <a name="performance"></a>Wydajność
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| System. CPU. Usage | System. CPU. Usage | Procent | Ostatnie użycie procesora CPU dla całego systemu (przestarzałe i nie Sugeruj ich przy użyciu). Ta wartość jest podwójnym interwałem [0.0, 1.0]. Wartość 0,0 oznacza, że wszystkie procesory były bezczynne w trakcie ostatniego zaobserwowanego okresu, a wartość 1,0 oznacza, że wszystkie procesory aktywnie działały 100% czasu w trakcie ostatniego zaobserwowanego okresu.|
>| Process. CPU. Usage | Procent użycia procesora aplikacji | Procent | Ostatnie użycie procesora CPU przez proces wirtualna maszyna Java (przestarzałe i nie Sugeruj ich przy użyciu). Ta wartość jest podwójnym interwałem [0.0, 1.0]. Wartość 0,0 oznacza, że żaden z procesorów CPU nie był uruchomiony z procesu JVM w trakcie ostatniego zaobserwowanego okresu, a wartość 1,0 oznacza, że wszystkie procesory aktywnie działały wątki z JVM 100% czasu podczas ostatniego zaobserwowanego okresu. Wątki z JVM obejmują wątki aplikacji oraz wewnętrzne wątki JVM.|
>| AppCpuUsage | Użycie procesora CPU przez aplikację | Procent | Ostatnie użycie procesora CPU przez proces JVM dla procesora CPU przydzielony do tej aplikacji, wartość typu Double z przedziału od [0.0, 1.0]. Wartość 0,0 oznacza, że żaden z procesorów CPU nie był uruchomiony z procesu JVM w trakcie ostatniego zaobserwowanego okresu, a wartość 1,0 oznacza, że wszystkie procesory aktywnie działały wątki z JVM 100% czasu podczas ostatniego zaobserwowanego okresu. Wątki z JVM obejmują wątki aplikacji oraz wewnętrzne wątki JVM.|
>| JVM. Memory. Committed | JVM. Memory. Committed | Bajty | Reprezentuje ilość pamięci, która ma być dostępna do użycia przez JVM. JVM może zwolnić pamięć do systemu, a zatwierdzenie może być mniejsze niż init. wartość zatwierdzone będzie zawsze większa lub równa używanej. |
>| JVM. Memory. użyty | JVM. Memory. użyty | Bajty | Reprezentuje ilość pamięci aktualnie używaną w bajtach. |
>| JVM. Memory. Max | JVM. Memory. Max | Bajty | Reprezentuje maksymalną ilość pamięci, która może być używana do zarządzania pamięcią. Ilość użytych i przekazanych pamięci będzie zawsze mniejsza lub równa Max, jeśli zdefiniowano wartość Max. Alokacja pamięci może zakończyć się niepowodzeniem, jeśli próbuje zwiększyć użytą pamięć, taką jak > zatwierdzone, nawet jeśli użyto <= Max (na przykład gdy w systemie brakuje pamięci wirtualnej). |
>| JVM. GC. max. Data. size | JVM. GC. max. Data. size | Bajty | Szczytowe użycie pamięci przez starszą pulę pamięci generacji od momentu uruchomienia maszyny wirtualnej Java. |
>| JVM. GC. Live. Data. size | JVM. GC. Live. Data. size | Bajty | Rozmiar starej puli pamięci generacji po pełnej operacji GC. |
>| JVM. GC. Memory. awansowana | JVM. GC. Memory. awansowana | Bajty | Liczba pozytywnych wzrostów rozmiaru starej puli pamięci generacji przed GC do po GC. |
>| JVM. GC. Memory. przydzielono | JVM. GC. Memory. przydzielono | Bajty | Zwiększa się w celu zwiększenia rozmiaru puli pamięci młodej generacji po jednej operacji GC do wcześniejszej. |
>| JVM. GC. Pause. Total. Count | JVM. GC. Pause (łącznie-Count) | Liczba | Łączna liczba GC po rozpoczęciu tego JMV, w tym w przypadku młodych i starych wykazów GC. |
>| JVM. GC. Pause. Total. Time | JVM. GC. Pause (całkowity czas) | ) | Łączny czas, zużyty przez GC po rozpoczęciu tego JMV, w tym dla młodych i starych WYKAZów. |

### <a name="performance-net"></a>Wydajność (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|------|-----------------------------|------|---------|
>| Użycie procesora       | Użycie procesora CPU      | Procent      | Procent użycia procesora CPU w stosunku do wszystkich zasobów procesora CPU systemu [0-100]. |
>| Zestaw roboczy     | zestaw roboczy    | Megabajtach    | Ilość zestawu roboczego używanego przez proces. |
>| Rozmiar sterty GC    | GC-sterta-rozmiar   | Megabajtach    | Łączny rozmiar sterty zgłoszony przez moduł wyrzucania elementów bezużytecznych. |
>| Liczba generacji GC 0  | Gen-0-GC-Count | Liczba        | Liczba wyrzucania elementów bezużytecznych generacji 0 na sekundę. |
>| Liczba generacji GC 1  | Gen-1-GC-Count | Liczba        | Liczba wyrzucania elementów bezużytecznych generacji 1 na sekundę. |
>| Liczba generacji GC 2  | Gen-2-GC-Count | Liczba        | Liczba wyrzucania elementów bezużytecznych generacji 2 na sekundę. |
>| Czas w GC      | czas oczekiwania — GC      | Procent      | Procent czasu w wyrzucaniu elementów bezużytecznych od czasu ostatniego wyrzucania elementów bezużytecznych. |
>| Rozmiar sterty generacji 0 | rozmiar generacji (0)     | Bajty        | Rozmiar sterty generacji 0. |
>| Rozmiar sterty generacji 1 | Gen-1-size     | Bajty        | Rozmiar sterty generacji 1. |
>| Rozmiar sterty generacji 2 | Gen-2 — rozmiar     | Bajty        | Rozmiar sterty generacji 2. |
>| Rozmiar sterty LOH   | LOH — rozmiar       | Bajty        | Rozmiar sterty sterty dużego obiektu. |
>| Szybkość alokacji | Alokacja — stawka     | Bajty        | Liczba bajtów przydzielono na sekundę. |
>| Liczba zestawów  | liczba zestawów | Liczba        | Liczba załadowanych zestawów. |
>| Liczba wyjątków | Liczba wyjątków | Liczba       | Liczba wyjątków na sekundę. |
>| Liczba wątków puli wątków      | wątków — liczba wątków              | Liczba | Liczba wątków puli wątków. |
>| Liczba rywalizacji o blokadę monitora | monitorowanie-blokowanie-rywalizacja-liczba        | Liczba | Liczba przypadków na sekundę wystąpiła rywalizacja podczas próby przeprowadzenia blokady monitora. |
>| Długość kolejki puli wątków      | puli wątków — długość kolejki              | Liczba | Długość kolejki elementów roboczych puli wątków. |
>| Liczba elementów ukończonych w puli wątków | z puli wątków — liczba elementów zakończonych | Liczba | Liczba elementów roboczych ukończonych przez pulę wątków. |
>| Liczba aktywnych czasomierzy               | Active-Timer-Count               | Liczba | Liczba czasomierzy, które są obecnie aktywne. Aktywny czasomierz to taki, który jest zarejestrowany w taktie w pewnym momencie w przyszłości i nie został jeszcze anulowany. |

Aby uzyskać więcej informacji, zobacz [liczniki dotnet](/dotnet/core/diagnostics/dotnet-counters).

### <a name="request"></a>Żądanie
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| Tomcat. Global. sent | Tomcat. Global. sent | Bajty | Liczba wysłanych serwerów sieci Web Tomcat danych |
>| Tomcat. Global. Receives | Tomcat. Global. Receives | Bajty | Liczba odebranych danych Tomcat serwera sieci Web |
>| Tomcat. Global. Request. Total. Count | Tomcat. Global. Request (Total-Count) | Liczba | Łączna liczba żądań przetworzonych przez serwer sieci Web Tomcat |
>| Tomcat. Global. Request. Max | Tomcat. Global. Request. Max | ) | Maksymalny czas przetwarzania żądania przez serwer sieci Web Tomcat |

### <a name="request-net"></a>Żądanie (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|------|-----------------------------|------|---------|
>| Żądania na sekundę | Liczba żądań na sekundę | Liczba | Liczba żądań. |
>| Łączna liczba żądań | łącznie — żądania | Liczba | Łączna liczba żądań. |
>| Bieżące żądania | bieżące żądania | Liczba | Liczba bieżących żądań. |
>| Żądania zakończone niepowodzeniem | Nieudane żądania | Liczba | Liczba żądań zakończonych niepowodzeniem. |

Aby uzyskać więcej informacji, zobacz [liczniki dotnet](/dotnet/core/diagnostics/dotnet-counters).

### <a name="session"></a>Sesja
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| Tomcat. Sessions. Active. Max | Tomcat. Sessions. Active. Max | Liczba | Maksymalna liczba sesji, które były aktywne w tym samym czasie |
>| Tomcat. Sessions. Alive. Max | Tomcat. Sessions. Alive. Max | ) | Najdłuższy czas (w sekundach) aktywności wygasłej sesji |
>| Tomcat. Sessions. Created | Tomcat. Sessions. Created | Liczba | Liczba utworzonych sesji |
>| Tomcat. Sessions. wygasła | Tomcat. Sessions. wygasła | Liczba | Liczba wygasłych sesji |
>| Tomcat. Sessions. rejected | Tomcat. Sessions. rejected | Liczba | Liczba sesji, które nie zostały utworzone, ponieważ osiągnięto maksymalną liczbę aktywnych sesji. |
>| Tomcat. Sessions. Active. Current | Tomcat. Sessions. Active. Current | Liczba | Liczba aktywnych sesji Tomcat |

## <a name="see-also"></a>Zobacz też

* [Szybki Start: monitorowanie aplikacji w chmurze platformy Azure z użyciem dzienników, metryk i śledzenia](spring-cloud-quickstart-logs-metrics-tracing.md)

* [Wprowadzenie do Eksploratora metryk platformy Azure](../azure-monitor/essentials/metrics-getting-started.md)

* [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](./diagnostic-services.md)

## <a name="next-steps"></a>Następne kroki

* [Samouczek: monitorowanie źródeł wiosennych w chmurze przy użyciu alertów i grup akcji](./spring-cloud-tutorial-alerts-action-groups.md)

* [Limity przydziału i plany usług dla chmury wiosennej platformy Azure](./spring-cloud-quotas.md)
