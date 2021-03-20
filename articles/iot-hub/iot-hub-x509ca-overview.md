---
title: Omówienie zabezpieczeń usługi Azure IoT Hub X. 509 urzędu certyfikacji | Microsoft Docs
description: Przegląd — jak uwierzytelniać urządzenia w IoT Hub przy użyciu urzędów certyfikacji X. 509.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 455f1f3e1c8181646fbe165d0f1aea6a2fb2fb13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92150701"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Uwierzytelnianie urządzeń za pomocą certyfikatów X.509 urzędu certyfikacji

W tym artykule opisano, jak za pomocą certyfikatów urzędu certyfikacji X. 509 uwierzytelniać urządzenia łączące IoT Hub.  W tym artykule przedstawiono następujące informacje:

* Jak uzyskać certyfikat urzędu certyfikacji X. 509
* Jak zarejestrować certyfikat certyfikatu X. 509 urzędu certyfikacji w celu IoT Hub
* Jak podpisać urządzenia przy użyciu certyfikatów programu X. 509 urzędu certyfikacji
* Jak są uwierzytelniane urządzenia z urzędem certyfikacji X. 509

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Omówienie

Funkcja urząd certyfikacji X. 509 umożliwia uwierzytelnianie urządzeń IoT Hub przy użyciu urzędu certyfikacji (CA). Znacznie upraszcza proces wstępnej rejestracji urządzeń oraz logistyki łańcucha dostaw podczas produkcji urządzenia. [Więcej informacji znajduje się w tym artykule o wartości użycia certyfikatów programu X. 509 urzędu certyfikacji](iot-hub-x509ca-concept.md) na potrzeby uwierzytelniania urządzeń.  Zachęcamy do przeczytania tego artykułu scenariusza przed kontynuowaniem, ponieważ wyjaśnia, dlaczego istnieją poniższe kroki.

## <a name="prerequisite"></a>Wymaganie wstępne

Korzystanie z funkcji urzędu certyfikacji X. 509 wymaga konta IoT Hub.  [Dowiedz się, jak utworzyć wystąpienie IoT Hub](quickstart-send-telemetry-dotnet.md) , jeśli jeszcze go nie masz.

## <a name="how-to-get-an-x509-ca-certificate"></a>Jak uzyskać certyfikat urzędu certyfikacji X. 509

Certyfikat X. 509 urzędu certyfikacji znajduje się u góry łańcucha certyfikatów dla każdego z urządzeń.  Możesz zakupić lub utworzyć jeden, w zależności od tego, w jaki sposób zamierzasz go używać.

W przypadku środowiska produkcyjnego zalecamy zakupienie certyfikatu X. 509 urzędu certyfikacji z publicznego urzędu certyfikacji. Zakup certyfikatu urzędu certyfikacji jest korzyścią dla głównego urzędu certyfikacji działającego jako zaufana osoba trzecia do zagwarantowania autentyczności urządzeń. Tę opcję należy wziąć pod uwagę, jeśli zamierzasz, aby urządzenia były częścią otwartej sieci IoT, w której mogą korzystać z produktów lub usług innych firm.

Możesz również utworzyć urząd certyfikacji X. 509 z podpisem własnym na potrzeby eksperymentowania lub do użytku w zamkniętych sieciach IoT.

Bez względu na to, w jaki sposób uzyskujesz certyfikat urzędu certyfikacji X. 509, pamiętaj, aby zachować jego odpowiedni klucz prywatny i chronić cały czas.  Jest to konieczne w przypadku zaufania do kompilowania zaufania w ramach uwierzytelniania urzędu certyfikacji X. 509.

Dowiedz się, jak [utworzyć certyfikat urzędu certyfikacji z](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)podpisem własnym, którego można użyć do eksperymentowania w tym opisie funkcji.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Podpisywanie urządzeń do łańcucha certyfikatów zaufania

Właściciel certyfikatu X. 509 urzędu certyfikacji może kryptograficznie podpisać pośredni urząd certyfikacji, który może z kolei podpisywać inny pośredni urząd certyfikacji itd. do momentu zakończenia przez ostatni pośredni urząd certyfikacji tego procesu przez podpisywanie urządzenia. Wynikiem tego jest kaskadowy łańcuch certyfikatów nazywany łańcuchem certyfikatów. W czasie rzeczywistym to delegowanie zaufania do urządzeń podpisywania. To delegowanie jest ważne, ponieważ ustanawia kryptograficznie zmienne łańcuchy opieki i unika udostępniania kluczy podpisywania.

![IMG-Generic-CERT-on-Trust](./media/generic-cert-chain-of-trust.png)

