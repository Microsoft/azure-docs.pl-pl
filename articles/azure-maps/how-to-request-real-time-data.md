---
title: Żądaj danych tranzytu publicznego w czasie rzeczywistym za pomocą usługi Microsoft Azure Maps Services (wersja zapoznawcza)
description: Dowiedz się, jak żądać danych tranzytu publicznego w czasie rzeczywistym, takich jak przybycia do zatrzymania tranzytu. W tym celu Zobacz, jak używać usług mobilności Azure Maps (wersja zapoznawcza).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3e3dc4b0e3bc64a38856da8344583b744ea62b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906050"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>Żądaj danych tranzytu publicznego w czasie rzeczywistym przy użyciu usług Azure Maps Mobility Services (wersja zapoznawcza) 

> [!IMPORTANT]
> Usługi mobilności Azure Maps są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


W tym artykule pokazano, jak za pomocą [usług mobilności](/rest/api/maps/mobility) Azure Maps żądania publicznego przesyłania danych w czasie rzeczywistym.

W tym artykule dowiesz się, jak zażądać następnych przyjęć w czasie rzeczywistym dla wszystkich wierszy, które docierają do danego zatrzymania

## <a name="prerequisites"></a>Wymagania wstępne

Najpierw musisz mieć konto Azure Maps i klucz subskrypcji, aby wykonać dowolne wywołania interfejsów API Azure Maps publicznego przesyłania. Aby uzyskać więcej informacji, postępuj zgodnie z instrukcjami podanymi w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-azure-maps-account) w celu utworzenia konta Azure Maps. Postępuj zgodnie z instrukcjami w temacie [Pobieranie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) , aby uzyskać klucz podstawowy dla Twojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

W tym artykule jest wykorzystywana [aplikacja Poster](https://www.getpostman.com/apps) do kompilowania wywołań REST. Możesz użyć dowolnego preferowanego środowiska deweloperskiego interfejsu API.

## <a name="request-real-time-arrivals-for-a-stop"></a>Żądaj przyjęć w czasie rzeczywistym na potrzeby zatrzymania

Aby można było zażądać danych o nadejściu w czasie rzeczywistym określonego tranzytu publicznego, należy wysłać żądanie do [interfejsu API przybycia w czasie rzeczywistym](/rest/api/maps/mobility/getrealtimearrivalspreview) [usługi mobilności Azure Maps (wersja zapoznawcza)](/rest/api/maps/mobility). Do ukończenia żądania potrzebne są **metroID** i **stopID** . Aby dowiedzieć się więcej o tym, jak zażądać tych parametrów, zobacz nasz przewodnik dotyczący [żądania publicznych tras tranzytowych](./how-to-request-transit-data.md).

Użyjmy "522" jako identyfikatora Metro, który jest IDENTYFIKATORem Metro dla obszaru "Seattle – Tacoma – Bellevue, WA". Użyj "522---2060603" jako identyfikatora zatrzymania, ta magistrala zatrzymuje się o godzinie "ne 24 St & 162nd Ave ne, Bellevue WA". Aby poprosić o następne pięć danych o nadejściu w czasie rzeczywistym dla wszystkich następnych na żywo przyjęć, wykonaj następujące czynności:

1. Otwórz aplikację Poster i Utwórz kolekcję do przechowywania żądań. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**.  Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** .

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku jako lokalizację, w której ma zostać zapisane żądanie. Następnie wybierz pozycję **Zapisz**.

    ![Utwórz żądanie w programie Poster](./media/how-to-request-transit-data/postman-new.png)

3. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL, aby utworzyć żądanie Get. Zamień na `{subscription-key}` klucz podstawowy Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Po pomyślnym żądaniu otrzymasz poniższą odpowiedź.  Należy zauważyć, że parametr "ScheduleType" określa, czy szacowany czas przybycia jest oparty na danych w czasie rzeczywistym, czy statycznym.

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

Dowiedz się, jak żądać danych tranzytowych przy użyciu usług mobilności (wersja zapoznawcza):

> [!div class="nextstepaction"]
> [Jak żądać danych tranzytowych](how-to-request-transit-data.md)

Zapoznaj się z dokumentacją interfejsu API usługi Azure Maps Mobility Services (wersja zapoznawcza):

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usług mobilności](/rest/api/maps/mobility)