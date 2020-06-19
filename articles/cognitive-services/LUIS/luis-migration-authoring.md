---
title: Migrowanie do zasobu platformy Azure w celu tworzenia 2
titleSuffix: Azure Cognitive Services
description: Przeprowadź migrację do klucza zasobu tworzenia platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: 5f0778436db7bd8c3a09e3ba346d8a9a6c4af454
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983812"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrowanie do klucza tworzenia zasobów platformy Azure

Language Understanding (LUIS) uwierzytelnianie autorstwa zostało zmienione z konta e-mail na zasób platformy Azure. Gdy obecnie nie jest to wymagane, przełączenie na zasób platformy Azure zostanie wymuszone w przyszłości.


## <a name="what-is-migration"></a>Co to jest migracja?

Migracja to proces zmiany uwierzytelniania autorstwa z konta e-mail na zasób platformy Azure. Twoje konto zostanie połączone z subskrypcją platformy Azure i zasobem tworzenia platformy Azure po przeprowadzeniu migracji. **Wszyscy użytkownicy LUIS (właściciele lub współpracownicy) będą ostatecznie musieli przeprowadzić migrację.** Należy przeprowadzić migrację z portalu LUIS. Jeśli utworzysz klucze tworzenia, na przykład w interfejsie wiersza polecenia LUIS, nadal będzie konieczne ukończenie procesu migracji w portalu LUIS. Nadal możesz mieć współautorów aplikacji po migracji, ale zostaną one dodane na poziomie zasobów platformy Azure, a nie na poziomie aplikacji.

> [!Note]
> Przed rozpoczęciem migracji Współautorzy są znani jako _współpracownicy_ na poziomie aplikacji Luis. Po przeprowadzeniu migracji rola _współautora_ systemu Azure jest używana na poziomie zasobów platformy Azure.

## <a name="note-before-you-migrate"></a>Uwaga przed przeprowadzeniem migracji

