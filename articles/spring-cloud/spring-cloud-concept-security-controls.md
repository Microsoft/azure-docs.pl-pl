---
title: Koncepcja — mechanizmy kontroli zabezpieczeń dla usługi Azure wiosennej w chmurze
description: Korzystaj z kontroli zabezpieczeń wbudowanych w usługę w chmurze Azure wiosną.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594988"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Mechanizmy kontroli zabezpieczeń dla usługi Azure wiosennej w chmurze
Funkcje kontroli zabezpieczeń są wbudowane w usługę w chmurze Azure wiosną.

Kontrola zabezpieczeń jest jakością lub funkcją usługi platformy Azure, która przyczynia się do możliwości zapobiegania i wykrywania luk w zabezpieczeniach, a także reagowanie na nie.  Dla każdej kontrolki używamy *opcji "tak* " lub " *nie* ", aby wskazać, czy jest ona aktualnie włączona dla usługi.  W przypadku kontrolki, która nie ma zastosowania do usługi, używana jest *wartość N/A* . 

**Kontrole zabezpieczeń ochrony danych**

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|:-------------|:-------|:-------------------------------|:----------------------|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Użytkownik przekazał źródło i artefakty, ustawienia serwera konfiguracji, ustawienia aplikacji i dane w magazynie trwałym są przechowywane w usłudze Azure Storage, która automatycznie szyfruje zawartość w stanie spoczynku.<br><br>Pamięć podręczna serwera konfiguracji, pliki binarne środowiska uruchomieniowego skompilowane z przekazanego źródła, a Dzienniki aplikacji w okresie istnienia aplikacji są zapisywane na dysku zarządzanym przez platformę Azure, który automatycznie szyfruje zawartość w stanie spoczynku.<br><br>Obrazy kontenerów skompilowane ze źródła przekazanego przez użytkownika są zapisywane w Azure Container Registry, które automatycznie szyfruje zawartość obrazu w stanie spoczynku. | [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Szyfrowanie po stronie serwera dla usługi Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Magazyn obrazów kontenerów w Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Szyfrowanie przejściowe | Tak | Publiczne punkty końcowe aplikacji użytkownika domyślnie używają protokołu HTTPS dla ruchu przychodzącego. |  |
| Wywołania interfejsu API są szyfrowane | Tak | Wywołania zarządzania w celu skonfigurowania usługi w chmurze Azure wiosennej są wykonywane za pośrednictwem wywołań Azure Resource Manager za pośrednictwem protokołu HTTPS | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

