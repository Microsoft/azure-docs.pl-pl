---
title: Żądanie danych dotyczących transportu publicznego w czasie rzeczywistym | Mapy platformy Microsoft Azure
description: Żądaj danych transportu publicznego w czasie rzeczywistym za pomocą usługi mobilności Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086081"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Żądanie danych transportu publicznego w czasie rzeczywistym przy użyciu usługi mobilności usługi Azure Maps

W tym artykule pokazano, jak używać usługi Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) do żądania danych transportu publicznego w czasie rzeczywistym.

W tym artykule dowiesz się, jak zamówić następne przyjazdy w czasie rzeczywistym dla wszystkich linii przybywających na danym przystanku

## <a name="prerequisites"></a>Wymagania wstępne

Najpierw musisz mieć konto usługi Azure Maps i klucz subskrypcji, aby wykonywać wszelkie wywołania interfejsów API transportu publicznego usługi Azure Maps. Aby uzyskać informacje, postępuj zgodnie z instrukcjami w [tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) w celu utworzenia konta usługi Azure Maps. Wykonaj kroki opisane w [celu uzyskania klucza podstawowego,](quick-demo-map-app.md#get-the-primary-key-for-your-account) aby uzyskać klucz podstawowy dla swojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md).

W tym artykule używa [aplikacji Postman](https://www.getpostman.com/apps) do tworzenia wywołań REST. Można użyć dowolnego środowiska programistycznego interfejsu API, które wolisz.

## <a name="request-real-time-arrivals-for-a-stop"></a>Poproś o przyjazdy w czasie rzeczywistym, aby zatrzymać

Aby zażądać danych o przyjazdach w czasie rzeczywistym określonego przystanku transportu publicznego, należy złożyć wniosek do [interfejsu API przylotów w czasie rzeczywistym](https://aka.ms/AzureMapsMobilityRealTimeArrivals) usługi azure maps [mobilności.](https://aka.ms/AzureMapsMobilityService) Aby wypełnić żądanie, musisz **metroID** i **stopID.** Aby dowiedzieć się więcej o tym, jak zażądać tych parametrów, zapoznaj się z naszym przewodnikiem, jak [żądać tras transportu publicznego.](https://aka.ms/AMapsHowToGuidePublicTransitRouting)

Użyjmy "522" jako naszego identyfikatora metra, który jest identyfikatorem metra dla obszaru "Seattle-Tacoma-Bellevue, WA". Użyj "522---2060603" jako identyfikator przystanku, ten przystanek autobusowy znajduje się pod adresem "Ne 24th St & 162nd Ave Ne, Bellevue WA". Aby uzyskać kolejne pięć danych o przyjazdach w czasie rzeczywistym, dla wszystkich następnych przylotów na żywo na tym przystanku, wykonaj następujące kroki:

1. Otwórz aplikację Postman i utwórzmy kolekcję do przechowywania żądań. U góry aplikacji Listonosz wybierz pozycję **Nowy**. W oknie **Utwórz nowy** wybierz pozycję **Kolekcja**.  Nazwij kolekcję i wybierz przycisk **Utwórz.**

2. Aby utworzyć żądanie, wybierz ponownie pozycję **Nowy.** W oknie **Utwórz nowy** wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku jako lokalizację, w której ma być zapisywane żądanie. Następnie wybierz pozycję **Zapisz**.

    ![Tworzenie żądania w listonoszu](./media/how-to-request-transit-data/postman-new.png)

3. Wybierz metodę **GET** HTTP na karcie konstruktora i wprowadź następujący adres URL, aby utworzyć żądanie GET. Zamień `{subscription-key}`klucz podstawowy usługi Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Po pomyślnym żądaniu otrzymasz następującą odpowiedź.  Należy zauważyć, że parametr "scheduleType" określa, czy szacowany czas przybycia jest oparty na danych w czasie rzeczywistym lub statycznych.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak żądać danych tranzytowych za pomocą usługi mobilności:

> [!div class="nextstepaction"]
> [Jak zażądać danych tranzytowych](how-to-request-transit-data.md)

Zapoznaj się z dokumentacją interfejsu API usługi mobilności usługi Azure Maps:

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)