* Migracja jest procesem **jednokierunkowym** . Nie można wrócić po migracji.
* Aplikacje zostaną **automatycznie zmigrowane** z Ciebie, jeśli jesteś **właścicielem** aplikacji.
* Właściciel nie może wybrać podzestawu aplikacji do migracji, a proces nie jest odwracalny.
* Po **przeprowadzeniu migracji właściciela**aplikacje **znikną ze stron współpracownika** .
* Właściciele są monitowani o wysłanie wiadomości e-mail do współpracowników, aby poinformować ich o migracji.
* Aplikacje **nie będą migrowane** z Ciebie, jeśli jesteś **współpracownikiem** aplikacji.
* Właściciel nie ma żadnego sposobu, aby znać jego współpracowników.
* **Migracja** nie zbiera automatycznie współpracowników i przenosi je ani nie dodaje do zasobu tworzenia platformy Azure. Właściciel aplikacji to ten, który musi wykonać ten krok po migracji. Ten krok wymaga [uprawnień do zasobu tworzenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Po przypisaniu do zasobu platformy Azure **współpracownicy będą musieli przeprowadzić migrację w celu uzyskania dostępu do aplikacji**. W przeciwnym razie nie będą mieli dostępu do tworzenia aplikacji.
* Nie można dodać zmigrowanego użytkownika jako współpracownika aplikacji.
* Jeśli **masz własne klucze przewidywania przypisane do aplikacji należących do innych użytkowników**, spowoduje to **zablokowanie migracji** zarówno dla właściciela, jak i współpracowników. Zapoznaj się z poniższymi zaleceniami.

> [!Note]
> Jeśli konieczne jest utworzenie zasobu predykcyjnego w środowisku uruchomieniowym, istnieje [osobny proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) tworzenia go.

## <a name="migration-prerequisites"></a>Wymagania wstępne dotyczące migracji

* Musisz być skojarzona z prawidłową subskrypcją platformy Azure. Poproszenie administratora dzierżawy o dodanie Cię do subskrypcji lub możesz utworzyć bezpłatne [konto.](https://azure.microsoft.com/free/)
* Musisz utworzyć zasób tworzenia LUIS platformy Azure z poziomu portalu LUIS lub z poziomu Azure Portal. Tworzenie zasobu tworzenia z portalu LUIS jest częścią przepływu migracji, który został omówiony w następnej sekcji.
* Jeśli jesteś **współpracownikem w aplikacjach**, aplikacje nie zostaną automatycznie zmigrowane. Zalecane jest **wykonanie kopii zapasowej tych aplikacji** przez ich wyeksportowanie lub użycie [interfejsu API eksportowania](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Możesz zaimportować aplikację z powrotem do LUIS po migracji. Proces importowania tworzy nową aplikację z nowym IDENTYFIKATORem aplikacji, dla którego jesteś właścicielem.
* Jeśli jesteś **właścicielem aplikacji**, nie musisz eksportować aplikacji, ponieważ zostaną one automatycznie zmigrowane. Zalecane jest **zapisanie listy collaborator's każdej aplikacji.** Szablon wiadomości e-mail, który ma tę listę, jest dostępny w ramach procesu migracji.


|Portal|Przeznaczenie|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Tworzenie zasobów przewidywania i tworzenia.<br>* Przypisz współautorów do zasobów.|
|[LUIS](https://www.luis.ai)|* Przeprowadź migrację do nowych zasobów tworzenia.<br>* Utwórz nowy zasób tworzenia w ramach przepływu migracji.<br>* Przypisz lub Cofnij przypisanie zasobów prognozowania i tworzenia do aplikacji z **zarządzania > stronie zasobów platformy Azure** . <br>* Przenieś aplikacje z jednego zasobu tworzenia do innego.  |

> [!Note]
> **Tworzenie aplikacji Luis jest bezpłatne**, wskazywane przez `F0` warstwę. Dowiedz się [więcej o warstwach cenowych](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Kroki migracji

1. W portalu LUIS, nad którym pracujesz, możesz rozpocząć proces migracji z ikony **platformy Azure** na górnym pasku narzędzi.

   > [!div class="mx-imgBorder"]
   > ![Ikona migracji](./media/migrate-authoring-key/migration-button.png)

2. Okno podręczne migracji umożliwia kontynuowanie migracji lub późniejszej migracji. Wybierz pozycję **Migruj teraz**.

   > [!div class="mx-imgBorder"]
   > ![Pierwsze okno podręczne w procesie migracji, wybierz pozycję Migruj teraz.](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Opcjonalnie, Jeśli którakolwiek z aplikacji ma współpracowników, zostanie wyświetlony monit o **wysłanie wiadomości e-mail** z informacją o migracji. Jest to opcjonalny krok.

   Dla każdego współpracownika i aplikacji zostanie otwarta domyślna aplikacja poczty e-mail z lekko sformatowaną wiadomością e-mail. Wiadomość e-mail można edytować przed jej wysłaniem. Szablon wiadomości e-mail zawiera dokładny identyfikator aplikacji i nazwę aplikacji.
   
   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources
      
   Thank you
   ```
   
   > [!Note]
   > Po przeprowadzeniu migracji konta na platformę Azure aplikacje nie będą już dostępne dla współpracowników.

4. Opcjonalnie, jeśli jesteś współpracownikem dowolnej aplikacji, zostanie wyświetlony monit o **wyeksportowanie kopii aplikacji** przez wybranie tej opcji podczas przepływu migracji. Po wybraniu opcji znajdziesz stronę poniżej, w której klikniesz przycisk Pobierz po lewej stronie, aby wyeksportować wybrane aplikacje. Te aplikacje można zaimportować z powrotem po migracji, ponieważ nie zostaną automatycznie zmigrowane. Jest to opcjonalny krok.

   > [!div class="mx-imgBorder"]
   > ![Monituj o wyeksportowanie aplikacji.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Można utworzyć nowy zasób tworzenia LUIS lub przeprowadzić migrację do istniejącego zasobu tworzenia, jeśli został on już utworzony na platformie Azure. Wybierz odpowiednią opcję, wybierając odpowiedni przycisk poniżej.

   > [!div class="mx-imgBorder"]
   > ![Utwórz zasób tworzenia](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Tworzenie nowego zasobu tworzenia z LUIS do migracji

Jeśli chcesz utworzyć nowy zasób tworzenia, wybierz opcję **Utwórz nowy zasób tworzenia** i podaj poniższe informacje w następnym oknie.

> [!div class="mx-imgBorder"]
> ![Utwórz zasób tworzenia](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Nazwa zasobu** — wybrana przez Ciebie Nazwa niestandardowa, używana jako część adresu URL dla zapytań dotyczących tworzenia i przewidywania punktów końcowych.
* **Nazwa subskrypcji** — subskrypcja, która zostanie skojarzona z zasobem. Jeśli masz więcej niż jedną subskrypcję, która należy do dzierżawy, wybierz żądaną wartość z listy rozwijanej.
* **Grupa zasobów** — niestandardowa nazwa grupy zasobów wybrana z listy rozwijanej. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.
* **Dzierżawca** — dzierżawa, z którą skojarzona jest subskrypcja platformy Azure. Ta opcja jest domyślnie ustawiona na wybraną dzierżawę. Możesz przełączyć dzierżawców, wybierając z prawej stronie awatar, który zawiera inicjały.

Po wprowadzeniu powyższych informacji wybierz pozycję **gotowe**.

Zwróć uwagę na to, że możesz mieć 10 bezpłatnych zasobów tworzenia na region na subskrypcję. Jeśli subskrypcja zawiera więcej niż 10 zasobów autorstwa w tym samym regionie, nie będzie można utworzyć nowego.

* Po utworzeniu zasobu tworzenia zostanie wyświetlony komunikat o powodzeniu. Wybierz pozycję **Zamknij** , aby zamknąć okno podręczne.

  > [!div class="mx-imgBorder"]
  > ![Zasób tworzenia został pomyślnie utworzony.](./media/migrate-authoring-key/migration-success-2.png)

### <a name="use-existing-authoring-resource-to-migrate"></a>Użyj istniejącego zasobu tworzenia do migracji

Jeśli subskrypcja jest już skojarzona z LUIS tworzenie zasobu platformy Azure lub jeśli utworzono ją z poziomu Azure Portal i chcesz przeprowadzić migrację do niej, zamiast tworzyć nowy zasób, wybierz opcję **Użyj istniejącego zasobu tworzenia** i podaj poniższe informacje w następnym oknie.

> [!div class="mx-imgBorder"]
>![Utwórz zasób tworzenia](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Dzierżawca** — dzierżawa, z którą skojarzona jest subskrypcja platformy Azure. Ta opcja jest domyślnie ustawiona na wybraną dzierżawę.
* **Nazwa subskrypcji** — subskrypcja, która zostanie skojarzona z zasobem. Jeśli masz więcej niż jedną subskrypcję, która należy do dzierżawy, wybierz ją z listy rozwijanej.
* **Nazwa zasobu** — wybierz zasób autorstwa, do którego chcesz przeprowadzić migrację.

> [!Note]
> Jeśli zasób tworzenia nie jest widoczny na liście rozwijanej, upewnij się, że został on utworzony w **odpowiedniej lokalizacji** zgodnie z zapisywanym portalem Luis. Upewnij się również, że utworzony element jest rzeczywiście **zasobem tworzenia** i nie jest **zasobem przewidywania**.


* Sprawdź poprawność nazwy zasobu tworzenia i kliknij przycisk **Migruj teraz** .

 > [!div class="mx-imgBorder"]
 > ![Utwórz zasób tworzenia](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

* Wyświetlany jest komunikat o powodzeniu. Wybierz pozycję **Zamknij** , aby zamknąć okno podręczne.

 > [!div class="mx-imgBorder"]
 > ![Zasób tworzenia został pomyślnie utworzony.](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Używanie aplikacji po migracji

* Po zakończeniu migracji wszystkie aplikacje LUIS, których jesteś właścicielem, zostaną teraz przypisane do pojedynczego zasobu tworzenia LUIS.
* Lista **Moje aplikacje** zawiera aplikacje migrowane do nowego zasobu tworzenia.
* Przed uzyskaniem dostępu do aplikacji wybierz zasób subskrypcja i LUIS tworzenie, aby wyświetlić aplikacje, które możesz utworzyć.

 > [!div class="mx-imgBorder"]
 > ![Wybierz pozycję subskrypcja i LUIS tworzenie zasobów, aby wyświetlić aplikacje, które mogą tworzyć autor.](./media/create-app-in-portal-select-subscription-luis-resource.png)

* Nie musisz znać klucza zasobu tworzenia, aby kontynuować edytowanie aplikacji w portalu LUIS.
* Jeśli planujesz edytować aplikacje programowo, będziesz potrzebować wartości klucza tworzenia. Te wartości są wyświetlane na stronie **zarządzaj > zasobów platformy Azure** w portalu Luis i są również dostępne na stronie Azure Portal na **kluczach** zasobów. Możesz również utworzyć więcej zasobów autorstwa i przypisać je z tej samej strony.

 > [!div class="mx-imgBorder"]
 > ![Zarządzanie zasobem tworzenia.](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="add-contributors-to-authoring-resources"></a>Dodawanie współautorów do zasobów tworzenia

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Dowiedz się [, jak dodać współautorów](luis-how-to-collaborate.md) do zasobu tworzenia. Współautorzy będą mieli dostęp do wszystkich aplikacji w ramach tego zasobu.

Współautorzy można dodać do zasobu tworzenia z _Azure Portal_na stronie **Access Control (IAM)** dla tego zasobu. Aby uzyskać więcej informacji, zobacz [Dodawanie dostępu współautora](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Jeśli właściciel aplikacji LUIS został zmigrowany i dodany do współpracownika jako współautor w ramach zasobu platformy Azure, współpracownik nadal nie będzie miał dostępu do aplikacji, o ile nie zostanie również zmigrowany.

## <a name="luis-portal-migration-reminders"></a>Przypomnienia dotyczące migracji portalu LUIS

[Portal Luis](https://www.luis.ai) zawiera proces migracji.

Zostanie wyświetlony monit o przeprowadzenie migracji, jeśli:
* Masz aplikacje w systemie uwierzytelniania poczty e-mail na potrzeby tworzenia.
* I jesteś właścicielem aplikacji.

Co tydzień zostanie wyświetlony monit o migrację aplikacji. To okno można anulować bez migrowania. Jeśli chcesz przeprowadzić migrację przed następnym zaplanowanym okresem, możesz rozpocząć proces migracji z ikony **platformy Azure** na górnym pasku narzędzi portalu Luis.

Można opóźnić proces migracji, anulując okno. Użytkownik jest okresowo monitowany o migrację do momentu przeprowadzenia migracji lub przekazanie ostatecznego terminu migracji. Proces migracji można rozpocząć od ikony blokady górnego paska nawigacyjnego.

## <a name="prediction-resources-blocking-migration"></a>Zablokuj migrację zasobów predykcyjnych
Migracja ma negatywny wpływ na wszystkie aplikacje środowiska uruchomieniowego. Podczas migracji wszystkie współpracownicy są usuwani z aplikacji, a użytkownik jest usuwany jako współpracownik z innych aplikacji. Ten proces oznacza, że klucze przypisane przez współpracownika również zostaną usunięte, co może spowodować uszkodzenie aplikacji w środowisku produkcyjnym. Jest to powód blokowania migracji do momentu ręcznego usunięcia przypisanych do nich współpracowników lub kluczy.

### <a name="when-does-prediction-resources-block-migration"></a>Kiedy usługa przewidywania zasobów blokuje migrację?
* Migracja jest blokowana, jeśli przypisano zasoby przewidywania/wykonywania w aplikacjach, które nie są właścicielami.
* Migracja zostanie zablokowana, jeśli inni użytkownicy przypisują zasoby do prognozowania/wykonywania do posiadanych aplikacji.

### <a name="recommended-steps-to-do-if-you-are-the-owner-of-the-app"></a>Zalecane czynności do wykonania, jeśli jesteś właścicielem aplikacji
Jeśli jesteś właścicielem niektórych aplikacji i masz współpracowników przypisanych do tych aplikacji klucz przewidywania/czasu wykonywania, podczas migrowania zostanie wyświetlona lista identyfikatorów aplikacji z przypisanymi do nich kluczami predykcyjnymi należącymi do innych użytkowników.

Zalecane jest:
* Powiadamiaj współpracowników o migracji.
* Usuń wszystkich współpracowników z aplikacji pokazanych w błędzie.
* W przypadku ręcznego usuwania współpracowników należy przeprowadzić proces migracji, który powinien zostać zakończony pomyślnie.
* Przypisz współpracowników jako współautorów do nowego zasobu tworzenia.
* Współpracownicy są do migracji i ponownego przypisania zasobów przewidywania do aplikacji.
Zwróć uwagę, że spowoduje to chwilowe przerwanie aplikacji do momentu ponownego przypisania zasobów przewidywania.

Innym rozwiązaniem jest to, że przed migracją właściciela, współpracownicy mogą dodawać właścicieli aplikacji jako współautorów w swoich subskrypcjach platformy Azure z Azure Portal. Spowoduje to przyznanie właścicielowi dostępu do zasobów przewidywania środowiska uruchomieniowego. Jeśli właściciel zostanie zmigrowany przy użyciu nowej subskrypcji, do której zostali dodani (która zostanie znaleziona w ramach nowej dzierżawy), to nie tylko odblokowuje proces migracji zarówno dla współpracownika, jak i właściciela aplikacji, ale zezwoli na bezproblemowe Migrowanie aplikacji z kluczem prognozowania, który jest nadal przypisany do nich i nie przerywa aplikacji.


### <a name="recommended-steps-to-do-if-you-are-a-collaborator-on-an-app"></a>Zalecane kroki, które należy wykonać, jeśli jesteś współpracownikiem w aplikacji
W przypadku współpracy z aplikacjami i przypisywania do tych aplikacji klucza przewidywania/środowiska uruchomieniowego jest wyświetlany błąd podczas migracji, który wyświetla listę identyfikatorów aplikacji i ścieżek kluczy, które blokują migrację.

Zalecane jest:
* Eksportuj aplikacje jako kopie zapasowe. Jest to opcjonalne krok w procesie migracji.
* Cofnij przypisanie zasobów przewidywania z strony **zarządzaj > zasoby platformy Azure** .
* Przeprowadzenie procesu migracji.
* Importuj aplikacje z tyłu po migracji.
* Ponownie Przypisz klucze prognoz do aplikacji **zarządzanie > zasobów platformy Azure** .

> [!Note]
> Po zaimportowaniu aplikacji po przeprowadzeniu migracji będą one mieć różne identyfikatory aplikacji i są inne niż te, które są trafień w środowisku produkcyjnym. Będziesz teraz właścicielem tych aplikacji.

## <a name="troubleshooting-migration-process"></a>Rozwiązywanie problemów z procesem migracji

Gdy próbujesz przeprowadzić migrację, ale nie możesz znaleźć subskrypcji platformy Azure na liście rozwijanej:
* Upewnij się, że masz prawidłową subskrypcję platformy Azure, która jest uprawniona do tworzenia zasobów Cognitive Services. Przejdź do [Azure Portal](https://ms.portal.azure.com) i sprawdź stan subskrypcji. Jeśli go nie masz, [Utwórz bezpłatną wersję próbną](https://azure.microsoft.com/free/).
* Upewnij się, że jesteś w odpowiedniej dzierżawie skojarzonej z prawidłową subskrypcją. Dzierżawy można przełączyć z dalszych z lewej awatara na pasku narzędzi poniżej: ![ przełączanie dzierżawców.](./media/migrate-authoring-key/switch-user-tenant-2.png)

 Jeśli masz już istniejący zasób tworzenia, ale nie można go znaleźć podczas wybierania opcji "Użyj istniejącego zasobu tworzenia":
* Zasób został prawdopodobnie utworzony w innej lokalizacji niż Portal, w którym się logujesz. Sprawdź [regiony tworzenia Luis i portale](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)
* Zamiast tego Utwórz nowy zasób z portalu LUIS

W przypadku wybrania opcji "Utwórz nowy zasób tworzenia" i migracji nie powiodła się. komunikat o błędzie "nie można pobrać informacji o platformie Azure użytkownika, spróbuj ponownie później".
* Twoja subskrypcja może mieć 10 lub więcej zasobów autorstwa na region na subskrypcję. W takim przypadku nie będzie można utworzyć nowego zasobu tworzenia.
* Przeprowadź migrację, wybierając opcję "Użyj istniejącego zasobu autorstwa" i wybierz jeden z istniejących zasobów w ramach subskrypcji.

Jeśli widzisz Poniższy błąd, sprawdź [zalecane kroki, aby zrobić, jeśli jesteś właścicielem sekcji aplikacji] ![ migracja nie powiedzie się dla właścicieli](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Jeśli widzisz Poniższy błąd, sprawdź [zalecane kroki, aby zrobić, jeśli jesteś współpracownikiem w sekcji aplikacji] ![ migracja nie powiedzie się dla współpracowników](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [pojęciami](luis-concept-keys.md) dotyczącymi tworzenia i kluczy czasu wykonywania
* Przejrzyj [sposób przypisywania kluczy](luis-how-to-azure-subscription.md) i Dodawanie [współautorów](luis-how-to-collaborate.md)
