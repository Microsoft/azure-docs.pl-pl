---
title: Zalety migracji do Media Services API v3
description: W tym artykule wymieniono zalety migrowania z programu Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: b6d51e05598f60de0e9c8fb85472b7c14bba990e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598392"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>Krok 1. zapoznanie się z zaletami migracji do Media Services API v3

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-1.svg)

Zachęcamy do rozpoczęcia korzystania z wersji 2020-05-01 interfejsu API Azure Media Services v3 teraz, aby uzyskać korzyści, ponieważ nowe funkcje, funkcje i optymalizacje wydajności są dostępne tylko w bieżącym interfejsie API v3.

Wersję interfejsu API można zmienić w portalu, najnowszych zestawach SDK, najnowszej wersji interfejsu wiersza polecenia i interfejsie API REST przy użyciu poprawnego ciągu.

Wprowadzono znaczne ulepszenia Media Services w wersji 3.  

## <a name="benefits-of-media-services-v3"></a>Zalety Media Services v3

| **Funkcja v3** | **Korzyść** |
| --- | --- |
| **Witryna Azure Portal** | |
| Aktualizacje Azure Portal | Azure Portal zostało zaktualizowane w celu uwzględnienia zarządzania jednostkami interfejsu API v3. Umożliwia ona klientom korzystanie z portalu do uruchamiania przesyłania strumieniowego na żywo, przesyłanie zadań transformacji v3, zarządzanie zasadami ochrony zawartości, punktami końcowymi przesyłania strumieniowego, uzyskiwanie dostępu do interfejsu API, Zarządzanie połączonymi kontami magazynu i wykonywanie zadań monitorowania. |
| **Konta i magazyn** | |
| Kontrola dostępu oparta na rolach (RBAC) na platformie Azure | Klienci mogą teraz definiować własne role i kontrolować dostęp do każdej jednostki w interfejsie API usługi Media Services ARM. Ułatwia to kontrolowanie dostępu do zasobów według kont usługi AAD. |
| Tożsamości zarządzane | Tożsamości zarządzane eliminują konieczność zarządzania poświadczeniami przez deweloperów, dostarczając tożsamość dla zasobu platformy Azure w usłudze Azure AD. Zobacz szczegóły dotyczące tożsamości zarządzanych [tutaj](../../active-directory/managed-identities-azure-resources/overview.md). |
| Obsługa linków prywatnych | Klienci będą uzyskiwać dostęp do Media Services punktów końcowych w celu dostarczania kluczy, LiveEvents i StreamingEndpoints za pośrednictwem PrivateEndpoint w swojej sieci wirtualnej. |
| [Klucze zarządzane przez klienta](concept-use-customer-managed-keys-byok.md) lub zapewniają obsługę własnych kluczy (BYOK) | Klienci mogą szyfrować dane na koncie Media Services przy użyciu klucza w ich Azure Key Vault. |
| **Elementy zawartości** | |
| Element zawartości może zawierać wiele [lokalizatorów przesyłania strumieniowego](streaming-locators-concept.md) z różnymi [pakietami dynamicznymi](dynamic-packaging-overview.md) i dynamicznymi ustawieniami szyfrowania. | W każdym elemencie zawartości jest dozwolony limit 100y lokalizatorów przesyłania strumieniowego. Klienci mogą przechowywać jedną kopię zawartości multimedialnej w ramach zasobu, ale udostępniać różne adresy URL przesyłania strumieniowego z różnymi zasadami przesyłania strumieniowego lub zasadami ochrony zawartości, które są oparte na docelowych odbiorców.
| **Przetwarzanie zadania** ||
| V3 wprowadza koncepcję [transformacji](transforms-jobs-concept.md)   dla przetwarzania zadań opartych na plikach. | Przekształcenie może służyć do kompilowania konfiguracji do wielokrotnego użytku, tworzenia Azure Resource Manager szablonów i izolowania ustawień przetwarzania między wieloma klientami lub dzierżawcami. |
| W przypadku przetwarzania zadań opartych na plikach można użyć adresu URL HTTP (S) jako danych wejściowych. | Nie musisz mieć już przechowywanej zawartości na platformie Azure ani nie musisz tworzyć zasobów wejściowych. |
| **Zdarzenia na żywo** ||
| Zdarzenia na żywo w warstwie Premium 1080p | Nowe jednostki SKU zdarzenia na żywo umożliwiają klientom pobieranie kodowania w chmurze z danymi wyjściowymi do wersji 1080p. |
| Obsługa przesyłania strumieniowego na żywo w przypadku nowych [małych opóźnień](live-event-latency.md) na żywo. | Pozwala to użytkownikom na oglądanie zdarzeń na żywo bliżej czasu rzeczywistego niż w przypadku, gdy to ustawienie nie zostało włączone. |
| Usługa Live Event Preview obsługuje [dynamiczne pakowanie](dynamic-packaging-overview.md)   i szyfrowanie dynamiczne. | Umożliwia to ochronę zawartości w przypadku wersji zapoznawczej, ŁĄCZNIKa i HLS. |
| Zamiana danych wyjściowych na żywo | Użycie danych wyjściowych na żywo jest łatwiejsze niż w przypadku jednostki programu w interfejsach API v2. |
| Ulepszono obsługę protokołu RTMP dla zdarzeń na żywo z obsługą większej liczby koderów | Zwiększa stabilność i zapewnia elastyczność kodera źródłowego. |
| Zdarzenia na żywo mogą przesyłać strumieniowo 24x7 | Możesz obsługiwać wydarzenie na żywo i utrzymywać odbiorców przez dłuższy okres. |
| Transkrypcja dynamiczna dla zdarzeń na żywo | Transkrypcja dynamiczna umożliwia klientom automatyczne Transkrypcja języka mówionego do tekstu w czasie rzeczywistym podczas emisji zdarzeń na żywo. Znacznie poprawia to dostępność zdarzeń na żywo. |
| [Tryb gotowości](live-events-outputs-concept.md#standby-mode) dla zdarzeń na żywo | Zdarzenia na żywo, które są w stanie wstrzymania, są tańsze niż uruchomione zdarzenia na żywo. Dzięki temu klienci mogą utrzymywać zestaw wydarzeń na żywo, które są gotowe do rozpoczęcia w ciągu kilku sekund od utrzymania zestawu uruchomionych wydarzeń na żywo. Obniżone ceny dla zdarzeń na żywo w stanie gotowości będą obowiązywać w lutym 2021 dla większości regionów, a pozostałe do przestrzegania w kwietniu 2021.
|**Ochrona zawartości** ||
| [Ochrona zawartości](content-key-policy-concept.md)   obsługuje funkcje wielokluczowe. | Klienci mogą teraz używać wielu kluczy szyfrowania zawartości w swoich lokalizatorach przesyłania strumieniowego. |
| **Monitorowanie** | |
| Obsługa powiadomień [usługi Azure EventGrid](monitoring/reacting-to-media-services-events.md) | Powiadomienia EventGrid są rozbudowane. Istnieje więcej typów powiadomień, szerokiej obsługi zestawu SDK do otrzymywania powiadomień w aplikacji i innych istniejących usług platformy Azure, które mogą działać jako programy obsługi zdarzeń. |
| [Azure Monitor pomoc techniczną i integrację w Azure Portal](monitoring/monitor-events-portal-how-to.md) | Dzięki temu klienci mogą wizualizować Media Services użycia przydziału kont, statystyk w czasie rzeczywistym dla punktów końcowych przesyłania strumieniowego oraz pozyskiwanie i archiwizowanie statystyk dla wydarzeń na żywo. Klienci mogą teraz ustawiać alerty i wykonywać niezbędne działania na podstawie danych metryk w czasie rzeczywistym. |

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]