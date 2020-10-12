---
title: Modyfikowanie zawartości strony w portalu dla deweloperów w API Management
titleSuffix: Azure API Management
description: Dowiedz się, jak edytować zawartość stron w portalu deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: ebf2cbd430339378a09d10d91ad61327d24842e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75430635"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Modyfikowanie zawartości i układu stron w portalu deweloperów w usłudze Azure API Management
Istnieją trzy podstawowe sposoby dostosowywania portalu deweloperów w usłudze Azure API Management:

* [Edycja zawartości stron statycznych i elementów układu strony][modify-content-layout] (opisana w tym przewodniku)
* [Aktualizacja stylów używanych dla elementów strony w portalu deweloperów][customize-styles]
* [Modyfikowanie szablonów używanych dla stron wygenerowanych przez portal][portal-templates] (na przykład dokumentacji interfejsu API, produktów, uwierzytelniania użytkowników itp.)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="structure-of-developer-portal-pages"></a><a name="page-structure"> </a>Struktura stron portalu deweloperów

Portal deweloperów jest oparty na systemie zarządzania zawartością. Układ każdej strony jest tworzony przez zestaw małych elementów nazywanych widżetami:

![Struktura stron portalu deweloperów][api-management-customization-widget-structure]

Wszystkie widżety można edytować.
* Podstawowa zawartość poszczególnych stron znajduje się w widżecie „Zawartość”. Edycja strony polega na edytowaniu zawartości tego widżetu.
* Wszystkie elementy układu strony są zawarte w pozostałych widżetach. Zmiany wprowadzone w tych widżetach są stosowane do wszystkich stron. Są one nazywane „widżetami układu”.

W ramach codziennej edycji stron zwykle modyfikowany jest tylko widżet Zawartość mający różną zawartość w przypadku poszczególnych stron.

## <a name="modifying-the-contents-of-a-layout-widget"></a><a name="modify-layout-widget"> </a>Modyfikowanie zawartości widżetu układu

Portal deweloperów jest dostępny z poziomu witryny Azure Portal.

1. Kliknij pozycję **Portal deweloperów** na pasku narzędzi wystąpienia usługi API Management.
2. Aby edytować zawartość widżetów, w menu **Portalu deweloperów** po lewej stronie kliknij ikonę przedstawiającą dwa pędzle.
3. Aby zmodyfikować zawartość nagłówka, przewiń listę po lewej stronie do sekcji **Nagłówek**.

    Widżety można edytować w polach.
4. Gdy zmiany będą gotowe do opublikowania, kliknij pozycję **Publikuj** w dolnej części strony.

Teraz nowy nagłówek powinien być widoczny na każdej stronie portalu dla deweloperów.

## <a name="next-steps"></a><a name="next-steps"> </a>Następne kroki
* [Aktualizacja stylów używanych dla elementów strony w portalu deweloperów][customize-styles]
* [Modyfikowanie szablonów używanych dla stron wygenerowanych przez portal][portal-templates] (na przykład dokumentacji interfejsu API, produktów, uwierzytelniania użytkowników itp.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
