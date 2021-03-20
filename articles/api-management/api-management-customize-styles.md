---
title: Dostosuj styl strony w API Management starszym portalu dla deweloperów
titleSuffix: Azure API Management
description: Wykonaj kroki tego przewodnika Szybki start, aby dostosować styl elementów w portalu deweloperów usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 0cdad245981ad02d3cdaefba447d131c775e242f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93145773"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Dostosowywanie stylu stron portalu dla deweloperów

Istnieją trzy najczęstsze sposoby dostosowywania portalu dla deweloperów w usłudze Azure API Management:
 
* [Edycja zawartości stron statycznych i elementów układu strony](api-management-modify-content-layout.md)
* Aktualizacja stylów używanych dla elementów strony w portalu deweloperów (opisana w tym przewodniku)
* [Modyfikowanie szablonów używanych dla stron wygenerowanych przez portal](api-management-developer-portal-templates.md) (na przykład dokumentacji interfejsu API, produktów czy uwierzytelniania użytkowników)

W tym artykule dowiesz się, jak dostosować styl elementów na stronach starszego portalu dla **deweloperów** i wyświetlić zmiany.

![Zrzut ekranu pokazujący, gdzie można zmienić ustawienia w starszym portalu dla deweloperów.](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Dostosowywanie portalu dla deweloperów

1. Wybierz pozycję **Omówienie**.
2. Kliknij przycisk **Portal dla deweloperów (starsza wersja)** w górnej części okna **Przegląd** .
3. W lewej górnej części ekranu będzie widoczna ikona składająca się z dwóch pędzli. Zatrzymaj wskaźnik myszy nad tą ikoną, aby otworzyć menu dostosowywania portalu.

    ![Zrzut ekranu, który podświetla ikonę z dwoma pędzlami programu Paint.](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Wybierz pozycję **Style** z menu, aby otworzyć okienko dostosowywania stylu.

    Na stronie zostaną wyświetlone wszystkie elementy, które można dostosować za pomocą **stylów**
5. Wprowadź wartość „headings-color” w polu **Change variable values to customize developer portal appearance:** (Zmień wartości zmiennych w celu dostosowania wyglądu portalu dla deweloperów:).

    Na stronie pojawi się element **\@ nagłówki — kolor** . Ta zmienna steruje kolorem tekstu.

    ![Dostosowywanie stylu](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Kliknij pole dla zmiennej **\@ nagłówki i kolor** . 
    
    Zostanie otwarta lista rozwijana selektora kolorów.
7. Wybierz nowy kolor z listy rozwijanej selektora kolorów.

    > [!TIP]
    > Dla wszystkich zmian wyświetlany jest podgląd w czasie rzeczywistym. W górnej części okienka dostosowywania widoczny jest wskaźnik postępu. Po kilku sekundach tekst nagłówka zmieni kolor na nowo wybrany.

8. Wybierz pozycję **Publikuj** w lewym dolnym rogu menu okienka dostosowywania.
9. Wybierz pozycję **Publikuj dostosowania**, aby udostępnić zmiany publicznie.

## <a name="view-your-change"></a>Wyświetlanie zmiany

1. Przejdź do portalu dla deweloperów.
2. Wprowadzone zmiany będą widoczne.

## <a name="next-steps"></a>Następne kroki

Może Cię również zainteresować poznanie [sposobu dostosowywania portalu dla deweloperów usługi Azure API Management przy użyciu szablonów](api-management-developer-portal-templates.md).