---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 80685dee7907b81832c94044d1feb8fcf2e41bde
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95998485"
---
### <a name="open-the-publish-wizard"></a>Otwieranie Kreatora publikacji

W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **SharingService** , a następnie wybierz pozycję **Publikuj**.

Zostanie uruchomiony Kreator publikowania. 

Wybierz pozycję **App Service**  >  **Opublikuj** , aby otworzyć okienko **Tworzenie App Service** .

### <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

Zaloguj się w witrynie Azure Portal.

W okienku **tworzenie App Service** wybierz pozycję **Dodaj konto**, a następnie zaloguj się do subskrypcji platformy Azure. Jeśli użytkownik jest już zalogowany, wybierz żądane konto z listy rozwijanej.

   > [!NOTE]
   > Jeśli przeprowadzono już logowanie, nie wybieraj jeszcze pozycji **Utwórz**.
   >

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](resource-group.md)]

Obok pozycji **Grupa zasobów** wybierz pozycję **Nowa**.

Nadaj **nazwę grupie zasobów**, a następnie wybierz przycisk **OK**.

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [app-service-plan](app-service-plan.md)]

Obok pozycji **Plan hostingu** wybierz pozycję **Nowy**.

W okienku **Konfigurowanie planu hostingu** Użyj następujących ustawień:

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Plan usługi App Service| MySharingServicePlan | Nazwa planu App Service |
| Lokalizacja | Zachodnie stany USA | Centrum danych, w którym jest hostowana aplikacja sieci Web |
| Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , która określa funkcje hostingu |

Wybierz pozycję **OK**.

### <a name="create-and-publish-the-web-app"></a>Tworzenie i publikowanie aplikacji internetowej

W polu **Nazwa aplikacji** wprowadź unikatową nazwę aplikacji. Prawidłowe znaki to a-z, 0-9 i kreski (-) lub akceptują automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `https://<app_name>.azurewebsites.net`, gdzie `<app_name>` to nazwa aplikacji.

Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

   Po zakończeniu pracy Kreatora publikuje ASP.NET Core aplikację sieci Web na platformie Azure, a następnie otwiera aplikację w domyślnej przeglądarce.

  ![Zrzut ekranu opublikowanej aplikacji sieci Web ASP.NET na platformie Azure.](./media/spatial-anchors-azure/web-app-running-live.png)

Nazwa aplikacji użyta w tej sekcji jest używana jako prefiks adresu URL w formacie `https://<app_name>.azurewebsites.net` . Skopiuj ten adres URL do edytora tekstów w celu późniejszego użycia.
