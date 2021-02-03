---
title: Monitorowanie aplikacji
description: Dowiedz się, jak monitorować aplikacje w Azure App Service przy użyciu Azure Portal. Zapoznaj się z raportowanymi przydziałami i metrykami.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 04/23/2020
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 587ad53fa3a4875f7019a8c094db1b9a5f096519
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509557"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorowanie aplikacji w Azure App Service
[Azure App Service](./overview.md) zapewnia wbudowaną funkcję monitorowania dla aplikacji sieci Web, urządzeń przenośnych i aplikacji interfejsu API w [Azure Portal](https://portal.azure.com).

W Azure Portal można sprawdzić *przydziały* i *metryki* dla aplikacji oraz planu *App Service i skonfigurować* metryki oparte na regułach *skalowania* automatycznego.

## <a name="understand-quotas"></a>Informacje o przydziałach

Aplikacje hostowane w App Service podlegają pewnym ograniczeniom dotyczącym zasobów, których mogą używać. Limity są definiowane przez plan App Service, który jest skojarzony z aplikacją.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Jeśli aplikacja jest hostowana w planie *bezpłatnym* lub *współdzielonym* , limity dla zasobów, które mogą być używane przez aplikację, są definiowane przez przydziały.

Jeśli aplikacja jest hostowana w planie w warstwie *podstawowa*, *standardowa* lub *Premium* , limity zasobów, których mogą używać są ustawiane przez *rozmiar* (mały, średni, duży) i *Liczba wystąpień* (1, 2, 3 i tak dalej) planu App Service.

Przydziały dla aplikacji bezpłatnych lub udostępnionych:

| limit przydziału | Opis |
| --- | --- |
| **PROCESOR (krótki)** | Ilość procesora CPU dozwolona dla tej aplikacji w interwale 5-minutowym. Ten przydział resetuje co pięć minut. |
| **PROCESOR (dzień)** | Całkowita ilość procesora CPU dozwolona dla tej aplikacji w ciągu dnia. Ten przydział resetuje co 24 godziny o północy czasu UTC. |
| **Pamięć** | Całkowita ilość pamięci dozwolonej dla tej aplikacji. |
| **Przepustowość** | Całkowita ilość wychodzącej przepustowości dozwolonej dla tej aplikacji w ciągu dnia. Ten przydział resetuje co 24 godziny o północy czasu UTC. |
| **Filesystem** | Łączna ilość dozwolonego miejsca w magazynie. |

Jedynym przydziałem stosowanym dla aplikacji hostowanych w warstwach *podstawowa*, *standardowa* i *Premium* jest system plików.

Aby uzyskać więcej informacji o określonych limitach przydziału, limitach i funkcjach dostępnych dla różnych jednostek SKU App Service, zobacz [limity usługi subskrypcji platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Wymuszanie przydziałów

Jeśli aplikacja przekroczy *procesor (krótki)*, *procesor (dzień)* lub limit *przepustowości* , aplikacja zostanie zatrzymana do momentu zresetowania limitu przydziału. W tym czasie wszystkie żądania przychodzące powodują błąd HTTP 403.

![komunikat o błędzie 403][http403]

W przypadku przekroczenia limitu przydziału pamięci aplikacji aplikacja zostanie zatrzymana tymczasowo.

W przypadku przekroczenia limitu przydziału systemu plików operacja zapisu nie powiedzie się. Błędy operacji zapisu obejmują wszystkie zapisy w dziennikach.

Można zwiększyć lub usunąć przydziały z aplikacji, uaktualniając plan App Service.

## <a name="understand-metrics"></a>Opis metryk

> [!NOTE]
> **Użycie systemu plików** to nowa Metryka, która jest rzutowana globalnie, nie jest oczekiwane, jeśli aplikacja nie jest hostowana w App Service Environment.
> 

> [!IMPORTANT]
> **Średni czas odpowiedzi** będzie przestarzały, aby uniknąć pomyłek w agregacji metryk. Użyj **czasu odpowiedzi** jako zamiennika.

> [!NOTE]
> Metryki dla aplikacji obejmują żądania do witryny SCM (kudu) aplikacji.  Obejmuje to żądania wyświetlenia logstream lokacji przy użyciu kudu.  Żądania Logstream mogą obejmować kilka minut, co wpłynie na metryki czasu żądania.  Użytkownicy powinni mieć świadomość tej relacji, gdy używają tych metryk z logiką skalowania automatycznego.
> 

Metryki zawierają informacje dotyczące aplikacji lub zachowania planu App Service.

W przypadku aplikacji dostępne są następujące metryki:

| Metric | Opis |
| --- | --- |
| **Czas odpowiedzi** | Czas potrzebny na obsługę żądań w aplikacji (w sekundach). |
| **Średni czas odpowiedzi (przestarzałe)** | Średni czas, w którym aplikacja będzie obsługiwała żądania (w sekundach). |
| **Średni zestaw roboczy pamięci** | Średnia ilość pamięci używanej przez aplikację w megabajtach (MiB). |
| **Połączenia** | Liczba powiązanych gniazd istniejących w piaskownicy (w3wp.exe i jej procesów podrzędnych).  Powiązane gniazdo jest tworzone przez wywoływanie interfejsów API bind ()/Connect () i pozostanie do momentu zamknięcia wskazanego gniazda przy użyciu funkcji CloseHandle ()/closesocket (). |
| **Czas procesora CPU** | Ilość procesora CPU zużywanego przez aplikację w sekundach. Aby uzyskać więcej informacji na temat tej metryki, zobacz [czas procesora CPU w porównaniu z wartością procentową procesora](#cpu-time-vs-cpu-percentage)CPU. |
| **Bieżące zestawy** | Bieżąca liczba zestawów załadowanych we wszystkich domenach aplikacji. |
| **Dane w** | Ilość przychodzącej przepustowości zużywanej przez aplikację w usłudze MiB. |
| **Dane wychodzące** | Ilość wychodzącej przepustowości zużywanej przez aplikację w usłudze MiB. |
| **Użycie systemu plików** | Ilość użycia w bajtach według udziału magazynu. |
| **Zbieranie elementów bezużytecznych generacji 0** | Liczba obiektów generacji 0, które są zbierane jako elementy bezużyteczne od momentu rozpoczęcia procesu aplikacji. Wyższa generacja operacje odzyskiwania pamięci obejmuje wszystkie niższe operacje odzyskiwania pamięci generacji.|
| **Wyrzucanie elementów bezużytecznych generacji 1** | Liczba obiektów generacji 1, które są zbierane jako elementy bezużyteczne od momentu rozpoczęcia procesu aplikacji. Wyższa generacja operacje odzyskiwania pamięci obejmuje wszystkie niższe operacje odzyskiwania pamięci generacji.|
| **Zbieranie elementów bezużytecznych generacji 2** | Liczba obiektów generacji 2, które są zbierane jako elementy bezużyteczne od momentu rozpoczęcia procesu aplikacji.|
| **Liczba dojść** | Łączna liczba dojść aktualnie otwartych przez proces aplikacji.|
| **Stan sprawdzania kondycji** | Średni stan kondycji w wystąpieniach aplikacji w planie App Service.|
| **Http 2xx** | Liczba żądań, które wynikają z kodu stanu HTTP ≥ 200, ale < 300. |
| **Http 3xx** | Liczba żądań, które wynikają z kodu stanu HTTP ≥ 300, ale < 400. |
| **HTTP 401** | Liczba żądań, które wynikają z kodu stanu HTTP 401. |
| **HTTP 403** | Liczba żądań, które wynikają z kodu stanu HTTP 403. |
| **HTTP 404** | Liczba żądań, które wynikają z kodu stanu HTTP 404. |
| **Http 406** | Liczba żądań, które wynikają z kodu stanu HTTP 406. |
| **Http 4xx** | Liczba żądań, które wynikają z kodu stanu HTTP ≥ 400, ale < 500. |
| **Błędy serwera http** | Liczba żądań, które wynikają z kodu stanu HTTP ≥ 500, ale < 600. |
| **Inne bajty we/wy na sekundę** | Szybkość, z jaką proces aplikacji wysyła bajty do operacji we/wy, które nie obejmują danych, takich jak operacje sterowania.|
| **Inne operacje we/wy na sekundę** | Szybkość, z jaką proces aplikacji wysyła operacje we/wy, które nie są operacjami odczytu lub zapisu.|
| **Bajty odczytu we/wy na sekundę** | Szybkość, z jaką proces aplikacji odczytuje bajty z operacji we/wy.|
| **Operacje odczytu we/wy na sekundę** | Szybkość, z jaką proces aplikacji wystawia operacje we/wy odczytu.|
| **Bajty zapisu we/wy na sekundę** | Szybkość, z jaką proces aplikacji zapisuje bajty w operacjach we/wy.|
| **Operacje zapisu we/wy na sekundę** | Szybkość, z jaką proces aplikacji wysyła operacje we/wy zapisu.|
| **Zestaw roboczy pamięci** | Bieżąca ilość pamięci używanej przez aplikację w usłudze MiB. |
| **Bajty prywatne** | Bajty prywatne to bieżący rozmiar (w bajtach) pamięci przydzielonej przez proces aplikacji, która nie może być współużytkowana z innymi procesami.|
| **Żądania** | Całkowita liczba żądań niezależnie od ich wyników w kodzie stanu HTTP. |
| **Żądania w kolejce aplikacji** | Liczba żądań w kolejce żądań aplikacji.|
| **Liczba wątków** | Liczba wątków aktywnych obecnie w procesie aplikacji.|
| **Łączna liczba domen aplikacji** | Bieżąca liczba domen aplikacji załadowanych w tej aplikacji.|
| **Całkowita liczba zwolnionych domen aplikacji** | Całkowita liczba elementów AppDomain zwolnionych od początku aplikacji.|


W przypadku planu App Service dostępne są następujące metryki:

> [!NOTE]
> Metryki planu App Service są dostępne tylko w przypadku planów w warstwach *podstawowa*, *standardowa* i *Premium* .
> 

| Metric | Opis |
| --- | --- |
| **Procent użycia procesora CPU** | Średni procesor używany przez wszystkie wystąpienia planu. |
| **Procent pamięci** | Średnia pamięć użyta we wszystkich wystąpieniach planu. |
| **Dane w** | Średnia przepustowość przychodząca używana przez wszystkie wystąpienia planu. |
| **Dane wychodzące** | Średnia wychodząca przepustowość używana we wszystkich wystąpieniach planu. |
| **Długość kolejki dysku** | Średnia liczba żądań odczytu i zapisu, które zostały dodane do kolejki w magazynie. Duża długość kolejki dysku to wskazanie aplikacji, która może ulec spowolnieniu z powodu nadmiernej liczby operacji we/wy dysku. |
| **Długość kolejki http** | Średnia liczba żądań HTTP, które musiały zostać obsłużone w kolejce przed ich ukończeniem. Duża lub większa długość kolejki HTTP jest objawem planu pod dużym obciążeniem. |

### <a name="cpu-time-vs-cpu-percentage"></a>Czas procesora (%) w porównaniu z procesorem CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Istnieją dwie metryki odzwierciedlające użycie procesora CPU:

**Czas procesora**: przydatne w przypadku aplikacji hostowanych w planach bezpłatnych lub wspólnych, ponieważ jeden z ich przydziałów jest zdefiniowany w minutach procesora używanego przez aplikację.

**Procent użycia procesora CPU**: przydatne w przypadku aplikacji hostowanych w planach Basic, standard i Premium, ponieważ mogą one być skalowane w poziomie. Wartość procentowa procesora CPU jest dobrym wskaźnikiem ogólnego użycia we wszystkich wystąpieniach.

## <a name="metrics-granularity-and-retention-policy"></a>Zasady szczegółowości i przechowywania metryk
Metryki dla aplikacji i planu usługi App Service są rejestrowane i agregowane przez usługę i [przechowywane zgodnie z tymi regułami](../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics).

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorowanie przydziałów i metryk w Azure Portal
Aby sprawdzić stan różnych przydziałów i metryk, które mają wpływ na aplikację, przejdź do [Azure Portal](https://portal.azure.com).

![Przydziały wykresu w Azure Portal][quotas]

Aby znaleźć przydziały, wybierz pozycję **Ustawienia**  >  **limity przydziału**. Na wykresie można przejrzeć: 
1. Nazwa przydziału.
1. Jego interwał resetowania.
1. Bieżący limit.
1. Jego bieżąca wartość.

![Wykres pomiarowy w Azure Portal ][metrics] można uzyskać dostęp do metryk bezpośrednio z poziomu strony **Przegląd** zasobów. Tutaj zobaczysz wykresy przedstawiające niektóre metryki aplikacji.

Kliknięcie dowolnego z tych wykresów spowoduje przejście do widoku metryk, w którym można tworzyć niestandardowe wykresy, wysyłać zapytania o różne metryki i wiele innych. 

Aby dowiedzieć się więcej o metrykach, zobacz [monitorowanie metryk usług](../azure-monitor/platform/data-platform.md).

## <a name="alerts-and-autoscale"></a>Alerty i automatyczne skalowanie
Metryki dla aplikacji lub planu App Service mogą być podłączane do alertów. Aby uzyskać więcej informacji, zobacz [Receive alert notifications](../azure-monitor/platform/alerts-classic-portal.md) (Otrzymywanie powiadomień o alertach).

App Service aplikacje hostowane w planach App Service w warstwie Podstawowa lub wyższa obsługują automatyczne skalowanie. Funkcja automatycznego skalowania umożliwia skonfigurowanie reguł, które monitorują metryki planu App Service. Reguły mogą zwiększyć lub zmniejszyć liczbę wystąpień, co może zapewnić dodatkowe zasoby w razie konieczności. Reguły mogą również ułatwić oszczędność pieniędzy, gdy aplikacja jest nadmiernie obsługiwana.

Aby uzyskać więcej informacji na temat automatycznego skalowania, zobacz [Jak skalować](../azure-monitor/platform/autoscale-get-started.md) i [najlepsze rozwiązania dotyczące Azure monitor skalowanie](../azure-monitor/platform/autoscale-best-practices.md)automatyczne.

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
