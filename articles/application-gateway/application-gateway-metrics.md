---
title: Metryki usługi Azure Monitor dla bramy aplikacji
description: Dowiedz się, jak używać danych do monitorowania wydajności bramy aplikacji
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: ebbdda39f019f374f8e5abe951d0180c0dd453f6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457879"
---
# <a name="metrics-for-application-gateway"></a>Metryki bramy aplikacji

Brama aplikacji publikuje punkty danych, nazywane metrykami, w [usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) w celu uzyskania wydajności wystąpień bramy aplikacji i wewnętrznej bazy danych. Te metryki są wartościami liczbowymi w uporządkowanym zestawie danych szeregów czasowych, które opisują niektóre aspekty bramy aplikacji w określonym czasie. Jeśli istnieją żądania przepływające przez bramę aplikacji, mierzy i wysyła jej metryki w odstępach 60-sekundowych. Jeśli nie ma żadnych żądań przepływających przez bramę aplikacji lub brak danych dla metryki, metryka nie jest zgłaszana. Aby uzyskać więcej informacji, zobacz [metryki usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metryki obsługiwane przez jednostkę SKU bramy aplikacji w wersji 2

### <a name="timing-metrics"></a>Metryki chronometrażu

Brama aplikacji zawiera kilka wbudowanych metryk chronometrażu związanych z żądaniem i odpowiedzią, które są mierzone w milisekundach. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Jeśli w bramie aplikacji znajduje się więcej niż jeden odbiornik, zawsze filtruj według wymiaru *odbiornika,* porównując różne metryki opóźnienia, aby uzyskać znaczące wnioskowanie.

- **Czas połączenia wewnętrznej bazy danych**

  Czas spędzony na nawiązywaniu połączenia z aplikacją wewnętrznej bazy danych. 

  Obejmuje to opóźnienie sieci, a także czas pochłonięcia stosu TCP serwera wewnętrznej bazy danych w celu nawiązania nowych połączeń. W przypadku protokołu TLS zawiera również czas poświęcony na uzgadnianie. 

- **Czas odpowiedzi pierwszego bajtu wewnętrznej bazy danych**

  Przedział czasu między rozpoczęciem ustanawiania połączenia z serwerem wewnętrznej bazy danych a odbieraniem pierwszego bajtu nagłówka odpowiedzi. 

  To przybliża sumę *czasu połączenia wewnętrznej bazy danych,* czasu czasu, który został odebrany przez żądanie, aby dotrzeć do wewnętrznej bazy danych z bramy aplikacji, czasu poświęconego przez aplikację wewnętrznej bazy danych w celu udzielenia odpowiedzi (czas, który serwer potrzebował do wygenerowania zawartości, potencjalnie pobrać zapytania bazy danych) oraz czasu, który został pobrany przez pierwszy bajt odpowiedzi, aby dotrzeć do bramy aplikacji z wewnętrznej bazy danych.

- **Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych**

  Przedział czasu między rozpoczęciem ustanawiania połączenia z serwerem wewnętrznej bazy danych a odbieraniem ostatniego bajtu treści odpowiedzi. 

  Przybliża to sumę *czasu odpowiedzi pierwszego bajtów bazy danych* i czasu transferu danych (liczba ta może się znacznie różnić w zależności od żądanego rozmiaru obiektów i opóźnienia sieci serwera).

- **Całkowita liczba bram aplikacji**

  Średni czas potrzebny na odebranie, przetworzenie i wysłanie odpowiedzi na żądanie. 

  Jest to interwał od momentu, gdy brama aplikacji odbiera pierwszy bajt żądania HTTP do czasu, gdy ostatni bajt odpowiedzi został wysłany do klienta. Obejmuje to czas przetwarzania przez bramę aplikacji, *czas odpowiedzi ostatniego bajtów wewnętrznej bazy*danych, czas wykonany przez bramę aplikacji w celu wysłania całej odpowiedzi i *rtt klienta.*

- **RtT klienta**

  Średni czas podróży w obie strony między klientami a bramą aplikacji.



Metryki te mogą służyć do określenia, czy obserwowane spowolnienie jest spowodowane siecią klienta, wydajność bramy aplikacji, sieci wewnętrznej bazy danych i serwera wewnętrznej bazy danych TCP stosu nasycenia, wydajność aplikacji wewnętrznej bazy danych lub duży rozmiar pliku.

Na przykład Jeśli istnieje skok w wewnętrznej bazy danych pierwszy trend *czasu odpowiedzi bajtów,* ale *trend czasu połączenia wewnętrznej bazy danych* jest stabilny, następnie można wywnioskować, że brama aplikacji do opóźnienia wewnętrznej bazy danych i czas przeznaczony do ustanowienia połączenia jest stabilny, a skok jest spowodowane ze względu na wzrost czasu odpowiedzi aplikacji wewnętrznej bazy danych. Z drugiej strony, jeśli skok w *czas odpowiedzi pierwszego bajtu bazy danych* jest skojarzony z odpowiednim skokiem w czasie połączenia wewnętrznej bazy *danych,* można wywnioskować, że sieć między bramą aplikacji a serwerem wewnętrznej bazy danych lub stos TCP serwera wewnętrznej bazy danych jest nasycony. 

Jeśli zauważysz skok w *wewnętrznej bazy danych ostatni czas odpowiedzi bajtów,* ale *backend pierwszy czas odpowiedzi bajtów* jest stabilny, a następnie można wywnioskować, że skok jest ze względu na większy plik żądany.

Podobnie jeśli *całkowita liczba czasu bramy aplikacji* ma skok, ale czas odpowiedzi *ostatniego bajtu wewnętrznej bazy danych* jest stabilny, może to być oznaką wąskiego gardła wydajności w bramie aplikacji lub wąskim gardłem w sieci między klientem a bramą aplikacji. Ponadto jeśli *rtt klienta* ma również odpowiednie skok, a następnie wskazuje, że degradacja jest ze względu na sieć między klientem i bramy aplikacji.

### <a name="application-gateway-metrics"></a>Metryki bramy aplikacji

W przypadku bramy aplikacji dostępne są następujące metryki:

- **Odebrane bajty**

   Liczba bajtów odebranych przez bramę aplikacji od klientów

- **Wysłane bajty**

   Liczba bajtów wysłanych przez bramę aplikacji do klientów

- **Protokół TLS klienta**

   Liczba żądań TLS i innych niż TLS zainicjowanych przez klienta, który nawiązał połączenie z bramą aplikacji. Aby wyświetlić dystrybucję protokołu TLS, filtruj według wymiaru protokołu TLS.

- **Obecne jednostki pojemności**

   Liczba jednostek pojemności zużytych do równoważenia obciążenia ruchu. Istnieją trzy determinanty jednostki pojemności - jednostka obliczeniowa, połączenia trwałe i przepływność. Każda jednostka pojemności składa się co najwyżej z: 1 jednostki obliczeniowej lub 2500 połączeń trwałych lub przepustowości 2,22 Mb/s.

- **Bieżące jednostki obliczeniowe**

   Liczba zużywanej pojemności procesora. Czynniki wpływające na jednostkę obliczeniową to połączenia TLS/s, obliczenia ponownego zapisu adresu URL i przetwarzanie reguł WAF. 

- **Bieżące połączenia**

   Całkowita liczba równoczesnych połączeń aktywnych z klientów do bramy aplikacji
   
- **Szacowane rozliczane jednostki pojemności**

  W jednostce SKU w wersji 2 model cenowy jest napędzany przez zużycie. Jednostki pojemności mierzą koszt oparty na zużyciu, który jest naliczany dodatkowo do kosztu stałego. *Szacowane jednostki rozliczanej pojemności* wskazują liczbę jednostek pojemności, za pomocą których jest szacowane. Jest to obliczane jako większa wartość między *bieżącymi jednostkami pojemności* (jednostki pojemności wymagane do zrównoważenia obciążenia ruchu) a *stałymi jednostkami zdolności produkcyjnych rozliczanymi* (jednostki minimalnej pojemności są aprowizowane).

- **Żądania zakończone niepowodzeniem**

  Liczba żądań obsługiwanych przez bramę aplikacji z kodami błędów serwera 5xx. Obejmuje to kody 5xx, które są generowane z bramy aplikacji, a także kody 5xx, które są generowane z wewnętrznej bazy danych. Liczba żądań może być dalej filtrowane, aby pokazać liczbę na każdy/konkretne zaplecza pool-http kombinacji ustawień.
   
- **Stałe jednostki mocy wyładowowej do wyładowania**

  Minimalna liczba jednostek pojemności przechowywane aprowizowana zgodnie z *ustawieniem minimalne jednostki skali* (jedno wystąpienie przekłada się na 10 jednostek pojemności) w konfiguracji bramy aplikacji.
   
 - **Nowe połączenia na sekundę**

   Średnia liczba nowych połączeń TCP na sekundę nawiązywała od klientów do bramy aplikacji i od bramy aplikacji do członków wewnętrznej bazy danych.


- **Stan odpowiedzi**

   Stan odpowiedzi HTTP zwrócony przez bramę aplikacji. Dystrybucja kodu stanu odpowiedzi może być dalej klasyfikowana w celu wyświetlania odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Przepływność**

   Liczba bajtów na sekundę, które obsługiwała brama aplikacji

- **Łączna liczba żądań**

   Liczba pomyślnych żądań obsługiwanych przez bramę aplikacji. Liczba żądań może być dalej filtrowane, aby pokazać liczbę na każdy/konkretne zaplecza pool-http kombinacji ustawień.

### <a name="backend-metrics"></a>Metryki wewnętrznej bazy danych

W przypadku bramy aplikacji dostępne są następujące metryki:

- **Stan odpowiedzi wewnętrznej bazy danych**

  Liczba kodów stanu odpowiedzi HTTP zwracanych przez zaplecze. Nie obejmuje to żadnych kodów odpowiedzi generowanych przez bramę aplikacji. Dystrybucja kodu stanu odpowiedzi może być dalej klasyfikowana w celu wyświetlania odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Liczba hostów w dobrej kondycji**

  Liczba zaplecza, które są określane w dobrej kondycji przez sondę kondycji. Można filtrować na podstawie puli wewnętrznej bazy danych, aby wyświetlić liczbę hostów w dobrej kondycji w określonej puli wewnętrznej bazy danych.

- **Liczba hostów w złej kondycji**

  Liczba zaplecza, które są określane w złej kondycji przez sondę kondycji. Można filtrować na podstawie puli wewnętrznej bazy danych, aby wyświetlić liczbę niezdrowych hostów w określonej puli zaplecza.
  
- **Żądania na minutę na zdrowego hosta**

  Średnia liczba żądań odebranych przez każdego członka w dobrej kondycji w puli wewnętrznej bazy danych w ciągu minuty. Należy określić pulę wewnętrznej bazy danych przy użyciu wymiaru *HttpSettings wewnętrznej bazy danych.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metryki obsługiwane przez jednostkę SKU bramy aplikacji w wersji 1

### <a name="application-gateway-metrics"></a>Metryki bramy aplikacji

W przypadku bramy aplikacji dostępne są następujące metryki:

- **Wykorzystanie procesora**

  Wyświetla wykorzystanie procesorów przydzielonych do bramy aplikacji.  W normalnych warunkach użycie procesora CPU nie powinno regularnie przekraczać 90%, ponieważ może to spowodować opóźnienie w witrynach sieci Web hostowanych za bramą aplikacji i zakłócić działanie klienta. Można pośrednio kontrolować lub poprawić wykorzystanie procesora CPU, modyfikując konfigurację bramy aplikacji, zwiększając liczbę wystąpień lub przenosząc się do większego rozmiaru jednostki SKU lub wykonując oba te elementy.

- **Bieżące połączenia**

  Liczba bieżących połączeń nawiązanych za pomocą bramy aplikacji

- **Żądania zakończone niepowodzeniem**

  Liczba żądań obsługiwanych przez bramę aplikacji z kodami błędów serwera 5xx. Obejmuje to kody 5xx, które są generowane z bramy aplikacji, a także kody 5xx, które są generowane z wewnętrznej bazy danych. Liczba żądań może być dalej filtrowane, aby pokazać liczbę na każdy/konkretne zaplecza pool-http kombinacji ustawień.

- **Stan odpowiedzi**

  Stan odpowiedzi HTTP zwrócony przez bramę aplikacji. Dystrybucja kodu stanu odpowiedzi może być dalej klasyfikowana w celu wyświetlania odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Przepływność**

  Liczba bajtów na sekundę, które obsługiwała brama aplikacji

- **Łączna liczba żądań**

  Liczba pomyślnych żądań obsługiwanych przez bramę aplikacji. Liczba żądań może być dalej filtrowane, aby pokazać liczbę na każdy/konkretne zaplecza pool-http kombinacji ustawień.

- **Liczba zablokowanych żądań zapory aplikacji sieci Web**
- **Dystrybucja zablokowanych żądań zapory aplikacji sieci Web**
- **Dystrybucja reguły całkowita zapory aplikacji sieci Web**

### <a name="backend-metrics"></a>Metryki wewnętrznej bazy danych

W przypadku bramy aplikacji dostępne są następujące metryki:

- **Liczba hostów w dobrej kondycji**

  Liczba zaplecza, które są określane w dobrej kondycji przez sondę kondycji. Można filtrować na podstawie puli wewnętrznej bazy danych, aby wyświetlić liczbę hostów w dobrej kondycji w określonej puli wewnętrznej bazy danych.

- **Liczba hostów w złej kondycji**

  Liczba zaplecza, które są określane w złej kondycji przez sondę kondycji. Można filtrować na podstawie puli wewnętrznej bazy danych, aby wyświetlić liczbę niezdrowych hostów w określonej puli zaplecza.

## <a name="metrics-visualization"></a>Wizualizacja metryk

Przejdź do bramy aplikacji w obszarze **Monitorowanie** wybranych **metryk**. Aby wyświetlić dostępne wartości, wybierz listę rozwijaną **METRYKA**.

Na poniższej ilustracji przedstawiono przykład z trzema metrykami wyświetlanymi w ciągu ostatnich 30 minut:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Aby wyświetlić bieżącą listę metryk, zobacz [Obsługiwane metryki za pomocą usługi Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Reguły alertów dotyczące metryk

Można uruchomić reguły alertów na podstawie metryk dla zasobu. Na przykład alert może wywołać element webhook lub e-mail administratora, jeśli przepływność bramy aplikacji jest powyżej, poniżej lub na progu dla określonego okresu.

W poniższym przykładzie przedstawiono tworzenie reguły alertu, która wysyła wiadomość e-mail do administratora po przekroczeniu przez przepływność progu:

1. Wybierz **pozycję Dodaj alert metryki,** aby otworzyć stronę Dodaj **regułę.** Możesz również dotrzeć do tej strony na stronie metryki.

   ![Przycisk "Dodaj alert metryczny"][6]

2. Na stronie **Dodaj regułę** wypełnij sekcje nazwa, warunek i powiadom, a następnie wybierz przycisk **OK**.

   * W selektorze **Condition** wybierz jedną z czterech wartości: **Większa niż**, Większa **lub równa**, Mniejsza **niż**lub Mniejsza **lub równa**.

   * W selektorze **Okres** wybierz okres od pięciu minut do sześciu godzin.

   * Jeśli wybierzesz **właścicieli wiadomości e-mail, współautorów i czytelników,** wiadomość e-mail może być dynamiczna na podstawie użytkowników, którzy mają dostęp do tego zasobu. W przeciwnym razie można udostępnić oddzieloną przecinkami listę użytkowników w polu **Dodatkowe wiadomości e-mail administratora.**

   ![Dodawanie strony reguły][7]

Jeśli próg zostanie przekroczony, nadejdzie wiadomość e-mail podobna do tej na poniższej ilustracji:

![Wiadomość e-mail z powodu przekroczonego progu][8]

Po utworzeniu alertu metrycznego pojawi się lista alertów. Zawiera przegląd wszystkich reguł alertów.

![Lista wpisów i reguł][9]

Aby dowiedzieć się więcej o powiadomieniach o alertach, zobacz [Odbieranie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Aby dowiedzieć się więcej o elementach webhook i jak można ich używać z alertami, odwiedź stronę [Konfigurowanie elementu webhook w alertie metryki platformy Azure.](../azure-monitor/platform/alerts-webhooks.md)

## <a name="next-steps"></a>Następne kroki

* Wizualizuj dzienniki liczników i zdarzeń przy użyciu [dzienników usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Wizualizuj swój dziennik aktywności platformy Azure za pomocą](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) wpisu w blogu usługi Power BI.
* [Wyświetlanie i analizowanie dzienników aktywności platformy Azure w usłudze Power BI i innych wpisach](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) w blogu.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
