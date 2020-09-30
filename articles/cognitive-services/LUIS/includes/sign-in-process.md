---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.openlocfilehash: fda9df6c7e9651bbd3b0b70ad9d47f23c0c19d01
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541439"
---
## <a name="sign-in-to-luis-portal"></a>Zaloguj się do portalu usługi LUIS

Nowy użytkownik do LUIS musi wykonać następującą procedurę:

1. Zaloguj się do [portalu Luis](https://www.luis.ai), wybierz swój kraj/region i zaakceptuj warunki użytkowania. Jeśli zamiast tego zobaczysz **Moje aplikacje** , zasób Luis już istnieje i należy przejść do sekcji Tworzenie aplikacji. W przypadku obsługiwanych regionów odwiedź witrynę [tworzenia i publikowania regionów oraz skojarzone klucze](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

1. Wybierz pozycję **Utwórz zasób platformy Azure** , a następnie wybierz pozycję **Utwórz zasób autorstwa, aby przeprowadzić migrację aplikacji do programu.**

    ![Wybierz typ zasobu Language Understanding tworzenia](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Wprowadź szczegółowe informacje o zasobie.

    ![Zrzut ekranu przedstawia okienko Utwórz nowy zasób dla tworzenia.](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Podczas **tworzenia nowego zasobu tworzenia**należy podać następujące informacje:

    * **Nazwa zasobu** — wybrana przez Ciebie Nazwa niestandardowa, używana jako część adresu URL dla zapytań dotyczących tworzenia i przewidywania punktów końcowych.
    * **Dzierżawca** — dzierżawa, z którą skojarzona jest subskrypcja platformy Azure.
    * **Nazwa subskrypcji** — subskrypcja, za którą zostanie naliczona stawka dla zasobu.
    * **Grupa zasobów** — wybrana lub utworzona nazwa niestandardowej grupy zasobów. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.
    * **Lokalizacja** — wybór lokalizacji zależy od wybranej **grupy zasobów** .
    * **Warstwa cenowa** — warstwa cenowa określa maksymalną liczbę transakcji na sekundę i miesiąc.

1. Zostanie wyświetlone podsumowanie zasobu, który ma zostać utworzony. Wybierz pozycję **Dalej**.

    ![Zrzut ekranu przedstawia stronę powitalną z opcją połączenia z kontem platformy Azure.](../media/sign-in/sign-in-confirm-key-selection.png)

1. Potwierdź, wybierając pozycję **Kontynuuj**.

    ![Zrzut ekranu przedstawia stronę powitalną po połączeniu z kontem platformy Azure.](../media/sign-in/sign-in-confirm-continue.png)
