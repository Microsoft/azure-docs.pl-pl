---
title: Tryb usługi w usłudze Azure Signal Service
description: Omówienie różnych trybów usługi w usłudze Azure Signal, wyjaśnij różnice i odpowiednie scenariusze użytkownika
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 60f1ab0440120cb9a96e6c05a4fc1987ead29188
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143260"
---
# <a name="service-mode-in-azure-signalr-service"></a>Tryb usługi w usłudze Azure Signal Service

Tryb usługi jest ważnym pojęciem w usłudze Azure Signal Service. Podczas tworzenia nowego zasobu sygnalizującego zostanie wyświetlony monit o określenie trybu usługi:

:::image type="content" source="media/concept-service-mode/create.png" alt-text="Wybierz tryb usługi podczas tworzenia":::

Możesz również zmienić ją później w menu Ustawienia:

:::image type="content" source="media/concept-service-mode/update.png" alt-text="Wybierz tryb usługi podczas tworzenia":::

Usługa Azure sygnalizująca obecnie obsługuje trzy tryby usługi: **domyślne**, **bezserwerowe** i **klasyczne**. Zasób sygnalizujący będzie zachowywać się inaczej w różnych trybach. Ten artykuł zawiera informacje o różnicach i sposobach wybierania odpowiedniego trybu usługi w oparciu o twój scenariusz.

## <a name="default-mode"></a>Tryb domyślny

Domyślny tryb jest wartością domyślną dla trybu usługi podczas tworzenia nowego zasobu sygnalizującego. W tym trybie aplikacja działa jako Typowa aplikacja sygnalizująca ASP.NET Core (lub ASP.NET), w której znajduje się serwer sieci Web, który hostuje centrum (o nazwie serwer centralny dalej), a klienci mogą komunikować się z serwerem centralnym w czasie rzeczywistym. Jedyną różnicą jest zamiast bezpośredniego łączenia klienta i serwera, klient i serwer programu łączą się z usługą sygnalizującą i używają usługi jako serwera proxy. Poniżej znajduje się diagram, który ilustruje typową strukturę aplikacji w trybie domyślnym:

:::image type="content" source="media/concept-service-mode/default.png" alt-text="Wybierz tryb usługi podczas tworzenia":::

Dlatego jeśli masz aplikację sygnalizującą i chcesz zintegrować ją z usługą sygnalizująca, w większości przypadków należy wybrać tryb domyślny.

### <a name="connection-routing-in-default-mode"></a>Routing połączeń w trybie domyślnym

W trybie domyślnym połączenia protokołu WebSocket są nawiązywane między serwerem centralnym a usługą sygnalizującej (nazywane połączeniami serwera). Te połączenia są używane do przesyłania komunikatów między serwerem a klientem. Gdy nowy klient jest połączony, usługa sygnalizująca kieruje klienta do jednego serwera Hub (Załóżmy, że masz więcej niż jeden serwer) za pomocą istniejących połączeń serwera. Po jego okresie istnienia połączenie z klientem zostanie nastąpić do tego samego serwera centrum. Gdy klient wysyła komunikaty, zawsze przechodzi do tego samego serwera centrum. W przypadku tego zachowania można bezpiecznie zachować niektóre Stany dla poszczególnych połączeń na serwerze centrum. Jeśli na przykład chcesz przesłać strumieniowo coś między serwerem a klientem, nie musisz uwzględniać przypadku, w którym pakiety danych są przekazywane do różnych serwerów.

> [!IMPORTANT]
> Oznacza to również, że klient trybu domyślnego nie może nawiązać połączenia bez wcześniejszego połączenia z serwerem. Jeśli wszystkie serwery centrów zostaną rozłączone z powodu przerwy w działaniu sieci lub ponownego uruchomienia serwera, nastąpi próba nawiązania połączenia przez klienta z informacją o braku połączenia z serwerem. Dlatego należy się upewnić, że w dowolnym momencie istnieje co najmniej jeden serwer centralny podłączony do usługi sygnalizującej (na przykład ma wiele serwerów centrów i upewnij się, że nie przejdzie do trybu offline w tym samym czasie, w którym jest to konserwacja).

