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
ms.openlocfilehash: ae51dca466a9aaf489ba4628e13a5e13de25b9bc
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546866"
---
# <a name="sign-in-to-luis-portal"></a>Zaloguj się do portalu usługi LUIS

Jeśli jesteś nowym użytkownikiem w celu logowania się do portalu LUIS, środowisko logowania będzie nieco się różnić w zależności od bieżącego konta użytkownika:
  * Skojarzone z subskrypcją platformy Azure
  * Nie skojarzono z subskrypcją platformy Azure

## <a name="determine-account-type"></a>Określ typ konta

Po pierwszym zalogowaniu się do portalu LUIS Użyj następujących wskaźników wizualizacji, aby określić typ konta.

### <a name="account-without-azure-subscription"></a>Konto bez subskrypcji platformy Azure

Konto, które nie jest skojarzone z subskrypcją platformy Azure, ma ikonę platformy Azure w prawym górnym pasku nawigacyjnym. Po przeprowadzeniu migracji do skojarzonego typu konta ikona nie jest już wyświetlana.

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="Część ekranu — zrzut paska nawigacyjnego LUIS z ikoną platformy Azure.":::

### <a name="account-with-azure-subscription"></a>Konto z subskrypcją platformy Azure

Konto skojarzone z subskrypcją platformy Azure umożliwia wybranie subskrypcji i zasobu do użycia.

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="Częściowy ekran — zrzut ekranu przedstawiający Portal LUIS z polami rozwijanymi subskrypcja i tworzenie zasobów.":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Zaloguj się przy użyciu konta skojarzonego z subskrypcją platformy Azure

