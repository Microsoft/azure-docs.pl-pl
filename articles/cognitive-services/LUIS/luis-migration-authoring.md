---
title: Migrowanie do klucza tworzenia zasobów platformy Azure
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób migracji Language Understanding (LUIS) tworzenia uwierzytelniania z konta e-mail do zasobu platformy Azure.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 926b79e672c14249ec7c2b053dba7eb3a31443a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536051"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrowanie do klucza tworzenia zasobów platformy Azure

Language Understanding (LUIS) uwierzytelnianie autorstwa zostało zmienione z konta e-mail na zasób platformy Azure. Chociaż nie jest to wymagane, przełączenie na zasób platformy Azure zostanie wymuszone w przyszłości.


## <a name="what-is-migration"></a>Co to jest migracja?

Migracja to proces zmiany uwierzytelniania autorstwa z konta e-mail na zasób platformy Azure. Twoje konto zostanie połączone z subskrypcją platformy Azure i zasobem tworzenia platformy Azure po przeprowadzeniu migracji. *Wszyscy użytkownicy LUIS (właściciele lub współpracownicy) będą ostatecznie musieli przeprowadzić migrację.*

Należy przeprowadzić migrację z portalu LUIS. Jeśli tworzysz klucze tworzenia przy użyciu interfejsu wiersza polecenia LUIS, na przykład musisz ukończyć proces migracji w portalu LUIS. Po migracji można nadal korzystać z współautorów aplikacji, ale zostaną one dodane na poziomie zasobów platformy Azure, a nie na poziomie aplikacji.

> [!Note]
> Przed migracją Współautorzy są znani jako _współpracownicy_ na poziomie aplikacji Luis. Po migracji rola _współautora_ jest używana na poziomie zasobów platformy Azure.

## <a name="note-before-you-migrate"></a>Uwaga przed przeprowadzeniem migracji

