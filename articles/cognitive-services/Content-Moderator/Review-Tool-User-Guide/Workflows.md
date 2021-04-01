---
title: Definiowanie i używanie przepływów pracy zawartości za pomocą narzędzia do przeglądu — Content Moderator
titleSuffix: Azure Cognitive Services
description: Za pomocą projektanta przepływów pracy usługi Azure Content Moderator można definiować niestandardowe przepływy pracy i progi na podstawie zasad dotyczących zawartości.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 6eb2a2d2762b60a12bb9a24b92e2edae4b846cd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96904146"
---
# <a name="define-and-use-moderation-workflows"></a>Definiowanie przepływów pracy moderowania i korzystanie z nich

W tym przewodniku dowiesz się, jak skonfigurować [przepływy pracy](../review-api.md#workflows) i korzystać z nich w witrynie internetowej [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com) . Przepływy pracy to dostosowane filtry oparte na chmurze, które umożliwiają efektywniejsze obsługiwanie zawartości. Przepływy pracy mogą łączyć się z różnymi usługami, aby filtrować zawartość na różne sposoby, a następnie podejmować odpowiednie działania. W tym przewodniku pokazano, jak używać łącznika Content Moderator (który jest włączony domyślnie) do filtrowania zawartości i konfigurowania weryfikacji ludzkich w typowym scenariuszu moderowania.

## <a name="create-a-new-workflow"></a>Utwórz nowy przepływ pracy

Przejdź do [Narzędzia do przeglądu Content moderator](https://contentmoderator.cognitive.microsoft.com/) i zaloguj się. Na karcie **Ustawienia** wybierz pozycję **przepływy pracy**.

![Ustawienie przepływów pracy](images/2-workflows-0.png)

Na następnym ekranie wybierz pozycję **Dodaj przepływ pracy**.

![Dodawanie przepływu pracy](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Przypisz nazwę i opis

Nadaj nazwę przepływowi pracy, wprowadź opis, a następnie wybierz, czy przepływ pracy będzie obsługiwał obrazy czy tekst.

![Nazwa i opis przepływu pracy](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Zdefiniuj kryteria oceny

Na następnym ekranie przejdź do sekcji **Jeśli** . W górnym menu rozwijanym wybierz pozycję **warunek**. Umożliwi to skonfigurowanie warunku działania przepływu pracy. Jeśli chcesz użyć wielu warunków, wybierz **kombinację** . 

Następnie wybierz łącznik. Ten przykład używa **Content moderator**. W zależności od wybranego łącznika uzyskasz różne opcje danych wyjściowych. Zobacz sekcję [Łączniki](./configure.md#connectors) przewodnika po ustawieniach narzędzia do przeglądu, aby dowiedzieć się, jak skonfigurować inne łączniki.

![Wybieranie łącznika przepływu pracy](images/image-workflow-connect-to.PNG)

Wybierz żądane dane wyjściowe do użycia i ustaw warunki, aby je sprawdzić.

![Zdefiniuj warunek przepływu pracy](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Zdefiniuj akcję

Przejdź do sekcji **then** , w której zostanie wybrana akcja. Poniższy przykład tworzy przegląd obrazu i przypisuje tag. Opcjonalnie można dodać alternatywną ścieżkę (inaczej) i ustawić dla niej akcję.

![Zdefiniuj akcję przepływu pracy](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Zapisz przepływ pracy

Zanotuj nazwę przepływu pracy; potrzebna jest nazwa, aby uruchomić zadanie moderowania z interfejsem API przepływu pracy (patrz poniżej). Na koniec Zapisz przepływ pracy, korzystając z przycisku **Zapisz** w górnej części strony.

## <a name="test-the-workflow"></a>Testowanie przepływu pracy

Po zdefiniowaniu niestandardowego przepływu pracy przetestuj go przy użyciu przykładowej zawartości. Przejdź do obszaru **przepływy pracy** i wybierz odpowiedni przycisk **Wykonaj przepływ pracy** .

![Test przepływu pracy](images/image-workflow-execute.PNG)

Zapisz ten [Przykładowy obraz](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) na dysku lokalnym. Następnie wybierz pozycję **Wybierz pliki** i Przekaż obraz do przepływu pracy.

![Moduł uruchamiający z nakładaniem się cytatem na obrazie](images/sample-text.jpg)

### <a name="track-progress"></a>Śledzenie postępu

Postęp przepływu pracy można wyświetlić w następnym oknie podręcznym.

![Śledź wykonywanie przepływu pracy](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Weryfikuj akcję przepływu pracy

Przejdź do karty **obraz** w obszarze **Przegląd** i sprawdź, czy jest tworzony nowo utworzony przegląd obrazu.

![Przegląd obrazów](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku zawarto informacje na temat konfigurowania przepływów pracy moderowania i korzystania z nich za pomocą [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com)Content Moderator. Następnie zapoznaj się z [przewodnikiem po konsoli interfejsu API](../try-review-api-workflow.md) , aby dowiedzieć się, jak tworzyć przepływy pracy programowo.
