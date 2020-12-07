---
title: Zaloguj się do portalu usługi LUIS
description: Jeśli jesteś nowym użytkownikiem w celu logowania się do portalu LUIS, środowisko logowania będzie nieco się różnić w zależności od bieżącego konta użytkownika.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: 3235f6285edb99776b42014678cd2b6c60d17f62
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763851"
---
# <a name="sign-in-to-luis-portal"></a>Zaloguj się do portalu usługi LUIS

Skorzystaj z tego artykułu, aby rozpocząć pracę z portalem LUIS i utworzyć zasób tworzenia. Po wykonaniu kroków opisanych w tym artykule będziesz mieć możliwość tworzenia i publikowania aplikacji LUIS.

## <a name="access-the-portal"></a>Dostęp do portalu


1. Aby rozpocząć pracę z usługą LUIS, przejdź do [portalu Luis](https://www.luis.ai). Jeśli nie masz jeszcze subskrypcji, zostanie wyświetlony monit o utworzenie [bezpłatnego konta](https://azure.microsoft.com//free/cognitive-services/) i powrót do portalu.
2. Odśwież stronę, aby ją zaktualizować przy użyciu nowo utworzonej subskrypcji
3. Wybierz subskrypcję z listy rozwijanej

    > [!div class="mx-imgBorder"]
    > ![Wybieranie subskrypcji](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. Jeśli subskrypcja jest w innej dzierżawie, nie będziesz w stanie przełączyć dzierżawców z istniejącego okna. Dzierżawcy można przełączyć przez zamknięcie tego okna i wybranie z prawej strony awatara zawierającego inicjały na górnym pasku. Kliknij pozycję **Wybierz inny zasób tworzenia** z góry, aby ponownie otworzyć okno.

    > [!div class="mx-imgBorder"]
    > ![Przełącz katalogi](./media/migrate-authoring-key/switch-directories.png)

5. Jeśli masz skojarzony z subskrypcją istniejący zasób LUIS, wybierz go z listy rozwijanej. Można wyświetlić wszystkie aplikacje, które są tworzone w ramach tego zasobu tworzenia.
6. Jeśli nie, kliknij przycisk **Utwórz nowy zasób tworzenia** u dołu tego modalnego.
7.  Podczas tworzenia nowego zasobu tworzenia należy podać następujące informacje:

    > [!div class="mx-imgBorder"]
    > ![Utwórz nowy zasób](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **Nazwa dzierżawy** — dzierżawy, z którą skojarzona jest subskrypcja platformy Azure. Nie będzie można przełączyć dzierżawców z istniejącego okna. Możesz przełączyć dzierżawców, zamykając to okno i wybierając awatar w prawym górnym rogu ekranu zawierającego inicjały. Wybierz pozycję **Wybierz inny zasób tworzenia** z góry, aby ponownie otworzyć okno.
    * **Nazwa grupy zasobów platformy Azure** — nazwa grupy zasobów niestandardowych wybrana w ramach subskrypcji. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania. Jeśli obecnie nie masz grupy zasobów w subskrypcji, nie będzie można jej utworzyć w portalu LUIS. Przejdź do [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) , aby utworzyć jeden, a następnie przejdź do Luis, aby kontynuować proces logowania.
    * **Nazwa zasobu platformy Azure** — wybrana przez Ciebie Nazwa niestandardowa, używana jako część adresu URL dla transakcji tworzenia. Nazwa zasobu może zawierać tylko znaki alfanumeryczne, `-` i nie może zaczynać się ani kończyć znakiem `-` . Jeśli wszystkie inne symbole są zawarte w nazwie, utworzenie zasobu zakończy się niepowodzeniem.
    * **Lokalizacja** — wybierz opcję tworzenia aplikacji w jednej z [trzech lokalizacji autorstwa](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions) , które są obecnie obsługiwane przez Luis, w tym: zachodnie stany USA, Europa Zachodnia i Australia Wschodnia
    * **Warstwa cenowa** — domyślnie wybrana jest warstwa cenowa F0 Authoring (niezalecane). Utwórz [klucz zarządzany przez klienta](https://docs.microsoft.com/azure/cognitive-services/luis/luis-encryption-of-data-at-rest#customer-managed-keys-for-language-understanding) na podstawie Azure Portal, jeśli szukasz dodatkowej warstwy zabezpieczeń.
8. Teraz pomyślnie zarejestrowano się w usłudze LUIS. Teraz możesz zacząć tworzyć aplikacje.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Podczas tworzenia nowego zasobu upewnij się, że nazwa zasobu zawiera tylko znaki alfanumeryczne, "-" i nie może zaczynać się ani kończyć znakiem "-". W przeciwnym razie zakończy się niepowodzeniem.
* Upewnij się, że masz [odpowiednie uprawnienia do subskrypcji, aby utworzyć zasób platformy Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem subskrypcji, aby udzielić wystarczających uprawnień.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [uruchomić nową aplikację](luis-how-to-start-new-app.md)
