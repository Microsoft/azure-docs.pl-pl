---
title: Metryki Azure Monitor dla Application Gateway
description: Dowiedz się, jak używać metryk do monitorowania wydajności bramy aplikacji
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: surmb
ms.openlocfilehash: 9faa3a284aa7151880526c1ee70cfadc3dbf3089
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576117"
---
# <a name="metrics-for-application-gateway"></a>Metryki dla Application Gateway

Application Gateway publikuje punkty danych o nazwie Metrics, aby [Azure monitor](../azure-monitor/overview.md) wydajności Application Gateway i wystąpień zaplecza. Te metryki to wartości liczbowe w uporządkowanym zestawie danych szeregów czasowych, które opisują kilka aspektów bramy aplikacji w danym momencie. Jeśli istnieją żądania przepływające przez Application Gateway, miary i wysyłają metryki w 60 sekund. Jeśli nie ma żadnych żądań przepływających przez Application Gateway lub Brak danych dla metryki, Metryka nie zostanie zgłoszona. Aby uzyskać więcej informacji, zobacz [Azure monitor metryki](../azure-monitor/essentials/data-platform-metrics.md).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metryki obsługiwane przez jednostkę SKU Application Gateway v2

### <a name="timing-metrics"></a>Metryki chronometrażu

Application Gateway zapewnia kilka wbudowanych metryk czasu związanych z żądaniem i odpowiedzią, które są mierzone w milisekundach. 

