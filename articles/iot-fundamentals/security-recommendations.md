---
title: Zalecenia dotyczące zabezpieczeń dla usługi Azure IoT | Microsoft Docs
description: Ten artykuł zawiera podsumowanie dodatkowych kroków zapewniających bezpieczeństwo rozwiązań IoT Hub platformy Azure.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: 5dd09988d37982c41b761688492bd2dc3642b2db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81728980"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Zalecenia dotyczące zabezpieczeń dla wdrożenia usługi Azure Internet rzeczy (IoT)

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń IoT. Wdrożenie tych zaleceń pomoże Ci zrealizować swoje zobowiązania w zakresie zabezpieczeń zgodnie z opisem w naszym wspólnym modelu odpowiedzialności. Aby uzyskać więcej informacji na temat tego, co firma Microsoft może spełnić obowiązki dostawcy usług, Przeczytaj [udostępnione obowiązki w chmurze obliczeniowej](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Niektóre zalecenia zawarte w tym artykule mogą być automatycznie monitorowane przez Azure Security Center. Azure Security Center to pierwszy wiersz obrony w ochronie zasobów na platformie Azure. Okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu identyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu ich rozwiązywania.

- Aby uzyskać więcej informacji o Azure Security Center zaleceniach, zapoznaj się [z zaleceniami dotyczącymi zabezpieczeń w programie Azure Security Center](../security-center/security-center-recommendations.md).
- Aby uzyskać informacje na temat Azure Security Center, zobacz [co to jest Azure Security Center?](../security-center/security-center-intro.md)

## <a name="general"></a>Ogólne

| Zalecenie | Komentarze | Obsługiwane przez ASC |
|-|----|--|
| Bądź na bieżąco | Używaj najnowszych wersji obsługiwanych platform, języków programowania, protokołów i struktur. | - |
| Zabezpieczanie kluczy uwierzytelniania | Przechowuj identyfikatory urządzeń i ich klucze uwierzytelniania fizycznie bezpiecznie po wdrożeniu. Pozwoli to uniknąć powstania złośliwego urządzenia jako zarejestrowanego urządzenia. | - |
| Użyj zestawów SDK urządzeń, gdy jest to możliwe | Zestawy SDK urządzeń implementują różne funkcje zabezpieczeń, takie jak szyfrowanie, uwierzytelnianie i tak dalej, aby pomóc w opracowaniu niezawodnej i bezpiecznej aplikacji urządzenia. Aby uzyskać więcej informacji [, zobacz Omówienie zestawów SDK platformy IoT Hub Azure i ich używanie](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) . | - |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem 

| Zalecenie | Komentarze | Obsługiwane przez ASC |
|-|----|--|
| Definiowanie kontroli dostępu do centrum | [Zrozumienie i Definiowanie typu dostępu](iot-security-deployment.md#securing-the-cloud) każdego składnika, który będzie znajdował się w rozwiązaniu IoT Hub, na podstawie jego funkcjonalności. Dozwolonymi uprawnieniami są *odczyty rejestru*, *RegistryReadWrite*, *serviceconnect*i *DeviceConnect*. Domyślne [zasady dostępu współdzielonego w usłudze IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) mogą także ułatwić Definiowanie uprawnień dla każdego składnika na podstawie jego roli. | - |
| Definiowanie kontroli dostępu dla usług zaplecza | Dane pozyskane przez rozwiązanie IoT Hub mogą być używane przez inne usługi platformy Azure, takie jak [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)i [Magazyn obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Upewnij się, że rozumiesz i zezwolisz na odpowiednie uprawnienia dostępu zgodnie z opisem dla tych usług. | - |

## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze | Obsługiwane przez ASC |
|-|----|--|
| Bezpieczne uwierzytelnianie urządzeń | Zapewnianie bezpiecznej komunikacji między urządzeniami i centrum IoT Hub przy użyciu [unikatowego klucza tożsamości lub tokenu zabezpieczającego](iot-security-deployment.md#iot-hub-security-tokens)lub [certyfikatu X. 509](iot-security-deployment.md#x509-certificate-based-device-authentication) dla każdego urządzenia. Użyj odpowiedniej metody, aby [użyć tokenów zabezpieczających na podstawie wybranego protokołu (MQTT, AMQP lub https)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). | - |
| Zabezpieczanie komunikacji urządzeń | IoT Hub zabezpiecza połączenie z urządzeniami przy użyciu standardu Transport Layer Security (TLS), obsługując wersje 1,2 i 1,0. Aby zapewnić maksymalne bezpieczeństwo, użyj [protokołu TLS 1,2](https://tools.ietf.org/html/rfc5246) . | - |
| Bezpieczna komunikacja z usługą | IoT Hub udostępnia punkty końcowe do nawiązywania połączeń z usługami zaplecza, takimi jak [usługa Azure Storage](/azure/storage/) , lub [Event Hubs](/azure/event-hubs) przy użyciu tylko protokołu TLS, a punkt końcowy nie jest ujawniany na nieszyfrowanym kanale. Gdy te dane osiągną te usługi zaplecza do przechowywania lub analizy, należy zastosować odpowiednie metody zabezpieczeń i szyfrowania dla tej usługi oraz chronić poufne informacje w zapleczu. | - |

## <a name="networking"></a>Networking

| Zalecenie | Komentarze | Obsługiwane przez ASC |
|-|----|--|
| Ochrona dostępu do urządzeń | Aby uniknąć niepożądanego dostępu, przechowuj porty sprzętowe na urządzeniach jako minimum systemu operacyjnego. Ponadto mechanizmy kompilacji, aby zapobiec lub wykryć fizyczne manipulowanie urządzeniem. Przeczytaj temat [najlepsze rozwiązania z zakresu zabezpieczeń IoT](iot-security-best-practices.md) , aby uzyskać szczegółowe informacje. | - |
| Tworzenie bezpiecznego sprzętu | Włączenie funkcji zabezpieczeń, takich jak zaszyfrowany magazyn lub moduł TPM (TPM), aby zapewnić lepszą ochronę urządzeń i infrastruktury. Zadbaj, aby system operacyjny i sterowniki sterownika były uaktualnione do najnowszych wersji, a jeśli miejsce zezwala, zainstaluj program antywirusowy i oprogramowanie chroniące przed złośliwym kodem. Przeczytaj temat [Architektura zabezpieczeń IoT](iot-security-architecture.md) , aby zrozumieć, jak może to pomóc w eliminowaniu kilku zagrożeń bezpieczeństwa. | - |

## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze | Obsługiwane przez ASC |
|-|----|--|
| Monitoruj nieautoryzowany dostęp do urządzeń |  Funkcja rejestrowania systemu operacyjnego urządzenia służy do monitorowania wszelkich naruszeń zabezpieczeń lub fizycznego manipulowania urządzeniem lub jego portów. | - |
| Monitoruj rozwiązanie IoT w chmurze | Monitoruj ogólną kondycję rozwiązania IoT Hub przy użyciu [metryk w Azure monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). | - |
| Konfigurowanie diagnostyki | Uważnie Monitoruj operacje przez rejestrowanie zdarzeń w rozwiązaniu, a następnie wysyłanie dzienników diagnostycznych do Azure Monitor, aby uzyskać wgląd w wydajność. Aby uzyskać więcej informacji, przeczytaj artykuł [monitorowanie i diagnozowanie problemów w centrum IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) . | - |

## <a name="next-steps"></a>Następne kroki

W przypadku zaawansowanych scenariuszy dotyczących usługi Azure IoT konieczne może być uwzględnienie dodatkowych wymagań w zakresie zabezpieczeń. Zobacz [Architektura zabezpieczeń IoT](iot-security-architecture.md) , aby uzyskać więcej wskazówek.

