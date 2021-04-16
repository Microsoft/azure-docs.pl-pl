---
title: Azure Monitor metryki dla Application Gateway
description: Dowiedz się, jak używać metryk do monitorowania wydajności bramy aplikacji
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: azhussai
ms.openlocfilehash: 3baaf49cb3d1c8c5502d96974f9729d05996c75b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519892"
---
# <a name="metrics-for-application-gateway"></a>Metryki dla Application Gateway

Application Gateway publikuje punkty danych, nazywane metrykami, [Azure Monitor](../azure-monitor/overview.md) na temat wydajności Application Gateway i wystąpień zaplecza. Te metryki są wartościami liczbowych w uporządkowanym zestawie danych szeregów czasowych, które opisują jakiś aspekt bramy aplikacji w określonym czasie. Jeśli istnieją żądania przepływające przez Application Gateway, mierzy i wysyła swoje metryki w 60-sekundowych odstępach czasu. Jeśli nie ma żadnych żądań przepływających przez Application Gateway danych dla metryki, metryka nie zostanie zgłoszona. Aby uzyskać więcej informacji, [zobacz Azure Monitor metryki.](../azure-monitor/essentials/data-platform-metrics.md)

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metryki obsługiwane przez Application Gateway V2 SKU

### <a name="timing-metrics"></a>Metryki chronometrażu

Application Gateway udostępnia kilka wbudowanych metryk chronometrażu związanych z żądaniem i odpowiedzią, które są mierzone w milisekundach. 

![Diagram metryk chronometrażu dla Application Gateway.](./media/application-gateway-metrics/application-gateway-metrics.jpg)

> [!NOTE]
>
> Jeśli istnieje więcej niż jeden odbiornik w Application Gateway, zawsze  filtruj według wymiaru odbiornika, porównując różne metryki opóźnień, aby uzyskać zrozumiałe wnioskowanie.

- **Czas połączenia z zaplecza**

  Czas spędzony na nawiązaniu połączenia z aplikacją zaplecza. 

  Obejmuje to opóźnienie sieci, a także czas używany przez stos TCP serwera zaplecza do nawiązywania nowych połączeń. W przypadku protokołu TLS obejmuje również czas potrzebny na uzgodnienie. 

- **Czas pierwszego bajtu odpowiedzi zaplecza**

  Przedział czasu między rozpoczęciem nawiązywania połączenia z serwerem zaplecza a odebraniem pierwszego bajtu nagłówka odpowiedzi. 

  Jest to przybliżona suma czasu połączenia z *zaplecza,* czasu żądania dotarcia z zaplecza z bazy danych Application Gateway, czasu odpowiedzi aplikacji zaplecza (czasu, który serwer trwał do wygenerowania zawartości, potencjalnie pobrania zapytań bazy danych) oraz czasu pierwszego bajtu odpowiedzi na dotarcie do Application Gateway z zaplecza.

- **Czas ostatniego bajtu odpowiedzi zaplecza**

  Przedział czasu między rozpoczęciem nawiązywania połączenia z serwerem zaplecza a odebraniem ostatniego bajtu treści odpowiedzi. 

  Jest to w przybliżeniu suma *czasu odebrania z zaplecza pierwszego bajtu odpowiedzi* oraz czasu transferu danych (ta liczba może się znacząco różnić w zależności od rozmiaru żądanych obiektów oraz opóźnienia w sieci serwera).

- **Łączny czas bramy aplikacji**

  Średni czas, który zajmuje otrzymanie, przetworzenie żądania i wysłania jego odpowiedzi. 

  Jest to interwał od Application Gateway od pierwszego bajtu żądania HTTP do czasu wysłania ostatniego bajtu odpowiedzi do klienta. Obejmuje to czas przetwarzania Application Gateway, czas ostatniego bajtu odpowiedzi *zaplecza,* czas Application Gateway wysłania całej odpowiedzi i czas *RTT klienta*.

- **RTT klienta**

  Średni czas rundy między klientami a Application Gateway.



Te metryki mogą służyć do określenia, czy zaobserwowane spowolnienie jest spowodowane siecią klienta, wydajnością usługi Application Gateway, nasyceniem stosu TCP sieci zaplecza i serwera zaplecza, wydajnością aplikacji zaplecza lub dużym rozmiarem pliku.

