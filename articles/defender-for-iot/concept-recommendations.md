---
title: Zalecenia dotyczące zabezpieczeń IoT Hub
description: Dowiedz się więcej na temat koncepcji zaleceń dotyczących zabezpieczeń i sposobu ich użycia w usłudze Defender dla IoT Hub.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779345"
---
# <a name="security-recommendations-for-iot-hub"></a>Zalecenia dotyczące zabezpieczeń IoT Hub

Usługa Defender for IoT skanuje zasoby platformy Azure i urządzenia IoT oraz zapewnia zalecenia dotyczące zabezpieczeń, aby zmniejszyć obszar narażony na ataki.
Zalecenia dotyczące zabezpieczeń są funkcjonalne i mają na celu pomóc klientom w spełnieniu najlepszych rozwiązań w zakresie zabezpieczeń.

W tym artykule znajdziesz listę zaleceń, które mogą być wyzwalane na IoT Hub.

## <a name="built-in-recommendations-in-iot-hub"></a>Wbudowane zalecenia w IoT Hub

Alerty dotyczące rekomendacji zapewniają wgląd i sugestie dotyczące działań usprawniających stan zabezpieczeń środowiska.

| Ważność | Nazwa | Źródło danych | Opis |
|--|--|--|--|
| Wys. | Identyczne poświadczenia uwierzytelniania używane przez wiele urządzeń | Usługa IoT Hub | Poświadczenia uwierzytelniania IoT Hub są używane przez wiele urządzeń. Ten proces może wskazywać, że urządzenie illegitimate personifikuje wiarygodne urządzenie. Zduplikowane użycie poświadczeń zwiększa ryzyko personifikacji urządzenia przez złośliwy aktor. |
| Śred. | Domyślne zasady filtrowania adresów IP powinny być Odmów | Usługa IoT Hub | Konfiguracja filtru IP powinna mieć reguły zdefiniowane dla dozwolonego ruchu i powinna domyślnie odmówić całego ruchu. |
| Śred. | Reguła filtru IP obejmuje duży zakres adresów IP | Usługa IoT Hub | Źródłowy zakres adresów IP reguły filtru adresów IP jest zbyt duży. Nadmiernie ograniczające reguły mogą uwidocznić Centrum IoT w złośliwych aktorach. |
| Niski | Włączanie dzienników diagnostycznych w IoT Hub | Usługa IoT Hub | Włącz dzienniki i zachowaj je przez maksymalnie rok. Przechowywanie dzienników umożliwia ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |

## <a name="next-steps"></a>Następne kroki

- Usługa Defender for IoT [— Omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej [na temat badania urządzenia](how-to-investigate-device.md)
