---
title: Łączenie warstwy Premium platformy Azure z linkiem prywatnym ze źródłem
description: Dowiedz się, jak połączyć swoją usługę Premium z platformą Azure, korzystając z usługi link prywatny przy użyciu Azure Portal.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099460"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>Łączenie warstwy Premium platformy Azure z linkiem prywatnym ze źródłem

Ten artykuł przeprowadzi Cię przez proces konfigurowania SKU platformy Azure z systemem Premium w celu nawiązania połączenia z aplikacjami hostowanymi w sieci wirtualnej za pomocą usługi Azure Private link.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Utwórz usługę [link prywatny](../../private-link/create-private-link-service-portal.md) dla serwerów sieci Web źródła.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>Włącz prywatny punkt końcowy w usłudze Azure front-drzwi

W tej sekcji utworzysz mapowanie usługi Azure Private link do prywatnego punktu końcowego utworzonego w sieci prywatnej jednostki SKU w warstwie Premium platformy Azure. 

1. W profilu Premium drzwi platformy Azure w obszarze *Ustawienia* wybierz pozycję **grupy pierwotne**.

1. Wybierz grupę pierwotną zawierającą źródło, dla którego chcesz włączyć prywatny link.

1. Wybierz pozycję **+ Dodaj źródło** , aby dodać nowe źródło lub wybrać wcześniej utworzone źródło z listy. Następnie zaznacz pole wyboru, aby **włączyć usługę łącza prywatnego**.

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="Zrzut ekranu przedstawiający Włączanie linku prywatnego w obszarze Dodawanie strony źródłowej.":::

1. W obszarze **Wybierz zasób platformy Azure** wybierz pozycję **w obszarze mój katalog**. Wybierz lub wprowadź następujące ustawienie, aby skonfigurować zasób, na który ma zostać nawiązane połączenie z platformą Azure w warstwie Premium.
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Region (Region) | Wybierz region, który jest taki sam lub najbliżej źródła. |
    | Typ zasobu | Wybierz pozycję **Microsoft. Network/privateLinkServices**. |
    | Zasób | Wybierz pozycję **myPrivateLinkService**. |
    | Docelowy zasób podrzędny | Pozostaw to pole puste. |
    | Komunikat żądania | Dostosuj komunikat lub wybierz komunikat domyślny. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [linku prywatnego usługi Azure Front drzwiczks Premium](concept-private-link.md).