Jeśli na przykład istnieje skok  trend czasu pierwszego bajtu odpowiedzi  zaplecza, ale trend czasu połączenia z zaplecza jest stabilny, można wywnioskować, że opóźnienie zaplecza usługi Application Gateway i czas do nawiązania połączenia są stabilne, a wzrost jest spowodowany wzrostem czasu odpowiedzi aplikacji zaplecza. Z drugiej strony, jeśli  wzrost czasu pierwszego bajtu odpowiedzi zaplecza jest skojarzony z odpowiadającym mu skokiem czasu połączenia z *zaplecza,* można wywnioskować, że przepełnił się stos TCP sieci między serwerem Application Gateway i serwerem zaplecza lub serwerem zaplecza. 

Jeśli zauważysz wzrost  czasu ostatniego bajtu odpowiedzi  zaplecza, ale czas pierwszego bajtu odpowiedzi zaplecza jest stabilny, można wywnioskować, że wzrost jest spowodowany większym żądanym plikiem.

Podobnie, jeśli  łączny czas bramy aplikacji  ma skok, ale czas ostatniego bajtu odpowiedzi zaplecza jest stabilny, może to oznaczać wąskie gardło wydajności na Application Gateway lub wąskie gardło w sieci między klientem a Application Gateway. Ponadto jeśli czas *RTT* klienta również ma odpowiedni wzrost, oznacza to, że spadek wynika z sieci między klientem a Application Gateway.

### <a name="application-gateway-metrics"></a>Application Gateway metryki

W Application Gateway dostępne są następujące metryki:

- **Odebrane bajty**

   Liczba bajtów odebranych przez Application Gateway od klientów

- **Wysłane bajty**

   Liczba bajtów wysłanych przez Application Gateway do klientów

- **Protokół TLS klienta**

   Liczba żądań TLS i innych niż TLS zainicjowanych przez klienta, który nawiąował połączenie z Application Gateway. Aby wyświetlić dystrybucję protokołu TLS, filtruj według wymiaru Protokół TLS.

- **Jednostki bieżącej wydajności**

   Liczba jednostek wydajności użytych do równoważenia obciążenia ruchu. Istnieją trzy czynniki determinujące jednostkę pojemności — jednostkę obliczeniową, połączenia trwałe i przepływność. Każda jednostka pojemności składa się co najwyżej z: 1 jednostki obliczeniowej, 2500 połączeń trwałych lub przepływności 2,22 Mb/s.

- **Bieżące jednostki obliczeniowe**

   Liczba zużytej pojemności procesora. Czynniki wpływające na jednostki obliczeniowe to liczba połączeń TLS/s, obliczenia ponownego zapisu adresu URL oraz przetwarzanie reguł zapory aplikacji internetowej. 

- **Bieżące połączenia**

   Łączna liczba aktywnych połączeń współbieżnych od klientów do Application Gateway
   
- **Szacowane rozliczane jednostki wydajności**

  W przypadku jednostki SKU w wersji 2 model cen zależy od użycia. Jednostki wydajności mierzą koszt w zależności od użycia, naliczany dodatkowo wobec kosztu stałego. *Szacowane rozliczane jednostki pojemności* wskazują liczbę jednostek wydajności, przy użyciu których szacowane jest rozliczanie. Oblicza się je na podstawie większej z dwóch wartości: liczby *jednostek bieżącej wydajności* (jednostek wydajności wymaganych do równoważenia obciążenia ruchu) oraz *jednostek stałej wydajności rozliczanej* (minimalnej aprowizowanej liczby jednostek wydajności).

- **Żądania nieudane**

  Liczba żądań, które Application Gateway z kodami błędów serwera 5xx. Obejmuje to kody 5xx generowane na podstawie Application Gateway, a także kody 5xx generowane z zaplecza. Liczbę żądań można dodatkowo filtrować, aby pokazać liczbę dla każdej/określonej kombinacji ustawienia puli zaplecza i protokołu HTTP.
   