![Diagram metryk czasu dla Application Gateway.](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Jeśli w Application Gateway znajduje się więcej niż jeden odbiornik, zawsze Przefiltruj według wymiaru *odbiornika* , porównując różne metryki opóźnienia w celu uzyskania zrozumiałego wnioskowania.

- **Czas połączenia z zapleczem**

  Czas nawiązywania połączenia z aplikacją zaplecza. 

  Obejmuje to opóźnienie sieci, a także czas potrzebny przez stos TCP serwera wewnętrznej bazy danych do ustanowienia nowych połączeń. W przypadku protokołu TLS obejmuje również czas potrzebny na uzgodnienie. 

- **Czas odpowiedzi na pierwszy bajt zaplecza**

  Interwał czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania pierwszego bajtu nagłówka odpowiedzi. 

  Jest to przybliżona suma *czasu łączenia zaplecza*, czasu podejmowanego przez żądanie do osiągnięcia wewnętrznej bazy danych z Application Gateway, czas trwania przez aplikację zaplecza (czas, w którym serwer wygenerował zawartość, potencjalnie pobierane kwerendy bazy danych) i czas potrzebny na pierwszy bajt odpowiedzi w celu uzyskania dostępu do Application Gateway z zaplecza.

- **Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych**

  Przedział czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania ostatniego bajtu treści odpowiedzi. 

  Jest to w przybliżeniu suma *czasu odebrania z zaplecza pierwszego bajtu odpowiedzi* oraz czasu transferu danych (ta liczba może się znacząco różnić w zależności od rozmiaru żądanych obiektów oraz opóźnienia w sieci serwera).

- **Łączny czas bramy aplikacji**

  Średni czas odbierania i przetwarzania żądania oraz jego odpowiedź na wysłanie. 

  Jest to interwał od momentu odebrania przez Application Gateway pierwszego bajtu żądania HTTP do momentu wysłania ostatniego bajtu odpowiedzi do klienta. Dotyczy to również czasu przetwarzania wykonywanego przez Application Gateway, *czas odpowiedzi ostatniego bajtu*, czas wykonania przez Application Gateway, aby wysłać całą odpowiedź i *RTT klienta*.

- **Czas RTT klienta**

  Średni czas błądzenia między klientami a Application Gateway.



Te metryki mogą służyć do określenia, czy obserwowane spowolnienie jest spowodowane przez sieć klienta, Application Gateway wydajność, nasycenie stosu TCP sieci i zaplecza, wydajność aplikacji zaplecza lub duży rozmiar pliku.

Na przykład, jeśli występuje wzrost trendu *czasu odpowiedzi na pierwszy bajt z zaplecza* , ale trend *czasu połączenia zaplecza* jest stabilny, można wywnioskować, że czas oczekiwania bramy aplikacji na zaplecze, a termin nawiązywania połączenia jest stabilny, a skok jest spowodowany zwiększeniem czasu odpowiedzi aplikacji zaplecza. Z drugiej strony, jeśli wartość w polu *czas odpowiedzi pierwszego bajtu w zapleczu* jest skojarzona z odpowiednim skokiem w *czasie połączenia zaplecza*, można ustalić, czy sieć między Application Gateway i serwerem zaplecza lub stosem TCP serwera wewnętrznej bazy danych zakończyła się nasyceniem. 

Jeśli zauważysz wzrost w *czasie odpowiedzi ostatniego bajtu* , ale *czas odpowiedzi pierwszego bajtu zaplecze* jest stabilny, można ustalić, że skok jest spowodowany większym żądanym plikiem.

Analogicznie, jeśli *łączny czas w usłudze Application Gateway* ma wartość skok, ale *czas odpowiedzi ostatniego bajtu* w zasobie jest stabilny, może to być znak wąskiego gardła wydajności w Application Gateway lub wąskie gardło w sieci między klientem i Application Gateway. Ponadto, jeśli plik *RTT klienta* ma również odpowiedni skok, oznacza to, że spadek wydajności jest spowodowany przez sieć między klientem a Application Gateway.

### <a name="application-gateway-metrics"></a>Metryki Application Gateway

W przypadku Application Gateway dostępne są następujące metryki:

- **Odebrane bajty**

   Liczba bajtów odebranych przez Application Gateway od klientów

- **Wysłane bajty**

   Liczba bajtów wysłanych przez Application Gateway do klientów

- **Protokół TLS klienta**

   Liczba żądań TLS i innych niż TLS zainicjowanych przez klienta, które ustanowiły połączenie z Application Gateway. Aby wyświetlić dystrybucję protokołu TLS, należy filtrować według protokołu TLS wymiaru.

- **Jednostki bieżącej wydajności**

   Liczba jednostek wydajności użytych do równoważenia obciążenia ruchu. Istnieją trzy uwarunkowania jednostek wydajności — jednostka obliczeniowa, trwałe połączenia i przepływność. Każda jednostka pojemności składa się z maksymalnie: 1 jednostki obliczeniowej lub 2500 połączeń trwałych lub przepływności 2,22 MB/s.

- **Bieżące jednostki obliczeniowe**

   Liczba zużytych pojemności procesora. Czynniki wpływające na jednostki obliczeniowe to liczba połączeń TLS/s, obliczenia ponownego zapisu adresu URL oraz przetwarzanie reguł zapory aplikacji internetowej. 

- **Bieżące połączenia**

   Całkowita liczba jednoczesnych połączeń aktywnych od klientów do Application Gateway
   
- **Szacowane rozliczane jednostki wydajności**

  W przypadku jednostki SKU w wersji 2 model cen zależy od użycia. Jednostki wydajności mierzą koszt w zależności od użycia, naliczany dodatkowo wobec kosztu stałego. *Szacowana liczba jednostek wydajności* wskazuje liczbę jednostek wydajności, przy użyciu których szacuje się rozliczanie. Oblicza się je na podstawie większej z dwóch wartości: liczby *jednostek bieżącej wydajności* (jednostek wydajności wymaganych do równoważenia obciążenia ruchu) oraz *jednostek stałej wydajności rozliczanej* (minimalnej aprowizowanej liczby jednostek wydajności).

- **Nieudane żądania**

  Liczba żądań obsłużonych przez Application Gateway z kodami błędów serwera 5xx. Obejmuje to kody 5xx, które są generowane na podstawie Application Gateway, a także kody 5xx, które są generowane na podstawie zaplecza. Liczbę żądań można w dalszej odfiltrować, aby pokazać liczbę dla każdej lub określonej puli zaplecza — kombinację ustawień http.
   
- **Jednostki stałej wydajności rozliczanej**

  Minimalna aprowizowana liczba jednostek wydajności zgodnie z ustawieniem *Minimalna liczba jednostek skalowania* (jedno wystąpienie przekłada się na 10 jednostek wydajności) w konfiguracji usługi Application Gateway.
   
 - **Nowe połączenia na sekundę**

   Średnia liczba nowych połączeń TCP na sekundę ustanowionych od klientów do Application Gateway oraz z Application Gateway do członków zaplecza.


- **Stan odpowiedzi**

   Stan odpowiedzi HTTP zwrócony przez Application Gateway. Rozkład kodu stanu odpowiedzi można dodatkowo podzielić na odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Przepływność**

   Liczba bajtów na sekundę obsłużonych przez Application Gateway

- **Łączna liczba żądań**

   Liczba pomyślnych żądań obsłużonych przez Application Gateway. Liczbę żądań można w dalszej odfiltrować, aby pokazać liczbę dla każdej lub określonej puli zaplecza — kombinację ustawień http.

### <a name="backend-metrics"></a>Metryki zaplecza

W przypadku Application Gateway dostępne są następujące metryki:

- **Stan odpowiedzi zaplecza**

  Liczba kodów stanu odpowiedzi HTTP zwracanych przez sekwencje końcowe. Nie obejmuje to żadnych kodów odpowiedzi wygenerowanych przez Application Gateway. Rozkład kodu stanu odpowiedzi można dodatkowo podzielić na odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Liczba hostów w dobrej kondycji**

  Liczba punktów końcowych, które są określone w dobrej kondycji przez sondę kondycji. Można filtrować według poszczególnych pul zaplecza, aby wyświetlić liczbę hostów w dobrej kondycji w określonej puli zaplecza.

- **Liczba hostów w złej kondycji**

  Liczba punktów końcowych, które są określone w złej kondycji przez sondę kondycji. Można filtrować według jednej puli zaplecza, aby wyświetlić liczbę hostów w złej kondycji w określonej puli zaplecza.
  
- **Liczba żądań na minutę na hosta w dobrej kondycji**

  Średnia liczba żądań odebranych przez każdy element członkowski dobrej kondycji w puli zaplecza w ciągu minuty. Należy określić pulę zaplecza przy użyciu wymiaru *ustawień httpsettings elementu backendpool* .  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metryki obsługiwane przez jednostkę SKU Application Gateway v1

### <a name="application-gateway-metrics"></a>Metryki Application Gateway

W przypadku Application Gateway dostępne są następujące metryki:

- **Wykorzystanie procesora**

  Wyświetla użycie procesorów przydzielonych do usługi Application Gateway.  W normalnych warunkach użycie procesora nie powinno regularnie przekraczać 90%, ponieważ może to powodować opóźnienie w witrynach internetowych hostowanych za bramą aplikacji i zakłócać środowisko klienta. Można pośrednio kontrolować lub poprawić użycie procesora, modyfikując konfigurację usługi Application Gateway w celu zwiększenia liczby wystąpień, przechodząc na większy rozmiar jednostki SKU lub stosując oba rozwiązania jednocześnie.

- **Bieżące połączenia**

  Liczba bieżących połączeń ustanowionych z Application Gateway

- **Nieudane żądania**

  Liczba żądań, które się nie powiodły z powodu problemów z połączeniem. Ta liczba obejmuje żądania, które nie powiodły się z powodu przekroczenia ustawienia HTTP "limit czasu żądania" oraz żądania, które nie powiodły się z powodu problemów z połączeniem między usługą Application Gateway i zapleczem. Ta liczba nie obejmuje błędów spowodowanych brakiem nieprawidłowej wewnętrznej bazy danych. odpowiedzi 4xx i 5xx z zaplecza nie są również uwzględniane jako część tej metryki.

- **Stan odpowiedzi**

  Stan odpowiedzi HTTP zwrócony przez Application Gateway. Rozkład kodu stanu odpowiedzi można dodatkowo podzielić na odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Przepływność**

  Liczba bajtów na sekundę obsłużonych przez Application Gateway

- **Łączna liczba żądań**

  Liczba pomyślnych żądań obsłużonych przez Application Gateway. Liczbę żądań można w dalszej odfiltrować, aby pokazać liczbę dla każdej lub określonej puli zaplecza — kombinację ustawień http.

- **Liczba zablokowanych żądań zapory aplikacji sieci Web**
- **Dystrybucja żądań zablokowanych przez zaporę aplikacji sieci Web**
- **Dystrybucja reguł dla zapory aplikacji sieci Web**

### <a name="backend-metrics"></a>Metryki zaplecza

W przypadku Application Gateway dostępne są następujące metryki:

- **Liczba hostów w dobrej kondycji**

  Liczba punktów końcowych, które są określone w dobrej kondycji przez sondę kondycji. Można filtrować według poszczególnych pul zaplecza, aby wyświetlić liczbę hostów w dobrej kondycji w określonej puli zaplecza.

- **Liczba hostów w złej kondycji**

  Liczba punktów końcowych, które są określone w złej kondycji przez sondę kondycji. Można filtrować według jednej puli zaplecza, aby wyświetlić liczbę hostów w złej kondycji w określonej puli zaplecza.

## <a name="metrics-visualization"></a>Wizualizacja metryk

Przejdź do bramy aplikacji, w obszarze **monitorowanie** wybierz pozycję **metryki**. Aby wyświetlić dostępne wartości, wybierz listę rozwijaną **METRYKA**.

Na poniższej ilustracji przedstawiono przykład z trzema metrykami wyświetlonymi w ciągu ostatnich 30 minut:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Widok metryki." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

Aby wyświetlić bieżącą listę metryk, zobacz temat [obsługiwane metryki z Azure monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Reguły alertów dla metryk

Reguły alertów można uruchomić na podstawie metryk dla zasobu. Na przykład alert może wywołać element webhook lub wysłać wiadomość e-mail do administratora, jeśli przepływność bramy aplikacji jest powyżej, poniżej lub w określonym przedziale czasu.

Poniższy przykład przeprowadzi Cię przez proces tworzenia reguły alertu, która wysyła wiadomość e-mail do administratora po naruszeniu przez przepływność wartości progowej:

1. Wybierz pozycję **Dodaj alert metryki** , aby otworzyć stronę **Dodawanie reguły** . Możesz również uzyskać dostęp do tej strony ze strony metryki.

   ![Przycisk "Dodaj alert dotyczący metryki"][6]

2. Na stronie **Dodawanie reguły** Wypełnij sekcje nazwa, warunek i powiadomienie, a następnie wybierz **przycisk OK**.

   * W selektorze **warunku** wybierz jedną z czterech wartości: **większe** niż, **większe niż lub równe**, **mniejsze niż** lub **mniejsze niż lub równe**.

   * W selektorze **okresu** Wybierz okres z pięciu minut do 6 godzin.

   * W przypadku wybrania opcji **właściciele, współautorzy i czytelnicy poczty** e-mail wiadomość e-mail może być dynamiczna na podstawie użytkowników, którzy mają dostęp do tego zasobu. W przeciwnym razie można podać listę użytkowników z rozdzieloną przecinkami w polu **dodatkowe adresy e-mail administratora** .

   ![Dodaj stronę reguły][7]

W przypadku naruszenia progu wiadomości e-mail podobnej do tej na poniższej ilustracji przychodzą:

![Adres e-mail pod kątem naruszenia progu][8]

Po utworzeniu alertu dotyczącego metryki pojawia się lista alertów. Zawiera przegląd wszystkich reguł alertów.

![Lista alertów i reguł][9]

Aby dowiedzieć się więcej na temat powiadomień o alertach, zobacz [Odbieranie powiadomień o alertach](../azure-monitor/alerts/alerts-overview.md).

Aby dowiedzieć się więcej o elementach webhook i sposobach ich użycia z alertami, odwiedź stronę [Konfigurowanie elementu webhook w ramach alertu dotyczącego metryki platformy Azure](../azure-monitor/alerts/alerts-webhooks.md).

## <a name="next-steps"></a>Następne kroki

* Wizualizuj dzienniki liczników i zdarzeń przy użyciu [dzienników Azure monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Wizualizuj dziennik aktywności platformy Azure przy użyciu wpisu w blogu Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) .
* [Wyświetlanie i analizowanie dzienników aktywności platformy Azure w Power BI i więcej wpisów w](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogu.

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