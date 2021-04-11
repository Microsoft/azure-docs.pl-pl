---
title: Jak skonfigurować Azure Functions przy użyciu sieci wirtualnej
description: Artykuł pokazujący, jak wykonywać pewne zadania dotyczące sieci wirtualnych dla Azure Functions.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: a28a59a0de40bba7914d1920b42034fbbc223ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609537"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Jak skonfigurować Azure Functions przy użyciu sieci wirtualnej

W tym artykule przedstawiono sposób wykonywania zadań związanych z konfigurowaniem aplikacji funkcji w celu łączenia się z siecią wirtualną i uruchamiania jej. Aby dowiedzieć się więcej na temat Azure Functions i sieci, zobacz [Opcje sieci Azure Functions](functions-networking-options.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Ograniczanie konta magazynu do sieci wirtualnej 

Podczas tworzenia aplikacji funkcji należy utworzyć konto usługi Azure Storage ogólnego przeznaczenia lub połączyć się z nim, które obsługuje magazyn obiektów blob, kolejek i tabel. Możesz zamienić to konto magazynu na takie, które jest zabezpieczone za pomocą punktów końcowych usługi lub prywatnego punktu końcowego. 

> [!NOTE]  
> Ta funkcja aktualnie działa dla wszystkich jednostek SKU obsługiwanych przez sieć wirtualną systemu Windows w ramach planu dedykowanego (App Service) i planów Premium. Plan zużycia nie jest obsługiwany. 

Aby skonfigurować funkcję z kontem magazynu ograniczonym do sieci prywatnej:

1. Utwórz funkcję z kontem magazynu, które nie ma włączonych punktów końcowych usługi.

1. Skonfiguruj funkcję do łączenia się z siecią wirtualną.

1. Utwórz lub skonfiguruj inne konto magazynu.  To konto magazynu jest zabezpieczane za pomocą punktów końcowych usługi i łącz naszą funkcję.

1. [Utwórz udział plików](../storage/files/storage-how-to-create-file-share.md#create-file-share) na koncie bezpiecznego magazynu.

1. Włącz punkty końcowe usługi lub prywatny punkt końcowy dla konta magazynu.  
    * W przypadku korzystania z połączeń prywatnych punktów końcowych konto magazynu będzie wymagało prywatnego punktu końcowego dla `file` `blob` zasobów podrzędnych i.  W przypadku korzystania z pewnych funkcji, takich jak Durable Functions, będzie również konieczne `queue` i `table` dostępne za pośrednictwem połączenia prywatnego punktu końcowego.
    * W przypadku korzystania z punktów końcowych usługi należy włączyć podsieć dedykowaną aplikacjom funkcji dla kont magazynu.

1. Skopiuj zawartość pliku i obiektu BLOB z konta magazynu aplikacji funkcji na zabezpieczone konto magazynu i udział plików.

1. Skopiuj parametry połączenia dla tego konta magazynu.

1. Zaktualizuj **Ustawienia aplikacji** w obszarze **Konfiguracja** dla aplikacji funkcji w następujący sposób:

    | Nazwa ustawienia | Wartość | Komentarz |
    |----|----|----|
    | `AzureWebJobsStorage`| Parametry połączenia magazynu | To są parametry połączenia dla zabezpieczonego konta magazynu. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Parametry połączenia magazynu | To są parametry połączenia dla zabezpieczonego konta magazynu. |
    | `WEBSITE_CONTENTSHARE` | Udział plików | Nazwa udziału plików utworzonego na koncie bezpiecznego magazynu, w którym znajdują się pliki wdrażania projektu. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Nowe ustawienie |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Wymusza cały ruch wychodzący za pomocą sieci wirtualnej. Wymagane, gdy konto magazynu korzysta z połączeń prywatnych punktów końcowych. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | Serwer DNS używany przez aplikację. Wymagane, gdy konto magazynu korzysta z połączeń prywatnych punktów końcowych. |

1. Wybierz pozycję **Zapisz** , aby zapisać ustawienia aplikacji. Zmiana ustawień aplikacji powoduje ponowne uruchomienie aplikacji.  

Po ponownym uruchomieniu aplikacja funkcji jest teraz połączona z bezpiecznym kontem magazynu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Opcje sieciowe usługi Azure Functions](functions-networking-options.md)

