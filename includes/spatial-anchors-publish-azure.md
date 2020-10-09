---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67182920"
---
### <a name="open-the-publish-wizard"></a>Otwieranie Kreatora publikacji

W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **SharingService** i wybierz polecenie **Publikuj**.

Zostanie uruchomiony Kreator publikowania. Wybierz pozycję **App Service**  >  **Opublikuj** , aby otworzyć okno dialogowe **Tworzenie App Service** .

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

W oknie dialogowym **tworzenie App Service** wybierz pozycję **Dodaj konto** i zaloguj się do subskrypcji platformy Azure. Jeśli użytkownik jest już zalogowany, wybierz żądane konto z listy rozwijanej.

> [!NOTE]
> Jeśli przeprowadzono już logowanie, nie wybieraj jeszcze pozycji **Utwórz**.
>

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](resource-group.md)]

Obok pozycji **Grupa zasobów** wybierz pozycję **Nowa**.

Nadaj grupie zasobów nazwę **myResourceGroup**, a następnie kliknij przycisk **OK**.

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [app-service-plan](app-service-plan.md)]

Obok pozycji **Plan hostingu** wybierz pozycję **Nowy**.

W oknie dialogowym **Konfiguruj plan hostingu** Użyj następujących ustawień:

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Plan usługi App Service| MySharingServicePlan | Nazwa planu usługi App Service. |
| Lokalizacja | Zachodnie stany USA | Centrum danych, w którym hostowana jest aplikacja internetowa. |
| Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , która określa funkcje hostingu. |

Kliknij przycisk **OK**.

### <a name="create-and-publish-the-web-app"></a>Tworzenie i publikowanie aplikacji internetowej

W polu **Nazwa aplikacji**wprowadź unikatową nazwę aplikacji (prawidłowe znaki to `a-z` , `0-9` i `-` ) lub zaakceptuj automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `https://<app_name>.azurewebsites.net`, gdzie `<app_name>` to nazwa aplikacji.

Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

Po zakończeniu pracy Kreatora publikuje ASP.NET Core aplikację sieci Web na platformie Azure, a następnie otwiera aplikację w domyślnej przeglądarce.

![Opublikowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Nazwa aplikacji użyta w tej sekcji jest używana jako prefiks adresu URL w formacie `https://<app_name>.azurewebsites.net` . Zanotuj ten adres URL, ponieważ będzie on potrzebny.
