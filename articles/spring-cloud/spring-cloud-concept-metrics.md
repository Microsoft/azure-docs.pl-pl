---
title: Informacje o metrykach dla chmury wiosennej platformy Azure
description: Dowiedz się, jak przejrzeć metryki w chmurze Azure wiosennej
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: f3283e70ec3e0b559b417285c4b154494a88980e
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248454"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Informacje o metrykach dla chmury wiosennej platformy Azure

Eksplorator metryk platformy Azure to składnik Microsoft Azure Portal, który umożliwia Wykreślanie wykresów, wizualnie skorelowanych trendów oraz badanie skoków i wartości DIP w metrykach. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobów. 

W chmurze Azure wiosną istnieją dwa punkty widoku dla metryk.
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

Typ agregacji wskazuje sposób agregowania punktów metryk na wykresie według czasu. Istnieje jeden nieprzetworzony punkt metryki co minutę, a typ przed agregacją w minutach jest wstępnie zdefiniowany przez typ metryki.
* Sum: Sumuj wszystkie wartości jako docelowe dane wyjściowe.
* Średnia: Użyj średniej wartości w okresie jako docelowy wynik.
* Max/min: Użyj wartości max/min w okresie jako docelowego wyjścia.

Można również zmodyfikować zakres czasu do pokazania. Zakres czasu można wybrać z ostatnich 30 minut do ostatnich 30 dni lub z niestandardowym zakresem czasu.

![Modyfikacja metryki](media/metrics/metrics-6.png)

Widok domyślny obejmuje wszystkie metryki application's usługi chmurowej platformy Azure ze sobą. Metryki jednej aplikacji lub wystąpienia można filtrować na ekranie.  Kliknij przycisk **Dodaj filtr**, ustaw właściwość na wartość **aplikacja**i wybierz aplikację docelową, którą chcesz monitorować, w polu tekstowym **wartości** . 

Można użyć dwóch rodzajów filtrów (właściwości):
* Aplikacja: Filtruj według nazwy aplikacji
* Wystąpienie: Filtruj według wystąpienia aplikacji

![Filtry metryk](media/metrics/metrics-7.png)

Można również użyć opcji **Zastosuj dzielenie** , która spowoduje narysowanie wielu wierszy dla jednej aplikacji:

![Podział metryk](media/metrics/metrics-8.png)

>[!TIP]
> Możesz tworzyć własne wykresy na stronie metryk i przypinać je do **pulpitu nawigacyjnego**. Zacznij od zmiany nazwy wykresu.  Następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego w prawym górnym rogu**. Teraz możesz sprawdzić swoją aplikację na **pulpicie nawigacyjnym**portalu.

## <a name="user-metrics-options"></a>Opcje metryk użytkownika

W poniższych tabelach przedstawiono dostępne metryki i szczegóły.

### <a name="error"></a>Błąd
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| TomcatErrorCount<br><br>Błąd globalny Tomcat (przestarzałe) (usunięto 1 lipca 2020) | Tomcat. Global. Error | Liczba | Liczba błędów przetworzonych żądań |
>| Tomcat. Global. Error | Tomcat. Global. Error | Liczba | Liczba błędów przetworzonych żądań |

