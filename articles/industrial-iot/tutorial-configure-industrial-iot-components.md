---
title: Skonfiguruj składniki usługi Azure Industrial IoT
description: W tym samouczku dowiesz się, jak zmienić wartości domyślne konfiguracji.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787749"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>Samouczek: Konfigurowanie przemysłowych składników IoT

Skrypt wdrożenia automatycznie skonfiguruje wszystkie składniki, aby współpracowały ze sobą przy użyciu wartości domyślnych. Jednak ustawienia wartości domyślnych można zmienić w celu spełnienia wymagań.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dostosuj konfigurację składników


Poniżej przedstawiono niektóre bardziej odpowiednie ustawienia dostosowywania dla składników programu:
* Usługa IoT Hub
    * Sieć → dostęp publiczny: Konfigurowanie dostępu do Internetu, na przykład filtry IP
    * Sieć → połączenia prywatnego punktu końcowego: Tworzenie punktu końcowego, który nie jest dostępny za pośrednictwem Internetu i może być używane wewnętrznie przez inne usługi platformy Azure lub urządzenia lokalne (na przykład za pośrednictwem połączenia sieci VPN)
    * IoT Edge: zarządzanie konfiguracją urządzeń brzegowych połączonych z serwerami OPC UA 
* Cosmos DB
    * Replikowanie danych globalnie: Konfigurowanie nadmiarowości danych
    * Zapora i sieci wirtualne: Skonfiguruj dostęp do Internetu i dostępu do sieci wirtualnej oraz filtry IP
    * Połączenia prywatnego punktu końcowego: Tworzenie punktu końcowego, który nie jest dostępny za pomocą Internetu 
* Key Vault
    * Wpisy tajne: Zarządzaj ustawieniami platformy
    * Zasady dostępu: Określ, które aplikacje i użytkownicy mogą uzyskiwać dostęp do danych w Key Vault i jakie operacje (na przykład odczyt, zapis, lista, usuwanie) mogą być wykonywane w sieci, zaporze, wirtualnej i prywatnych punktach końcowych
* Azure Active Directory (AAD) → Rejestracje aplikacji
    * <APP_NAME> → uwierzytelniania sieci Web: Zarządzanie identyfikatorami URI odpowiedzi, czyli listą identyfikatorów URI, które mogą być używane jako strony docelowe po pomyślnym uwierzytelnieniu. Skrypt wdrażania może nie być w stanie automatycznie skonfigurować tego programu w określonych scenariuszach, takich jak brak praw administratora usługi AAD. Możesz chcieć dodać lub zmodyfikować identyfikatory URI podczas zmiany nazwy hosta aplikacji sieci Web, na przykład numeru portu używanego przez localhost do debugowania
* App Service
    * Konfiguracja: Zarządzanie zmiennymi środowiskowymi kontrolującymi usługi lub interfejs użytkownika
* Maszyna wirtualna
    * Sieć: Konfigurowanie obsługiwanych sieci i reguł zapory
    * Konsola szeregowa: dostęp SSH do uzyskiwania szczegółowych informacji lub debugowania, pobieranie poświadczeń z danych wyjściowych skryptu wdrożenia lub Resetowanie hasła
* IoT Hub → IoT Edge
    * Zarządzaj tożsamościami urządzeń IoT Edge, które mogą uzyskać dostęp do centrum, skonfigurować moduły, które są instalowane i których konfiguracji używają, na przykład parametry kodowania dla wydawcy OPC
* IoT Hub → IoT Edge → \<DEVICE> → Set modules → OpcPublisher (tylko w przypadku autonomicznej operacji wydawcy)


## <a name="configuration-options"></a>Opcje konfiguracji

|Opcja konfiguracji (skróty/pełna nazwa)    |    Opis   |
|----------------------------------------------|------------------|
PF/publishfile |Nazwa pliku, aby skonfigurować węzły do opublikowania. Jeśli ta opcja jest określona, nadaje wydawcy OPC w trybie autonomicznym.
LF/LogFile |Nazwa pliku dziennika do użycia.
szystkie/LogLevel |Poziom dziennika do użycia (dozwolone: krytyczne, błąd, ostrzeżenie, informacje, debugowanie, pełne).
Me/MessageEncoding |Kodowanie wiadomości dla wiadomości wychodzących dozwolone wartości: JSON, UADP
mm/messagingmode |Tryb obsługi komunikatów dla wiadomości wychodzących dozwolone wartości: PubSub, przykłady
FM/fullfeaturedmessage |Pełny tryb proponowany dla komunikatów (wszystkie pola wypełnione). Wartość domyślna to "true". w przypadku starszej zgodności Użyj wartości "false"
AA/autoakceptuj |Wydawca ufa wszystkim serwerom, z którym jest nawiązywane połączenie
BS/BatchSize |Liczba komunikatów o zmianach danych OPC UA, które mają być buforowane na potrzeby przetwarzania wsadowego.
si/iothubsendinterval |Interwał wsadowy wyzwalacza (w sekundach).
MS/iothubmessagesize |Maksymalny rozmiar komunikatu (IoT D2C).
OM/maxoutgressmessages |Maksymalny rozmiar buforu wychodzącego komunikatu (IoT D2C).
di/diagnosticsinterval |Wyświetla informacje diagnostyczne wydawcy w określonym interwale (w sekundach) (wymagane są informacje na temat poziomu dziennika). -1 wyłącza zdalny dziennik diagnostyczny i dane wyjściowe diagnostyki
lt/logflugtimespan |Czas (w sekundach), po którym powinien zostać opróżniony plik dziennika.
ih/iothubprotocol |Protokół używany do komunikacji z centrum. Dozwolone wartości: AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, AMQP, MQTT, TCP, WebSocket, any
HB/HeartBeatInterval |Wydawca używa tej opcji jako wartości domyślnej w sekundach dla ustawienia interwału pulsu bez ustawienia interwału pulsu.
OT/OperationTimeout |Limit czasu operacji dla klienta wydawcy OPC UA w MS.
olu/opcmaxstringlen |Maksymalna długość ciągu OPC można przesłać/odebrać.
Oi/opcsamplinginterval |Wartość domyślna w milisekundach, aby zażądać serwerów do próbkowania wartości
op/opcpublishinginterval |Wartość domyślna w milisekundach dla ustawienia interwału publikowania subskrypcji dla serwera OPC UA.
CT/createsessiontimeout |Interwał wysyłania przez wydawcę komunikatów utrzymywania aktywności w ciągu kilku sekund do serwerów OPC w punktach końcowych, z którymi jest połączona.
kt/keepalivethresholt |Określ liczbę pakietów utrzymywania aktywności, które serwer może pominąć, zanim sesja zostanie odłączona.
TM/trustmyself |Certyfikat wydawcy jest automatycznie umieszczany w zaufanym magazynie.
at/appcertstoretype |Typ magazynu certyfikatów aplikacji (dozwolone: katalog, X509Store).


## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak zmienić wartości domyślne konfiguracji, możesz 

> [!div class="nextstepaction"]
> [Ściąganie danych IIoT do ADX](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Wizualizuj i analizuj dane przy użyciu Time Series Insights](tutorial-visualize-data-time-series-insights.md)
