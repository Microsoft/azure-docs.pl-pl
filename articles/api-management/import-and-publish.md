---
title: Samouczek — Importowanie i publikowanie pierwszego interfejsu API na platformie Azure API Management
description: W tym samouczku zaimportujemy interfejs API specyfikacji OpenAPI do usługi Azure API Management, a następnie testujesz interfejs API w Azure Portal.
author: mikebudzynski
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 9ff64f57e61002101b4e2c560bdcd91863cc461e
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626975"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>Samouczek: Importowanie i publikowanie pierwszego interfejsu API

W tym samouczku przedstawiono sposób importowania interfejsu API zaplecza specyfikacji OpenAPI w formacie JSON do usługi Azure API Management. Firma Microsoft udostępnia interfejs API zaplecza, który jest używany w tym przykładzie, i hostuje go na platformie Azure pod adresem [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json) .

Po zaimportowaniu interfejsu API zaplecza do API Management, interfejs API API Management stał się elewacją dla interfejsu API zaplecza. Możesz dostosować elewację do swoich potrzeb w API Management bez dotykania interfejsu API zaplecza. Aby uzyskać więcej informacji, zobacz [Przekształcanie i ochrona interfejsu API](transform-api.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie interfejsu API do API Management
> * Testowanie interfejsu API w witrynie Azure Portal

Po zaimportowaniu można zarządzać interfejsem API w Azure Portal.

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="Nowy interfejs API w API Management":::

## <a name="prerequisites"></a>Wymagania wstępne

- Poznaj [terminologię dotyczącą API Management platformy Azure](api-management-terminology.md).
- [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>Importowanie i publikowanie interfejsu API zaplecza

W tej sekcji przedstawiono sposób importowania i publikowania interfejsu API zaplecza specyfikacji OpenAPI.

1. Na lewym pasku nawigacyjnym wystąpienia API Management wybierz pozycję **interfejsy API**.
1. Wybierz kafelek **openapi** .
1. W oknie **Tworzenie ze specyfikacji openapi** wybierz opcję **pełna**.
1. Wprowadź wartości z poniższej tabeli. Następnie wybierz pozycję **Utwórz** , aby utworzyć interfejs API.

   Możesz ustawić wartości interfejsu API podczas tworzenia lub później, przechodząc do karty **Ustawienia** .

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="Nowy interfejs API w API Management":::


   |Ustawienie|Wartość|Opis|
   |-------|-----|-----------|
   |**Specyfikacja OpenAPI**|*https: \/ /conferenceapi.azurewebsites.NET? format = JSON*|Usługa implementująca interfejs API. API Management przekazuje żądania do tego adresu.|
   |**Nazwa wyświetlana**|Po wprowadzeniu powyższego adresu URL usługi API Management wypełnia to pole na podstawie kodu JSON.|Nazwa wyświetlana w portalu dla [deweloperów](api-management-howto-developer-portal.md).|
   |**Nazwa**|Po wprowadzeniu powyższego adresu URL usługi API Management wypełnia to pole na podstawie kodu JSON.|Unikatowa nazwa interfejsu API.|
   |**Opis**|Po wprowadzeniu powyższego adresu URL usługi API Management wypełnia to pole na podstawie kodu JSON.|Opcjonalny opis interfejsu API.|
   |**Schemat adresu URL**|**HTTPS**|Protokoły, które mogą uzyskać dostęp do interfejsu API.|
   |**Sufiks adresu URL interfejsu API**|*conference*|Sufiks dołączany do podstawowego adresu URL usługi API Management. API Management odróżnia interfejsy API według ich sufiksów, więc sufiks musi być unikatowy dla każdego interfejsu API dla danego wydawcy.|
   |**Tagi**| |Tagi służące do organizowania interfejsów API do wyszukiwania, grupowania lub filtrowania.|
   |**Produkty**|**Nieograniczona liczba**|Skojarzenie jednego lub więcej interfejsów API. Każde wystąpienie API Management zawiera dwa przykładowe produkty: **Starter** i **nieograniczone**. Interfejs API jest publikowany przez skojarzenie interfejsu API z produktem, **nieograniczoną** w tym przykładzie.<br/><br/> Możesz dołączyć kilka interfejsów API w produkcie i zaoferować je deweloperom za pomocą portalu dla deweloperów. Aby dodać ten interfejs API do innego produktu, wpisz lub wybierz nazwę produktu. Powtórz ten krok, aby dodać interfejs API do wielu produktów. Możesz również dodać interfejsy API do produktów później ze strony **ustawień** .<br/><br/>  Aby uzyskać więcej informacji na temat produktów, zobacz [Tworzenie i publikowanie produktu](api-management-howto-add-products.md).|
   |**Bramy**|**Zarządzany**|Bramy interfejsu API, które uwidaczniają interfejs API. To pole jest dostępne tylko w ramach usług dla **deweloperów** i warstwy **Premium** .<br/><br/>**Zarządzany** oznacza bramę wbudowaną w usługę API Management i hostowaną przez firmę Microsoft na platformie Azure. [Bramy samoobsługowe](self-hosted-gateway-overview.md) są dostępne tylko w warstwach usług premium i developer. Można wdrożyć je lokalnie lub w innych chmurach.<br/><br/> Jeśli nie wybrano żadnych bram, interfejs API nie będzie dostępny i żądania interfejsu API nie powiodą się.|
   |**Czy chcesz utworzyć wersję tego interfejsu API?**|Zaznacz lub usuń zaznaczenie|Aby uzyskać więcej informacji, zobacz [Publikowanie wielu wersji interfejsu API](api-management-get-started-publish-versions.md).|

   > [!NOTE]
   > Aby opublikować interfejs API dla odbiorców interfejsu API, należy skojarzyć go z produktem.

2. Wybierz przycisk **Utwórz**.

Jeśli masz problemy z importowaniem definicji interfejsu API, zobacz [listę znanych problemów i ograniczeń](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Przetestuj nowy interfejs API w Azure Portal

Operacje interfejsu API można wywoływać bezpośrednio z Azure Portal, co zapewnia wygodny sposób wyświetlania i testowania operacji.

1. Na lewym pasku nawigacyjnym wystąpienia **API Management wybierz pozycję API**  >  **demona wideokonferencji interfejs**użytkownika.
1. Wybierz kartę **test** , a następnie wybierz pozycję **getgłośników**. Na stronie są wyświetlane parametry i **nagłówki** **zapytania** , jeśli istnieją. Wartość **OCP-APIM-Subscription-Key** jest automatycznie wypełniana dla klucza subskrypcji skojarzonego z tym interfejsem API.
1. Wybierz pozycję **Wyślij**.

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="Nowy interfejs API w API Management":::

   Zaplecze reaguje na **200 OK** i pewne dane.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie pierwszego interfejsu API
> * Testowanie interfejsu API w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć i opublikować produkt:

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie produktu](api-management-howto-add-products.md)