### <a name="performance"></a>Wydajność
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| SystemCpuUsagePercentage<br><br>Procent użycia procesora CPU (przestarzałe) (usunięto 1 lipca 2020) | System. CPU. Usage | Wartość procentowa | Ostatnie użycie procesora CPU całego systemu. Ta wartość jest podwójnym interwałem [0.0, 1.0]. Wartość 0,0 oznacza, że wszystkie procesory były bezczynne w trakcie ostatniego zaobserwowanego okresu, a wartość 1,0 oznacza, że wszystkie procesory aktywnie działały 100% czasu w trakcie ostatniego zaobserwowanego okresu.|
>| System. CPU. Usage | System. CPU. Usage | Wartość procentowa | Ostatnie użycie procesora CPU całego systemu. Ta wartość jest podwójnym interwałem [0.0, 1.0]. Wartość 0,0 oznacza, że wszystkie procesory były bezczynne w trakcie ostatniego zaobserwowanego okresu, a wartość 1,0 oznacza, że wszystkie procesory aktywnie działały 100% czasu w trakcie ostatniego zaobserwowanego okresu.|
>| AppCpuUsagePercentage<br><br>Procent użycia procesora aplikacji (przestarzały) (usunięto 1 lipca 2020) | Procent użycia procesora aplikacji | Wartość procentowa | Ostatnie użycie procesora CPU przez proces wirtualna maszyna Java. Ta wartość jest podwójnym interwałem [0.0, 1.0]. Wartość 0,0 oznacza, że żaden z procesorów CPU nie był uruchomiony z procesu JVM w trakcie ostatniego zaobserwowanego okresu, a wartość 1,0 oznacza, że wszystkie procesory aktywnie działały wątki z JVM 100% czasu podczas ostatniego zaobserwowanego okresu. Wątki z JVM obejmują wątki aplikacji oraz wewnętrzne wątki JVM.|
>| Process. CPU. Usage | Procent użycia procesora aplikacji | Wartość procentowa | Ostatnie użycie procesora CPU przez proces wirtualna maszyna Java. Ta wartość jest podwójnym interwałem [0.0, 1.0]. Wartość 0,0 oznacza, że żaden z procesorów CPU nie był uruchomiony z procesu JVM w trakcie ostatniego zaobserwowanego okresu, a wartość 1,0 oznacza, że wszystkie procesory aktywnie działały wątki z JVM 100% czasu podczas ostatniego zaobserwowanego okresu. Wątki z JVM obejmują wątki aplikacji oraz wewnętrzne wątki JVM.|
>| AppMemoryCommitted<br><br>Przypisana pamięć aplikacji (przestarzałe)) (usunięto 1 lipca 2020) | JVM. Memory. Committed | Bajty | Reprezentuje ilość pamięci, która ma być dostępna do użycia przez JVM. JVM może zwolnić pamięć do systemu, a zatwierdzenie może być mniejsze niż init. wartość zatwierdzone będzie zawsze większa lub równa używanej. |
>| JVM. Memory. Committed | JVM. Memory. Committed | Bajty | Reprezentuje ilość pamięci, która ma być dostępna do użycia przez JVM. JVM może zwolnić pamięć do systemu, a zatwierdzenie może być mniejsze niż init. wartość zatwierdzone będzie zawsze większa lub równa używanej. |
>| AppMemoryUsed <br><br>Użyta pamięć aplikacji (przestarzałe) (usunięto 1 lipca 2020) | JVM. Memory. użyty | Bajty | Reprezentuje ilość pamięci aktualnie używaną w bajtach. |
>| JVM. Memory. użyty | JVM. Memory. użyty | Bajty | Reprezentuje ilość pamięci aktualnie używaną w bajtach. |
>| AppMemoryMax<br><br>Maks. pamięć aplikacji (przestarzałe) (usunięto 1 lipca 2020) | JVM. Memory. Max | Bajty | Reprezentuje maksymalną ilość pamięci, która może być używana do zarządzania pamięcią. Ilość użytych i przekazanych pamięci będzie zawsze mniejsza lub równa Max, jeśli zdefiniowano wartość Max. Alokacja pamięci może zakończyć się niepowodzeniem, jeśli próbuje zwiększyć użytą pamięć, taką jak > zatwierdzone, nawet jeśli użyto <= Max (na przykład gdy w systemie brakuje pamięci wirtualnej). |
>| JVM. Memory. Max | JVM. Memory. Max | Bajty | Reprezentuje maksymalną ilość pamięci, która może być używana do zarządzania pamięcią. Ilość użytych i przekazanych pamięci będzie zawsze mniejsza lub równa Max, jeśli zdefiniowano wartość Max. Alokacja pamięci może zakończyć się niepowodzeniem, jeśli próbuje zwiększyć użytą pamięć, taką jak > zatwierdzone, nawet jeśli użyto <= Max (na przykład gdy w systemie brakuje pamięci wirtualnej). |
>| MaxOldGenMemoryPoolBytes<br><br>Maksymalna dostępna wartość starego rozmiaru danych generacji (przestarzałe) (usunięto 1 lipca 2020) | JVM. GC. max. Data. size | Bajty | Szczytowe użycie pamięci przez starszą pulę pamięci generacji od momentu uruchomienia maszyny wirtualnej Java. |
>| JVM. GC. max. Data. size | JVM. GC. max. Data. size | Bajty | Szczytowe użycie pamięci przez starszą pulę pamięci generacji od momentu uruchomienia maszyny wirtualnej Java. |
>| OldGenMemoryPoolBytes<br><br>Stary rozmiar danych generacji (przestarzały) (usunięto 1 lipca 2020) | JVM. GC. Live. Data. size | Bajty | Rozmiar starej puli pamięci generacji po pełnej operacji GC. |
>| JVM. GC. Live. Data. size | JVM. GC. Live. Data. size | Bajty | Rozmiar starej puli pamięci generacji po pełnej operacji GC. |
>| OldGenPromotedBytes<br><br>Podnieś poziom do starego rozmiaru danych generacji (przestarzałe) (usunięto 1 lipca 2020) | JVM. GC. Memory. awansowana | Bajty | Liczba pozytywnych wzrostów rozmiaru starej puli pamięci generacji przed GC do po GC. |
>| JVM. GC. Memory. awansowana | JVM. GC. Memory. awansowana | Bajty | Liczba pozytywnych wzrostów rozmiaru starej puli pamięci generacji przed GC do po GC. |
>| YoungGenPromotedBytes<br><br>Podwyższ poziom do rozmiaru danych dla małych generacji (przestarzałe) (usunięto 1 lipca 2020) | JVM. GC. Memory. przydzielono | Bajty | Zwiększa się w celu zwiększenia rozmiaru puli pamięci młodej generacji po jednej operacji GC do wcześniejszej. |
>| JVM. GC. Memory. przydzielono | JVM. GC. Memory. przydzielono | Bajty | Zwiększa się w celu zwiększenia rozmiaru puli pamięci młodej generacji po jednej operacji GC do wcześniejszej. |
>| GCPauseTotalCount<br><br>Liczba wstrzymań GC (przestarzałe) (usunięto 1 lipca 2020) | JVM. GC. Pause (łącznie-Count) | Liczba | Łączna liczba GC po rozpoczęciu tego JMV, w tym w przypadku młodych i starych wykazów GC. |
>| JVM. GC. Pause. Total. Count | JVM. GC. Pause (łącznie-Count) | Liczba | Łączna liczba GC po rozpoczęciu tego JMV, w tym w przypadku młodych i starych wykazów GC. |
>| GCPauseTotalTime<br><br>Całkowity czas wstrzymania odzyskiwania pamięci (przestarzały) (usunięty 1 lipca 2020) | JVM. GC. Pause (całkowity czas) | ) | Łączny czas, zużyty przez GC po rozpoczęciu tego JMV, w tym dla młodych i starych WYKAZów. |
>| JVM. GC. Pause. Total. Time | JVM. GC. Pause (całkowity czas) | ) | Łączny czas, zużyty przez GC po rozpoczęciu tego JMV, w tym dla młodych i starych WYKAZów. |
>| Tomcat. Threads. config. Max | Tomcat. Threads. config. Max | Liczba | Maksymalna liczba wątków w konfiguracji Tomcat |
>| Tomcat. Threads. Current | Tomcat. Threads. Current | Liczba | Tomcat bieżąca liczba wątków |
>| Tomcat. Global. Request. AVG. Time | Tomcat. Global. Request. AVG. Time | ) | Średni czas żądania Tomcat |