- **Jednostki stałej wydajności rozliczanej**

  Minimalna aprowizowana liczba jednostek wydajności zgodnie z ustawieniem *Minimalna liczba jednostek skalowania* (jedno wystąpienie przekłada się na 10 jednostek wydajności) w konfiguracji usługi Application Gateway.
   
 - **Nowe połączenia na sekundę**

   Średnia liczba nowych połączeń TCP na sekundę ustanowionych od klientów do Application Gateway i z Application Gateway do składowych zaplecza.


- **Stan odpowiedzi**

   Stan odpowiedzi HTTP zwrócony przez Application Gateway. Rozkład kodu stanu odpowiedzi można dodatkowo skategoryzować w celu pokazania odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Przepływność**

   Liczba bajtów na sekundę, które Application Gateway obsługiwane

- **Łączna liczba żądań**

   Liczba pomyślnych żądań, które Application Gateway zostały obsłużyne. Liczbę żądań można dodatkowo filtrować, aby pokazać liczbę dla każdej/określonej kombinacji ustawienia puli zaplecza i protokołu HTTP.

### <a name="backend-metrics"></a>Metryki zaplecza

W Application Gateway dostępne są następujące metryki:

- **Stan odpowiedzi zaplecza**

  Liczba kodów stanu odpowiedzi HTTP zwracanych przez zaplecza. Nie obejmuje to żadnych kodów odpowiedzi generowanych przez Application Gateway. Rozkład kodu stanu odpowiedzi można dodatkowo skategoryzować w celu pokazania odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Liczba hostów w dobrej kondycji**

  Liczba za zaplecza określonych w dobrej kondycji przez sondę kondycji. Można filtrować według puli zaplecza, aby pokazać liczbę hostów w dobrej kondycji w określonej puli zaplecza.

- **Liczba hostów w złej kondycji**

  Liczba za zaplecza, które są określane jako w złej kondycji przez sondę kondycji. Można filtrować według puli zaplecza, aby pokazać liczbę hostów w złej kondycji w określonej puli zaplecza.
  
- **Żądania na minutę na hosta w dobrej kondycji**

  Średnia liczba żądań odebranych przez każdego członka w dobrej kondycji w puli zaplecza w ciągu minuty. Należy określić pulę zaplecza przy użyciu *wymiaru HttpSettings* puli zaplecza.  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metryki obsługiwane przez Application Gateway V1 SKU

### <a name="application-gateway-metrics"></a>Application Gateway metryki

W Application Gateway dostępne są następujące metryki:

- **Wykorzystanie procesora**

  Wyświetla użycie procesorów przydzielonych do usługi Application Gateway.  W normalnych warunkach użycie procesora nie powinno regularnie przekraczać 90%, ponieważ może to powodować opóźnienie w witrynach internetowych hostowanych za bramą aplikacji i zakłócać środowisko klienta. Można pośrednio kontrolować lub poprawić użycie procesora, modyfikując konfigurację usługi Application Gateway w celu zwiększenia liczby wystąpień, przechodząc na większy rozmiar jednostki SKU lub stosując oba rozwiązania jednocześnie.

- **Bieżące połączenia**

  Liczba bieżących połączeń ustanowionych za pomocą Application Gateway

- **Żądania nieudane**

  Liczba żądań, które nie powiodły się z powodu problemów z połączeniem. Ta liczba obejmuje żądania, które nie powiodły się z powodu przekroczenia ustawienia HTTP "Limit czasu żądania" i żądania, które nie powiodły się z powodu problemów z połączeniem między usługą Application Gateway i zaplecza. Ta liczba nie obejmuje błędów z powodu braku dostępnego zaplecza w dobrej kondycji. Odpowiedzi 4xx i 5xx z zaplecza również nie są traktowane jako część tej metryki.

- **Stan odpowiedzi**

  Stan odpowiedzi HTTP zwrócony przez Application Gateway. Rozkład kodu stanu odpowiedzi można dodatkowo skategoryzować w celu pokazania odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Przepływność**

  Liczba bajtów na sekundę, Application Gateway została Application Gateway

- **Łączna liczba żądań**

  Liczba pomyślnych żądań, które Application Gateway obsłużył. Liczbę żądań można dodatkowo filtrować, aby pokazać liczbę dla każdej/określonej kombinacji ustawienia puli zaplecza i protokołu HTTP.

