---
title: Samouczek — bezpieczne API Management zmian interfejsu API przy użyciu poprawek w interfejsie API
titleSuffix: Azure API Management
description: Wykonaj kroki tego samouczka, aby dowiedzieć się, jak przy użyciu poprawek wprowadzać zmiany niepowodujące niezgodności w usłudze API Management.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 1d99d6f876e4896bb4321afb8dc4d8e7c3a404e7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483558"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Samouczek: bezpieczne stosowanie poprawek w celu bezpiecznego stosowania niepowiązywanych zmian interfejsu API
Gdy interfejs API jest gotowy i zaczyna być używany przez deweloperów, zazwyczaj musisz wprowadzać zmiany w tym interfejsie API i jednocześnie nie zakłócać pracy wywołującym interfejs API. Ponadto warto poinformować deweloperów o wprowadzanych zmianach. 

W usłudze Azure API Management użyj *poprawek,* aby wprowadzić niepowiązywająco istotne zmiany interfejsu API, aby można było bezpiecznie modelować i testować zmiany. Gdy wszystko będzie gotowe, możesz wprowadzić poprawkę jako bieżącą i zastąpić bieżący interfejs API. 

Aby uzyskać więcej informacji, [zobacz Wersje & i](https://azure.microsoft.com/blog/versions-revisions/) [Wersjonowanie interfejsu API za pomocą usługi Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowej poprawki
> * Wprowadzanie w poprawce zmian niepowodujących niezgodności
> * Ustawianie poprawki jako bieżącej oraz dodawanie wpisu dziennika zmian
> * Przeglądanie portalu dla deweloperów w celu wyświetlenia zmian i dziennika zmian

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Poprawki interfejsu API w Azure Portal":::

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj czynności z następującego przewodnika Szybki start: [tworzenie wystąpienia API Management Azure.](get-started-create-service-instance.md)
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Dodawanie nowej poprawki

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i przejdź do swojego API Management wystąpienia.
1. Wybierz pozycję **Interfejsy API**.
2. Wybierz **pozycję Pokaz interfejsu API Konferencji** z listy interfejsów API (lub inny interfejs API, do którego chcesz dodać poprawki).
3. Wybierz **kartę Poprawki.**
4. Wybierz **pozycję + Dodaj poprawkę.**

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="Dodawanie poprawki interfejsu API":::

    > [!TIP]
    > Możesz również wybrać pozycję **Dodaj poprawkę** w menu kontekstowym **(...)** interfejsu API.

5. Podaj opis nowej poprawki, aby łatwiej zapamiętać, do czego służy.
6. Wybierz **pozycję Utwórz**,
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
1. W wersji Poprawka 2 w wejdą **zmiany.** Użyj **selektora poprawek** w górnej części strony, aby przełączyć się z powrotem do **wersji Poprawka 1.**
1. Zauważ, że nowa operacja nie pojawia się w ramach poprawki **Poprawka 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Ustawianie poprawki jako bieżącej oraz dodawanie wpisu dziennika zmian

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz kartę **Poprawki** w menu w górnej części strony.
1. Otwórz menu kontekstowe (**...**) dla poprawki **Poprawka 2**.
1. Wybierz **pozycję Określ jako bieżącą.**
1. Zaznacz pole wyboru Opublikuj w publicznym dzienniku zmian dla tego interfejsu **API,** jeśli chcesz publikować notatki dotyczące tej zmiany. Podaj opis zmiany, który zobaczą deweloperzy, na przykład: **Testowanie poprawek. Dodano nową operację "test".**
1. **Poprawka 2** jest teraz bieżącą poprawką.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Menu Poprawka w oknie Poprawki":::

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby rozpocząć korzystanie z interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Użyj tej procedury, aby utworzyć i zaktualizować wydanie.

1. Uruchom polecenie [az apim api list,](/cli/azure/apim/api#az_apim_api_list) aby wyświetlić identyfikatory interfejsów API:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   Identyfikator interfejsu API do użycia w następnym poleceniu to `Name` wartość . Poprawka interfejsu API znajduje się w `ApiRevision` kolumnie .

1. Aby utworzyć wydanie z informacjami o wersji, uruchom [polecenie az apim api release create:](/cli/azure/apim/api/release#az_apim_api_release_create)

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   Wydanie poprawki staje się bieżącą poprawą.

1. Aby wyświetlić swoje wydania, użyj [polecenia az apim api release list:](/cli/azure/apim/api/release#az_apim_api_release_list)

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   Określone uwagi są wyświetlane w ładzie zmian. Można je wyświetlić w danych wyjściowych poprzedniego polecenia.

1. Podczas tworzenia wydania parametr `--notes` jest opcjonalny. Notatki można dodać lub zmienić później przy użyciu polecenia [az apim api release update:](/cli/azure/apim/api/release#az_apim_api_release_update)

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   Użyj wartości w kolumnie `Name` dla identyfikatora wydania.

Możesz usunąć dowolną wersję, uruchamiając [polecenie az apim api release delete:](/cli/azure/apim/api/release#az_apim_api_release_delete)

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Przeglądanie portalu dla deweloperów w celu wyświetlenia zmian i dziennika zmian

Jeśli wypróbowano portal dla [deweloperów,](api-management-howto-developer-portal-customize.md)możesz przejrzeć zmiany interfejsu API i w tym miejscu przejrzeć dziennik zmian.

1. W Azure Portal wybierz pozycję **Interfejsy API.**
1. Wybierz **pozycję Portal deweloperów** z górnego menu.
1. W portalu dla deweloperów wybierz pozycję **Interfejsy API,** a następnie wybierz **pozycję Demo Conference API (Pokazowy interfejs API konferencji).**
1. Zauważ, że nowa operacja **test** jest teraz dostępna.
1. Wybierz **pozycję Changelog w** pobliżu nazwy interfejsu API.
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