### <a name="request"></a>Żądanie
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| TomcatSentBytes<br><br>Całkowita liczba wysłanych bajtów Tomcat (przestarzałe) (usunięto 1 lipca 2020) | Tomcat. Global. sent | Bajty | Liczba wysłanych serwerów sieci Web Tomcat danych |
>| Tomcat. Global. sent | Tomcat. Global. sent | Bajty | Liczba wysłanych serwerów sieci Web Tomcat danych |
>| TomcatReceivedBytes<br><br>Całkowita liczba odebranych bajtów Tomcat (przestarzałe) (usunięto 1 lipca 2020) | Tomcat. Global. Receives | Bajty | Liczba odebranych danych Tomcat serwera sieci Web |
>| Tomcat. Global. Receives | Tomcat. Global. Receives | Bajty | Liczba odebranych danych Tomcat serwera sieci Web |
>| TomcatRequestTotalTime<br><br>Łączny czas żądania Tomcat (przestarzały) (usunięto 1 lipca 2020) | Tomcat. Global. Request (łącznie-Time) | ) | Łączny czas przetwarzania żądań przez serwer sieci Web Tomcat |
>| TomcatRequestTotalCount<br><br>Łączna liczba żądań Tomcat (przestarzałe) (usunięto 1 lipca 2020) | Tomcat. Global. Request (Total-Count) | Liczba | Łączna liczba żądań przetworzonych przez serwer sieci Web Tomcat |
>| Tomcat. Global. Request. Total. Count | Tomcat. Global. Request (Total-Count) | Liczba | Łączna liczba żądań przetworzonych przez serwer sieci Web Tomcat |
>| TomcatRequestMaxTime<br><br>Maksymalny czas żądania Tomcat (przestarzały) (usunięto 1 lipca 2020) | Tomcat. Global. Request. Max | ) | Maksymalny czas przetwarzania żądania przez serwer sieci Web Tomcat |
>| Tomcat. Global. Request. Max | Tomcat. Global. Request. Max | ) | Maksymalny czas przetwarzania żądania przez serwer sieci Web Tomcat |

