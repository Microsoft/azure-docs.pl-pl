---
title: Łączenie warstwy Premium platformy Azure z dodatkiem do wewnętrznego źródła modułu równoważenia obciążenia z linkiem prywatnym
titleSuffix: Azure Private Link
description: Dowiedz się, jak połączyć platformę internetową systemu Azure w warstwie Premium z wewnętrznym modułem równoważenia obciążenia.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803821"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Łączenie warstwy Premium platformy Azure z dodatkiem do wewnętrznego źródła modułu równoważenia obciążenia z linkiem prywatnym

Ten artykuł przeprowadzi Cię przez proces konfigurowania jednostki SKU systemu Azure w warstwie Premium w celu nawiązania połączenia z wewnętrznym źródłem modułu równoważenia obciążenia przy użyciu usługi link prywatny platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz [usługę linku prywatnego](../../private-link/create-private-link-service-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-private-link-to-an-internal-load-balancer"></a>Włączanie prywatnego linku do wewnętrznego modułu równoważenia obciążenia
 
W tej sekcji utworzysz mapowanie usługi link prywatny do prywatnego punktu końcowego utworzonego w sieci prywatnej drzwi frontonu platformy Azure. 

1. W profilu Premium drzwi platformy Azure w obszarze *Ustawienia* wybierz pozycję **grupy pierwotne**.

1. Wybierz grupę pierwotną, w której chcesz włączyć prywatny link do wewnętrznego modułu równoważenia obciążenia.

1. Wybierz pozycję **+ Dodaj źródło** , aby dodać Źródło wewnętrznego modułu równoważenia obciążenia.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="Zrzut ekranu przedstawiający Włączanie prywatnego linku do wewnętrznego modułu równoważenia obciążenia.":::

1. W obszarze **Wybierz zasób platformy Azure** wybierz pozycję **w obszarze mój katalog**. Wybierz lub wprowadź następujące ustawienia, aby skonfigurować witrynę, dla której ma zostać nawiązane połączenie z platformą Azure — wersja Premium.

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Region (Region) | Wybierz region, który jest taki sam lub najbliżej źródła. |
    | Typ zasobu | Wybierz pozycję **Microsoft. Network/privateLinkServices**. |
    | Zasób | Wybierz prywatne łącze powiązane z wewnętrznym modułem równoważenia obciążenia. |
    | Docelowy zasób podrzędny | Pozostaw to pole puste. |
    | Komunikat żądania | Dostosuj komunikat lub wybierz opcję domyślną. |

1. Następnie wybierz pozycję **Dodaj** , a następnie polecenie **Aktualizuj** , aby zapisać konfigurację.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Zatwierdź połączenie prywatnego punktu końcowego z konta magazynu

1. Przejdź do centrum linku prywatnego i wybierz pozycję **usługi połączeń prywatnych**. Następnie wybierz nazwę prywatnego linku.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Zrzut ekranu przedstawiający prywatną listę linków.":::

1. Wybierz pozycję **połączenia prywatne punktów końcowych** w obszarze *Ustawienia*.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Zrzut ekranu przedstawiający stronę omówienia linku prywatnego.":::

1. Wybierz *oczekujące* żądanie prywatnego punktu końcowego z warstwy Premium platformy Azure, a następnie wybierz pozycję **Zatwierdź**.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Zrzut ekranu przedstawiający oczekujące zatwierdzenie linku prywatnego.":::

1. Po zatwierdzeniu powinien wyglądać jak Poniższy zrzut ekranu. Połączenie zostanie w pełni potrwać kilka minut. Teraz możesz uzyskać dostęp do wewnętrznego modułu równoważenia obciążenia z platformy Azure z warstwy Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Zrzut ekranu przedstawiający zatwierdzone żądanie linku prywatnego.":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [usługi linków prywatnych](../../private-link/private-link-service-overview.md).
