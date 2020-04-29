---
title: Typowe wzorce zapytań — usługa Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Zapoznaj się z kilkoma typowymi wzorcami zapytań interfejsu API dla interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 133c0e0dcc07afb85a0f3af9ae51d2207abac293
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77589117"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Jak wykonywać zapytania dotyczące interfejsów API Digital bliźniaczych reprezentacji platformy Azure dla typowych zadań

W tym artykule przedstawiono wzorce zapytań ułatwiające wykonywanie typowych scenariuszy dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Przyjęto założenie, że wystąpienie bliźniaczych reprezentacji cyfrowe jest już uruchomione. Można użyć dowolnego klienta REST, takiego jak program Poster. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Zapytania dotyczące spacji i typów

Ta sekcja zawiera przykładowe zapytania, aby uzyskać więcej informacji o zainicjowanych miejscach. Wykonaj uwierzytelnione żądania GET HTTP z przykładowymi zapytaniami, zastępując symbole zastępcze wartościami z Instalatora. 

- Pobierz spacje, które są węzłami głównymi.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Pobierz spację według nazwy i Uwzględnij urządzenia, czujniki, obliczone wartości i wartości czujnika. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Pobierz spacje i informacje o urządzeniu/czujniku, których elementem nadrzędnym jest dany identyfikator obszaru, i które są na poziomach od dwóch do pięciu [względem danego obszaru](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Pobierz miejsce z danym IDENTYFIKATORem i Dołącz wartości obliczone i czujnika.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Pobierz klucze właściwości dla określonego obszaru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Pobierz spacje przy użyciu klucza właściwości o nazwie *AreaInSqMeters* , a jego wartość to 30. Możesz również wykonywać operacje na ciągach, na przykład Pobierz spacje zawierające klucz właściwości `name = X contains Y`z.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Pobierz wszystkie nazwy z *temperaturą* nazwy i skojarzonymi zależnościami i ontologie.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Zapytania dotyczące ról i przypisań ról

W tej sekcji przedstawiono niektóre zapytania, aby uzyskać więcej informacji na temat ról i ich przypisań. 

- Pobierz wszystkie role obsługiwane przez usługę Azure Digital bliźniaczych reprezentacji.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Pobierz wszystkie przypisania ról w wystąpieniu Digital bliźniaczych reprezentacji. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Pobieranie przypisań ról dla określonej ścieżki.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Zapytania dotyczące urządzeń

W tej sekcji przedstawiono kilka przykładów użycia interfejsów API zarządzania w celu uzyskania szczegółowych informacji o urządzeniach. Wszystkie wywołania interfejsu API muszą być uwierzytelniani ODBIERAją żądania HTTP.

- Pobierz wszystkie urządzenia.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Znajdź wszystkie Stany urządzeń.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Pobierz określone urządzenie.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Pobierz wszystkie urządzenia dołączone do obszaru głównego.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Pobierz wszystkie urządzenia dołączone do miejsc na poziomach od 2 do 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Pobierz wszystkie urządzenia bezpośrednio dołączone do określonego identyfikatora obszaru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Pobierz wszystkie urządzenia dołączone do określonego obszaru i jego obiektów podrzędnych.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Pobierz wszystkie urządzenia dołączone do elementów podrzędnych miejsca, z wyłączeniem tego miejsca.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Pobierz wszystkie urządzenia dołączone do bezpośrednich elementów podrzędnych miejsca.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Pobierz wszystkie urządzenia dołączone do jednego z elementów nadrzędnych obszaru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Pobierz wszystkie urządzenia dołączone do elementów podrzędnych miejsca o poziomie mniejszym lub równym 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Pobierz wszystkie urządzenia dołączone do miejsc, które znajdują się na tym samym poziomie, co miejsce z IDENTYFIKATORem *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Pobierz IoT Hub parametry połączenia z urządzeniem.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Pobierz urządzenie z danym IDENTYFIKATORem sprzętu, w tym z dołączonymi czujnikami.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Uzyskaj czujniki dla konkretnych typów danych, w tym przypadku *ruchu* i *temperatury*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Zapytania dotyczące odpowiedników i funkcji zdefiniowanych przez użytkownika 

- Pobierz wszystkie przydziały i ich identyfikatory.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Uzyskaj szczegółowe informacje o konkretnym dopasowaniu, w tym informacje o przestrzeni i zdefiniowanej przez użytkownika funkcji.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Oceń dopasowanie do czujnika i Włącz rejestrowanie na potrzeby debugowania. Zwrot tego komunikatu HTTP GET informuje o tym, czy dopasowanie i czujnik należą do typu danych. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Pobierz identyfikator funkcji zdefiniowanych przez użytkownika. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Pobierz zawartość określonej funkcji zdefiniowanej przez użytkownika 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Zapytania dla użytkowników

Ta sekcja zawiera przykładowe zapytania interfejsu API do zarządzania użytkownikami w usłudze Azure Digital bliźniaczych reprezentacji. Utwórz żądanie HTTP GET zastępujące symbole zastępcze wartościami z Twojego Instalatora. 

- Pobierz wszystkich użytkowników. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Uzyskaj określonego użytkownika.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak uwierzytelniać się za pomocą interfejsu API zarządzania, przeczytaj artykuł [uwierzytelnianie za pomocą interfejsów API](./security-authenticating-apis.md).

Aby dowiedzieć się więcej na temat punktów końcowych interfejsu API, zobacz [jak używać programu Digital bliźniaczych reprezentacji Swagger](./how-to-use-swagger.md).