Ten model routingu oznacza również, gdy serwer centralny przejdzie w tryb offline, połączenia kierowane do tego serwera zostaną usunięte. Dlatego należy oczekiwać, że połączenie jest porzucane, gdy serwer centralny jest w trybie offline na potrzeby konserwacji i prawidłowo ponownie nawiąże połączenie, aby nie miał negatywnego wpływu na aplikację.

## <a name="serverless-mode"></a>Tryb bezserwerowy

Tryb bezserwerowy, zgodnie z którym wskazuje, jest trybem, który nie może mieć żadnego serwera Hub. Porównywanie z trybem domyślnym w tym trybie klient nie wymaga, aby serwer centralny mógł nawiązać połączenie. Wszystkie połączenia są połączone z usługą w trybie "bezserwerowym" i usługa jest odpowiedzialna za obsługę połączeń klienta, takich jak obsługa poleceń ping klienta (w trybie domyślnym jest to obsługiwane przez serwery centrów).

Ponadto w tym trybie nie ma połączenia z serwerem (Jeśli spróbujesz użyć zestawu SDK usługi do ustanowienia połączenia z serwerem, zostanie wyświetlony komunikat o błędzie). W związku z tym nie ma także routingu połączenia i klienta serwera lepkość (zgodnie z opisem w sekcji tryb domyślny). Mimo że aplikacja po stronie serwera może być w stanie wypychania komunikatów do klientów. Można to zrobić na dwa sposoby, używać [interfejsów API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) do wysyłania jednorazowego lub za pomocą połączenia protokołu WebSocket, dzięki czemu można szybciej wysyłać wiele komunikatów (należy zauważyć, że połączenie z użyciem protokołu WebSocket jest inne niż połączenie z serwerem).

> [!NOTE]
> Interfejs API REST i Metoda WebSocket są obsługiwane w [zestawie SDK zarządzania](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md)usługą. Jeśli używasz języka innego niż .NET, możesz również ręcznie wywołać interfejsy API REST, postępując zgodnie z tą [specyfikacją](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md).
>
> Jeśli używasz Azure Functions, możesz użyć [powiązań usługi sygnałów dla Azure Functions](../azure-functions/functions-bindings-signalr-service.md) (zwanych dalej wiązaniem funkcji) do wysyłania komunikatów jako powiązania danych wyjściowych.

Istnieje również możliwość, aby aplikacja serwera odbierał komunikaty i zdarzenia połączenia od klientów. Usługa będzie dostarczać komunikaty i zdarzenia połączeń do wstępnie skonfigurowanych punktów końcowych (nazywanych jako nadrzędnych) za pomocą elementów webhook. Porównanie z trybem domyślnym nie gwarantuje, że żądania lepkość i HTTP mogą być mniej wydajne niż połączenia protokołu WebSocket.

Aby uzyskać więcej informacji na temat konfigurowania nadrzędnego, zobacz ten [dokument](./concept-upstream.md).

Poniżej znajduje się diagram, który ilustruje sposób działania trybu bezserwerowego:

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="Wybierz tryb usługi podczas tworzenia":::

> [!NOTE]
> Należy pamiętać, że w trybie domyślnym można także użyć interfejsu API REST/zarządzanie SDK/funkcja, aby bezpośrednio wysyłać komunikaty do klienta, jeśli nie chcesz przechodzić przez serwer centrum. Jednak połączenia klienta w trybie domyślnym nadal są obsługiwane przez serwery centrów, a tryb nadrzędny nie będzie działał w tym trybie.

## <a name="classic-mode"></a>Tryb klasyczny

