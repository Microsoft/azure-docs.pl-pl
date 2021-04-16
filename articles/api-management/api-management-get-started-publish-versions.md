---
title: Samouczek — publikowanie wersji interfejsu API przy użyciu usługi Azure API Management
description: Wykonaj kroki tego samouczka, aby dowiedzieć się, jak opublikować wiele wersji interfejsu API w API Management.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: apimpm
ms.openlocfilehash: 5930979258372e4269c4d453ffbc5e0d46258088
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483616"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Samouczek: publikowanie wielu wersji interfejsu API 

Czasami użycie dokładnie tej samej wersji przez wszystkie wywołujące interfejs API jest niepraktyczne. Gdy wywołujący chcą uaktualnić do nowszej wersji, chcą mieć łatwe do zrozumienia podejście. Jak pokazano w tym samouczku,  w usłudze Azure API Management można dostarczać wiele API Management. 

Aby uzyskać podstawowe informacje, [zobacz Wersje & poprawki](https://azure.microsoft.com/blog/versions-revisions/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowej wersji do istniejącego interfejsu API
> * Wybieranie schematu wersji
> * Dodawanie wersji do produktu
> * Przeglądanie portalu dla deweloperów w celu wyświetlenia wersji

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Wersja pokazana w Azure Portal":::

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj czynności z następującego przewodnika Szybki start: [Tworzenie wystąpienia API Management Azure.](get-started-create-service-instance.md)
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="add-a-new-version"></a>Dodawanie nowej wersji

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego API Management wystąpienia.
1. Wybierz pozycję **Interfejsy API**.
1. Z listy interfejsów API wybierz pozycję **Wersja demonstracyjna interfejsu API Conference**. 
1. Wybierz menu kontekstowe (**...**) obok pozycji **Demo Conference API (Interfejs API pokazu konferencji).**
1. Wybierz **pozycję Dodaj wersję.**

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="Menu kontekstowe interfejsu API — dodawanie wersji":::


> [!TIP]
> Wersje można również włączyć podczas tworzenia nowego interfejsu API. Na **ekranie Dodawanie interfejsu API** wybierz pozycję **Wersja tego interfejsu API?**.

## <a name="choose-a-versioning-scheme"></a>Wybieranie schematu przechowywania wersji

W usłudze Azure API Management możesz wybrać sposób określania wersji interfejsu API przez wybranie schematu *wersji:* **ścieżki, nagłówka** lub **ciągu zapytania.** W poniższym przykładzie *ścieżka jest* używana jako schemat wersji.

Wprowadź wartości z poniższej tabeli. Następnie wybierz **pozycję Utwórz,** aby utworzyć wersję.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Okno Dodawania wersji":::



|Ustawienie   |Wartość  |Opis  |
|---------|---------|---------|
|**Nazwa**     |  *demo-conference-api-v1*       |  Unikatowa nazwa w API Management wystąpienia.<br/><br/>Ponieważ wersja jest w rzeczywistości nowym interfejsem API opartym na poprawce interfejsu [API,](api-management-get-started-revise-api.md)to ustawienie jest nazwą nowego interfejsu API.   |
|**Schemat wersjonarowania**     |  **Ścieżka**       |  Sposób, w jaki wywołujący określają wersję interfejsu API.     |
|**Identyfikator wersji**     |  *Wersja 1*       |  Wskaźnik wersji specyficzny dla schematu. W **polu Ścieżka** sufiks ścieżki adresu URL interfejsu API. <br/><br/> Jeśli **wybrano** **opcję Nagłówek** lub Ciąg zapytania, wprowadź dodatkową wartość: nazwę parametru nagłówka lub ciągu zapytania.<br/><br/> Zostanie wyświetlony przykład użycia.        |
|**Produkty**     |  **Nieograniczona liczba**       |  Opcjonalnie co najmniej jeden produkt, z który jest skojarzona wersja interfejsu API. Aby opublikować interfejs API, musisz skojarzyć go z produktem. Możesz również [dodać wersję do produktu](#add-the-version-to-a-product) później.      |

Po utworzeniu wersji jest ona teraz wyświetlana poniżej **interfejsu API Demo Conference** na liście interfejsów API. Zobaczysz teraz dwa interfejsy API: **Oryginalny** i **v1.**

![Wersje wymienione na liście w sekcji interfejsów API w witrynie Azure Portal](media/api-management-getstarted-publish-versions/version-list.png)

Teraz można edytować i konfigurować interfejs API w wersji **1,** który jest oddzielony od oryginalnego **interfejsu .** Zmiany wprowadzane w jednej wersji nie wpływają na drugą.

> [!Note]
> Jeśli dodasz wersję do interfejsu API bez wersji, **automatycznie** zostanie również utworzony oryginalny interfejs API. Ta wersja odpowiada na domyślny adres URL. Utworzenie oryginalnej wersji gwarantuje, że proces dodawania wersji nie zostanie przerwany przez wszystkie istniejące wywołujące. Jeśli utworzysz nowy interfejs API z wersjami włączonymi na początku, oryginalny interfejs API nie zostanie utworzony.

## <a name="add-the-version-to-a-product"></a>Dodawanie wersji do produktu

Aby wywołujący mogli zobaczyć nową wersję, musi zostać ona dodana do *produktu*. Jeśli nie dodasz jeszcze wersji do produktu, możesz dodać ją do produktu w dowolnym momencie.

Aby na przykład dodać wersję do produktu **Unlimited:**
1. W Azure Portal przejdź do swojego API Management wystąpienia.
1. Wybierz **pozycję Products**  >  **Unlimited**  >  **APIs**+  >  **Add**.
1. Wybierz **pozycję Pokaz interfejsu API Konferencji** w wersji **1.**
1. Kliknij pozycję **Wybierz**.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Dodawanie wersji do produktu":::

## <a name="use-version-sets"></a>Korzystanie z zestawów wersji

W przypadku tworzenia wielu wersji Azure Portal zestaw wersji *,* który reprezentuje zestaw wersji dla pojedynczego logicznego interfejsu API. Wybierz nazwę interfejsu API, który ma wiele wersji. W Azure Portal zostanie wyświetlony zestaw **wersji**. Możesz dostosować pola **Nazwa** **i Opis** zestawu wirtualnego.

Bezpośrednią interakcję z zestawami wersji można uzyskać przy użyciu interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Aby wyświetlić wszystkie zestawy wersji, uruchom [polecenie az apim api versionset list:](/cli/azure/apim/api/versionset#az_apim_api_versionset_list)

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Gdy Azure Portal zestaw wersji, przypisuje nazwę alfanumeryczną, która jest wyświetlana w  kolumnie Nazwa listy. Użyj tej nazwy w innych poleceniach interfejsu wiersza polecenia platformy Azure.

Aby wyświetlić szczegółowe informacje o zestawie wersji, uruchom [polecenie az apim api versionset show:](/cli/azure/apim/api/versionset#az_apim_api_versionset_show)

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

Aby uzyskać więcej informacji na temat zestawów wersji, zobacz [Wersje w usłudze Azure API Management](api-management-versions.md#how-versions-are-represented).

## <a name="browse-the-developer-portal-to-see-the-version"></a>Przeglądanie portalu dla deweloperów w celu wyświetlenia wersji

Jeśli wypróbowano portal dla [deweloperów](api-management-howto-developer-portal-customize.md), możesz tam zobaczyć wersje interfejsu API.

1. Wybierz opcję **Portal dla deweloperów** z górnego menu.
2. Wybierz pozycję **Interfejsy API**, a następnie wybierz pozycję **Wersja demonstracyjna interfejsu API Conference**.
3. Obok nazwy interfejsu API powinna zostać wyświetlony lista rozwijana z wieloma wersjami.
4. Wybierz opcję **v1**.
5. Zwróć uwagę na **Adres URL żądania** pierwszej operacji na liście. Pozycja pokazuje, że ścieżka adresu URL interfejsu API uwzględnia ciąg **v1**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowej wersji do istniejącego interfejsu API
> * Wybieranie schematu wersji 
> * Dodawanie wersji do produktu
> * Przeglądanie portalu dla deweloperów w celu wyświetlenia wersji

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Dostosowywanie stylu stron portalu deweloperów](api-management-howto-developer-portal-customize.md)
