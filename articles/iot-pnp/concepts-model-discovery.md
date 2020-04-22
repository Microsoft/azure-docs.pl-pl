---
title: Implementowanie odnajdowania modelu podglądu i odnowy ioT Plug and Play | Dokumenty firmy Microsoft
description: Jako deweloper rozwiązania dowiedz się, jak zaimplementować odnajdowanie modeli IoT Plug and Play w rozwiązaniu.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 74eb38269a3c7fbdc6d95554a8a8cef14eb0b787
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770472"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementowanie odnajdowania modelu IoT Plug and Play Preview w rozwiązaniu IoT

W tym artykule opisano, jak jako deweloper rozwiązania można zaimplementować odnajdowanie modelu IoT Plug and Play Preview w rozwiązaniu IoT.  Odnajdowanie modeli IoT Plug and Play to sposób, w jaki urządzenia Typu Plug and Play IoT identyfikują obsługiwane modele i interfejsy oraz jak rozwiązanie IoT pobiera te modele i interfejsy.

Istnieją dwie szerokie kategorie rozwiązań IoT: rozwiązania specjalnie zaprojektowane, które współpracują ze znanym zestawem urządzeń IoT Plug and Play oraz rozwiązania oparte na modelu, które współpracują z dowolnym urządzeniem Typu Plug and Play IoT.

W tym artykule opisano sposób implementacji odnajdowania modelu w obu typach rozwiązania.

## <a name="model-discovery"></a>Odnajdywanie modelu

Gdy urządzenie Typu Plug and Play IoT po raz pierwszy łączy się z centrum IoT hub, wysyła komunikat telemetrii informacji modelu. Ten komunikat zawiera identyfikatory interfejsów, które implementuje urządzenie. Aby rozwiązanie działało z urządzeniem, musi rozwiązać te identyfikatory i pobrać definicje dla każdego interfejsu.

Poniżej przedstawiono kroki, które urządzenie IoT Plug and Play wykonuje, gdy używa usługi inicjowania obsługi administracyjnej (DPS) do łączenia się z koncentratorem:

1. Gdy urządzenie jest włączone, łączy się z globalnym punktem końcowym dps i uwierzytelnia się przy użyciu jednej z dozwolonych metod.
1. Dps następnie uwierzytelnia urządzenie i wyszukuje regułę, która informuje, które Centrum IoT przypisać urządzenie do. Dps następnie rejestruje urządzenie z tym koncentratorem.
1. DPS zwraca ciąg połączenia usługi IoT Hub do urządzenia.
1. Następnie urządzenie wysyła komunikat telemetryczny odnajdywania do centrum IoT Hub. Komunikat telemetryczny odnajdywania zawiera identyfikatory interfejsów implementuje urządzenia.
1. Urządzenie IoT Plug and Play jest teraz gotowe do pracy z rozwiązaniem, które używa centrum IoT Hub.

Jeśli urządzenie łączy się bezpośrednio z centrum IoT hub, łączy się przy użyciu ciągu połączenia, który jest osadzony w kodzie urządzenia. Następnie urządzenie wysyła komunikat telemetryczny odnajdywania do centrum IoT Hub.

Zobacz [interfejs informacji modelu,](concepts-common-interfaces.md) aby dowiedzieć się więcej o wiadomości telemetrii informacji modelu.

### <a name="purpose-built-iot-solutions"></a>Specjalnie zaprojektowane rozwiązania IoT

Specjalnie zaprojektowane rozwiązanie IoT współpracuje ze znanym zestawem modeli i interfejsów urządzeń IoT Plug and Play.

Będziesz mieć model możliwości i interfejsy dla urządzeń, które będą łączyć się z rozwiązaniem z wyprzedzeniem. Aby przygotować rozwiązanie, należy wykonać następujące czynności:

1. Przechowuj pliki JSON interfejsu w [repozytorium modelu,](./howto-manage-models.md) gdzie rozwiązanie można je odczytać.
1. Zapisuj logikę w rozwiązaniu IoT na podstawie oczekiwanych modeli funkcji IoT Plug and Play i interfejsu.
1. Subskrybuj powiadomienia z centrum IoT hub, którego używa twoje rozwiązanie.

Po otrzymaniu powiadomienia o nowym połączeniu urządzenia wykonaj następujące czynności:

1. Przeczytaj komunikat telemetryczny odnajdywania, aby pobrać identyfikatory modelu możliwości i interfejsów zaimplementowanych przez urządzenie.
1. Porównaj identyfikator modelu możliwości z identyfikatorami modeli możliwości przechowywanych z wyprzedzeniem.
1. Teraz już wiesz, jaki typ urządzenia się połączył. Użyj logiki, którą napisałeś wcześniej, aby umożliwić użytkownikom odpowiednią interakcję z urządzeniem.

### <a name="model-driven-solutions"></a>Rozwiązania oparte na modelu

Rozwiązanie IoT oparte na modelu może współpracować z dowolnym urządzeniem Typu Plug and Play IoT. Tworzenie rozwiązania IoT opartego na modelu jest bardziej złożone, ale zaletą jest to, że rozwiązanie działa z dowolnymi urządzeniami dodanymi w przyszłości.

Aby utworzyć rozwiązanie IoT oparte na modelu, należy utworzyć logikę względem interfejsów typu Plug and Play IoT: telemetrii, właściwości i poleceń. Logika rozwiązania IoT reprezentuje urządzenie, łącząc wiele funkcji telemetrycznych, właściwości i poleceń.

Rozwiązanie musi również subskrybować powiadomienia z używanego centrum IoT Hub.

Gdy rozwiązanie otrzyma powiadomienie o nowym połączeniu z urządzeniem, wykonaj następujące kroki:

1. Przeczytaj komunikat telemetryczny odnajdywania, aby pobrać identyfikatory modelu możliwości i interfejsów zaimplementowanych przez urządzenie.
1. Dla każdego identyfikatora przeczytaj pełny plik JSON, aby znaleźć możliwości urządzenia.
1. Sprawdź, czy każdy interfejs jest obecny w pamięci podręcznej, które zostały utworzone do przechowywania plików JSON pobranych wcześniej przez rozwiązanie.
1. Następnie sprawdź, czy interfejs o tym identyfikatorze jest obecny w repozytorium modelu publicznego. Aby uzyskać więcej informacji, zobacz [Repozytorium modeli publicznych](howto-manage-models.md).
1. Jeśli interfejs nie jest obecny w repozytorium modelu publicznego, spróbuj wyszukać go w dowolnych repozytoriach modelu firmy znanych z rozwiązania. Aby uzyskać dostęp do repozytorium modelu firmy, potrzebny jest ciąg połączenia. Aby uzyskać więcej informacji, zobacz [Repozytorium modeli firmy](howto-manage-models.md).
1. Jeśli nie można znaleźć wszystkie interfejsy w repozytorium modelu publicznego lub w repozytorium modelu firmy, można sprawdzić, czy urządzenie może podać definicję interfejsu. Urządzenie może zaimplementować standardowy interfejs [ModelDefinition,](concepts-common-interfaces.md) aby opublikować informacje dotyczące sposobu pobierania plików interfejsu za pomocą polecenia.
1. Jeśli znaleziono pliki JSON dla każdego interfejsu zaimplementowanego przez urządzenie, można wyliczyć możliwości urządzenia. Użyj logiki, którą napisałeś wcześniej, aby umożliwić użytkownikom interakcję z urządzeniem.
1. W dowolnym momencie można wywołać interfejs API cyfrowych bliźniąt, aby pobrać identyfikator modelu możliwości i identyfikatory interfejsu dla urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiedziałeś się o odnajdowaniu modelu rozwiązania IoT, dowiedz się więcej o [platformie Azure IoT,](overview-iot-plug-and-play.md) aby wykorzystać inne możliwości rozwiązania.