Klasyczny jest trybem mieszanym i trybem bezserwerowym. W tym trybie tryb połączenia jest określany przez określenie, czy serwer centralny jest połączony po nawiązaniu połączenia z klientem. Jeśli jest serwer centralny, połączenie z klientem zostanie przekazane do serwera centralnego. W przeciwnym razie wprowadzi tryb bezserwerowy, w którym komunikat klient do serwera nie może zostać dostarczony do serwera centrum. Spowoduje to pewne rozbieżności, na przykład jeśli wszystkie serwery Hub są niedostępne przez krótki czas, wszystkie połączenia klienta utworzone w tym czasie będą w trybie bezserwerowym i nie mogą wysyłać komunikatów do serwera Hub.

> [!NOTE]
> Tryb klasyczny jest przeznaczony głównie do zapewnienia zgodności z poprzednimi wersjami dla tych aplikacji utworzonych przed trybem domyślnym i bezserwerowym. Zdecydowanie zaleca się, aby nie korzystać już z tego trybu. W przypadku nowych aplikacji wybierz opcję domyślne lub bezserwerowe na podstawie Twojego scenariusza. W przypadku istniejących aplikacji zaleca się również przejrzenie przypadków użycia i wybranie odpowiedniego trybu usługi.

Tryb klasyczny nie obsługuje również niektórych nowych funkcji, takich jak nadrzędny w trybie bezserwerowym.

## <a name="choose-the-right-service-mode"></a>Wybieranie odpowiedniego trybu usługi

Teraz należy zrozumieć różnice między trybami usług i wiedzieć, jak wybierać między nimi. Jak już znasz w poprzedniej sekcji, Tryb klasyczny nie jest wspierany i należy wybrać tylko między ustawieniem domyślnym i bezserwerowym. Poniżej przedstawiono kilka dodatkowych porad, które mogą pomóc w dokonaniu właściwego wyboru dla nowych aplikacji i wycofaniu klasycznego trybu dla istniejących aplikacji.

* Jeśli wiesz już, jak działa Biblioteka sygnałów i chcesz przejść od własnego sygnału do korzystania z usługi Azure Signal Service, wybierz tryb domyślny. Tryb domyślny działa tak samo jak w przypadku samodzielnego sygnalizowania (i można użyć tego samego modelu programowania w bibliotece sygnałów), usługa sygnalizująca działa jako serwer proxy między klientami a serwerami usługi Hub.

* Jeśli tworzysz nową aplikację i nie chcesz zarządzać połączeniami serwera i serwerów centrum, wybierz tryb bez serwera. Ten tryb zazwyczaj działa razem z Azure Functions, aby nie trzeba było w ogóle obsługiwać żadnego serwera. Nadal można korzystać z komunikacji dwukierunkowej (z interfejsem API REST/zarządzaniem SDK/funkcją Binding + a), ale model programowania będzie inny niż biblioteka sygnałów.

* Jeśli używasz obu serwerów centrów do zapewnienia połączeń klienckich i aplikacji zaplecza do bezpośredniego wypychania komunikatów do klientów (na przykład przy użyciu interfejsu API REST), nadal powinien być wybierany tryb domyślny. Należy pamiętać, że kluczową różnicą między domyślnym i bezserwerowym trybem jest to, czy masz serwery Hub i jak są kierowane połączenia klientów. W obu trybach można używać zestawu SDK interfejsu API REST/zarządzania i powiązania funkcji.

* Jeśli naprawdę masz scenariusz mieszany, na przykład masz dwa różne centra w tym samym zasobie sygnalizującym, jeden używany jako tradycyjne centrum sygnału, a drugi używany z Azure Functions i nie ma serwera Hub, należy rozważyć rozdzielenie ich na dwa zasoby sygnalizujące, jeden w trybie domyślnym i jeden w trybie bezserwerowym.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat używania trybu domyślnego i bezserwerowego, przeczytaj następujące artykuły:

* [Wewnętrzne elementy usługi Azure SignalR](signalr-concept-internals.md)

* [Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service](signalr-concept-serverless-development-config.md)