### <a name="session"></a>Sesja
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika | Jednostka | Szczegóły |
>|----|----|----|------------|
>| TomcatSessionActiveMaxCount<br><br>Maksymalna liczba aktywnych sesji Tomcat (przestarzałe) (usunięto 1 lipca 2020) | Tomcat. Sessions. Active. Max | Liczba | Maksymalna liczba sesji, które były aktywne w tym samym czasie |
>| Tomcat. Sessions. Active. Max | Tomcat. Sessions. Active. Max | Liczba | Maksymalna liczba sesji, które były aktywne w tym samym czasie |
>| TomcatSessionAliveMaxTime<br><br>Czas aktywności sesji Tomcat (przestarzały) (usunięto 1 lipca 2020) | Tomcat. Sessions. Alive. Max | ) | Najdłuższy czas (w sekundach) aktywności wygasłej sesji |
>| Tomcat. Sessions. Alive. Max | Tomcat. Sessions. Alive. Max | ) | Najdłuższy czas (w sekundach) aktywności wygasłej sesji |
>| TomcatSessionCreatedCount<br><br>Liczba utworzonych sesji Tomcat (przestarzałe) (usunięto 1 lipca 2020) | Tomcat. Sessions. Created | Liczba | Liczba utworzonych sesji |
>| Tomcat. Sessions. Created | Tomcat. Sessions. Created | Liczba | Liczba utworzonych sesji |
>| TomcatSessionExpiredCount<br><br>Liczba wygasłych sesji Tomcat (przestarzałe) (usunięto 1 lipca 2020) | Tomcat. Sessions. wygasła | Liczba | Liczba wygasłych sesji |
>| Tomcat. Sessions. wygasła | Tomcat. Sessions. wygasła | Liczba | Liczba wygasłych sesji |
>| TomcatSessionRejectedCount<br><br>Liczba odrzuconych sesji Tomcat (przestarzałe) (usunięto 1 lipca 2020) | Tomcat. Sessions. rejected | Liczba | Liczba sesji, które nie zostały utworzone, ponieważ osiągnięto maksymalną liczbę aktywnych sesji. |
>| Tomcat. Sessions. rejected | Tomcat. Sessions. rejected | Liczba | Liczba sesji, które nie zostały utworzone, ponieważ osiągnięto maksymalną liczbę aktywnych sesji. |
>| Tomcat. Sessions. Active. Current | Tomcat. Sessions. Active. Current | Liczba | Liczba aktywnych sesji Tomcat |

## <a name="see-also"></a>Zobacz też
* [Wprowadzenie do Eksploratora metryk platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Następne kroki
* [Samouczek: monitorowanie źródeł wiosennych w chmurze przy użyciu alertów i grup akcji](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Limity przydziału i plany usług dla chmury wiosennej platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

