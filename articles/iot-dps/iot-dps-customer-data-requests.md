---
title: Funkcje żądania danych klienta dla urządzeń usługi Azure DPS
description: W przypadku urządzeń zarządzanych w usłudze Azure Device Provisioning (DPS), które są osobiste, ten artykuł pokazuje administratorów, jak eksportować lub usuwać dane osobowe.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 75a2762a0937efbceaa168f8a2d6409e2e3a7ae4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967231"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta

IoT Hub Device Provisioning Service platformy Azure to usługa w chmurze oparta na interfejsie API REST przeznaczona dla klientów korporacyjnych, która umożliwia bezproblemowe i zautomatyzowane Inicjowanie obsługi administracyjnej urządzeń na platformie Azure IoT Hub z zabezpieczeniami, które zaczynają się na urządzeniu i kończą się chmurą.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Do poszczególnych urządzeń są przypisywane identyfikatory rejestracji i identyfikatory urządzeń przez administratora dzierżawy. Dane z i informacje o tych urządzeniach są oparte na tych identyfikatorach. Firma Microsoft nie przechowuje żadnych informacji i nie ma dostępu do danych, które pozwolą na korelację tych urządzeń z osobą.

Wiele urządzeń zarządzanych w usłudze Device Provisioning nie jest urządzeniami osobistymi, na przykład z termostatem lub robotem fabryki biurowej. Klienci mogą jednak rozważyć, że niektóre urządzenia mają być identyfikowalne i według ich uznania mogą zachować własne metody śledzenia zasobów lub spisu, które wiążą urządzenia z osobami. Usługa Device Provisioning zarządza i przechowuje wszystkie dane skojarzone z urządzeniami, tak jakby były danymi osobistymi.

Administratorzy dzierżawy mogą używać Azure Portal lub interfejsów API REST usługi do realizacji żądań informacji przez eksportowanie lub usuwanie danych skojarzonych z IDENTYFIKATORem lub IDENTYFIKATORem rejestracji.

> [!NOTE]
> Urządzenia, które zostały wdrożone w usłudze Azure IoT Hub za pomocą usługi Device Provisioning, mają dodatkowe dane przechowywane w usłudze Azure IoT Hub. Zapoznaj się z [dokumentacją dotyczącą platformy Azure IoT Hub](../iot-hub/iot-hub-customer-data-requests.md) w celu wykonania pełnego żądania dla danego urządzenia.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Usługa Device Provisioning przechowuje rejestracje i rekordy rejestracji. Rejestracje zawierają informacje o urządzeniach, które mogą być inicjowane, a rekordy rejestracji pokazują, które urządzenia zostały już przetworzone przez proces aprowizacji.

Administratorzy dzierżawy mogą usuwać rejestracje z Azure Portal. spowoduje to również usunięcie wszystkich skojarzonych rekordów rejestracji.

Aby uzyskać więcej informacji, zobacz [jak zarządzać rejestracjami urządzeń](how-to-manage-enrollments.md).

Możliwe jest również wykonywanie operacji usuwania dla rejestracji i rekordów rejestracji przy użyciu interfejsów API REST:

* Aby usunąć informacje o rejestracji dla pojedynczego urządzenia, można użyć funkcji [rejestracji urządzeń — Delete](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Aby usunąć informacje o rejestracji dla grupy urządzeń, można użyć [grupy rejestracji urządzeń-Delete](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Aby usunąć informacje o urządzeniach, które zostały zainicjowane, można użyć [stanu rejestracji — Usuń stan rejestracji](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Usługa Device Provisioning przechowuje rejestracje i rekordy rejestracji. Rejestracje zawierają informacje o urządzeniach, które mogą być inicjowane, a rekordy rejestracji pokazują, które urządzenia zostały już przetworzone przez proces aprowizacji.

Administratorzy dzierżawy mogą wyświetlać rejestracje i rekordy rejestracji za pośrednictwem Azure Portal i eksportować je przy użyciu funkcji kopiowania i wklejania.

Aby uzyskać więcej informacji na temat zarządzania rejestracjami, zobacz [jak zarządzać rejestracjami urządzeń](how-to-manage-enrollments.md).

Możliwe jest również wykonywanie operacji eksportowania na potrzeby rejestracji i rekordów rejestracji przy użyciu interfejsów API REST:

* Aby wyeksportować informacje o rejestracji dla pojedynczego urządzenia, można użyć funkcji [rejestracji urządzeń — Get](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Aby wyeksportować informacje o rejestracji dla grupy urządzeń, można użyć [grupy rejestracji urządzeń — Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Aby wyeksportować informacje o urządzeniach, które zostały już zainicjowane, można użyć [stanu rejestracji — pobieranie stanu rejestracji](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> Gdy korzystasz z usług firmy Microsoft dla przedsiębiorstw, firma Microsoft generuje pewne informacje, znane jako dzienniki generowane przez system. Niektóre dzienniki generowane przez system usługi Device Provisioning nie są dostępne ani eksportowane przez administratorów dzierżawy. Te dzienniki składają się na faktyczne działania wykonywane w ramach usługi i danych diagnostycznych związanych z poszczególnymi urządzeniami.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Pełna dokumentacja dotycząca interfejsów API usługi Device Provisioning znajduje się w lokalizacji [https://docs.microsoft.com/rest/api/iot-dps](/rest/api/iot-dps) .

[Funkcje żądania danych klienta](../iot-hub/iot-hub-customer-data-requests.md)na platformie Azure IoT Hub.