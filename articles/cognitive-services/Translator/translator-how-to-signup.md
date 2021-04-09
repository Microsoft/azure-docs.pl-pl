---
title: Tworzenie zasobu usługi translator
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób tworzenia zasobu usługi Azure Cognitive Services translator i uzyskiwania klucza subskrypcji i adresu URL punktu końcowego.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a0d8532d19aff41bc5e7defb3b58462e81018749
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "101712933"
---
# <a name="create-a-translator-resource"></a>Tworzenie zasobu usługi translator

W tym artykule dowiesz się, jak utworzyć zasób usługi Translator w Azure Portal. [Azure translator](translator-info-overview.md) to oparta na chmurze usługa tłumaczenia maszynowego, która jest częścią usługi [Azure Cognitive Services](../what-are-cognitive-services.md) w ramach interfejsów API REST. Zasoby platformy Azure to wystąpienia usług, które tworzysz. Wszystkie żądania interfejsu API do usług platformy Azure wymagają adresu URL **punktu końcowego** i **klucza subskrypcji** tylko do odczytu w celu uwierzytelniania dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz mieć aktywne [**konto platformy Azure**](https://azure.microsoft.com/free/cognitive-services/).  Jeśli go nie masz, możesz [**utworzyć bezpłatną subskrypcję 12-miesięczną**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Typy zasobów usługi translator

Dostęp do usługi translator można uzyskać za pomocą dwóch różnych typów zasobów:

* Typy zasobów **pojedynczej usługi** umożliwiają dostęp do klucza interfejsu API i punktu końcowego usługi Single Service.  

* Typy zasobów dla **wielu usług** umożliwiają dostęp do wielu Cognitive Services przy użyciu jednego klucza interfejsu API i punktu końcowego. Zasób Cognitive Services jest obecnie dostępny dla następujących usług:
  * Język ([translator](../translator/translator-info-overview.md), [Language Understanding (LUIS)](../luis/what-is-luis.md), [Analiza tekstu](../text-analytics/overview.md))  
  * Vision ([Przetwarzanie obrazów](../computer-vision/overview.md)), (z[przodu](../face/overview.md))  
  * Decyzja ([Content moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Tworzenie zasobu

* Przejdź bezpośrednio do strony [**Tworzenie usługi Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) w Azure Portal, aby zakończyć szczegóły projektu.

* Przejdź bezpośrednio do strony [**tworzenie Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) w Azure Portal, aby zakończyć szczegóły projektu.

>[!TIP]
>Jeśli wolisz, możesz zacząć od strony głównej witryny Azure Portal, aby rozpocząć proces **tworzenia** w następujący sposób:
>
> 1. Przejdź do strony głównej witryny [**Azure Portal**](https://ms.portal.azure.com/#home) .
> 1. Wybierz pozycję ➕**utworzyć zasób**  z poziomu menu usługi platformy Azure.
>1. W polu Wyszukaj w **portalu Marketplace** wprowadź i wybierz pozycję **translator** (zasób pojedynczego usługi) lub **Cognitive Services** (zasób usługi).  *Zobacz* [Wybierz typ zasobu](#create-your-resource)powyżej.
> 1. Wybierz pozycję **Utwórz** i nastąpi przekierowanie do strony szczegółów projektu.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Uzupełnij szczegóły projektu i wystąpienia

1. **Subskrypcja**. Wybierz jedną z dostępnych subskrypcji platformy Azure.

1. **Grupa zasobów**. Wybrana grupa zasobów platformy Azure będzie stanowić kontener wirtualny dla nowego zasobu. Można utworzyć nową grupę zasobów lub dodać zasób do istniejącej grupy zasobów, która współużytkuje ten sam cykl życia, uprawnienia i zasady.

1. **Region zasobu**. Wybierz pozycję **globalne** , chyba że Twoja firma lub aplikacja nie wymaga określonego regionu. Translator jest usługą nieregionalną — nie ma zależności w określonym regionie świadczenia usługi Azure. *Zobacz* [regiony i strefy dostępności na platformie Azure](../../availability-zones/az-overview.md).

1. **Nazwa**. Wprowadź nazwę wybraną dla zasobu. Wybrana nazwa musi być unikatowa w ramach platformy Azure.

> [!NOTE]
> Jeśli używasz funkcji translatora wymagającej niestandardowego punktu końcowego domeny, wartość wprowadzona w polu Nazwa będzie niestandardowym parametrem nazwy domeny dla punktu końcowego.

5. **Warstwa cenowa**. Wybierz [warstwę cenową](https://azure.microsoft.com/pricing/details/cognitive-services/translator) , która spełnia Twoje potrzeby:

   * Każda subskrypcja ma bezpłatną warstwę.
   * Warstwa Bezpłatna ma takie same funkcje jak płatne plany i nie wygasa.
   * Dozwolona jest tylko jedna bezpłatna subskrypcja na konto.</li></ul>

1. Jeśli utworzono zasób z obsługą kilku usług, należy potwierdzić dodatkowe szczegóły użycia za pomocą pól wyboru.

1. Wybierz pozycję **Przejrzyj i utwórz**.

1. Przejrzyj warunki korzystania z usługi i wybierz pozycję **Utwórz** , aby wdrożyć zasób.

1. Po pomyślnym wdrożeniu zasobu wybierz pozycję **Przejdź do zasobu**.

### <a name="authentication-keys-and-endpoint-url"></a>Klucze uwierzytelniania i adres URL punktu końcowego

Wszystkie żądania interfejsu API Cognitive Services wymagają adresu URL punktu końcowego i klucza tylko do odczytu na potrzeby uwierzytelniania.

* **Klucze uwierzytelniania**. Klucz jest unikatowym ciągiem, który jest przesyłany na każde żądanie do usługi tłumaczenia. Klucz można przekazać za pomocą parametru zapytania lub przez określenie go w nagłówku żądania HTTP.

* **Adres URL punktu końcowego**. Użyj globalnego punktu końcowego w żądaniu interfejsu API, chyba że potrzebujesz określonego regionu świadczenia usługi Azure. *Zobacz* [podstawowe adresy URL](reference/v3-0-reference.md#base-urls). Globalny adres URL punktu końcowego to `api.cognitive.microsofttranslator.com` .

## <a name="get-your-authentication-keys-and-endpoint"></a>Pobieranie kluczy uwierzytelniania i punktu końcowego

1. Po wdrożeniu nowego zasobu wybierz pozycję **Przejdź do zasobu** lub przejdź bezpośrednio do strony zasobów.
1. W lewej szynie w obszarze *Zarządzanie zasobami* wybierz pozycję **klucze i punkt końcowy**.
1. Skopiuj i wklej klucze subskrypcji oraz adres URL punktu końcowego w wygodnej lokalizacji, np. w *notatniku firmy Microsoft*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Pobierz klucz i punkt końcowy.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Jak usunąć zasób lub grupę zasobów

> [!Warning]
> Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie.

Aby usunąć zasób Cognitive Services lub translator, można **usunąć zasób** lub **usunąć grupę zasobów**.

Aby usunąć zasób:

1. Przejdź do grupy zasobów w Azure Portal.
1. Wybierz zasoby do usunięcia, zaznaczając sąsiadujące pole wyboru.
1. Wybierz pozycję **Usuń** z górnego menu obok prawej krawędzi.
1. Wpisz *tak* w oknie dialogowym **usunięte zasoby** .
1. Wybierz pozycję **Usuń**.

Aby usunąć grupę zasobów:

1. Przejdź do grupy zasobów w Azure Portal.
1. Wybierz pozycję **Usuń grupę zasobów** z górnego paska menu obok lewej krawędzi.
1. Potwierdź żądanie usunięcia, wprowadzając nazwę grupy zasobów i wybierając pozycję **Usuń**.

## <a name="how-to-get-started-with-translator"></a>Jak rozpocząć pracę z usługą translator

W naszym przewodniku szybki start dowiesz się, jak korzystać z usługi translatora przy użyciu interfejsów API REST.

> [!div class="nextstepaction"]
> [Wprowadzenie do usługi translator](quickstart-translator.md)

## <a name="more-resources"></a>Więcej zasobów

* [Przykłady kodu usługi Microsoft Translator](https://github.com/MicrosoftTranslator).  Przykłady kodu usługi tłumaczenia w wielu językach są dostępne w serwisie GitHub.
* [Forum pomocy technicznej usługi Microsoft Translator](https://www.aka.ms/TranslatorForum)
* [Wprowadzenie do platformy Azure (wideo 3-minutowy)](https://azure.microsoft.com/get-started/?b=16.24)