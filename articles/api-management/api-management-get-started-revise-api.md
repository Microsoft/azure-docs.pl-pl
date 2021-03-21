---
title: Samouczek — Użyj poprawek w API Management, aby bezpiecznie wprowadzać niekrytyczne zmiany interfejsu API
titleSuffix: Azure API Management
description: Wykonaj kroki tego samouczka, aby dowiedzieć się, jak przy użyciu poprawek wprowadzać zmiany niepowodujące niezgodności w usłudze API Management.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: acb121bb00df481c926ebed9594bf0fe1b9b17ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546639"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Samouczek: bezpieczne wprowadzanie nieprzerwanych zmian interfejsu API
Gdy interfejs API jest gotowy i zaczyna być używany przez deweloperów, zazwyczaj musisz wprowadzać zmiany w tym interfejsie API i jednocześnie nie zakłócać pracy wywołującym interfejs API. Ponadto warto poinformować deweloperów o wprowadzanych zmianach. 

Na platformie Azure API Management Użyj *poprawek* , aby wprowadzić niekrytyczne zmiany interfejsu API, dzięki czemu można bezpiecznie modelować i testować zmiany. Gdy wszystko będzie gotowe, możesz wprowadzić poprawkę bieżącą i zastąpić bieżący interfejs API. 

W przypadku programu w tle zobacz [wersje & poprawki](https://azure.microsoft.com/blog/versions-revisions/) i [wersja interfejsu API za pomocą usługi Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowej poprawki
> * Wprowadzanie w poprawce zmian niepowodujących niezgodności
> * Ustawianie poprawki jako bieżącej oraz dodawanie wpisu dziennika zmian
> * Przeglądanie portalu dla deweloperów w celu wyświetlenia zmian i dziennika zmian

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Poprawki interfejsu API w Azure Portal":::

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Dodawanie nowej poprawki

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i przejdź do wystąpienia API Management.
1. Wybierz pozycję **Interfejsy API**.
2. Wybierz opcję **demona Konferencja interfejsu API** z listy interfejsów API (lub innego interfejsu API, do którego chcesz dodać poprawki).
3. Wybierz kartę **poprawki** .
4. Wybierz pozycję **+ Dodaj poprawkę**.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="Dodawanie poprawki interfejsu API":::

    > [!TIP]
    > Możesz również wybrać pozycję **Dodaj poprawkę** w menu kontekstowym (**...**) interfejsu API.

5. Podaj opis nowej poprawki, aby łatwiej zapamiętać, do czego służy.
6. Wybierz pozycję **Utwórz**,
7. Nowa poprawka zostanie utworzona.

    > [!NOTE]
    > Oryginalny interfejs API pozostaje w poprawce **Poprawka 1**. Jest to poprawka, którą nadal będą wywoływać użytkownicy, dopóki nie wybierzesz innej poprawki jako bieżącej.

## <a name="make-non-breaking-changes-to-your-revision"></a>Wprowadzanie w poprawce zmian niepowodujących niezgodności

1. Z listy interfejsów API wybierz pozycję **Wersja demonstracyjna interfejsu API Conference**.
1. Wybierz kartę **Projektowanie** w górnej części ekranu.
1. Zwróć uwagę, że **selektor poprawek** (bezpośrednio nad kartą projektowania) wskazuje poprawkę **Poprawka 2** jako aktualnie wybraną.

    > [!TIP]
    > Za pomocą selektora poprawek możesz przełączać się pomiędzy poprawkami, nad którymi chcesz pracować.
1. Wybierz opcję **+ Dodaj operację**.
1. Ustaw nową operację na **POST**, a nazwę, nazwę wyświetlaną i adres URL operacji na **test**.
1. **Zapisz** nową operację.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Modyfikowanie poprawki":::
1. Wprowadzono teraz zmianę w **wersji 2**. Użyj **selektora poprawek** w górnej części strony, aby przełączyć się z powrotem do **poprawki 1**.
1. Zauważ, że nowa operacja nie pojawia się w ramach poprawki **Poprawka 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Ustawianie poprawki jako bieżącej oraz dodawanie wpisu dziennika zmian

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz kartę **Poprawki** w menu w górnej części strony.
1. Otwórz menu kontekstowe (**...**) dla poprawki **Poprawka 2**.
1. Wybierz pozycję **Ustaw jako bieżącą**.
1. Zaznacz pole wyboru **Publikuj w publicznym dzienniku zmian tego interfejsu API** , jeśli chcesz opublikować uwagi dotyczące tej zmiany. Podaj opis zmiany, którą widzą deweloperzy, na przykład: **testowanie poprawek. Dodano nową operację "test".**
1. **Poprawka 2** jest teraz bieżącą poprawką.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Menu poprawki w oknie poprawki":::

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby rozpocząć korzystanie z interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Użyj tej procedury, aby utworzyć i zaktualizować wydanie.

1. Uruchom polecenie [AZ APIM API list](/cli/azure/apim/api#az_apim_api_list) , aby wyświetlić identyfikatory interfejsów API:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   IDENTYFIKATORem interfejsu API do użycia w następnym poleceniu jest `Name` wartość. Poprawka interfejsu API znajduje się w `ApiRevision` kolumnie.

1. Aby utworzyć wydanie, z uwagą do wersji, uruchom polecenie [AZ APIM API Release Create](/cli/azure/apim/api/release#az_apim_api_release_create) :

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   Wersja, którą zwolnisz, jest bieżącą poprawką.

1. Aby wyświetlić swoje wersje, użyj polecenia [AZ APIM API Release list](/cli/azure/apim/api/release#az_apim_api_release_list) :

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   Określone uwagi są wyświetlane w dziennikach zmian. Są one widoczne w danych wyjściowych poprzedniego polecenia.

1. Gdy tworzysz wydanie, `--notes` parametr jest opcjonalny. Możesz dodać lub zmienić uwagi później za pomocą polecenia [AZ APIM API Release Update](/cli/azure/apim/api/release#az_apim_api_release_update) :

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   Użyj wartości w `Name` kolumnie identyfikatora zlecenia.

Możesz usunąć dowolną wersję, uruchamiając polecenie [AZ APIM API Release Delete ](/cli/azure/apim/api/release#az_apim_api_release_delete) :

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Przeglądanie portalu dla deweloperów w celu wyświetlenia zmian i dziennika zmian

Jeśli w [portalu dla deweloperów](api-management-howto-developer-portal-customize.md)podjęto próbę, możesz przejrzeć zmiany w interfejsie API i tam zmienić dziennik.

1. W Azure Portal wybierz pozycję **interfejsy API**.
1. W górnym menu wybierz pozycję **Portal dla deweloperów** .
1. W portalu dla deweloperów wybierz opcję **interfejsy API**, a następnie wybierz pozycję **interfejs API konferencji demonstracyjnej**.
1. Zauważ, że nowa operacja **test** jest teraz dostępna.
1. Wybierz pozycję **Dziennik zmian** obok nazwy interfejsu API.
1. Zauważ, że na liście pojawia się wpis dziennika zmian.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowej poprawki
> * Wprowadzanie w poprawce zmian niepowodujących niezgodności
> * Ustawianie poprawki jako bieżącej oraz dodawanie wpisu dziennika zmian
> * Przeglądanie portalu dla deweloperów w celu wyświetlenia zmian i dziennika zmian

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Publikowanie wielu wersji interfejsu API](api-management-get-started-publish-versions.md)
