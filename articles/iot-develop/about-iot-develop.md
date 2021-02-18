---
title: Wprowadzenie do tworzenia urządzeń i aplikacji na platformie Azure IoT
description: Dowiedz się, jak korzystać z usługi Azure IoT do projektowania urządzeń osadzonych i kompilowania aplikacji w chmurze obsługujących urządzenia.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: dd4e53eebe6593db457798f009d3d05ddcbd77b8
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100655029"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Co to jest projektowanie urządzeń i aplikacji w usłudze Azure IoT?

Azure IoT to zbiór usług zarządzanych i platform, które nawiązują połączenie, monitorowanie i kontrolowanie urządzeń IoT. Usługa Azure IoT oferuje deweloperom kompleksowy zestaw opcji. Dostępne opcje obejmują platformy urządzeń, obsługę usług Cloud Services, zestawów SDK i narzędzi do tworzenia aplikacji w chmurze z obsługą urządzeń.

W tym artykule opisano kilka najważniejszych zagadnień dla deweloperów, którzy rozpoczynają pracę z usługą Azure IoT. Te pojęcia będą zorientowane na użytkownika jako deweloper urządzenia IoT, a także opcje usługi Azure IoT i sposób rozpoczęcia. W tym artykule omówiono następujące koncepcje:
- [Informacje o rolach projektowania urządzeń](#device-development-roles)
- [Wybieranie sprzętu](#choosing-your-hardware)
- [Wybieranie zestawu SDK](#choosing-an-sdk)
- [Wybieranie opcji połączenia](#selecting-connection-options)

## <a name="device-development-roles"></a>Role opracowywania urządzeń
W tym artykule omówiono dwie typowe role, które można obserwować wśród deweloperów urządzeń. Jak tutaj użyto, rola to kolekcja powiązanych zadań deweloperskich. Warto zrozumieć, jakiego typu rolę programistyczną aktualnie pracujesz. Twoja rola ma wpływ na wiele wybranych opcji tworzenia.

* **Opracowywanie aplikacji dla urządzeń:** Jest zgodny z nowoczesnymi praktykami programistycznymi, przeznaczonymi dla wielu języków o wyższej kolejności i wykonywanych w systemie operacyjnym ogólnego przeznaczenia, takim jak Windows lub Linux.

* **Programowanie urządzeń osadzonych:** Opisuje urządzenia objęte ograniczeniem zasobów dla deweloperów. Urządzenie z ograniczonym zasobem często będzie używane do redukcji kosztów jednostkowych, zużycia mocy lub rozmiaru urządzenia. Te urządzenia mają bezpośrednią kontrolę nad platformą sprzętową, w której są wykonywane.

### <a name="device-application-development"></a>Opracowywanie aplikacji dla urządzeń
Deweloperzy aplikacji urządzeń dostosowują istniejące urządzenia do łączenia się z chmurą i integrują się z rozwiązaniami IoT. Te urządzenia mogą obsługiwać Języki o wyższej kolejności, takie jak C# lub Python, i często obsługują niezawodne systemy operacyjne ogólnego przeznaczenia, takie jak Windows lub Linux. Typowe urządzenia docelowe obejmują komputery, kontenery, Raspberry PiS i urządzenia przenośne. 

Zamiast opracowywać ograniczone urządzenia na dużą skalę, tacy deweloperzy koncentrują się na włączeniu określonego scenariusza IoT wymaganego przez rozwiązanie w chmurze. Niektórzy z tych deweloperów będą również współpracować z ograniczonymi urządzeniami dla rozwiązań w chmurze. W przypadku deweloperów pracujących z ograniczonymi urządzeniami zapoznaj się z tematem ścieżka [projektowania urządzeń osadzonych](#embedded-device-development) poniżej.

> [!TIP]
> Aby rozpocząć, zobacz [zestawy SDK urządzeń](about-iot-sdks.md#unconstrained-device-sdks) nieograniczone.

### <a name="embedded-device-development"></a>Projektowanie urządzeń osadzonych
Osadzone urządzenia docelowe przeznaczone do programowania, które mają ograniczoną pamięć i przetwarzanie. Ograniczone urządzenia ograniczają, co można osiągnąć w porównaniu z tradycyjną platformą programistyczną.

Urządzenia osadzone zwykle korzystają z systemu operacyjnego w czasie rzeczywistym (RTO) lub bez systemu operacyjnego. Urządzenia osadzone mają pełną kontrolę nad ich sprzętem ze względu na brak systemu operacyjnego ogólnego przeznaczenia. Dzięki temu urządzenia osadzone są dobrym rozwiązaniem dla systemów w czasie rzeczywistym.

Bieżące osadzone zestawy SDK są przeznaczone dla języka **C** . Osadzone zestawy SDK nie zapewniają systemu operacyjnego lub pomocy technicznej platformy Azure RTO. Są one zaprojektowane z uwzględnieniem osadzonych elementów docelowych. Zagadnienia dotyczące projektowania obejmują potrzebę minimalnego rozmiaru i nie przydzielają pamięci.

Jeśli urządzenie jest w stanie uruchomić system operacyjny ogólnego przeznaczenia, zalecamy wykonanie poniższej ścieżki [opracowywania aplikacji urządzenia](#device-application-development) . Zapewnia bogatszy zestaw opcji programistycznych.

> [!TIP]
> Zapoznaj się z [ograniczonymi zestawami SDK urządzeń](about-iot-sdks.md#constrained-device-sdks) , aby rozpocząć pracę.

## <a name="choosing-your-hardware"></a>Wybieranie sprzętu
Urządzenia IoT na platformie Azure są podstawowymi blokami konstrukcyjnymi rozwiązania IoT i są odpowiedzialne za przestrzeganie środowiska i korzystanie z nich. Istnieje wiele różnych typów urządzeń IoT, które ułatwiają zrozumienie rodzajów urządzeń, które istnieją i jakie mogą mieć wpływ na proces opracowywania.

Aby uzyskać więcej informacji na temat różnic między typami urządzeń pokrytymi w tym artykule, Przeczytaj [Informacje o typach urządzeń IoT](concepts-iot-device-types.md).

## <a name="choosing-an-sdk"></a>Wybieranie zestawu SDK
Jako deweloper urządzenia Azure IoT masz różne zestawy SDK urządzeń i zestawy SDK usług platformy Azure, które ułatwiają tworzenie aplikacji w chmurze obsługujących urządzenia. Zestawy SDK usprawnią pracę programistyczną i upraszczają większość złożoności łączenia urządzeń i zarządzania nimi. 

Zgodnie z opisem w sekcji [role dotyczące projektowania urządzeń](#device-development-roles) istnieją trzy rodzaje zestawów SDK IoT na potrzeby projektowania urządzeń:
- Zestawy SDK urządzeń osadzonych (dla urządzeń z ograniczeniami)
- Zestawy SDK urządzeń (w przypadku używania wyższych kolejności do łączenia istniejących urządzeń z aplikacjami IoT)
- Zestawy SDK usług (do tworzenia rozwiązań usługi Azure IoT, które łączą urządzenia z usługami)

Aby dowiedzieć się więcej na temat wybierania zestawu SDK urządzeń lub usługi Azure IoT, zobacz [Omówienie zestawów SDK urządzeń usługi Azure IoT](about-iot-sdks.md).

## <a name="selecting-connection-options"></a>Wybieranie opcji połączenia
Ważnym krokiem w procesie tworzenia jest wybranie zestawu opcji, które będą używane do nawiązywania połączenia z urządzeniami i zarządzania nimi. Istnieją dwa krytyczne aspekty, które należy wziąć pod uwagę:
- Wybieranie platformy aplikacji IoT do hostowania urządzeń. W przypadku usługi Azure IoT oznacza to wybranie IoT Hub lub IoT Central.
- Wybieranie narzędzi deweloperskich, które ułatwiają łączenie i monitorowanie urządzeń oraz zarządzanie nimi.

Aby dowiedzieć się więcej na temat wybierania platformy aplikacji i narzędzi, zobacz [Omówienie: opcje połączenia dla deweloperów urządzeń usługi Azure IoT](concepts-overview-connection-options.md).

## <a name="next-steps"></a>Następne kroki
Wybierz jedną z następujących serii szybkiego startu, która jest najbardziej odpowiednia dla roli deweloperskiej. W tych artykułach przedstawiono podstawowe informacje na temat tworzenia aplikacji usługi Azure IoT na potrzeby hostowania urządzeń przy użyciu zestawu SDK, łączenia urządzenia i wysyłania danych telemetrycznych.  
- Tworzenie aplikacji dla urządzeń:  [Szybki Start: wysyłanie danych telemetrycznych z urządzenia do usługi Azure IoT Central](quickstart-send-telemetry-python.md)
- Projektowanie urządzeń osadzonych: [wprowadzenie do tworzenia urządzeń z usługą Azure IoT Embedded](quickstart-device-development.md)
