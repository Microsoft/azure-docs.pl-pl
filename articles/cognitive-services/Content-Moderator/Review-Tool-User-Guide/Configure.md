---
title: Konfigurowanie ustawień narzędzia do przeglądu — Content Moderator
titleSuffix: Azure Cognitive Services
description: Skorzystaj z narzędzia do przeglądu, aby skonfigurować lub pobrać zespół, znaczniki, łączniki, przepływy pracy i poświadczenia dla Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 8812fd1e6c1efb2aa44c77573bc4b8f1c099834d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92912025"
---
# <a name="configure-the-review-tool"></a>Konfigurowanie narzędzia do przeglądu

[Narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com) ma kilka ważnych funkcji, do których można uzyskać dostęp za pomocą menu **Ustawienia** na pulpicie nawigacyjnym.

![Menu Content Moderator przegląd zbytu ustawień](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Zarządzanie zespołem i podzespołów

Karta **zespół** umożliwia zarządzanie zespołami i &mdash; grupami podzespołów użytkowników, którzy mogą otrzymywać powiadomienia o rozpoczęciu niektórych [recenzji ludzkich](../review-api.md#reviews) . Możesz mieć tylko jeden zespół (tworzony podczas tworzenia konta za pomocą narzędzia do przeglądu), ale można utworzyć wiele podzespołów. Administrator zespołu może zapraszać członków, ustawiać ich uprawnienia i przypisywać je do różnych zespołów.

![Przegląd ustawień zespołu narzędzi](images/settings-2-team.png)

Podzespoły są przydatne do tworzenia zespołów lub zespołów eskalacji przeznaczonych do przeglądania określonych kategorii zawartości. Na przykład można wysłać zawartość dla dorosłych do osobnego zespołu w celu dalszej kontroli.

W tej sekcji opisano sposób tworzenia podzespołów i szybkiego przypisywania przeglądów na bieżąco. Za pomocą [przepływów pracy](workflows.md) można jednak przypisywać przeglądy na podstawie określonych kryteriów.

### <a name="create-a-subteam"></a>Tworzenie podzespołu

Przejdź do sekcji **podzespołów** , a następnie kliknij pozycję **Dodaj podzespół**. W oknie dialogowym wprowadź nazwę swojego zespołu, a następnie kliknij przycisk **Zapisz**.

![Nazwa podzespołu](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Zapraszanie członków zespołu

Nie można przypisać kogoś do zespołu, jeśli nie jest jeszcze członkiem zespołu domyślnego, więc musisz najpierw dodać recenzentów do zespołu domyślnego. Na karcie **zespół** kliknij pozycję **Zaproś** .

![Zapraszanie użytkowników](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Przypisywanie członków zespołu do podzespołów

Kliknij przycisk **Dodaj członka** , aby przypisać członków z domyślnego zespołu do co najmniej jednego podzespołu. Istniejących użytkowników można dodawać tylko do podzespołu. Aby dodać nowych użytkowników, którzy nie znajdują się w narzędziu do recenzowania, zaproś ich przy użyciu przycisku "Zaproś" na stronie ustawień zespołu.

![Przypisywanie członków zespołu](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Przypisywanie przeglądów do podzespołów

Po utworzeniu podzespołów i przypisanych elementów członkowskich można zacząć przypisywać [przeglądy](../review-api.md#reviews) zawartości do tych podzespołów. Odbywa się to na karcie **Przegląd** w witrynie.
Aby przypisać zawartość do podzespołu, kliknij wielokropek w prawym górnym rogu, wybierz pozycję **Przenieś do** i wybierz podzespół.

![Przypisz przegląd obrazu do podzespołu](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Przełączanie między podzespołów

Jeśli jesteś członkiem więcej niż jednego podzespołu, możesz przełączać się między nimi, aby zmienić, które przeglądy zawartości są wyświetlane. Na karcie **Recenzja** wybierz menu rozwijane **domyślne** z etykietą i wybierz pozycję **wybierz podzespół**. Możesz przeglądać przeglądy zawartości dla różnych zespołów, ale tylko te, których jesteś członkiem.

![Przełączanie między podzespołów](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tagi

Na karcie **Tagi** można zdefiniować niestandardowe znaczniki moderowania oprócz dwóch domyślnych tagów moderowania &mdash; **isdorosły** (**a**) i **isracy** (**r**). Gdy tworzysz tag niestandardowy, będzie on dostępny w przeglądach obok znaczników domyślnych. Możesz zmienić, które Tagi będą widoczne w przeglądach, przełączając ich ustawienia widoczności.

![Widok tagów, w tym pola wyboru "jest widoczne"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Tworzenie tagów niestandardowych

Aby utworzyć nowy tag, należy wprowadzić krótki kod, nazwę i opis w odpowiednich polach.

- **Krótki kod**: wprowadź dwuliterowy kod dla tagu. Przykład: **CB**
- **Nazwa**: wprowadź krótką i opisową nazwę tagu małymi literami bez spacji. Przykład: **isbullying**.
- **Opis**: (opcjonalnie) wprowadź opis rodzaju zawartości, do której odwołuje się tag. Przykład: **sceny lub wystąpienia elementu cybernetycznymi bullying**.

Kliknij przycisk **Dodaj** , aby dodać tag, a następnie kliknij przycisk **Zapisz** po zakończeniu tworzenia tagów.

![Okno dialogowe Tworzenie nowego tagu narzędzia do przeglądania](images/settings-3-tags.png)

### <a name="delete-tags"></a>Usuń Tagi

Tagi niestandardowe można usunąć, wybierając ikonę kosza obok pozycji na liście Tagi, ale nie można usunąć tagów domyślnych.

## <a name="connectors"></a>Łączniki

Karta **Łączniki** umożliwia zarządzanie łącznikami, które są wtyczkami specyficznymi dla usług, które mogą przetwarzać zawartość na różne sposoby w ramach [przepływów pracy](../review-api.md#workflows)zawartości.

Domyślnym łącznikiem podczas tworzenia przepływu pracy jest łącznik Content Moderator, który może oznaczać zawartość jako dorosłą lub **erotycznej**, znaleźć **niepełnoletni** itd. Można jednak użyć innych łączników, wymienionych tutaj, jeśli masz poświadczenia dla odpowiednich usług (aby użyć łącznika czołowego, na przykład musisz uzyskać klucz subskrypcji [frontu](../../face/overview.md) ).

[Narzędzie do przeglądu](./human-in-the-loop.md) obejmuje następujące łączniki:

- Emocja
- Rozpoznawanie twarzy
- Usługa w chmurze PhotoDNA
- Analiza tekstu

### <a name="add-a-connector"></a>Dodawanie łącznika

Aby dodać łącznik (i udostępnić go do użycia w [przepływach pracy](../review-api.md#workflows)zawartości), wybierz odpowiedni przycisk **Połącz** . W następnym oknie dialogowym wprowadź klucz subskrypcji dla tej usługi. Gdy wszystko będzie gotowe, nowy łącznik powinien pojawić się u góry strony.

![Ustawienia Content Moderator łączników](images/settings-4-connectors.png)

## <a name="workflows"></a>Przepływy pracy

Karta **przepływy pracy** umożliwia zarządzanie [przepływami pracy](../review-api.md#workflows). Przepływy pracy są filtrami opartymi na chmurze dla zawartości i pracują z łącznikami, aby sortować zawartość na różne sposoby i podejmować odpowiednie działania. Tutaj możesz definiować, edytować i testować przepływy pracy. Zobacz [Definiowanie i używanie przepływów pracy,](Workflows.md) Aby uzyskać wskazówki dotyczące sposobu wykonania tej czynności.

![Content Moderator ustawienia przepływu pracy](images/settings-5-workflows.png)

## <a name="credentials"></a>Poświadczenia

Karta **poświadczenia** zapewnia szybki dostęp do klucza subskrypcji Content moderator, do którego należy uzyskać dostęp do dowolnych usług moderowania z wywołania REST lub zestawu SDK klienta.

![Poświadczenia Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Używanie zewnętrznych poświadczeń dla przepływów pracy

[Narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com) generuje bezpłatny klucz wersji próbnej dla usług Azure Content moderator Services podczas rejestracji, ale można również skonfigurować go do korzystania z istniejącego klucza z konta platformy Azure. Jest to zalecane w scenariuszach o dużej skali, ponieważ bezpłatne klucze próbne mają ścisłe limity użycia ([ceny i limity](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Jeśli utworzono [zasób Content moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) na platformie Azure, przejdź do niego w Azure Portal i wybierz blok **klucze** . Skopiuj jeden z kluczy.

![Klucze Content Moderator w Azure Portal](images/credentials-azure-portal-keys.PNG)

Na karcie **poświadczenia** [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com)przejdź do okienka **Ustawienia przepływu pracy** , wybierz pozycję **Edytuj** i Wklej klucz do pola **OCP-APIM-Subscription-Key** . Teraz przepływy pracy, które wywołują interfejsy API moderowania, będą używać poświadczeń platformy Azure.

> [!NOTE]
> Pozostałe dwa pola w okienku **Ustawienia przepływu pracy** dotyczą niestandardowych terminów i list obrazów. Aby dowiedzieć się więcej, zobacz przewodniki dotyczące [niestandardowych terminów](../try-terms-list-api.md) lub [obrazów niestandardowych](../try-image-list-api.md) .

### <a name="use-your-azure-account-with-the-review-apis"></a>Korzystanie z Twojego konta platformy Azure z użyciem interfejsów API przeglądu

Aby użyć klucza platformy Azure z interfejsami API przeglądu, należy pobrać identyfikator zasobu. Przejdź do zasobu Content Moderator w Azure Portal i wybierz blok **Właściwości** . Skopiuj wartość Identyfikator zasobu i wklej ją do pola **identyfikatorów zasobów listy dozwolonych** na karcie **poświadczenia** narzędzia do przeglądu.

![Content Moderator identyfikator zasobu w Azure Portal](images/credentials-azure-portal-resourceid.PNG)

Jeśli klucz subskrypcji został wprowadzony w obu miejscach, klucz wersji próbnej, który jest dostarczany z Twoim kontem narzędzia do przeglądu, nie zostanie użyty, ale pozostanie dostępny.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [przewodnikiem Szybki Start narzędzia](../quick-start.md) do przeglądu, aby zacząć korzystać z narzędzia do przeglądu w scenariuszach moderowania zawartości.