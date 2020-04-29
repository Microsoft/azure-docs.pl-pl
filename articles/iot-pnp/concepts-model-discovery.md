---
title: Wdrożenie odnajdywania modelu w usłudze IoT Plug and Play w wersji zapoznawczej | Microsoft Docs
description: Jako deweloper rozwiązań dowiesz się, jak wdrożyć odnajdywanie modeli Plug and Play IoT w rozwiązaniu.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 74eb38269a3c7fbdc6d95554a8a8cef14eb0b787
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770472"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Wdrażanie odnajdywania modelu Plug and Play IoT w wersji zapoznawczej w rozwiązaniu IoT

W tym artykule opisano, jak programista rozwiązań może zaimplementować odnajdywanie modelu w usłudze IoT Plug and Play w wersji zapoznawczej w rozwiązaniu IoT.  Funkcja odnajdywania modeli Plug and Play IoT to sposób, w jaki urządzenia IoT Plug and Play identyfikują obsługiwane modele i interfejsy możliwości oraz jak rozwiązanie IoT pobiera te modele i interfejsy.

Dostępne są dwie szerokie kategorie rozwiązania IoT: opracowane przez siebie rozwiązania, które współpracują z znanym zestawem urządzeń Plug and Play IoT i rozwiązaniami opartymi na modelu, które działają z dowolnym urządzeniem Plug and Play IoT.

W tym artykule opisano sposób implementacji odnajdywania modeli w obu typach rozwiązań.

## <a name="model-discovery"></a>Odnajdywanie modelu

Gdy urządzenie IoT Plug and Play w pierwszej kolejności nawiązuje połączenie z usługą IoT Hub, wysyła komunikat telemetrii informacji o modelu. Ten komunikat zawiera identyfikatory interfejsów, które implementuje urządzenie. Aby Twoje rozwiązanie działało z urządzeniem, należy rozwiązać te identyfikatory i pobrać definicje dla każdego interfejsu.

Poniżej przedstawiono kroki podejmowane przez urządzenie Plug and Play IoT, gdy usługa Device Provisioning Service (DPS) zostanie użyta do nawiązania połączenia z centrum:

1. Gdy urządzenie jest włączone, nawiązuje połączenie z globalnym punktem końcowym usługi DPS i uwierzytelnia przy użyciu jednej z dozwolonych metod.
1. Następnie usługa DPS uwierzytelnia urządzenie i wyszukuje regułę, która informuje usługę IoT Hub, do której ma zostać przypisane urządzenie. Następnie usługa DPS rejestruje urządzenie w tym centrum.
1. Usługa DPS zwraca IoT Hub parametry połączenia z urządzeniem.
1. Następnie urządzenie wysyła komunikat telemetrii odnajdywania do IoT Hub. Komunikat Telemetria odnajdowania zawiera identyfikatory interfejsów, które implementuje urządzenie.
1. Urządzenie Plug and Play IoT jest teraz gotowe do pracy z rozwiązaniem korzystającym z Centrum IoT.

Jeśli urządzenie łączy się bezpośrednio z Centrum IoT Hub, nawiązuje połączenie przy użyciu parametrów połączenia, które są osadzone w kodzie urządzenia. Następnie urządzenie wysyła komunikat telemetrii odnajdywania do IoT Hub.

Zobacz interfejs [ModelInformation](concepts-common-interfaces.md) , aby dowiedzieć się więcej o komunikacie telemetrii informacji o modelu.

### <a name="purpose-built-iot-solutions"></a>Opracowane przez siebie rozwiązania IoT

Rozwiązanie IoT opracowane specjalnie dla danego celu współpracuje z znanym zestawem modeli i interfejsów możliwości urządzeń IoT Plug and Play.

Będziesz mieć model możliwości i interfejsy dla urządzeń, które będą łączyć się z rozwiązaniem przed czasem. Wykonaj następujące kroki, aby przygotować swoje rozwiązanie:

1. Przechowywanie plików JSON interfejsu w [repozytorium modelu](./howto-manage-models.md) , w którym rozwiązanie może je odczytać.
1. Pisanie logiki w rozwiązaniu IoT na podstawie oczekiwanych modeli i interfejsów możliwości Plug and Play IoT.
1. Subskrybowanie powiadomień z Centrum IoT Hub używanego przez rozwiązanie.

Po otrzymaniu powiadomienia o nowym połączeniu z urządzeniem wykonaj następujące czynności:

1. Odczytaj komunikat Telemetria odnajdowania, aby pobrać identyfikatory modelu możliwości i interfejsów zaimplementowane przez urządzenie.
1. Porównaj Identyfikator modelu możliwości względem identyfikatorów modeli możliwości przechowywanych wcześniej.
1. Teraz wiesz, jakiego typu urządzenie zostało połączone. Użyj wcześniej napisanej logiki, aby umożliwić użytkownikom odpowiednie współpracującie z urządzeniem.

### <a name="model-driven-solutions"></a>Rozwiązania oparte na modelu

Oparte na modelu rozwiązanie IoT może współpracowało z dowolnym urządzeniem Plug and Play IoT. Tworzenie rozwiązania IoT opartego na modelu jest bardziej skomplikowane, ale korzyścią jest to, że rozwiązanie działa z dowolnymi urządzeniami, które zostały dodane w przyszłości.

Aby utworzyć rozwiązanie IoT oparte na modelu, należy utworzyć logikę dla podstawowych elementów interfejsu Plug and Play IoT: telemetrii, właściwości i poleceń. Logika rozwiązania IoT reprezentuje urządzenie, łącząc wiele funkcji telemetrii, właściwości i poleceń.

Twoje rozwiązanie musi również subskrybować powiadomienia z usługi IoT Hub, której używa.

Po otrzymaniu przez rozwiązanie powiadomienia o nowym połączeniu z urządzeniem wykonaj następujące czynności:

1. Odczytaj komunikat Telemetria odnajdowania, aby pobrać identyfikatory modelu możliwości i interfejsów zaimplementowane przez urządzenie.
1. Dla każdego identyfikatora Przeczytaj pełny plik JSON, aby znaleźć możliwości urządzenia.
1. Sprawdź, czy każdy interfejs jest obecny w każdej pamięci podręcznej skompilowanej do przechowywania plików JSON pobranych wcześniej przez rozwiązanie.
1. Następnie sprawdź, czy interfejs o tym IDENTYFIKATORze znajduje się w repozytorium modelu publicznego. Aby uzyskać więcej informacji, zobacz [repozytorium modelu publicznego](howto-manage-models.md).
1. Jeśli interfejs nie znajduje się w repozytorium modelu publicznego, spróbuj poszukać go w dowolnych repozytoriach modelu firmy znanych dla Twojego rozwiązania. Do uzyskania dostępu do repozytorium modelu firmy potrzebne są parametry połączenia. Aby uzyskać więcej informacji, zobacz [repozytorium modelu firmy](howto-manage-models.md).
1. Jeśli nie możesz znaleźć wszystkich interfejsów w repozytorium modelu publicznego lub w repozytorium modelu firmy, możesz sprawdzić, czy urządzenie może zapewnić definicję interfejsu. Urządzenie może zaimplementować standardowy interfejs [ModelDefinition](concepts-common-interfaces.md) do publikowania informacji na temat sposobu pobierania plików interfejsu za pomocą polecenia.
1. W przypadku znalezienia plików JSON dla każdego interfejsu zaimplementowanego przez urządzenie można wyliczyć możliwości urządzenia. Użyj wcześniej napisanej logiki, aby umożliwić użytkownikom współpracującie z urządzeniem.
1. W dowolnym momencie można wywołać interfejs API Digital bliźniaczych reprezentacji, aby pobrać identyfikator modelu możliwości i identyfikatory interfejsów dla urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak odkrywać rozwiązanie IoT, Dowiedz się więcej o [platformie Azure IoT](overview-iot-plug-and-play.md) , aby korzystać z innych możliwości rozwiązania.