- **Web Application Firewall liczba zablokowanych żądań**
- **Web Application Firewall dystrybucja zablokowanych żądań**
- **Web Application Firewall łącznej dystrybucji reguł**

### <a name="backend-metrics"></a>Metryki zaplecza

W Application Gateway dostępne są następujące metryki:

- **Liczba hostów w dobrej kondycji**

  Liczba za zaplecza, które są określane jako w dobrej kondycji przez sondę kondycji. Możesz filtrować według puli zaplecza, aby pokazać liczbę hostów w dobrej kondycji w określonej puli zaplecza.

- **Liczba hostów w złej kondycji**

  Liczba za zaplecza, które są określane jako w złej kondycji przez sondę kondycji. Możesz filtrować według puli zaplecza, aby pokazać liczbę hostów w złej kondycji w określonej puli zaplecza.

## <a name="metrics-visualization"></a>Wizualizacja metryk

Przejdź do bramy aplikacji, a następnie w **obszarze Monitorowanie** wybierz **pozycję Metryki.** Aby wyświetlić dostępne wartości, wybierz listę rozwijaną **METRYKA**.

Na poniższej ilustracji przedstawiono przykład z trzema metrykami wyświetlanymi w ciągu ostatnich 30 minut:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Widok metryki." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

Aby wyświetlić bieżącą listę metryk, zobacz [Obsługiwane metryki](../azure-monitor/essentials/metrics-supported.md)z Azure Monitor .

### <a name="alert-rules-on-metrics"></a>Reguły alertów dotyczące metryk

Reguły alertów można uruchamiać na podstawie metryk dla zasobu. Na przykład alert może wywołać element webhook lub wysłać wiadomość e-mail do administratora, jeśli przepływność bramy aplikacji jest powyżej, poniżej lub na poziomie progu w określonym przedziale czasu.

Poniższy przykład umożliwia utworzenie reguły alertu, która wysyła wiadomość e-mail do administratora po przekroczeniu progu przepływności:

1. Wybierz **pozycję Dodaj alert metryki,** aby otworzyć stronę **Dodawanie** reguły. Możesz również przejść do tej strony ze strony metryk.

   ![Przycisk "Dodaj alert metryki"][6]

2. Na stronie **Dodawanie reguły** wypełnij sekcje nazwa, warunek i powiadomienie, a następnie wybierz przycisk **OK.**

   * W **selektorze** Warunek wybierz jedną z czterech wartości: **Większe** niż , **Większe** niż lub równe, **Mniejsze niż** lub równe . 

   * W **selektorze** Okres wybierz okres od pięciu minut do sześciu godzin.

   * W przypadku wybrania **opcji Właściciele, współautorzy** i czytelnicy wiadomości e-mail mogą być dynamiczne w zależności od użytkowników, którzy mają dostęp do tego zasobu. W przeciwnym razie możesz podać rozdzielaną przecinkami listę użytkowników w polu **Dodatkowe adresy e-mail** administratora.

   ![Strona Dodawania reguły][7]

Jeśli próg zostanie naruszony, zostanie nadejdzie wiadomość e-mail podobna do tej na poniższej ilustracji:

![Wiadomość e-mail dla naruszonego progu][8]

Po utworzeniu alertu metryki zostanie wyświetlona lista alertów. Zawiera on omówienie wszystkich reguł alertów.

![Lista alertów i reguł][9]

Aby dowiedzieć się więcej na temat powiadomień o alertach, [zobacz Receive alert notifications (Otrzymywanie powiadomień o alertach).](../azure-monitor/alerts/alerts-overview.md)

Aby dowiedzieć się więcej o elementach webhook i o tym, jak można ich używać z alertami, odwiedź stronę Configure a webhook on an Azure metric alert (Konfigurowanie element [webhook w alercie metryki platformy Azure).](../azure-monitor/alerts/alerts-webhooks.md)

## <a name="next-steps"></a>Następne kroki

* Wizualizowanie dzienników liczników [i zdarzeń przy użyciu Azure Monitor dzienników .](../azure-monitor/insights/azure-networking-analytics.md)
* [Wizualizowanie dziennika aktywności platformy Azure Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) wpis w blogu.
* [Wyświetl i analizuj dzienniki aktywności platformy Azure Power BI wpis w](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogu.

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
