---
title: Samouczek — Tworzenie i publikowanie produktu na platformie Azure API Management
description: W tym samouczku utworzysz i opublikujesz produkt na platformie Azure API Management. Po jej opublikowaniu deweloperzy mogą zacząć korzystać z interfejsów API produktu.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 2f298f240d8aa7a38b42a8c78ee3c90fe3423d10
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630582"
---
# <a name="tutorial-create-and-publish-a-product"></a>Samouczek: Tworzenie i publikowanie produktu  

Na platformie Azure API Management [*produkt*](api-management-terminology.md#term-definitions) zawiera jeden lub więcej interfejsów API, a także limit przydziału użycia i warunki użytkowania. Po opublikowaniu produktu deweloperzy mogą go zasubskrybować i zacząć korzystać z jego interfejsów API.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i publikowanie produktu
> * Dodawanie interfejsu API do produktu

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="API Management produkty w portalu":::


## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Tworzenie i publikowanie produktu

1. Zaloguj się do Azure Portal i przejdź do wystąpienia API Management.
1. W lewym okienku nawigacji wybierz pozycję **produkty**  >  **+ Dodaj**.
1.  W oknie **Dodawanie produktu** wprowadź wartości opisane w poniższej tabeli, aby utworzyć produkt.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="API Management produkty w portalu":::

    | Nazwa                     | Opis                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nazwa wyświetlana             | Nazwa, która ma być wyświetlana w [portalu dla deweloperów](api-management-howto-developer-portal.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Opis              | Podaj informacje o produkcie, takie jak jego przeznaczenie, interfejsy API, do których zapewnia dostęp, oraz inne szczegóły.                                                                                                                                               |
    | Stan                    | Wybierz pozycję **opublikowano** , jeśli chcesz opublikować produkt. Aby możliwe było wywołanie interfejsów API w produkcie, produkt musi zostać najpierw opublikowany. Domyślnie nowe produkty nie są publikowane i są widoczne tylko dla grupy  **administratorzy** .                                                                                      |
    | Wymaga subskrypcji    | Wybierz, czy użytkownik musi subskrybować korzystanie z produktu.                                                                                                                                                                                                                                   |
    | Wymaga zatwierdzenia        | Wybierz, czy chcesz, aby administrator przeglądał i akceptował lub odrzucał próby subskrypcji tego produktu. Jeśli nie zostanie wybrana, próby subskrypcji są zatwierdzane domyślnie.                                                                                                                         |
    | Limit liczby subskrypcji | Opcjonalnie można ograniczyć liczbę równoczesnych subskrypcji.                                                                                                                                                                                                                                |
    | Postanowienia prawne              | Możesz uwzględnić warunki użytkowania produktu, które jego subskrybenci muszą zaakceptować, aby z niego korzystać.                                                                                                                                                                                                             |
    | Interfejsy API                     | Wybierz co najmniej jeden interfejs API. Możesz również dodać interfejsy API po utworzeniu produktu. Aby uzyskać więcej informacji, zobacz [Dodawanie interfejsów API do produktu](#add-apis-to-a-product) w dalszej części tego artykułu. |

3. Wybierz pozycję **Utwórz** , aby utworzyć nowy produkt.

### <a name="add-more-configurations"></a>Dodawanie dodatkowych konfiguracji

Kontynuuj Konfigurowanie produktu po jego zapisaniu. W wystąpieniu API Management wybierz produkt z okna **produkty** . Dodaj lub zaktualizuj:


|Element   |Opis  |
|---------|---------|
|Ustawienia     |    Metadane i stan produktu     |
|Interfejsy API     |  Interfejsy API skojarzone z produktem       |
|[Zasady](api-management-howto-policies.md)     |  Zasady stosowane do interfejsów API produktu      |
|Kontrola dostępu     |  Widoczność produktów dla deweloperów lub Gości       |
|[Subskrypcje](api-management-subscriptions.md)    |    Subskrybenci produktu     |

## <a name="add-apis-to-a-product"></a>Dodawanie interfejsów API do produktu

Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Podczas tworzenia produktu można dodać jeden lub więcej istniejących interfejsów API. Interfejsy API można również dodać do produktu później, na stronie **Ustawienia** produktów lub podczas tworzenia interfejsu API.

Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc domyślnie posiada subskrypcję każdego produktu.

### <a name="add-an-api-to-an-existing-product"></a>Dodawanie interfejsu API do istniejącego produktu


1. Na lewym pasku nawigacyjnym wystąpienia API Management wybierz pozycję **produkty**.
1. Wybierz produkt, a następnie wybierz pozycję **interfejsy API**.
1. Wybierz pozycję **+ Dodaj**.
1. Wybierz co najmniej jeden interfejs API, a następnie **Wybierz opcję**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="API Management produkty w portalu":::

> [!TIP]
> Możesz utworzyć lub zaktualizować subskrypcję użytkownika do produktu przy użyciu niestandardowych kluczy subskrypcji za pomocą [interfejsu API REST](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) lub polecenia programu PowerShell.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i publikowanie produktu
> * Dodawanie interfejsu API do produktu

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Tworzenie pustego interfejsu API i testowanie odpowiedzi interfejsu API](mock-api-responses.md)
