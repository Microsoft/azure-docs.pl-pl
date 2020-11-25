---
title: Łączenie Rigado kaskadowego 500 na platformie Azure IoT Central | Microsoft Docs
description: Dowiedz się, jak połączyć Rigado kaskadowe urządzenie bramy 500 z aplikacją IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 0000e7690ab92f469a7417e82cb375c524e0b343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014766"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Podłączanie Rigado kaskadowego urządzenia bramy 500 do aplikacji IoT Central platformy Azure

*Ten artykuł ma zastosowanie do konstruktorów rozwiązań.*

W tym artykule opisano, jak w ramach konstruktora rozwiązań można podłączyć urządzenie bramy Rigado Kaskada 500 do aplikacji IoT Central Microsoft Azure. 

## <a name="what-is-cascade-500"></a>Co to jest Kaskada 500?

Kaskada 500 IoT Gateway to Oferta sprzętowa z Rigado, która jest dołączana jako część rozwiązania do tworzenia kaskadowych rozwiązań jako usługi. Udostępnia ona komercyjne projekty IoT i zespoły produktów o elastycznej mocy obliczeniowej, niezawodne środowisko aplikacji w kontenerze oraz szeroką gamę opcji łączności z urządzeniami bezprzewodowymi, w tym Bluetooth 5, LTE, & Wi-Fi.

Kaskada 500 jest wstępnie certyfikowana dla usługi Azure IoT Plug and Play (wersja zapoznawcza), dzięki której konstruktory rozwiązań mogą łatwo dołączać urządzenie do kompleksowych rozwiązań. Brama kaskadowa umożliwia bezprzewodową łączenie się z różnymi czujnikami monitorowania warunków, które znajdują się w pobliżu urządzenia bramy. Czujniki te można dołączyć do IoT Central za pośrednictwem urządzenia bramy.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać czynności opisane w tym przewodniku, potrzebujesz następujących zasobów:

* Urządzenie kaskadowe Rigado 500. Aby uzyskać więcej informacji, odwiedź stronę [Rigado](https://www.rigado.com/).
* Aplikacja IoT Central platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie nowej aplikacji](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Dodawanie szablonu urządzenia

W celu dołączenia urządzenia bramy kaskadowej 500 do wystąpienia aplikacji platformy Azure IoT Central należy skonfigurować odpowiedni szablon urządzenia w aplikacji.

Aby dodać kaskadowy szablon urządzenia 500: 

1. Przejdź do karty ***Szablony urządzeń** _ w lewym okienku, wybierz _ * + nowy * *: ![ Utwórz nowy szablon urządzenia](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. Strona udostępnia opcję **_Utwórz szablon niestandardowy_* _ lub _*_Użyj wstępnie skonfigurowanego szablonu urządzenia_*_
1. Wybierz szablon urządzenia C500 z listy wstępnie skonfigurowanych szablonów urządzeń, jak pokazano poniżej: ![ Wybierz szablon urządzenia C500](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Wybierz pozycję _*_Dalej: Dostosuj_*_ , aby przejść do następnego kroku. 
1. Na następnym ekranie wybierz pozycję _*_Utwórz_*_ , aby dołączyć szablon urządzenia C500 do aplikacji IoT Central.

## <a name="retrieve-application-connection-details"></a>Pobierz szczegóły połączenia z aplikacją

Teraz musisz pobrać *Identyfikator zakresu** i **klucz podstawowy** dla aplikacji IoT Central platformy Azure, aby połączyć urządzenie kaskadowe 500. 

1. W lewym okienku przejdź do obszarze **Administracja**  , a następnie kliknij pozycję **połączenie z urządzeniem**. 
2. Zanotuj **Identyfikator zakresu** dla aplikacji IoT Central.
![Identyfikator zakresu aplikacji](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Teraz kliknij przycisk **Wyświetl klucze** i zanotuj klucz **Primary key** 
 ![ podstawowy klucza podstawowego](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Skontaktuj się z Rigado, aby połączyć bramę 

Aby podłączyć urządzenie kaskadowo 500 do aplikacji IoT Central, musisz skontaktować się z usługą Rigado i udostępnić je szczegółowym informacjom dotyczącym połączenia aplikacji z powyższych kroków. 

Gdy urządzenie jest połączone z Internetem, Rigado będzie w stanie wypchnąć aktualizację konfiguracji na urządzenie z bramą kaskadową 500 za pośrednictwem bezpiecznego kanału. 

Ta aktualizacja zastosuje IoT Central szczegóły połączenia na urządzeniu kaskadowym 500 i pojawi się na liście urządzeń. 

![Lista urządzeń](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Teraz możesz przystąpić do korzystania z urządzenia C500 w aplikacji IoT Central!

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, Oto kilka sugerowanych następnych kroków:

- Przeczytaj o [łączności urządzeń w usłudze Azure IoT Central](./concepts-get-connected.md)
- Dowiedz się, jak [monitorować łączność z urządzeniem przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
