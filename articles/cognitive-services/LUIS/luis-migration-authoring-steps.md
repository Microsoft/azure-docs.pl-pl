---
title: Migrowanie do zasobu tworzenia platformy Azure
titleSuffix: Azure Cognitive Services
description: Przeprowadź migrację do zasobu tworzenia platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78194646"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Kroki migracji do zasobu tworzenia platformy Azure

W portalu Language Understanding (LUIS) Dokonaj migracji wszystkich aplikacji, których używasz, aby użyć zasobu tworzenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* **Opcjonalnie możesz**utworzyć kopię zapasową aplikacji z listy aplikacji portalu Luis, eksportując każdą aplikację lub korzystając z [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)eksportowania.
* **Opcjonalnie**Zapisz listę collaborator's każdej aplikacji. Wszyscy współpracownicy mogą wysyłać wiadomości e-mail w ramach procesu migracji.
* **Wymagana**jest [subskrypcja platformy Azure](https://azure.microsoft.com/free/). Część procesu subskrypcji wymaga informacji dotyczących rozliczeń. Można jednak korzystać z bezpłatnych warstw cenowych (F0), gdy używasz LUIS. Może się okazać, że potrzebujesz warstwy płatnej w miarę wzrostu użycia.

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się](https://azure.microsoft.com/free/).

## <a name="access-the-migration-process"></a>Dostęp do procesu migracji

Co tydzień zostanie wyświetlony monit o migrację aplikacji. To okno można anulować bez migrowania. Jeśli chcesz przeprowadzić migrację przed następnym zaplanowanym okresem, możesz rozpocząć proces migracji z ikony **platformy Azure** na górnym pasku narzędzi portalu Luis.

> [!div class="mx-imgBorder"]
> ![Ikona migracji](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>Właściciel aplikacji rozpoczyna proces migracji

Proces migracji jest dostępny, jeśli jesteś właścicielem dowolnych aplikacji LUIS.

1. Zaloguj się do [portalu Luis](https://www.luis.ai) i zaakceptuj warunki użytkowania.
1. Okno podręczne migracji umożliwia kontynuowanie migracji lub późniejszej migracji. Wybierz pozycję **Migruj teraz**. Jeśli zdecydujesz się na migrację później, będziesz mieć 9 miesięcy na migrację do nowego klucza tworzenia na platformie Azure.

    ![Pierwsze okno podręczne w procesie migracji, wybierz pozycję Migruj teraz.](./media/migrate-authoring-key/migrate-now.png)

1. Opcjonalnie, Jeśli którakolwiek z aplikacji ma współpracowników, zostanie wyświetlony monit o **wysłanie wiadomości e-mail** z informacją o migracji. Jest to opcjonalny krok.

    Po przeprowadzeniu migracji konta na platformę Azure aplikacje nie będą już dostępne dla współpracowników.

    Dla każdego współpracownika i aplikacji zostanie otwarta domyślna aplikacja poczty e-mail z lekko sformatowaną wiadomością e-mail. Wiadomość e-mail można edytować przed jej wysłaniem.

    Szablon wiadomości e-mail zawiera dokładny identyfikator aplikacji i nazwę aplikacji.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Wybierz, aby utworzyć zasób tworzenia LUIS, wybierając opcję użycia istniejącego zasobu tworzenia lub aby utworzyć nowy zasób tworzenia.

    > [!div class="mx-imgBorder"]
    > ![Utwórz zasób tworzenia](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. W następnym oknie wprowadź informacje o kluczu zasobu. Po wprowadzeniu informacji wybierz pozycję **Utwórz zasób**. Na każdy region można mieć 10 bezpłatnych zasobów do tworzenia na subskrypcję.

    ![Utwórz zasób tworzenia](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    Podczas **tworzenia nowego zasobu tworzenia**należy podać następujące informacje:

    * **Nazwa zasobu** — wybrana przez Ciebie Nazwa niestandardowa, używana jako część adresu URL dla zapytań dotyczących tworzenia i przewidywania punktów końcowych.
    * **Dzierżawca** — dzierżawa, z którą skojarzona jest subskrypcja platformy Azure.
    * **Nazwa subskrypcji** — subskrypcja, za którą zostanie naliczona stawka dla zasobu.
    * **Grupa zasobów** — wybrana lub utworzona nazwa niestandardowej grupy zasobów. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.
    * **Lokalizacja** — wybór lokalizacji zależy od wybranej **grupy zasobów** .
    * **Warstwa cenowa** — warstwa cenowa określa maksymalną liczbę transakcji na sekundę i miesiąc.

1. Sprawdź poprawność zasobu tworzenia i **Wykonaj migrację teraz**.

    ![Utwórz zasób tworzenia](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. Po utworzeniu zasobu tworzenia zostanie wyświetlony komunikat o powodzeniu. Wybierz pozycję **Zamknij** , aby zamknąć okno podręczne.

    ![Zasób tworzenia został pomyślnie utworzony.](./media/migrate-authoring-key/migration-success.png)

    Lista **Moje aplikacje** zawiera aplikacje migrowane do nowego zasobu tworzenia.

    Nie musisz znać klucza zasobu tworzenia, aby kontynuować edytowanie aplikacji w portalu LUIS. Jeśli planujesz edytować aplikacje programowo, potrzebujesz wartości klucza tworzenia. Te wartości są wyświetlane na stronie **zarządzaj > zasobów platformy Azure** w portalu Luis i są również dostępne na stronie Azure Portal na **kluczach** zasobów.

1. Przed uzyskaniem dostępu do aplikacji wybierz zasób subskrypcja i LUIS tworzenie, aby wyświetlić aplikacje, które możesz utworzyć.

    ![Wybierz pozycję subskrypcja i LUIS tworzenie zasobów, aby wyświetlić aplikacje, które mogą tworzyć autor.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>Współautor aplikacji rozpoczyna proces migracji

Wykonaj te same czynności co właściciel aplikacji do migracji. Proces tworzy nowy zasób tworzenia rodzaju `LUIS.Authoring`.

Należy zmigrować konto, aby można je było dodać jako współautor do zmigrowanych aplikacji należących do innych użytkowników.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Po zakończeniu procesu migracji Dodaj współautorów do zasobu tworzenia

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Dowiedz się [, jak dodać współautorów](luis-how-to-collaborate.md).

## <a name="troubleshooting-errors-with-the-migration-process"></a>Rozwiązywanie problemów z procesem migracji

Jeśli podczas procesu migracji `MissingSubscriptionRegistration` wystąpi błąd w portalu Luis z czerwonym paskiem powiadomień, utwórz zasób usługi poznawczej w [Azure Portal](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) lub [interfejsie wiersza polecenia platformy Azure](luis-how-to-azure-subscription.md#create-resources-in-azure-cli). Dowiedz się więcej o [przyczynach tego błędu](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>Następne kroki


* Zapoznaj się z [pojęciami](luis-concept-keys.md) dotyczącymi tworzenia i kluczy czasu wykonywania
* Przejrzyj [sposób przypisywania kluczy](luis-how-to-azure-subscription.md) i Dodawanie [współautorów](luis-how-to-collaborate.md)
