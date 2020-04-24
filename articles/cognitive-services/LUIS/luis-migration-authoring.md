---
title: Migrowanie do zasobu platformy Azure w celu tworzenia
titleSuffix: Azure Cognitive Services
description: Przeprowadź migrację do klucza zasobu tworzenia platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: 679073715588a4a81e69e3e7ba2d18341b1bab4b
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096626"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrowanie do klucza tworzenia zasobów platformy Azure

Language Understanding (LUIS) uwierzytelnianie autorstwa zostało zmienione z konta e-mail na zasób platformy Azure. Gdy obecnie nie jest to wymagane, przełączenie na zasób platformy Azure zostanie wymuszone w przyszłości.

## <a name="why-migrate"></a>Dlaczego warto przeprowadzić migrację?

Przy użyciu zasobu platformy Azure do tworzenia można jako właściciel zasobu kontrolować dostęp do tworzenia. Można utworzyć i nazwać zasoby tworzenia do zarządzania różnymi grupami autorów.

Na przykład jesteś właścicielem aplikacji 2 LUIS i masz różnych członków, którzy są współpracownikami w każdej aplikacji. Można utworzyć dwa różne zasoby tworzenia i przypisać każdą aplikację do każdego oddzielnego zasobu. Następnie przypisz każdego członka jako współautora do odpowiedniego zasobu tworzenia, w zależności od tego, która aplikacja jest w nim współpracować. Zasób tworzenia platformy Azure kontroluje autoryzację.

> [!Note]
> Przed rozpoczęciem migracji Współautorzy są znani jako _współpracownicy_ na poziomie aplikacji Luis. Po przeprowadzeniu migracji rola _współautora_ systemu Azure jest używana na poziomie zasobów platformy Azure.

## <a name="what-is-migrating"></a>Co to jest migracja?

Migracja obejmuje:

* Wszyscy użytkownicy LUIS, właściciele i współautorzy.
* **Wszystkie** aplikacje.
* Migracja **jednokierunkowa** .

Właściciel nie może wybrać podzestawu aplikacji do migracji, a proces nie jest odwracalny.

Migracja nie jest:

* Proces, który zbiera współpracowników i automatycznie przenosi lub dodaje do zasobu tworzenia platformy Azure. Jako właściciel aplikacji musisz wykonać ten krok. Ten krok wymaga uprawnień do odpowiedniego zasobu.
* Proces tworzenia i przypisywania zasobu środowiska uruchomieniowego predykcyjnego. Jeśli potrzebujesz zasobu przewidywania środowiska uruchomieniowego, który jest [oddzielnym procesem](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) i nie jest zmieniany.

## <a name="how-are-the-apps-migrating"></a>Jak są migrowane aplikacje?

