---
title: Strumienie urządzeń usługi Azure IoT Hub | Microsoft Docs
description: Omówienie strumieni urządzeń z systemem Azure IoT Hub, które ułatwiają bezpieczne dwukierunkowe tunele protokołu TCP dla różnych scenariuszy komunikacji między chmurą i urządzeniem.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 9487fc562fa099d2650aabc8d15fc1449c7fcb5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97825185"
---
# <a name="iot-hub-device-streams-preview"></a>Strumienie urządzeń IoT Hub (wersja zapoznawcza)

*Strumienie urządzeń* usługi Azure IoT Hub ułatwiają tworzenie bezpiecznych dwukierunkowych tuneli protokołu TCP dla różnych scenariuszy komunikacji między chmurą i urządzeniem. Strumień urządzenia jest korygowany przez *punkt końcowy przesyłania strumieniowego* IoT Hub, który działa jako serwer proxy między urządzeniami i punktami końcowymi usługi. Ten Instalator przedstawiony na poniższym diagramie jest szczególnie przydatny, gdy urządzenia znajdują się za zaporą sieci lub znajdują się w sieci prywatnej. W związku z tym, IoT Hub strumienie urządzenia ułatwiają klientom nawiązanie połączenia z urządzeniami IoT w sposób przyjazny dla zapory, bez konieczności rozległego otwierania portów przychodzących lub wychodzących zapory sieciowej.

