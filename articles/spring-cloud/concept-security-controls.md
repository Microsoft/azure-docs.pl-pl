---
title: Mechanizmy kontroli zabezpieczeń dla usługi Azure Spring Cloud
description: Korzystaj z kontroli zabezpieczeń wbudowanych w usługę w chmurze Azure wiosną.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1447e7eec9909c8af33005aab00c267e1a251720
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105966830"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Mechanizmy kontroli zabezpieczeń dla usługi Azure Spring Cloud

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Funkcje kontroli zabezpieczeń są wbudowane w usługę w chmurze Azure wiosną.

Kontrola zabezpieczeń jest jakością lub funkcją usługi platformy Azure, która przyczynia się do możliwości zapobiegania i wykrywania luk w zabezpieczeniach, a także reagowanie na nie.  Dla każdej kontrolki używamy *opcji "tak* " lub " *nie* ", aby wskazać, czy jest ona aktualnie włączona dla usługi.  W przypadku kontrolki, która nie ma zastosowania do usługi, używana jest *wartość N/A* . 

**Kontrole zabezpieczeń ochrony danych**

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|:-------------|:-------|:-------------------------------|:----------------------|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Użytkownik przekazał źródło i artefakty, ustawienia serwera konfiguracji, ustawienia aplikacji i dane w magazynie trwałym są przechowywane w usłudze Azure Storage, która automatycznie szyfruje zawartość w stanie spoczynku.<br><br>Pamięć podręczna serwera konfiguracji, pliki binarne środowiska uruchomieniowego skompilowane z przekazanego źródła i dzienniki aplikacji w okresie istnienia aplikacji są zapisywane na dysku zarządzanym platformy Azure, który automatycznie szyfruje zawartość w stanie spoczynku.<br><br>Obrazy kontenerów skompilowane ze źródła przekazanego przez użytkownika są zapisywane w Azure Container Registry, które automatycznie szyfruje zawartość obrazu w stanie spoczynku. | [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)<br><br>[Szyfrowanie po stronie serwera dla usługi Azure Managed disks](../virtual-machines/disk-encryption.md)<br><br>[Magazyn obrazów kontenerów w Azure Container Registry](../container-registry/container-registry-storage.md) |
| Szyfrowanie przejściowe | Tak | Publiczne punkty końcowe aplikacji użytkownika domyślnie używają protokołu HTTPS dla ruchu przychodzącego. |  |
| Wywołania interfejsu API są szyfrowane | Tak | Wywołania zarządzania w celu skonfigurowania usługi w chmurze Azure wiosennej są wykonywane za pośrednictwem wywołań Azure Resource Manager za pośrednictwem protokołu HTTPS | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**Kontrola zabezpieczeń dostępu do sieci**

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|:-------------|:-------|:-------------------------------|:----------------------|
| Tag usługi | Tak | Użyj znacznika usługi **AzureSpringCloud** do definiowania kontroli dostępu do sieci wychodzącej w [sieciowych grupach zabezpieczeń](../virtual-network/network-security-groups-overview.md#security-rules) lub [zaporze platformy Azure](../firewall/service-tags.md), aby zezwolić na ruch do aplikacji w chmurze z systemem Azure. | [Tagi usługi](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: wdrażanie pierwszej aplikacji w chmurze Azure wiosny](spring-cloud-quickstart.md)
