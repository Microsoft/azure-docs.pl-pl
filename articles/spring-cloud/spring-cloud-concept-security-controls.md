---
title: Koncepcja — mechanizmy kontroli zabezpieczeń dla usługi Azure wiosennej w chmurze
description: Korzystaj z kontroli zabezpieczeń wbudowanych w usługę w chmurze Azure wiosną.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1472da4c87dc4579a30290460fb7811cf228be47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892492"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Mechanizmy kontroli zabezpieczeń dla usługi Azure Spring Cloud

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Funkcje kontroli zabezpieczeń są wbudowane w usługę w chmurze Azure wiosną.

Kontrola zabezpieczeń jest jakością lub funkcją usługi platformy Azure, która przyczynia się do możliwości zapobiegania i wykrywania luk w zabezpieczeniach, a także reagowanie na nie.  Dla każdej kontrolki używamy *opcji "tak* " lub " *nie* ", aby wskazać, czy jest ona aktualnie włączona dla usługi.  W przypadku kontrolki, która nie ma zastosowania do usługi, używana jest *wartość N/A* . 

**Kontrole zabezpieczeń ochrony danych**

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|:-------------|:-------|:-------------------------------|:----------------------|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Użytkownik przekazał źródło i artefakty, ustawienia serwera konfiguracji, ustawienia aplikacji i dane w magazynie trwałym są przechowywane w usłudze Azure Storage, która automatycznie szyfruje zawartość w stanie spoczynku.<br><br>Pamięć podręczna serwera konfiguracji, pliki binarne środowiska uruchomieniowego skompilowane z przekazanego źródła i dzienniki aplikacji w okresie istnienia aplikacji są zapisywane na dysku zarządzanym platformy Azure, który automatycznie szyfruje zawartość w stanie spoczynku.<br><br>Obrazy kontenerów skompilowane ze źródła przekazanego przez użytkownika są zapisywane w Azure Container Registry, które automatycznie szyfruje zawartość obrazu w stanie spoczynku. | [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Szyfrowanie po stronie serwera dla usługi Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Magazyn obrazów kontenerów w Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Szyfrowanie przejściowe | Tak | Publiczne punkty końcowe aplikacji użytkownika domyślnie używają protokołu HTTPS dla ruchu przychodzącego. |  |
| Wywołania interfejsu API są szyfrowane | Tak | Wywołania zarządzania w celu skonfigurowania usługi w chmurze Azure wiosennej są wykonywane za pośrednictwem wywołań Azure Resource Manager za pośrednictwem protokołu HTTPS | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

**Kontrola zabezpieczeń dostępu do sieci**

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|:-------------|:-------|:-------------------------------|:----------------------|
| Tag usługi | Tak | Użyj znacznika usługi **AzureSpringCloud** do definiowania kontroli dostępu do sieci wychodzącej w [sieciowych grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) lub [zaporze platformy Azure](https://docs.microsoft.com/azure/firewall/service-tags), aby zezwolić na ruch do aplikacji w chmurze z systemem Azure.<br><br>*Uwaga:* Obecnie tylko nowe wystąpienie usługi Azure wiosenne w chmurze utworzone po 2020/07/14 obsługuje tag usługi **AzureSpringCloud** . | [Tagi usługi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) |

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: wdrażanie pierwszej aplikacji w chmurze Azure wiosny](spring-cloud-quickstart.md)