!["IoT Hub Omówienie strumieni urządzeń"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Przy użyciu strumieni urządzeń IoT Hub urządzenia pozostają bezpieczne i będą musiały otwierać wychodzące połączenia TCP do punktu końcowego przesyłania strumieniowego usługi IoT Hub przez port 443. Po ustanowieniu strumienia aplikacje po stronie usług i po stronie urządzenia będą miały dostęp programistyczny do obiektu klienta WebSocket w celu wysyłania i odbierania nieprzetworzonych bajtów do siebie. Gwarancje niezawodności i porządkowania zapewniane przez ten tunel są z użyciem protokołu TCP.

## <a name="benefits"></a>Korzyści

IoT Hub strumienie urządzeń zapewniają następujące korzyści:

* **Bezpieczna łączność w zaporze:** Urządzenia IoT mogą być osiągalne z punktów końcowych usługi bez konieczności otwierania portu zapory ruchu przychodzącego na urządzeniach lub sieci (tylko łączność wychodząca do IoT Hub jest wymagana przez port 443).

* **Uwierzytelnianie:** Obydwie urządzenia i usługi w tunelu muszą uwierzytelniać się za pomocą IoT Hub przy użyciu odpowiednich poświadczeń.

* **Szyfrowanie:** Domyślnie strumienie urządzeń IoT Hub korzystają z połączeń z obsługą protokołu TLS. Gwarantuje to, że ruch jest zawsze szyfrowany niezależnie od tego, czy aplikacja używa szyfrowania, czy nie.

* **Prostota łączności:** W wielu przypadkach użycie strumieni urządzeń eliminuje konieczność stosowania złożonej konfiguracji wirtualnych sieci prywatnych w celu umożliwienia łączności z urządzeniami IoT.

* **Zgodność ze stosem TCP/IP:** Strumienie urządzeń IoT Hub mogą obsługiwać ruch aplikacji TCP/IP. Oznacza to, że można korzystać z tej funkcji w szerokim zakresie własności protokołów opartych na standardach.

* **Łatwość użycia w konfiguracjach sieci prywatnej:** Usługa może komunikować się z urządzeniem, odwołując się do jego identyfikatora urządzenia, a nie adresu IP urządzenia. Jest to przydatne w sytuacji, gdy urządzenie znajduje się w sieci prywatnej i ma prywatny adres IP lub jego adres IP jest przypisywany dynamicznie i jest nieznany po stronie usługi.

## <a name="device-stream-workflows"></a>Przepływy pracy strumienia urządzeń

Strumień urządzenia jest inicjowany, gdy usługa żąda połączenia z urządzeniem, podając jego identyfikator urządzenia. Ten przepływ pracy w szczególności mieści się w modelu komunikacji klient/serwer, w tym SSH i RDP, gdzie użytkownik zamierza zdalnie połączyć się z serwerem SSH lub RDP działającym na urządzeniu przy użyciu programu klienckiego SSH lub RDP.

Proces tworzenia strumienia urządzeń obejmuje negocjowanie między urządzeniem, usługą, głównymi punktami końcowymi i przesyłania strumieniowego usługi IoT Hub. Gdy główny punkt końcowy usługi IoT Hub organizuje tworzenie strumienia urządzenia, punkt końcowy przesyłania strumieniowego obsługuje ruch przepływający między usługą i urządzeniem.

### <a name="device-stream-creation-flow"></a>Przepływ tworzenia strumienia urządzeń

Programowe tworzenie strumienia urządzeń za pomocą zestawu SDK obejmuje następujące kroki, które również przedstawiono na poniższej ilustracji:

!["Proces uzgadniania strumienia urządzenia"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Aplikacja urządzenia rejestruje wywołanie zwrotne z wyprzedzeniem, aby otrzymywać powiadomienia o tym, kiedy nowy strumień urządzenia zostanie zainicjowany do urządzenia. Ten krok zazwyczaj odbywa się podczas uruchamiania urządzenia i łączenia się z IoT Hub.

2. Program po stronie usługi inicjuje strumień urządzenia w razie konieczności, podając identyfikator urządzenia (_nie_ adres IP).

3. Centrum IoT powiadamia program po stronie urządzenia przez wywołanie wywołania zwrotnego zarejestrowanego w kroku 1. Urządzenie może zaakceptować lub odrzucić żądanie zainicjowania strumienia. Ta logika może być specyficzna dla scenariusza aplikacji. Jeśli żądanie strumienia zostało odrzucone przez urządzenie, IoT Hub informuje odpowiednio usługę; w przeciwnym razie wykonaj poniższe kroki.

4. Urządzenie tworzy bezpieczne wychodzące połączenie TCP z punktem końcowym przesyłania strumieniowego przez port 443 i uaktualnia połączenie do protokołu WebSocket. Adres URL punktu końcowego przesyłania strumieniowego oraz poświadczenia służące do uwierzytelniania są przekazywane do urządzenia przez IoT Hub w ramach żądania wysłanego w kroku 3.

5. Usługa zostanie powiadomiona o wyniku przyjęcia strumienia przez urządzenie i będzie kontynuowała Tworzenie własnego klienta protokołu WebSocket do punktu końcowego przesyłania strumieniowego. Podobnie odbierze adres URL punktu końcowego przesyłania strumieniowego i informacje o uwierzytelnianiu z IoT Hub.

W powyższym procesie uzgadniania:

* Proces uzgadniania musi zakończyć się w ciągu 60 sekund (krok 2 do 5). w przeciwnym razie uzgadnianie zakończy się niepowodzeniem z upływem limitu czasu, a usługa zostanie odpowiednio powiadomiona.

* Po powyższym przepływie tworzenia strumienia punkt końcowy przesyłania strumieniowego będzie pełnić rolę serwera proxy i przetransferuje ruch między usługą i urządzeniem przez odpowiednie obiekty WebSockets.

* Urządzenia i usługi potrzebują łączności wychodzącej do głównego punktu końcowego IoT Hub, a także punktu końcowego przesyłania strumieniowego przez port 443. Adres URL tych punktów końcowych jest dostępny na karcie *Przegląd* w portalu IoT Hub.

* Gwarancje niezawodności i porządkowania ustalonego strumienia są z użyciem protokołu TCP.

* Wszystkie połączenia do IoT Hub i przesyłania strumieniowego punktu końcowego używają protokołu TLS i są szyfrowane.

### <a name="termination-flow"></a>Przepływ zakończenia

Ustanowiony strumień kończy się, gdy jedno z połączeń TCP z bramą jest odłączone (przez usługę lub urządzenie). Można to zrobić dobrowolnie, zamykając protokół WebSocket na urządzeniu lub w programie albo w sposób niezależny w przypadku limitu czasu lub awarii procesu łączności sieciowej. Po zakończeniu połączenia urządzenia lub usługi z punktem końcowym przesyłania strumieniowego inne połączenie TCP również zostanie zakończone (wymuszone), a usługa i urządzenie są odpowiedzialne za ponowne utworzenie strumienia, w razie potrzeby.

## <a name="connectivity-requirements"></a>Wymagania dotyczące łączności

Zarówno urządzenie, jak i po stronie usługi strumienia urządzenia musi mieć możliwość ustanowienia połączeń z obsługą protokołu TLS do IoT Hub i jego punktu końcowego przesyłania strumieniowego. Wymaga to łączności wychodzącej przez port 443 do tych punktów końcowych. Nazwę hosta skojarzoną z tymi punktami końcowymi można znaleźć na karcie *Przegląd* w IoT Hub, jak pokazano na poniższym rysunku:

!["Punkty końcowe strumienia urządzenia"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternatywnie informacje o punktach końcowych można pobrać przy użyciu interfejsu wiersza polecenia platformy Azure w sekcji Właściwości centrum, w tym w oddziałach `property.hostname` i `property.deviceStreams` kluczach.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

Dane wyjściowe to obiekt JSON wszystkich punktów końcowych, do których może być konieczne połączenie urządzenia i usługi centrum w celu ustanowienia strumienia urządzenia.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.57 lub nowszej. Najnowszą wersję można pobrać ze strony [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) .
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Zezwalaj na połączenia wychodzące z punktami końcowymi przesyłania strumieniowego urządzeń

Jak wspomniano na początku tego artykułu, urządzenie tworzy połączenie wychodzące do IoT Hub punktu końcowego przesyłania strumieniowego podczas procesu inicjowania strumieni urządzeń. Zapory na urządzeniu lub jego sieci muszą zezwalać na łączność wychodzącą z bramą przesyłania strumieniowego przez port 443 (należy zauważyć, że komunikacja odbywa się za pośrednictwem połączenia protokołu WebSocket zaszyfrowanego przy użyciu protokołu TLS).

Nazwę hosta punktu końcowego przesyłania strumieniowego urządzeń można znaleźć w portalu usługi Azure IoT Hub na karcie ![ Omówienie. Punkty końcowe strumienia urządzenia "](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Możesz również znaleźć te informacje za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.57 lub nowszej. Najnowszą wersję można pobrać ze strony [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) .
>

## <a name="troubleshoot-via-device-streams-resource-logs"></a>Rozwiązywanie problemów za pomocą dzienników zasobów strumieni urządzeń

Można skonfigurować Azure Monitor do zbierania [dzienników zasobów dla strumieni urządzeń](monitor-iot-hub-reference.md#device-streams-preview) emitowanych przez IoT Hub. Może to być bardzo przydatne w scenariuszach rozwiązywania problemów.

Wykonaj poniższe kroki, aby utworzyć ustawienie diagnostyki umożliwiające wysyłanie dzienników strumieni urządzeń dla IoT Hub do Azure Monitor dzienników:

1. W Azure Portal przejdź do centrum IoT Hub. W okienku po lewej stronie w obszarze **monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**. Następnie wybierz pozycję **Dodaj ustawienie diagnostyczne**.

2. Podaj nazwę dla ustawienia diagnostyki i wybierz pozycję **DeviceStreams** z listy dzienników. Następnie wybierz pozycję **Wyślij do log Analytics**. Będziesz mieć możliwość wybrania istniejącego obszaru roboczego Log Analytics lub utworzenia nowego.

    :::image type="content" source="media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png" alt-text="Włączanie dzienników strumieni urządzeń":::

3. Po utworzeniu ustawienia diagnostycznego w celu wysłania dzienników strumieni urządzeń do obszaru roboczego Log Analytics można uzyskać dostęp do dzienników, wybierając pozycję **dzienniki** w obszarze **monitorowanie** w lewym okienku centrum IoT w programie Azure Portal. Dzienniki strumieni urządzeń będą widoczne w `AzureDiagnostics` tabeli i mają `Category=DeviceStreams` . Należy pamiętać, że może upłynąć kilka minut po operacji, w której dzienniki będą widoczne w tabeli.

   Jak pokazano poniżej, tożsamość urządzenia docelowego i wynik operacji są również dostępne w dziennikach.

   !["Dostęp do dzienników strumieni urządzeń"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

Aby dowiedzieć się więcej o korzystaniu z Azure Monitor z IoT Hub, zobacz [Monitor IoT Hub](monitor-iot-hub.md). Informacje o wszystkich dziennikach zasobów, metrykach i tabelach dostępnych dla IoT Hub można znaleźć w temacie [monitoring Azure IoT Hub Data Reference](monitor-iot-hub-reference.md).

## <a name="regional-availability"></a>Dostępność w regionach

W publicznej wersji zapoznawczej IoT Hub strumienie urządzeń są dostępne w regionach Środkowe stany USA, środkowe stany USA — EUAP, Europa Północna i Azja Południowo-Wschodnia. Upewnij się, że tworzysz centrum w jednym z tych regionów.

## <a name="sdk-availability"></a>Dostępność zestawu SDK

Dwie strony każdego strumienia (po stronie urządzenia i usługi) używają zestawu SDK IoT Hub do ustanowienia tunelu. W publicznej wersji zapoznawczej klienci mogą wybierać spośród następujących języków zestawu SDK:

* Strumienie urządzenia obsługi zestawu SDK C i C# po stronie urządzenia.

* Zestaw SDK NodeJS i C# obsługuje strumienie urządzeń po stronie usługi.

## <a name="iot-hub-device-stream-samples"></a>Przykłady IoT Hub strumienia urządzeń

Na stronie IoT Hub są dostępne dwa [przykłady przewodnika Szybki Start](./index.yml) . Przedstawiają one użycie strumieni urządzeń przez aplikacje.

* Przykład *ECHA* ilustruje programistyczne wykorzystanie strumieni urządzeń (przez bezpośrednie wywołanie interfejsu API zestawu SDK).

* W przykładzie *lokalnego serwera proxy* zademonstrowano tunelowanie ruchu aplikacji klienta/serwera (na przykład SSH, RDP lub sieci Web) poza szczytem za pośrednictwem strumieni urządzeń.

Te przykłady zostały omówione bardziej szczegółowo poniżej.

### <a name="echo-sample"></a>Echo — przykład

Przykład ECHA ilustruje programistyczne wykorzystanie strumieni urządzeń do wysyłania i odbierania bajtów między aplikacjami usługi i urządzeń. Należy pamiętać, że można używać programów usługi i urządzeń w różnych językach. Można na przykład użyć programu urządzenia C z programem usługi C#.

Oto przykłady ECHA:

* [Program usług i usług w języku C#](quickstart-device-streams-echo-csharp.md)

* [ ProgramNode.js Service](quickstart-device-streams-echo-nodejs.md)

* [Program urządzenia C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Przykład lokalnego serwera proxy (dla protokołu SSH lub RDP)

W przykładzie lokalnego serwera proxy przedstawiono sposób włączania tunelowania ruchu istniejącej aplikacji, który obejmuje komunikację między klientem a programem serwera. Ta konfiguracja działa dla protokołów klient/serwer, takich jak SSH i RDP, gdzie po stronie usługi działa jako klient (z uruchomionymi programami klienta SSH lub RDP), a po stronie urządzenia działa jako serwer (z uruchomionym programem SSH lub serwerem RDP).

W tej sekcji opisano użycie strumieni urządzeń w celu umożliwienia użytkownikowi SSH na urządzeniu za pośrednictwem strumieni urządzeń (przypadek dla protokołu RDP lub innej aplikacji klienta/serwera jest podobny przy użyciu odpowiedniego portu protokołu).

Instalator korzysta z dwóch *lokalnych programów serwera proxy* przedstawionych na poniższym rysunku, mianowicie *serwera proxy lokalnego urządzenia* i *lokalnego serwera proxy usługi*. Lokalne programy proxy są odpowiedzialne za wykonywanie [uzgadniania inicjacji strumienia urządzenia](#device-stream-creation-flow) z IoT Hub i współdziałanie z klientem SSH oraz demonem SSH przy użyciu zwykłych gniazd klienta/serwera.

!["Konfiguracja serwera proxy usługi Device Stream dla protokołu SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. Użytkownik uruchamia lokalny serwer proxy usługi, aby zainicjować strumień urządzenia do urządzenia.

2. Lokalny serwer proxy urządzenia akceptuje żądanie inicjacji strumienia, a tunel zostaje ustanowiony do punktu końcowego przesyłania strumieniowego IoT Hub (jak opisano powyżej).

3. Lokalny serwer proxy urządzenia łączy się z punktem końcowym demona SSH nasłuchuje na porcie 22 na urządzeniu.

4. Lokalny serwer proxy nasłuchuje na wydzielonym porcie, który oczekuje na nowe połączenia SSH od użytkownika (port 2222 użyty w przykładzie, ale można go skonfigurować na dowolnym innym dostępnym porcie). Użytkownik wskazuje port lokalnego serwera proxy usługi na hoście lokalnym.

### <a name="notes"></a>Uwagi

* Powyższe kroki pełnią kompleksowy tunel między klientem SSH (po prawej stronie) do demona SSH (po lewej stronie). Część tej kompleksowej łączności obejmuje wysyłanie ruchu przez strumień urządzenia do IoT Hub.

* Strzałki na rysunku wskazują kierunek ustanawiania połączeń między punktami końcowymi. Należy zauważyć, że nie ma połączeń przychodzących przechodzenia do urządzenia (jest to często blokowane przez zaporę).

* Wybór korzystania z portu 2222 na lokalnym serwerze proxy usługi jest dowolnym wyborem. Serwer proxy można skonfigurować tak, aby korzystał z dowolnego innego dostępnego portu.

* Wybór portu 22 jest zależny od protokołu i jest w tym przypadku specyficzny dla SSH. W przypadku protokołu RDP należy użyć portu 3389. Tę konfigurację można skonfigurować w dostarczonych przykładowych programach.

Skorzystaj z poniższych linków, aby uzyskać instrukcje dotyczące sposobu uruchamiania lokalnych programów serwera proxy w wybranym języku. Podobnie jak w przypadku [przykładu ECHA](#echo-sample), można uruchamiać programy i lokalne serwery proxy usługi w różnych językach, ponieważ są one w pełni obsługiwane.

* [Program usług i usług w języku C#](quickstart-device-streams-proxy-csharp.md)

* [ ProgramNode.js Service](quickstart-device-streams-proxy-nodejs.md)

* [Program urządzenia C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Następne kroki

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o strumieniach urządzeń.

> [!div class="nextstepaction"]
> [Strumienie urządzeń w programie IoT Show (kanał 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
