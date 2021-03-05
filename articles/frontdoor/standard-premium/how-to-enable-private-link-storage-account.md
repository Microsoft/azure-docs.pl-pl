---
title: Połącz z platformą Azure Front-The-Premium w pochodzeniu konta magazynu z prywatnym linkiem
titleSuffix: Azure Private Link
description: Dowiedz się, jak łączyć się z platformą internetową w warstwie Premium na koncie magazynu prywatnie.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201672"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Połącz z platformą Azure Front-The-Premium w pochodzeniu konta magazynu z prywatnym linkiem

Ten artykuł przeprowadzi Cię przez proces konfigurowania jednostki SKU systemu Azure w warstwie Premium w celu prywatnego nawiązywania połączenia z pochodzeniem do konta magazynu przy użyciu usługi link prywatny platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-private-link-to-a-storage-account"></a>Włączanie prywatnego linku do konta magazynu
 
W tej sekcji utworzysz mapowanie usługi link prywatny do prywatnego punktu końcowego utworzonego w sieci prywatnej drzwi frontonu platformy Azure. 

1. W profilu Premium drzwi platformy Azure w obszarze *Ustawienia* wybierz pozycję **grupy pierwotne**.

1. Wybierz grupę pierwotną, która zawiera źródło konta magazynu, dla którego chcesz włączyć prywatny link.

1. Wybierz pozycję **+ Dodaj źródło** , aby dodać nowe źródło konta magazynu, lub wybierz wcześniej utworzone źródło konta magazynu z listy.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="Zrzut ekranu przedstawiający Włączanie prywatnego linku do konta magazynu.":::

1. W obszarze **Wybierz zasób platformy Azure** wybierz pozycję **w obszarze mój katalog**. Wybierz lub wprowadź następujące ustawienia, aby skonfigurować witrynę, dla której ma zostać nawiązane połączenie z platformą Azure — wersja Premium.

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Region (Region) | Wybierz region, który jest taki sam lub najbliżej źródła. |
    | Typ zasobu | Wybierz pozycję **Microsoft. Storage/storageAccounts**. |
    | Zasób | Wybierz swoje konto magazynu. |
    | Docelowy zasób podrzędny | Możesz wybrać opcję *obiekt BLOB* lub *Sieć Web*. |
    | Komunikat żądania | Dostosuj komunikat lub wybierz opcję domyślną. |

1. Następnie wybierz pozycję **Dodaj** , aby zapisać konfigurację.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Zatwierdź połączenie prywatnego punktu końcowego z konta magazynu

1. Przejdź do konta magazynu, dla którego skonfigurowano link prywatny w ostatniej sekcji. W obszarze **Ustawienia** wybierz pozycję **Sieć** .

1. W obszarze **Sieć** wybierz pozycję **połączenia prywatne punktów końcowych**. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Zrzut ekranu ustawień sieci w aplikacji sieci Web.":::

1. Wybierz *oczekujące* żądanie prywatnego punktu końcowego z warstwy Premium platformy Azure, a następnie wybierz pozycję **Zatwierdź**.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="Zrzut ekranu przedstawiający żądanie prywatnego punktu końcowego magazynu.":::

1. Po zatwierdzeniu powinien wyglądać jak Poniższy zrzut ekranu. Połączenie zostanie w pełni potrwać kilka minut. Teraz możesz uzyskiwać dostęp do konta magazynu z poziomu systemu Azure z warstwy Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Zrzut ekranu przedstawiający zatwierdzone żądanie punktu końcowego magazynu.":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze link prywatny z kontem magazynu](../../storage/common/storage-private-endpoints.md).
