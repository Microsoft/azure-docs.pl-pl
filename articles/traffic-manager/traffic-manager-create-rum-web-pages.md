---
title: Pomiary rzeczywistego użytkownika ze stronami sieci Web — Azure Traffic Manager
description: W tym artykule dowiesz się, jak skonfigurować strony sieci Web do wysyłania Pomiary rzeczywistego użytkownika do usługi Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 2bb104fd9cfc3c4ffddb82e4cf442e94c4650550
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184562"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Jak wysyłać Pomiary rzeczywistego użytkownika do usługi Azure Traffic Manager przy użyciu stron sieci Web

Strony sieci Web można skonfigurować do wysyłania Pomiary rzeczywistego użytkownika do Traffic Manager przez uzyskanie klucza Pomiary rzeczywistego użytkownika (RUM) i osadzenie wygenerowanego kodu na stronie sieci Web.

## <a name="obtain-a-real-user-measurements-key"></a>Uzyskaj klucz Pomiary rzeczywistego użytkownika

Pomiary wykonywane i wysyłane do Traffic Manager z aplikacji klienckiej są identyfikowane przez usługę przy użyciu unikatowego ciągu, zwanego **kluczem pomiary rzeczywistego użytkownika (rum)**. Klucz RUM można uzyskać przy użyciu Azure Portal, interfejsu API REST lub polecenia programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby uzyskać klucz RUM przy użyciu Azure Portal:
1. Z poziomu przeglądarki zaloguj się do witryny Azure Portal. Jeśli jeszcze nie masz konta, możesz skorzystać z bezpłatnej miesięcznej wersji próbnej.
2. Korzystając z paska wyszukiwania portalu, wyszukaj nazwę profilu usługi Traffic Manager, który chcesz zmodyfikować, a następnie kliknij profil usługi Traffic Manager w wyświetlonych wynikach wyszukiwania.
3. W bloku profil Traffic Manager kliknij pozycję **pomiary rzeczywistego użytkownika** w obszarze **Ustawienia**.
4. Kliknij przycisk **Generuj klucz** , aby utworzyć nowy klucz rum.
 
   ![Generuj klucz Pomiary rzeczywistego użytkownika](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Rysunek 1: generowanie klucza Pomiary rzeczywistego użytkownika**

5. W bloku jest teraz wyświetlany wygenerowany klucz RUM i fragment kodu JavaScript, który musi zostać osadzony na stronie HTML.
 
    ![Kod JavaScript dla klucza Pomiary rzeczywistego użytkownika](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Rysunek 2: Pomiary rzeczywistego użytkownika klucz i pomiar JavaScript**
 
6. Kliknij przycisk **Kopiuj** , aby skopiować kod JavaScript. 

>[!IMPORTANT]
> Użyj wygenerowanej funkcji JavaScript do Pomiary rzeczywistego użytkownika, aby działać prawidłowo. Wszelkie zmiany w tym skrypcie lub skrypty używane przez Pomiary rzeczywistego użytkownika mogą prowadzić do nieprzewidywalnego zachowania.

## <a name="embed-the-code-to-an-html-web-page"></a>Osadź kod na stronie sieci Web HTML

Po uzyskaniu klucza RUM następnym krokiem jest osadzenie skopiowanego kodu JavaScript na stronę HTML, którą odwiedzają użytkownicy końcowi. Edytowanie HTML można wykonać na wiele sposobów i przy użyciu różnych narzędzi i przepływów pracy. Ten przykład pokazuje, jak zaktualizować stronę HTML, aby dodać ten skrypt. Możesz użyć tych wskazówek, aby dostosować je do przepływu pracy zarządzania źródłami HTML.

1.  Otwieranie strony HTML w edytorze tekstu
2.  Wklej kod JavaScript skopiowany we wcześniejszym kroku do sekcji BODY HTML (skopiowany kod jest w wierszu 8 & 9, patrz rysunek 3).
 
    ![Osadź kod JavaScript na stronie sieci Web dla Pomiary rzeczywistego użytkownika](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Rysunek 3. prosty kod HTML z osadzonym Pomiary rzeczywistego użytkownika JavaScript**

3.  Zapisz plik HTML i Host go na serwerze webpodłączonym do Internetu. 
4. Następnym razem, gdy ta strona jest renderowana w przeglądarce sieci Web, zostanie pobrane odwołanie do kodu JavaScript, a skrypt wykona operacje pomiarów i raportowania.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [pomiary rzeczywistego użytkownika](traffic-manager-rum-overview.md)
- Dowiedz się, [jak działa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez Traffic Manager
- Dowiedz się, jak [utworzyć profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)