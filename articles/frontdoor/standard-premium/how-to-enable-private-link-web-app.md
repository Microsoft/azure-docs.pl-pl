---
title: Łączenie warstwy Premium platformy Azure z pakietem internetowym do pochodzenia aplikacji internetowej z linkiem prywatnym
titleSuffix: Azure Private Link
description: Dowiedz się, jak łączyć się z usługą webapp na platformie Azure w sposób prywatny.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 805c3ba0360fcffe2bfd4217c0ef625fe61e5d64
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030583"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Łączenie warstwy Premium platformy Azure z pakietem internetowym do pochodzenia aplikacji internetowej z linkiem prywatnym

Ten artykuł przeprowadzi Cię przez proces konfigurowania jednostki SKU platformy Azure z warstwy Premium w celu samodzielnego łączenia się z aplikacją internetową przy użyciu usługi link prywatny platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Utwórz usługę [link prywatny](../../private-link/create-private-link-service-portal.md) dla serwerów sieci Web źródła.

> [!Note]
> Prywatny punkt końcowy jest dostępny w regionach publicznych dla PremiumV2 warstwy PremiumV3 aplikacji sieci Web systemu Windows, aplikacji sieci Web w systemie Linux oraz planu Azure Functions Premium (czasami określanego jako elastyczny plan Premium).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Włączenie prywatnego linku do aplikacji sieci Web w warstwie Premium platformy Azure
 
W tej sekcji utworzysz mapowanie usługi link prywatny do prywatnego punktu końcowego utworzonego w sieci prywatnej drzwi frontonu platformy Azure. 

1. W profilu Premium drzwi platformy Azure w obszarze *Ustawienia* wybierz pozycję **grupy pierwotne**.

1. Wybierz grupę pierwotną, która zawiera źródło aplikacji sieci Web, dla którego chcesz włączyć prywatny link.

1. Wybierz pozycję **+ Dodaj źródło** , aby dodać nowe źródło aplikacji sieci Web, lub wybierz wcześniej utworzone źródło aplikacji sieci Web z listy.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="Zrzut ekranu przedstawiający Włączanie prywatnego linku do aplikacji sieci Web.":::

1. W obszarze **Wybierz zasób platformy Azure** wybierz pozycję **w obszarze mój katalog**. Wybierz lub wprowadź następujące ustawienia, aby skonfigurować witrynę, dla której ma zostać nawiązane połączenie z platformą Azure — wersja Premium.

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Region (Region) | Wybierz region, który jest taki sam lub najbliżej źródła. |
    | Typ zasobu | Wybierz pozycję **Microsoft. Web/Sites**. |
    | Zasób | Wybierz pozycję **myPrivateLinkService**. |
    | Docelowy zasób podrzędny | lokacje |
    | Komunikat żądania | Dostosuj komunikat lub wybierz opcję domyślną. |

1. Następnie wybierz pozycję **Dodaj** , aby zapisać konfigurację.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>Zatwierdź połączenie frontonu prywatnego z usługą Azure z poziomu aplikacji sieci Web

1. Przejdź do aplikacji sieci Web, dla której skonfigurowano link prywatny w ostatniej sekcji. W obszarze **Ustawienia** wybierz pozycję **Sieć** .

1. W obszarze **Sieć** wybierz pozycję **Konfiguruj połączenia prywatnego punktu końcowego**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Zrzut ekranu ustawień sieci w aplikacji sieci Web.":::

1. Wybierz *oczekujące* żądanie prywatnego punktu końcowego z warstwy Premium platformy Azure, a następnie wybierz pozycję **Zatwierdź**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="Zrzut ekranu przedstawiający oczekujące żądanie prywatnego punktu końcowego.":::

1. Po zatwierdzeniu powinien wyglądać jak Poniższy zrzut ekranu. Połączenie zostanie w pełni potrwać kilka minut. Teraz możesz uzyskiwać dostęp do aplikacji sieci Web z poziomu platformy Azure z warstwy Premium. Bezpośredni dostęp do aplikacji sieci Web z publicznej sieci Internet jest wyłączony po włączeniu prywatnego punktu końcowego.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="Zrzut ekranu przedstawiający żądanie zatwierdzonego punktu końcowego.":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze link prywatny z aplikacją sieci Web platformy Azure](../../app-service/networking/private-endpoint.md).