1. Zaloguj się do [portalu Luis](https://www.luis.ai) i zaakceptuj warunki użytkowania.

1. Dostępne są dwie opcje rejestracji:

    * Kontynuuj korzystanie z zasobu platformy Azure, który jest zalecaną ścieżką i wkrótce będzie jedyną dostępną ścieżką. Ta ścieżka umożliwia łączenie konta LUIS z zasobem tworzenia platformy Azure podczas rejestrowania się przez wybranie istniejącego zasobu w ramach subskrypcji lub utworzenie nowego zasobu. Jest to równoważne zarejestrowaniu się bez konieczności późniejszego przechodzenia do [procesu migracji](luis-migration-authoring.md#what-is-migration) . Wszyscy użytkownicy będą musieli przeprowadzić migrację do 2 listopada 2020.

    * Kontynuuj korzystanie z klucza początkowego lub wersji próbnej. Ta ścieżka umożliwia zalogowanie się do LUIS przy użyciu początkowego lub próbnego zasobu, który jest dostarczany bez konieczności tworzenia zasobów. W przypadku wybrania tej ścieżki w przyszłości będzie wymagane przeprowadzenie [migracji konta](luis-migration-authoring.md#migration-steps) i połączenie aplikacji z zasobem tworzenia. Dlatego zaleca się wybranie ścieżki, w której będziesz kontynuować korzystanie z zasobów platformy Azure.

    [Dowiedz się więcej o kluczach tworzenia i starterów](luis-how-to-azure-subscription.md#luis-resources). Oba zasoby zapewniają 1 000 000 bezpłatnych transakcji tworzenia i 1000 bezpłatnych transakcji prognozowanych.

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="Zrzut ekranu częściowego, aby wybrać typ zasobu Language Understanding tworzenia.":::

1. Użyj istniejącego zasobu tworzenia

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="Wybieranie zasobu tworzenia":::

    Jeśli masz już LUIS zasoby tworzenia w ramach subskrypcji i masz skojarzenie z kontem usługi LUIS podczas logowania, wybierz opcję **Użyj istniejącego zasobu tworzenia** i podaj następujące informacje:

    * **Dzierżawca** — dzierżawa, z którą skojarzona jest subskrypcja platformy Azure. Nie będzie można przełączyć dzierżawców z istniejącego okna. Możesz przełączyć dzierżawców, wybierając z prawej strony awatar, który zawiera inicjały na górnym pasku.
    * **Nazwa subskrypcji** — subskrypcja, która zostanie skojarzona z zasobem. Jeśli masz więcej niż jedną subskrypcję, która należy do dzierżawy, wybierz żądaną wartość z listy rozwijanej.
    * **Nazwa zasobu** — zasobu tworzenia, z którym chcesz skojarzyć konto.

    > [!Note]
    > Jeśli na liście rozwijanej jest wyszarzony zasób tworzenia, oznacza to, że zalogowano się do innego portalu regionalnego. [Zrozumienie koncepcji portali regionalnych](luis-reference-regions.md#luis-authoring-regions).

1. Utwórz nowy zasób tworzenia

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="Utwórz zasób tworzenia":::

    Podczas **tworzenia nowego zasobu tworzenia** należy podać następujące informacje:

    * **Dzierżawca** — dzierżawa, z którą skojarzona jest subskrypcja platformy Azure. Nie będzie można przełączyć dzierżawców z istniejącego okna. Możesz przełączyć dzierżawców, wybierając z prawej strony awatar, który zawiera inicjały na górnym pasku.
    * **Nazwa zasobu** — wybrana przez Ciebie Nazwa niestandardowa, używana jako część adresu URL dla transakcji tworzenia. Nazwa zasobu może zawierać tylko znaki alfanumeryczne, znak "-" i nie może rozpoczynać się ani kończyć znakiem "-". Jeśli wszystkie inne symbole są zawarte w nazwie, utworzenie zasobu zakończy się niepowodzeniem.
    * **Nazwa subskrypcji** — subskrypcja, która zostanie skojarzona z zasobem. Jeśli masz więcej niż jedną subskrypcję, która należy do dzierżawy, wybierz żądaną wartość z listy rozwijanej.
    * **Grupa zasobów** — niestandardowa nazwa grupy zasobów wybrana w ramach subskrypcji. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania. Jeśli obecnie nie masz grupy zasobów w subskrypcji, nie będzie można jej utworzyć w portalu LUIS. Przejdź do [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) , aby utworzyć jeden, a następnie przejdź do Luis, aby kontynuować proces logowania.

1. Po wybraniu ścieżki może upłynąć kilka sekund, aż zostanie wyświetlony komunikat "pomyślnie przeprowadzono migrację Twojego konta. Zakończ, wybierając pozycję **Kontynuuj**.

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="Potwierdź zasób autorstwa":::

    > [!Note]
    > Jeśli masz subskrypcję i co najmniej jeden zasób tworzenia w tym samym regionie, w którym rejestrujesz się w portalu, możesz automatycznie zalogować się do LUIS zmigrowany i skojarzonych z zasobem bez konieczności wybierania ścieżki, która ma zostać przeprowadzona.


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Zaloguj się przy użyciu konta użytkownika, które nie jest skojarzone z subskrypcją platformy Azure

1. Zaloguj się do [portalu Luis](https://www.luis.ai) i sprawdź, czy zgadzasz się na warunki użytkowania.

1. Zakończ, wybierając pozycję **Kontynuuj**. Spowoduje to automatyczne zalogowanie się przy użyciu klucza próbnego/początkowego. Oznacza to, że ostatecznie konieczne będzie przeprowadzenie [migracji konta](luis-migration-authoring.md#migration-steps) i połączenie aplikacji z zasobem tworzenia. Aby przejść do procesu migracji, należy zalogować się do [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/).

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="Brak scenariusza subskrypcji":::

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* W przypadku utworzenia zasobu tworzenia z Azure Portal w innym regionie niż w portalu, w którym się logujesz, zasób tworzenia będzie wyszarzony.
* Podczas tworzenia nowego zasobu upewnij się, że nazwa zasobu zawiera tylko znaki alfanumeryczne, "-" i nie może zaczynać się ani kończyć znakiem "-". W przeciwnym razie zakończy się niepowodzeniem.
* Upewnij się, że masz [odpowiednie uprawnienia do subskrypcji, aby utworzyć zasób platformy Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem subskrypcji, aby udzielić wystarczających uprawnień.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [uruchomić nową aplikację](luis-how-to-start-new-app.md)
