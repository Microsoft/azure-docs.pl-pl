---
title: Jak skonfigurować Azure Functions z siecią wirtualną
description: Artykuł, w którym opisano sposób wykonywania pewnych zadań sieci wirtualnych dla Azure Functions.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: c123b20e163731f9a872a969f2f1564479b6e308
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718435"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Jak skonfigurować Azure Functions z siecią wirtualną

W tym artykule pokazano, jak wykonywać zadania związane z konfigurowaniem aplikacji funkcji w celu nawiązywania połączenia i uruchamiania w sieci wirtualnej. Aby dowiedzieć się więcej na Azure Functions i sieci, zobacz [Azure Functions opcje sieciowe.](functions-networking-options.md)

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Ograniczanie konta magazynu do sieci wirtualnej 

Podczas tworzenia aplikacji funkcji należy utworzyć lub połączyć konto usługi Azure Storage ogólnego przeznaczenia, które obsługuje usługi Blob Storage, Queue Storage i Table Storage. To konto magazynu można zastąpić kontem zabezpieczonym za pomocą punktów końcowych usługi lub prywatnego punktu końcowego. 

> [!NOTE]  
> Ta funkcja obecnie działa dla wszystkich obsługiwanych przez sieć wirtualną jednostki SKU systemu Windows w planie dedykowanym (App Service) i w planach Premium. Plan Zużycia nie jest obsługiwany. 

Aby skonfigurować funkcję z kontem magazynu ograniczonym do sieci prywatnej:

1. Utwórz funkcję z kontem magazynu, które nie ma włączonych punktów końcowych usługi.

1. Skonfiguruj funkcję w celu nawiązania połączenia z siecią wirtualną.

1. Utwórz lub skonfiguruj inne konto magazynu.  Będzie to konto magazynu, które zabezpieczymy za pomocą punktów końcowych usługi i połączymy z naszą funkcją.

1. [Utwórz udział plików na](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) zabezpieczonym koncie magazynu.

1. Włącz punkty końcowe usługi lub prywatny punkt końcowy dla konta magazynu.  
    * W przypadku korzystania z połączeń z prywatnym punktem końcowym konto magazynu będzie potrzebować prywatnego punktu końcowego dla `file` zasobów `blob` podrzędnych i .  W przypadku korzystania z niektórych Durable Functions, takich jak Durable Functions, konieczne będzie również połączenie prywatnego punktu końcowego i dostęp do `queue` `table` niego.
    * Jeśli używasz punktów końcowych usługi, włącz podsieć dedykowaną dla aplikacji funkcji dla kont magazynu.

1. Skopiuj zawartość pliku i obiektu blob z konta magazynu aplikacji funkcji na zabezpieczone konto magazynu i udział plików.

1. Skopiuj wartości parametrów połączenia dla tego konta magazynu.

1. Zaktualizuj ustawienia **aplikacji w** **obszarze Konfiguracja** dla aplikacji funkcji na następujące:

    | Nazwa ustawienia | Wartość | Komentarz |
    |----|----|----|
    | `AzureWebJobsStorage`| Ciąg połączenia magazynu | Są to ciągi połączenia dla zabezpieczonego konta magazynu. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Ciąg połączenia magazynu | Są to ciągi połączenia dla zabezpieczonego konta magazynu. |
    | `WEBSITE_CONTENTSHARE` | Udział plików | Nazwa udziału plików utworzonego na zabezpieczonym koncie magazynu, w którym znajdują się pliki wdrożenia projektu. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Nowe ustawienie |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Wymusza cały ruch wychodzący przez sieć wirtualną. Wymagane, gdy konto magazynu korzysta z połączeń z prywatnym punktem końcowym. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | Serwer DNS używany przez aplikację. Wymagane, gdy konto magazynu korzysta z połączeń z prywatnym punktem końcowym. |

1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia aplikacji. Zmiana ustawień aplikacji powoduje ponowne uruchomienie aplikacji.  

Po ponownym uruchomieniu aplikacja funkcji jest teraz połączona z zabezpieczonym kontem magazynu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Opcje sieciowe usługi Azure Functions](functions-networking-options.md)