[Portal Luis](https://www.luis.ai) zawiera proces migracji.

Zostanie wyświetlony monit o przeprowadzenie migracji, jeśli:

* Masz aplikacje w systemie uwierzytelniania poczty e-mail na potrzeby tworzenia.
* I jesteś właścicielem aplikacji.

Można opóźnić proces migracji, anulując okno. Użytkownik jest okresowo monitowany o migrację do momentu przeprowadzenia migracji lub przekazanie ostatecznego terminu migracji. Proces migracji można rozpocząć od ikony blokady górnego paska nawigacyjnego.

## <a name="migration-for-the-app-owner"></a>Migracja dla właściciela aplikacji

### <a name="before-you-migrate"></a>Przed przeprowadzeniem migracji

* **Wymagana**jest [subskrypcja platformy Azure](https://azure.microsoft.com/free/). Część procesu subskrypcji wymaga informacji dotyczących rozliczeń. Można jednak użyć warstwy cenowej bezpłatna (`F0`), jeśli używasz Luis.
* **Opcjonalnie możesz**utworzyć kopię zapasową aplikacji z listy aplikacji portalu Luis, eksportując każdą aplikację lub korzystając z [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)eksportowania.
* **Opcjonalnie**Zapisz listę collaborator's każdej aplikacji. Ta lista poczty e-mail jest świadczona jako część procesu migracji.


**Tworzenie aplikacji Luis jest bezpłatne**, wskazywane przez `F0` warstwę. Dowiedz się [więcej o warstwach cenowych](luis-limits.md#key-limits).

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się](https://azure.microsoft.com/free/).

### <a name="migration-steps"></a>Kroki migracji

Postępuj zgodnie z [tymi krokami migracji](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Po migracji

Po zakończeniu migracji wszystkie Twoje aplikacje LUIS są teraz przypisane do pojedynczego zasobu tworzenia LUIS.

Można utworzyć więcej zasobów autorstwa i przypisać je na stronie **zarządzaj > zasobów platformy Azure** w _portalu Luis_.

Współautorzy można dodać do zasobu tworzenia z _Azure Portal_na stronie **Access Control (IAM)** dla tego zasobu. Aby uzyskać więcej informacji, zobacz [Dodawanie dostępu współautora](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

|Portal|Przeznaczenie|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Tworzenie zasobów przewidywania i tworzenia.<br>* Przypisz współautorów.|
|[LUIS](https://www.luis.ai)|* Przeprowadź migrację do nowych zasobów tworzenia.<br>* Przypisz lub Cofnij przypisanie zasobów prognozowania i tworzenia do aplikacji z **zarządzania > stronie zasobów platformy Azure** .|

## <a name="migration-for-the-app-contributor"></a>Migracja dla współautora aplikacji

Każdy użytkownik programu LUIS musi przeprowadzić migrację, w tym współpracowników/współautorów. Współpracownik musi przeprowadzić migrację, aby miał dostęp do aplikacji.

> [!Note]
> Jeśli właściciel aplikacji LUIS został zmigrowany i dodany do współpracownika jako współautor w ramach zasobu platformy Azure, współpracownik nadal nie będzie miał dostępu do aplikacji, o ile nie zostanie również zmigrowany.

### <a name="before-the-app-is-migrated"></a>Przed przeprowadzeniem migracji aplikacji

Możesz wyeksportować aplikację, której jesteś współpracownikiem, a następnie zaimportować aplikację z powrotem do LUIS. Proces importowania tworzy nową aplikację z nowym IDENTYFIKATORem aplikacji, dla którego jesteś właścicielem.

### <a name="after-the-app-is-migrated"></a>Po migracji aplikacji

Właściciel aplikacji musi [dodać swój adres e-mail do zasobu tworzenia platformy Azure jako współpracownika](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource).

Po zakończeniu procesu migracji wszystkie posiadane aplikacje są dostępne na stronie **Moje aplikacje** w portalu Luis.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Rozwiązywanie problemów z procesem migracji w celu LUIS tworzenia

* Klucze tworzenia LUIS są widoczne tylko w portalu LUIS po zakończeniu procesu migracji. Jeśli utworzysz klucze tworzenia, na przykład w interfejsie wiersza polecenia LUIS, użytkownik nadal musi zakończyć proces migracji w portalu LUIS.
* Jeśli migrowany użytkownik doda niezmigrowanych użytkowników jako współautora w swoim zasobie platformy Azure, niezmigrowany użytkownik nie będzie miał dostępu do aplikacji, chyba że dokonają migracji.
* Jeśli Niemigrowany użytkownik nie jest właścicielem żadnej aplikacji, ale jest współpracownikiem innych aplikacji należących do innych użytkowników, a właściciele przeszedł proces migracji, ten użytkownik będzie musiał przeprowadzić migrację w celu uzyskania dostępu do aplikacji.
* Jeśli użytkownik, którego nie przeprowadzono migracji, dodał innego zmigrowanego użytkownika jako współpracownika do swojej aplikacji, wystąpi błąd, ponieważ nie będzie można dodać zmigrowanego użytkownika jako współpracownika do aplikacji. Niemigrowany użytkownik będzie musiał przejść przez proces migracji i utworzyć zasób platformy Azure i dodać zmigrowanego użytkownika jako współautora do tego zasobu.

Wystąpił błąd podczas procesu migracji, jeśli:
* Twoja subskrypcja nie upoważnia do tworzenia zasobów Cognitive Services
* Migracja ma negatywny wpływ na wszystkie aplikacje środowiska uruchomieniowego. Podczas migracji wszystkie współpracownicy są usuwani z aplikacji, a użytkownik jest usuwany jako współpracownik z innych aplikacji. Ten proces oznacza, że przydzielone klucze zostaną również usunięte. Migracja zostanie zablokowana, jeśli masz przypisane klucze w innych aplikacjach. Przed przeprowadzeniem migracji Usuń przypisany klucz. Jeśli wiesz, że przypisany klucz nie jest używany w środowisku uruchomieniowym, musisz go usunąć, aby móc postępować w ramach migracji.

Uzyskaj dostęp do listy zasobów platformy Azure aplikacji przy użyciu następującego formatu adresu URL:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>Następne kroki

* [Jak przeprowadzić migrację aplikacji do zasobu tworzenia](luis-migration-authoring-steps.md)
