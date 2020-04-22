---
title: Zalecenia dotyczące zabezpieczeń dla usługi Azure IoT | Dokumenty firmy Microsoft
description: W tym artykule podsumowano dodatkowe kroki, aby zapewnić bezpieczeństwo w rozwiązaniu usługi Azure IoT Hub.
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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728980"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Zalecenia dotyczące zabezpieczeń dla wdrażania internetu rzeczy platformy Azure (IoT)

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń dla IoT. Wdrożenie tych zaleceń pomoże Ci wypełnić swoje zobowiązania w zakresie bezpieczeństwa opisane w naszym modelu wspólnej odpowiedzialności. Aby uzyskać więcej informacji na temat tego, co firma Microsoft robi, aby wypełniać obowiązki dostawcy usług, zobacz [Współużytkowane obowiązki związane z przetwarzaniem w chmurze](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Niektóre z zaleceń zawartych w tym artykule mogą być automatycznie monitorowane przez usługę Azure Security Center. Usługa Azure Security Center to pierwsza linia ochrony w ochronie zasobów na platformie Azure. Okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu rozwiązania ich.

- Aby uzyskać więcej informacji na temat zaleceń usługi Azure Security Center, zobacz [Zalecenia dotyczące zabezpieczeń w usłudze Azure Security Center](../security-center/security-center-recommendations.md).
- Aby uzyskać informacje na temat Usługi Azure Security Center, zobacz [What is Azure Security Center?](../security-center/security-center-intro.md)

## <a name="general"></a>Ogólne

| Zalecenie | Komentarze | Obsługiwane przez ASC |
|-|----|--|
| Bądź na bieżąco | Użyj najnowszych wersji obsługiwanych platform, języków programowania, protokołów i struktur. | - |
| Bezpieczeństwo kluczy uwierzytelniania | Zachowaj identyfikatory urządzeń i ich klucze uwierzytelniania fizycznie bezpieczne po wdrożeniu. Pozwoli to uniknąć złośliwego urządzenia maskarady jako zarejestrowanego urządzenia. | - |
| W miarę możliwości używaj sdk urządzeń | SDK urządzeń implementują wiele funkcji zabezpieczeń, takich jak szyfrowanie, uwierzytelnianie i tak dalej, aby pomóc w tworzeniu niezawodnej i bezpiecznej aplikacji urządzenia. Zobacz [Opis i używanie zestawów SDK usługi Azure IoT Hub, aby](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) uzyskać więcej informacji. | - |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem 

| Zalecenie | Komentarze | Obsługiwane przez ASC |
|-|----|--|
| Definiowanie kontroli dostępu dla koncentratora | [Zrozumienie i zdefiniowanie typu dostępu](iot-security-deployment.md#securing-the-cloud) każdego składnika będzie miał w rozwiązaniu Usługi IoT Hub, na podstawie funkcji. Dozwolone uprawnienia to *Odczyt rejestru,* *RegistryReadWrite,* *ServiceConnect*i *DeviceConnect*. Domyślne [zasady dostępu współdzielonego w centrum IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) może również pomóc w zdefiniowaniu uprawnień dla każdego składnika na podstawie jego roli. | - |
| Definiowanie kontroli dostępu dla usług zaplecza | Dane pozyskiwania przez rozwiązanie Usługi IoT Hub mogą być używane przez inne usługi platformy Azure, takie jak [Usługi Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/) [Analiza strumienia,](https://docs.microsoft.com/azure/stream-analytics/) [Usługa aplikacji,](https://docs.microsoft.com/azure/app-service/) [Aplikacje logiczne](https://docs.microsoft.com/azure/logic-apps/)i [magazyn obiektów Blob.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Upewnij się, że rozumiesz i zezwalaj na odpowiednie uprawnienia dostępu udokumentowane dla tych usług. | - |

## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze | Obsługiwane przez ASC |
|-|----|--|
| Bezpieczne uwierzytelnianie urządzenia | Zapewnij bezpieczną komunikację między urządzeniami a centrum IoT, używając [unikatowego klucza tożsamości lub tokenu zabezpieczającego](iot-security-deployment.md#iot-hub-security-tokens)lub [certyfikatu X.509 na urządzeniu](iot-security-deployment.md#x509-certificate-based-device-authentication) dla każdego urządzenia. Użyj odpowiedniej metody [używania tokenów zabezpieczających opartych na wybranym protokole (MQTT, AMQP lub HTTPS).](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security) | - |
| Bezpieczna komunikacja z urządzeniem | Usługa IoT Hub zabezpiecza połączenie z urządzeniami przy użyciu standardu TLS (Transport Layer Security), obsługując wersje 1.2 i 1.0. Użyj [protokołu TLS 1.2,](https://tools.ietf.org/html/rfc5246) aby zapewnić maksymalne bezpieczeństwo. | - |
| Bezpieczna komunikacja serwisowa | Usługa IoT Hub udostępnia punkty końcowe do łączenia się z usługami [zaplecza,](/azure/storage/) takimi jak Usługa Azure Storage lub [Usługi Event Hubs](/azure/event-hubs) przy użyciu tylko protokołu TLS, a żaden punkt końcowy nie jest udostępniany w kanale niezaszyfrowanym. Gdy te dane dotrą do tych usług wewnętrznej bazy danych do przechowywania lub analizy, upewnij się, że stosuje odpowiednie metody zabezpieczeń i szyfrowania dla tej usługi i chronić poufne informacje w wewnętrznej bazy danych. | - |

## <a name="networking"></a>Networking

| Zalecenie | Komentarze | Obsługiwane przez ASC |
|-|----|--|
| Ochrona dostępu do urządzeń | Utrzymuj porty sprzętowe w urządzeniach do minimum, aby uniknąć niepożądanego dostępu. Ponadto należy utworzyć mechanizmy zapobiegające lub wykrywające fizyczne manipulowanie urządzeniem. Przeczytaj [najważniejsze wskazówki dotyczące zabezpieczeń IoT,](iot-security-best-practices.md) aby uzyskać szczegółowe informacje. | - |
| Tworzenie bezpiecznego sprzętu | Uwzględnij funkcje zabezpieczeń, takie jak szyfrowana pamięć masowa lub moduł TPM (Trusted Platform Module), aby zapewnić większe bezpieczeństwo urządzeń i infrastruktury. Utrzymuj uaktualnienie systemu operacyjnego urządzenia i sterowników do najnowszych wersji, a jeśli pozwala na to miejsce, zainstaluj funkcje antywirusowe i chroniące przed złośliwym oprogramowaniem. Przeczytaj [architekturę zabezpieczeń IoT,](iot-security-architecture.md) aby dowiedzieć się, jak może to pomóc w ograniczeniu kilku zagrożeń bezpieczeństwa. | - |

## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze | Obsługiwane przez ASC |
|-|----|--|
| Monitorowanie nieautoryzowanego dostępu do urządzeń |  Funkcja rejestrowania systemu operacyjnego urządzenia służy do monitorowania wszelkich naruszeń zabezpieczeń lub fizycznej ingerencji urządzenia lub jego portów. | - |
| Monitorowanie rozwiązania IoT z chmury | Monitoruj ogólną kondycję rozwiązania Usługi IoT Hub przy użyciu [metryk w usłudze Azure Monitor.](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) | - |
| Konfigurowanie diagnostyki | Uważnie obserwuj operacje, rejestrując zdarzenia w rozwiązaniu, a następnie wysyłając dzienniki diagnostyczne do usługi Azure Monitor, aby uzyskać wgląd w wydajność. Przeczytaj [monitorowanie i diagnozowanie problemów w centrum IoT, aby](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) uzyskać więcej informacji. | - |

## <a name="next-steps"></a>Następne kroki

W przypadku zaawansowanych scenariuszy dotyczących usługi Azure IoT może być konieczne rozważenie dodatkowych wymagań dotyczących zabezpieczeń. Zobacz [architekturę zabezpieczeń IoT,](iot-security-architecture.md) aby uzyskać więcej wskazówek.

