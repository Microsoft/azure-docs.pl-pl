---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 155c88ec4766391f70701b17038b915c399d8b0c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77371708"
---
## <a name="sign-in-to-luis-portal"></a>Zaloguj się do portalu LUIS

Nowy użytkownik do LUIS musi wykonać następującą procedurę:

1. Zaloguj się do [portalu Luis (wersja zapoznawcza)](https://preview.luis.ai), wybierz swój kraj i zaakceptuj warunki użytkowania. Jeśli zamiast tego zobaczysz **Moje aplikacje** , zasób Luis już istnieje i należy przejść do sekcji Tworzenie aplikacji.

1. Wybierz pozycję **Utwórz zasób platformy Azure** , a następnie wybierz pozycję **Utwórz zasób autorstwa, aby przeprowadzić migrację aplikacji do programu.**

    ![Wybierz typ zasobu Language Understanding tworzenia](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Wprowadź szczegółowe informacje o zasobie.

    ![Utwórz zasób tworzenia](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Podczas **tworzenia nowego zasobu tworzenia**należy podać następujące informacje:

    * **Nazwa zasobu** — wybrana przez Ciebie Nazwa niestandardowa, używana jako część adresu URL dla zapytań dotyczących tworzenia i przewidywania punktów końcowych.
    * **Dzierżawca** — dzierżawa, z którą skojarzona jest subskrypcja platformy Azure.
    * **Nazwa subskrypcji** — subskrypcja, za którą zostanie naliczona stawka dla zasobu.
    * **Grupa zasobów** — wybrana lub utworzona nazwa niestandardowej grupy zasobów. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.
    * **Lokalizacja** — wybór lokalizacji zależy od wybranej **grupy zasobów** .
    * **Warstwa cenowa** — warstwa cenowa określa maksymalną liczbę transakcji na sekundę i miesiąc.

1. Zostanie wyświetlone podsumowanie zasobu, który ma zostać utworzony. Wybierz pozycję **Dalej**.

    ![Utwórz zasób tworzenia](../media/sign-in/sign-in-confirm-key-selection.png)

1. Potwierdź, wybierając pozycję **Kontynuuj**.

    ![Utwórz zasób tworzenia](../media/sign-in/sign-in-confirm-continue.png)