---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 2f490a5b12484a91e963d068810b292d7761521a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484305"
---
> [!NOTE]
> Aby skonfigurować niestandardową nazwę DNS dla Azure App Service, można użyć Azure DNS. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](../articles/dns/dns-custom-domain.md#app-service-web-apps) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).
>
>

1. Zaloguj się do witryny internetowej dostawcy domeny.

1. Znajdź stronę służącą do zarządzania rekordami DNS. Każdy dostawca domeny ma własny interfejs rekordów DNS, dlatego zapoznaj się z dokumentacją dostawcy. Poszukaj obszarów witryny z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerami nazw**.

   Często można znaleźć stronę rekordów DNS, wyświetlając informacje o koncie, a następnie szukając linku, takiego jak **Moje domeny**. Przejdź do tej strony, a następnie poszukaj linku o nazwie, takiego jak **plik strefy**, **rekordy DNS**lub **Konfiguracja zaawansowana**.

   Poniższy zrzut ekranu przedstawia przykład strony rekordów DNS:

   ![Zrzut ekranu pokazujący przykładową stronę rekordów DNS.](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Na przykładowym zrzucie ekranu wybierz pozycję **Dodaj** , aby utworzyć rekord. Niektórzy dostawcy udostępniają różne linki na potrzeby dodawania różnych typów rekordów. Zapoznaj się z dokumentacją dostawcy.

> [!NOTE]
> W przypadku niektórych dostawców, np. GoDaddy, zmiany rekordów DNS nie zaczynają obowiązywać, dopóki nie wybierzesz oddzielnego linku **Zapisz zmiany**.
