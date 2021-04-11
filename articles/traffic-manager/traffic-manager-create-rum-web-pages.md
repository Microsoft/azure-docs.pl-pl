---
title: Pomiary rzeczywistego użytkownika ze stronami sieci Web — Azure Traffic Manager
description: W tym artykule dowiesz się, jak skonfigurować strony sieci Web do wysyłania Pomiary rzeczywistego użytkownika do usługi Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580350"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Jak wysyłać Pomiary rzeczywistego użytkownika do usługi Azure Traffic Manager przy użyciu stron sieci Web

Strony sieci Web można skonfigurować do wysyłania Pomiary rzeczywistego użytkownika do Traffic Manager przez uzyskanie klucza Pomiary rzeczywistego użytkownika (RUM) i osadzenie wygenerowanego kodu na stronie sieci Web.

## <a name="obtain-a-real-user-measurements-key"></a>Uzyskaj klucz Pomiary rzeczywistego użytkownika

Pomiary wykonywane i wysyłane do Traffic Manager z aplikacji klienckiej są identyfikowane przez usługę przy użyciu unikatowego ciągu, zwanego **kluczem pomiary rzeczywistego użytkownika (rum)**. Klucz RUM można uzyskać za pomocą Azure Portal, interfejsu API REST, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby uzyskać klucz RUM przy użyciu Azure Portal:
1. Z poziomu przeglądarki zaloguj się do witryny Azure Portal. Jeśli jeszcze nie masz konta, możesz skorzystać z bezpłatnej miesięcznej wersji próbnej.

1. Na pasku wyszukiwania portalu Wyszukaj nazwę profilu Traffic Manager, który chcesz zmodyfikować, a następnie wybierz profil Traffic Manager w wyświetlonych wynikach.

1. Na stronie profil Traffic Manager wybierz pozycję **pomiary rzeczywistego użytkownika** w obszarze **Ustawienia**.

1. Wybierz pozycję **Generuj klucz** , aby utworzyć nowy klucz rum.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="Zrzut ekranu przedstawiający sposób generowania klucza."::: 

   **Rysunek 1: generowanie klucza Pomiary rzeczywistego użytkownika**

1. Na stronie jest teraz wyświetlany wygenerowany klucz RUM i fragment kodu JavaScript, który musi zostać osadzony na stronie HTML.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="Zrzut ekranu przedstawiający wygenerowany kod JavaScript."::: 

    **Rysunek 2: Pomiary rzeczywistego użytkownika klucz i pomiar JavaScript**
 
1. Wybierz przycisk **Kopiuj** , aby skopiować kod JavaScript. 

> [!IMPORTANT]
> Użyj wygenerowanej funkcji JavaScript do Pomiary rzeczywistego użytkownika, aby działać prawidłowo. Wszelkie zmiany w tym skrypcie lub skrypty używane przez Pomiary rzeczywistego użytkownika mogą prowadzić do nieprzewidywalnego zachowania.

## <a name="embed-the-code-to-an-html-web-page"></a>Osadź kod na stronie sieci Web HTML

Po uzyskaniu klucza RUM następnym krokiem jest osadzenie skopiowanego kodu JavaScript na stronę HTML, którą odwiedzają użytkownicy końcowi. Edytowanie HTML można wykonać na wiele sposobów i przy użyciu różnych narzędzi i przepływów pracy. Ten przykład pokazuje, jak zaktualizować stronę HTML, aby dodać ten skrypt. Możesz użyć tych wskazówek, aby dostosować je do przepływu pracy zarządzania źródłami HTML.

1. Otwórz stronę HTML w edytorze tekstu.

1. Wklej kod JavaScript skopiowany w ostatniej sekcji do sekcji treść w kodzie HTML. Skopiowany kod jest w wierszu 8 & 9, patrz rysunek 3.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Zrzut ekranu przedstawiający wygenerowany kod JavaScript osadzony na stronie sieci Web."::: 

    **Rysunek 3. prosty kod HTML z osadzonym Pomiary rzeczywistego użytkownika JavaScript**

1. Zapisz plik HTML i Host go na serwerze webpodłączonym do Internetu.

1. Następnym razem, gdy ta strona będzie renderowana w przeglądarce sieci Web, odwołanie do kodu JavaScript zostanie pobrane, a skrypt wykona operacje pomiarów i raportowania.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [pomiary rzeczywistego użytkownika](traffic-manager-rum-overview.md)
- Dowiedz się, [jak działa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez Traffic Manager
- Dowiedz się, jak [utworzyć profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)