Certyfikat urządzenia (nazywany również certyfikatem liścia) musi mieć ustawioną *nazwę podmiotu* na **Identyfikator urządzenia** ( `CN=deviceId` ), który został użyty podczas rejestrowania urządzenia IoT w usłudze Azure IoT Hub. To ustawienie jest wymagane do uwierzytelniania.

Dowiedz się tutaj, jak [utworzyć łańcuch certyfikatów](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) jako gotowy podczas podpisywania urządzeń.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Jak zarejestrować certyfikat certyfikatu X. 509 urzędu certyfikacji w celu IoT Hub

Zarejestruj certyfikat certyfikatu X. 509 urzędu certyfikacji, aby IoT Hub, w którym będzie on używany do uwierzytelniania urządzeń podczas rejestracji i nawiązywania połączenia.  Rejestrowanie certyfikatu urzędu certyfikacji X. 509 jest procesem dwuetapowym, który obejmuje przekazywanie pliku certyfikatu i potwierdzenie jego posiadania.

Proces przekazywania wiąże się z przekazywaniem pliku, który zawiera certyfikat.  Ten plik nigdy nie powinien zawierać żadnych kluczy prywatnych.

Krok "dowód posiadania" obejmuje proces wyzwania i reakcji kryptograficznej między ty i IoT Hub.  Z uwagi na to, że zawartość certyfikatu cyfrowego jest publiczna i dlatego jest podatna na podsłuchiwanie, IoT Hub chce upewnić się, że certyfikat urzędu certyfikacji jest naprawdę własny.  Należy to zrobić przez wygenerowanie losowego wyzwania, które należy podpisać przy użyciu odpowiedniego klucza prywatnego certyfikatu urzędu certyfikacji.  Jeśli klucz tajny został zachowany i został wcześniej poinformowany, tylko ty będziesz mieć wiedzę na temat wykonywania tego kroku. Tajemnica kluczy prywatnych jest źródłem zaufania w tej metodzie.  Po podpisaniu wyzwania wykonaj ten krok, przekazując plik zawierający wyniki.

Dowiedz się tutaj, jak [zarejestrować certyfikat urzędu certyfikacji](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Jak utworzyć urządzenie w IoT Hub

Aby wykluczyć personifikację urządzenia, IoT Hub wymaga poinformowania o urządzeniach, które mają być oczekiwane.  W tym celu należy utworzyć wpis urządzenia w rejestrze urządzenia IoT Hub.  Ten proces jest zautomatyzowany w przypadku korzystania z [usługi IoT Hub Device Provisioning](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/). 

Dowiedz się tutaj, jak [ręcznie utworzyć urządzenie w IoT Hub](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Tworzenie urządzenia X. 509 dla Centrum IoT Hub

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Uwierzytelnianie urządzeń podpisanych za pomocą certyfikatów X. 509 urzędu certyfikacji

Po zarejestrowaniu certyfikatu X. 509 urzędu certyfikacji i zarejestrowaniu urządzeń w łańcuchu zaufania certyfikatu pozostanie uwierzytelnianie urządzenia, nawet po raz pierwszy.  Po nawiązaniu połączenia z podpisanym przez urząd certyfikacji X. 509 urządzenie przekazuje swój łańcuch certyfikatów do walidacji. Łańcuch obejmuje wszystkie pośrednich urzędów certyfikacji i certyfikatów urządzeń.  Korzystając z tych informacji, IoT Hub uwierzytelniać urządzenie w procesie dwuetapowym.  IoT Hub kryptograficznie sprawdza poprawność łańcucha certyfikatów pod kątem spójności wewnętrznej, a następnie wystawia wyzwanie dowodu.  IoT Hub deklaruje urządzenie jako autentyczne w odpowiedzi na pomyślne potwierdzenie posiadania z urządzenia.  W tej deklaracji przyjęto założenie, że klucz prywatny urządzenia jest chroniony i że tylko urządzenie może pomyślnie odpowiedzieć na to wyzwanie.  Zalecamy używanie bezpiecznych mikroukładów, takich jak sprzętowe zabezpieczenia modułów (HSM) na urządzeniach, aby chronić klucze prywatne.

Pomyślne połączenie z urządzeniem IoT Hub kończy proces uwierzytelniania i jest również wskaźnikiem prawidłowej konfiguracji.

Dowiedz się tutaj, jak [ukończyć ten krok połączenia z urządzeniem](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat wartości uwierzytelniania urzędu certyfikacji X. 509](iot-hub-x509ca-concept.md) w usłudze IoT.

Rozpocznij pracę z [usługą IoT Hub Device Provisioning](../iot-dps/index.yml).