* Należy przeprowadzić migrację środowiska tworzenia przez **lis, 2, 2020**. 
* Migracja jest procesem jednokierunkowym. Nie można wrócić po migracji.
* Aplikacje zostaną automatycznie zmigrowane z Ciebie, jeśli jesteś właścicielem aplikacji.
* Właściciel nie może wybrać podzestawu aplikacji do migracji, a proces nie jest odwracalny.
* Po migracji właściciela aplikacje znikną ze strony współpracownika.
* Właściciele są monitowani o wysłanie wiadomości e-mail do współpracowników, aby poinformować ich o migracji.
* Aplikacje nie będą migrowane z Ciebie, jeśli jesteś współpracownikiem aplikacji.
* Właściciel nie ma żadnego sposobu, aby wiedzieć, że współpracownicy zostali zmigrowani.
* Migracja nie zbiera automatycznie współpracowników i przenosi je ani nie dodaje do zasobu tworzenia platformy Azure. Właściciel aplikacji to ten, który musi wykonać ten krok po migracji. Ten krok wymaga [uprawnień do zasobu tworzenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Po przypisaniu współpracowników do zasobu platformy Azure muszą oni przeprowadzić migrację w celu uzyskania dostępu do aplikacji. W przeciwnym razie nie będą mieli dostępu do tworzenia aplikacji.
* Nie można dodać zmigrowanego użytkownika jako współpracownika aplikacji.
* Jeśli masz własne klucze przewidywania przypisane do aplikacji należących do innych użytkowników, spowoduje to zablokowanie migracji zarówno dla właściciela, jak i współpracowników. Zapoznaj się z zaleceniami w dalszej części tego artykułu.

> [!Note]
> Jeśli konieczne jest utworzenie zasobu predykcyjnego w środowisku uruchomieniowym, istnieje [osobny proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) tworzenia go.

## <a name="migration-prerequisites"></a>Wymagania wstępne dotyczące migracji

* Musisz być skojarzona z prawidłową subskrypcją platformy Azure. Poproszenie administratora dzierżawy o dodanie Cię do subskrypcji lub [zarejestrowanie się w celu uzyskania bezpłatnego konta](https://azure.microsoft.com/free/cognitive-services).
* Musisz utworzyć zasób tworzenia LUIS platformy Azure z poziomu portalu LUIS lub z poziomu Azure Portal. Tworzenie zasobu tworzenia z portalu LUIS jest częścią przepływu migracji, który został omówiony w następnej sekcji.
* Jeśli jesteś współpracownikem w aplikacjach, aplikacje nie będą automatycznie migrowane. Zalecamy utworzenie kopii zapasowej tych aplikacji przez ich wyeksportowanie lub użycie [interfejsu API eksportowania](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Możesz zaimportować aplikację z powrotem do LUIS po migracji. Proces importowania tworzy nową aplikację z nowym IDENTYFIKATORem aplikacji, dla którego jesteś właścicielem.
* Jeśli jesteś właścicielem aplikacji, nie musisz eksportować aplikacji, ponieważ zostaną one zmigrowane automatycznie. Zalecamy zapisanie listy współpracowników każdej aplikacji. Szablon wiadomości e-mail, który ma tę listę, jest dostępny w ramach procesu migracji.


|Portal|Przeznaczenie|
|--|--|
|[Azure](https://azure.microsoft.com/free/cognitive-services)| Tworzenie zasobów przewidywania i tworzenia.<br> Przypisywanie współautorów do zasobów.|
|[LUIS](https://www.luis.ai)| Migrowanie do nowych zasobów tworzenia.<br> Utwórz nowe zasoby tworzenia w ramach przepływu migracji.<br> Przypisywanie i cofanie przypisywania zasobów predykcyjnych i tworzenia do aplikacji ze strony **Zarządzanie**  >  **zasobami platformy Azure** . <br> Przenoszenie aplikacji z jednego zasobu tworzenia do innego.  |

> [!Note]
> Tworzenie aplikacji LUIS jest bezpłatne, zgodnie z wyznaczonym warstwą F0. Dowiedz się [więcej o warstwach cenowych](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Kroki migracji

1. W portalu LUIS, nad którym pracujesz, możesz rozpocząć proces migracji z ikony **platformy Azure** na górnym pasku narzędzi.

   > [!div class="mx-imgBorder"]
   > ![Ikona migracji](./media/migrate-authoring-key/migration-button.png)

2. Okno podręczne migracji umożliwia kontynuowanie migracji lub późniejszej migracji. Wybierz pozycję **Migruj teraz**.

   > [!div class="mx-imgBorder"]
   > ![Pierwsze okno podręczne w procesie migracji, w którym można wybrać pozycję Migruj teraz](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Jeśli dowolna z aplikacji ma współpracowników, zostanie wyświetlony monit o wysłanie do nich wiadomości e-mail z informacją o migracji. Ten krok jest opcjonalny.

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

4. Jeśli jesteś współpracownikiem z dowolnej aplikacji, zostanie wyświetlony monit o wyeksportowanie kopii aplikacji przez wybranie tej opcji podczas przepływu migracji. Ten krok jest opcjonalny.

   W przypadku wybrania opcji zostanie wyświetlona następująca strona. Wybierz przyciski pobierania po lewej stronie, aby wyeksportować wybrane aplikacje. Te aplikacje można zaimportować z powrotem po migracji, ponieważ nie będą one automatycznie migrowane.

   > [!div class="mx-imgBorder"]
   > ![Monituj o wyeksportowanie aplikacji.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Można utworzyć nowy zasób tworzenia LUIS lub przeprowadzić migrację do istniejącego zasobu tworzenia, jeśli został on już utworzony na platformie Azure. Wybierz żądaną opcję, wybierając jeden z następujących przycisków.

   > [!div class="mx-imgBorder"]
   > ![Przyciski do tworzenia nowego zasobu tworzenia i korzystania z istniejącego zasobu tworzenia](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Tworzenie nowego zasobu tworzenia z LUIS do migracji

Jeśli chcesz utworzyć nowy zasób tworzenia, wybierz opcję **Utwórz nowy zasób tworzenia** i podaj poniższe informacje w następnym oknie. Następnie wybierz pozycję **Done** (Gotowe).

> [!div class="mx-imgBorder"]
> ![Okno do tworzenia zasobu tworzenia](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Nazwa dzierżawcy**: Dzierżawca, z którym jest skojarzona subskrypcja platformy Azure. Ta wartość jest domyślnie ustawiona dla dzierżawy, która jest obecnie używana. Możesz przełączyć dzierżawców, wybierając z prawej stronie awatar, który zawiera inicjały.
* **Nazwa zasobu**: wybrana przez Ciebie Nazwa niestandardowa. Jest ona używana jako część adresu URL zapytań dotyczących tworzenia i przewidywania punktów końcowych.
* **Nazwa subskrypcji**: subskrypcja, która zostanie skojarzona z zasobem. Jeśli masz więcej niż jedną subskrypcję, która należy do dzierżawy, wybierz żądaną wartość z listy rozwijanej.
* **Nazwa grupy zasobów platformy Azure**: niestandardowa nazwa grupy zasobów wybrana z listy rozwijanej. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.

Zwróć uwagę na to, że możesz mieć 10 bezpłatnych zasobów tworzenia na region na subskrypcję. Jeśli subskrypcja zawiera więcej niż 10 zasobów autorstwa w tym samym regionie, nie będzie można utworzyć nowego.

Po utworzeniu zasobu tworzenia zostanie wyświetlony komunikat o powodzeniu. Wybierz pozycję **Zamknij** , aby zamknąć okno podręczne.

  > [!div class="mx-imgBorder"]
  > ![Komunikat wskazujący, że zasób tworzenia został pomyślnie utworzony](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>Użyj istniejącego zasobu tworzenia do migracji

Jeśli subskrypcja jest już skojarzona z LUIS tworzenie zasobu platformy Azure lub jeśli utworzono zasób z Azure Portal i chcesz przeprowadzić migrację do niego, zamiast tworzyć nowe, wybierz opcję **Użyj istniejącego zasobu tworzenia**. Podaj następujące informacje w następnym oknie, a następnie wybierz pozycję **gotowe**.

> [!div class="mx-imgBorder"]
>![Okno umożliwiające zmianę istniejącego zasobu tworzenia](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Nazwa dzierżawcy**: Dzierżawca, z którym jest skojarzona subskrypcja platformy Azure. Ta wartość jest domyślnie ustawiona dla dzierżawy, która jest obecnie używana.
* **Nazwa subskrypcji**: subskrypcja, która zostanie skojarzona z zasobem. Jeśli masz więcej niż jedną subskrypcję, która należy do dzierżawy, wybierz żądaną wartość z listy rozwijanej.
* **Nazwa zasobu**: zasób tworzenia, do którego chcesz przeprowadzić migrację.

> [!Note]
> Jeśli zasób tworzenia nie jest widoczny na liście rozwijanej, upewnij się, że został on utworzony w odpowiedniej lokalizacji zgodnie z portalem LUIS, w którym użytkownik jest zalogowany. Upewnij się również, że utworzony element to zasób autorstwa, a nie zasób predykcyjny.


Sprawdź poprawność nazwy zasobu tworzenia i wybierz przycisk **Migrowanie** .

> [!div class="mx-imgBorder"]
> ![Okno, aby wybrać zasób tworzenia, z przyciskiem Migruj teraz dostępne](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

Zostanie wyświetlony komunikat o powodzeniu. Wybierz pozycję **Zamknij** , aby zamknąć okno podręczne.

> [!div class="mx-imgBorder"]
> ![Komunikat informujący o tym, że zasób tworzenia został pomyślnie zmigrowany](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Używanie aplikacji po migracji

Po zakończeniu migracji wszystkie Twoje aplikacje LUIS, dla których jesteś właścicielem, zostaną teraz przypisane do pojedynczego zasobu tworzenia LUIS.

Lista **Moje aplikacje** zawiera aplikacje migrowane do nowego zasobu tworzenia. Przed uzyskaniem dostępu do aplikacji wybierz zasób subskrypcja i LUIS tworzenie, aby wyświetlić aplikacje, które możesz utworzyć.

 > [!div class="mx-imgBorder"]
 > ![Pola dla subskrypcji i zasobu tworzenia](./media/create-app-in-portal-select-subscription-luis-resource.png)

Nie musisz znać klucza zasobu tworzenia, aby kontynuować edytowanie aplikacji w portalu LUIS.

Jeśli planujesz edytować aplikacje programowo, będziesz potrzebować wartości klucza tworzenia. Te wartości są wyświetlane na stronie **Zarządzanie**  >  **zasobami platformy Azure** w portalu Luis. Są one również dostępne w Azure Portal na stronie **klucze** zasobu. Możesz również utworzyć więcej zasobów autorstwa i przypisać je z tej samej strony.

 > [!div class="mx-imgBorder"]
 > ![Strona do zarządzania zasobami tworzenia](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>Dodawanie współautorów do zasobów tworzenia

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Dowiedz się [, jak dodać współautorów](luis-how-to-collaborate.md) do zasobu tworzenia. Współautorzy będą mieli dostęp do wszystkich aplikacji w ramach tego zasobu.

Współautorzy można dodać do zasobu tworzenia z Azure Portal na stronie **Access Control (IAM)** dla tego zasobu. Aby uzyskać więcej informacji, zobacz [Dodawanie dostępu współautora](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Jeśli właściciel aplikacji LUIS został zmigrowany i dodany do współpracownika jako współautor w ramach zasobu platformy Azure, współpracownik nadal nie będzie miał dostępu do aplikacji, o ile nie zostanie również zmigrowany.

## <a name="luis-portal-migration-reminders"></a>Przypomnienia dotyczące migracji portalu LUIS

[Portal Luis](https://www.luis.ai) zawiera proces migracji.

Użytkownik zostanie poproszony o przeprowadzenie migracji, jeśli oba te warunki są spełnione:
* Masz aplikacje w systemie uwierzytelniania poczty e-mail na potrzeby tworzenia.
* Jesteś właścicielem aplikacji.

Co tydzień zostanie wyświetlony monit o migrację aplikacji. Możesz zamknąć to okno bez migracji. Jeśli chcesz przeprowadzić migrację przed następnym zaplanowanym okresem, możesz rozpocząć proces migracji z ikony **platformy Azure** na górnym pasku narzędzi portalu Luis.

## <a name="prediction-resources-blocking-migration"></a>Zablokuj migrację zasobów predykcyjnych
Migracja ma negatywny wpływ na środowisko uruchomieniowe aplikacji. Podczas migracji wszystkie współpracownicy są usuwani z aplikacji, a użytkownik jest usuwany jako współpracownik z innych aplikacji. Ten proces oznacza, że klucze przypisane przez współpracownika również zostaną usunięte, co może spowodować uszkodzenie aplikacji w środowisku produkcyjnym. Jest to powód blokowania migracji do momentu ręcznego usunięcia przypisanych do nich współpracowników lub klawiszy.

Migracja jest blokowana, jeśli spełniony jest jeden z następujących warunków:

* Przypisano zasoby przewidywania/wykonywania w nieposiadanych aplikacjach.
* Inni użytkownicy mogą przypisywać zasoby do prognozowania/środowiska uruchomieniowego do posiadanych aplikacji.

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>Zalecane kroki, jeśli jesteś właścicielem aplikacji
Jeśli jesteś właścicielem niektórych aplikacji i masz współpracowników przypisaną do tych aplikacji klucz przewidywania/środowiska uruchomieniowego, podczas migracji wystąpi błąd. Ten błąd zawiera listę identyfikatorów aplikacji z przypisanymi do nich kluczami prognoz.

Zalecamy wykonanie następujących czynności:
* Powiadamiaj współpracowników o migracji.
* Usuń wszystkich współpracowników z aplikacji pokazanych w błędzie.
* Przeprowadzenie procesu migracji, który powinien zakończyć się pomyślnie, jeśli ręcznie usuniesz współpracowników.
* Przypisz współpracowników jako współautorów do nowego zasobu tworzenia. Współpracownicy będą migrować i ponownie przypisywać zasoby przewidywania z powrotem do aplikacji. Należy zauważyć, że spowoduje to chwilowe przerwanie aplikacji do momentu ponownego przypisania zasobów przewidywania.

Istnieje również inne potencjalne rozwiązanie. Przed migracją właściciela współpracownicy mogą dodawać właścicieli aplikacji jako współautorów w swoich subskrypcjach platformy Azure z Azure Portal. Ten krok spowoduje przyznanie właścicielowi dostępu do zasobów przewidywania środowiska uruchomieniowego. Jeśli właściciel zostanie zmigrowany przy użyciu nowej subskrypcji, do której zostały dodane (która zostanie znaleziona w ramach nowej dzierżawy), ten krok nie spowoduje po prostu odblokowania procesu migracji zarówno dla współpracownika, jak i właściciela aplikacji. Umożliwi to również bezproblemową migrację aplikacji z kluczem przewidywania, który nadal jest przypisywany do nich i nie przerywa aplikacji.


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>Zalecane kroki, jeśli jesteś współpracownikiem w aplikacji
Jeśli pracujesz nad aplikacjami, a do tych aplikacji przypisano klucz przewidywania/środowiska uruchomieniowego, podczas migrowania pojawia się błąd. Ten błąd zawiera listę identyfikatorów aplikacji i ścieżek kluczy, które blokują migrację.

Zalecamy wykonanie następujących czynności:
* Eksportuj aplikacje jako kopię zapasową. Ten opcjonalny krok w procesie migracji.
* Cofnij przypisanie zasobów predykcyjnych ze strony **Zarządzanie**  >  **zasobami platformy Azure** .
* Przeprowadzenie procesu migracji.
* Importuj aplikacje z tyłu po migracji.
* Ponownie Przypisz klucze przewidywania do aplikacji ze strony **Zarządzanie**  >  **zasobami platformy Azure** .

> [!Note]
> Po zaimportowaniu aplikacji po migracji będą one mieć różne identyfikatory aplikacji. Będą one również różne od trafień w środowisku produkcyjnym. Będziesz teraz właścicielem tych aplikacji.

## <a name="troubleshooting-the-migration-process"></a>Rozwiązywanie problemów z procesem migracji

Gdy próbujesz przeprowadzić migrację, ale nie możesz znaleźć subskrypcji platformy Azure na liście rozwijanej:
* Upewnij się, że masz prawidłową subskrypcję platformy Azure, która jest uprawniona do tworzenia zasobów Cognitive Services. Przejdź do [Azure Portal](https://ms.portal.azure.com) i sprawdź stan subskrypcji. Jeśli go nie masz, [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/cognitive-services/).
* Upewnij się, że jesteś w odpowiedniej dzierżawie skojarzonej z prawidłową subskrypcją. Możesz przełączyć dzierżawców z awatara z lewej strony inicjałów na tym pasku narzędzi: ![ pasek narzędzi umożliwiający przełączanie dzierżawców](./media/migrate-authoring-key/switch-user-tenant-2.png)

Jeśli masz istniejący zasób tworzenia, ale nie można go znaleźć po wybraniu opcji **Użyj istniejącego zasobu tworzenia** :
* Zasób został prawdopodobnie utworzony w lokalizacji innej niż witryna, w której jest zalogowany. Sprawdź [regiony i portale tworzenia Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions).
* Zamiast tego Utwórz nowy zasób z portalu LUIS.

W przypadku wybrania opcji **Utwórz nowy zasób tworzenia** i migracji kończy się niepowodzeniem z komunikatem o błędzie "nie powiodło się pobranie informacji o platformie Azure użytkownika, spróbuj ponownie później":
* Twoja subskrypcja może mieć 10 lub więcej zasobów autorstwa na region na subskrypcję. W takim przypadku nie będzie można utworzyć nowego zasobu tworzenia.
* Migruj przez wybranie opcji **Użyj istniejącego zasobu tworzenia** i wybranie jednego z istniejących zasobów w ramach subskrypcji.

Jeśli zobaczysz następujący błąd, sprawdź [zalecane kroki, jeśli jesteś właścicielem aplikacji](#recommended-steps-if-youre-the-owner-of-the-app).
![Błąd pokazujący, że migracja nie powiodła się dla właścicieli](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Jeśli zobaczysz następujący błąd, sprawdź [zalecane kroki, jeśli jesteś współpracownikiem w aplikacji](#recommended-steps-if-youre-a-collaborator-on-an-app).
![Błąd pokazujący, że migracja nie powiodła się dla współpracowników](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Następne kroki

* Zapoznaj się [z pojęciami dotyczącymi tworzenia i kluczy czasu wykonywania](luis-how-to-azure-subscription.md).
* Zapoznaj się z tematem [przypisywanie kluczy](luis-how-to-azure-subscription.md) i [Dodawanie współautorów](luis-how-to-collaborate.md).
