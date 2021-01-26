---
title: Migrowanie do klucza tworzenia zasobów platformy Azure
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób migracji Language Understanding (LUIS) tworzenia uwierzytelniania z konta e-mail do zasobu platformy Azure.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 3ff48ff5a3f46d8ec0fbf81b4cd20d20c217344b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787641"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrowanie do klucza tworzenia zasobów platformy Azure

> [!IMPORTANT]
>  Od 3 grudnia istniejący użytkownicy LUIS muszą zakończyć proces migracji, aby kontynuować tworzenie aplikacji LUIS.

Language Understanding (LUIS) uwierzytelnianie autorstwa zostało zmienione z konta e-mail na zasób platformy Azure. Skorzystaj z tego artykułu, aby dowiedzieć się, jak przeprowadzić migrację konta, jeśli jeszcze nie wykonano migracji.  


## <a name="what-is-migration"></a>Co to jest migracja?

Migracja to proces zmiany uwierzytelniania autorstwa z konta e-mail na zasób platformy Azure. Twoje konto zostanie połączone z subskrypcją platformy Azure i zasobem tworzenia platformy Azure po przeprowadzeniu migracji.

Należy przeprowadzić migrację z [portalu Luis](https://www.luis.ai). Jeśli tworzysz klucze tworzenia przy użyciu interfejsu wiersza polecenia LUIS, na przykład musisz ukończyć proces migracji w portalu LUIS. Nadal możesz mieć współautorów aplikacji po migracji, ale zostaną one dodane na poziomie zasobów platformy Azure, a nie na poziomie aplikacji. Nie można wycofać migracji konta.

> [!Note]
> * Jeśli konieczne jest utworzenie zasobu predykcyjnego w środowisku uruchomieniowym, istnieje [osobny proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) tworzenia go.
> * Zapoznaj się z sekcją [uwagi dotyczące migracji](#migration-notes) poniżej, aby uzyskać informacje o tym, w jaki sposób mają wpływ Twoje aplikacje i współautorzy. 
> * Tworzenie aplikacji LUIS jest bezpłatne, zgodnie z wyznaczonym warstwą F0. Dowiedz się [więcej o warstwach cenowych](luis-limits.md#key-limits).

## <a name="migration-prerequisites"></a>Wymagania wstępne dotyczące migracji

* Prawidłowa subskrypcja platformy Azure. Poproszenie administratora dzierżawy o dodanie Cię do subskrypcji lub [zarejestrowanie się w celu uzyskania bezpłatnego konta](https://azure.microsoft.com/free/cognitive-services).
* LUIS tworzenie zasobu platformy Azure z poziomu portalu LUIS lub z [Azure Portal](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne). 
    * Tworzenie zasobu tworzenia z portalu LUIS jest częścią procesu migracji opisanego w następnej sekcji.
* Jeśli jesteś współpracownikem w aplikacjach, aplikacje nie będą automatycznie migrowane. Użytkownik zostanie poproszony o wyeksportowanie tych aplikacji podczas przechodzenia przez przepływ migracji. Możesz również użyć [interfejsu API eksportowania](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Możesz zaimportować aplikację z powrotem do LUIS po migracji. Proces importowania tworzy nową aplikację z nowym IDENTYFIKATORem aplikacji, dla którego jesteś właścicielem.        
* Jeśli jesteś właścicielem aplikacji, nie musisz eksportować aplikacji, ponieważ zostaną one zmigrowane automatycznie. Podano szablon wiadomości e-mail z listą wszystkich współpracowników dla każdej aplikacji, dzięki czemu można powiadamiać o procesie migracji.

## <a name="migration-steps"></a>Kroki migracji

1. Po zalogowaniu się do [portalu Luis](https://www.luis.ai)zostanie otwarte okno migracji platformy Azure z krokami migracji. Jeśli odłączysz ją, nie będziesz w stanie kontynuować tworzenia aplikacji LUIS i zostanie wyświetlona jedyną akcją, aby kontynuować migrację.

    > [!div class="mx-imgBorder"]
    > ![Wprowadzenie do okna migracji](./media/migrate-authoring-key/notify-azure-migration.png)

2. Jeśli masz współpracowników w dowolnych aplikacjach, zobaczysz listę nazw aplikacji należących do użytkownika, a także region tworzenia i wiadomości e-mail współpracowników w każdej aplikacji. Zalecamy wysłanie współpracownikom wiadomości e-mail z powiadomieniem o migracji, klikając przycisk **Wyślij** symbol po lewej stronie nazwy aplikacji.
`*`Symbol będzie wyświetlany obok nazwy aplikacji, jeśli współpracownik ma przydzielony zasób predykcyjny do aplikacji. Po migracji te aplikacje nadal będą mieć przypisane do nich zasoby prognozowania, mimo że współpracownicy nie będą mieli dostępu do tworzenia aplikacji. Jednak to przypisanie zostanie przerwane, jeśli właściciel zasobu predykcyjnego ponownie [wygenerował klucze](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) z Azure Portal.  

   > [!div class="mx-imgBorder"]
   > ![Powiadamiaj współpracowników](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


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

3. Jeśli jesteś współpracownikem z dowolnej aplikacji, lista nazw aplikacji udostępnionych Tobie jest wyświetlana wraz z regionem tworzenia i wiadomościami e-mail właściciela dla każdej aplikacji. Zaleca się wyeksportowanie kopii aplikacji, klikając przycisk Eksportuj po lewej stronie nazwy aplikacji. Te aplikacje można zaimportować z powrotem po migracji, ponieważ nie będą one automatycznie migrowane.
`*`Symbol pojawi się obok nazwy aplikacji, jeśli masz zasób predykcyjny przypisany do aplikacji. Po migracji zasób do prognozowania będzie nadal przypisany do tych aplikacji, nawet jeśli nie będziesz mieć już dostępu do tworzenia tych aplikacji. Jeśli chcesz przerwać przypisanie między zasobem przewidywania a aplikacją, musisz przejść do Azure Portal i [ponownie wygenerować klucze](./luis-how-to-azure-subscription.md#regenerate-an-azure-key).

   > [!div class="mx-imgBorder"]
   > ![Wyeksportuj aplikacje.](./media/migrate-authoring-key/migration-export-apps.png)


4. W oknie do migrowania regionów zostanie wyświetlony monit o Migrowanie aplikacji do zasobu platformy Azure w tym samym regionie, w którym zostały utworzone. LUIS ma trzy regiony [i portale](./luis-reference-regions.md#luis-authoring-regions)tworzenia. W oknie zostaną wyświetlone regiony, w których posiadane aplikacje zostały utworzone. Wyświetlane regiony migracji mogą się różnić w zależności od używanego portalu regionalnego i aplikacji, które zostały przez Ciebie utworzone. 

   > [!div class="mx-imgBorder"]
   > ![Migracja z wieloregionem.](./media/migrate-authoring-key/migration-regional-flow.png)

5. Dla każdego regionu wybierz opcję utworzenia nowego zasobu LUIS Authoring lub migracji do istniejącego przy użyciu przycisków.

   > [!div class="mx-imgBorder"]
   > ![Wybierz utworzenie lub istniejący zasób tworzenia](./media/migrate-authoring-key/migration-multiregional-resource.png)

   Podaj następujące informacje:

   * **Nazwa dzierżawcy**: Dzierżawca, z którym jest skojarzona subskrypcja platformy Azure. Domyślnie jest to ustawienie dzierżawy, która jest obecnie używana. Dzierżawcy można przełączyć przez zamknięcie tego okna i wybranie awatara w prawym górnym rogu ekranu zawierającego inicjały. Kliknij pozycję **Migruj do platformy Azure** , aby ponownie otworzyć okno.
   * **Nazwa subskrypcji platformy Azure**: subskrypcja, która zostanie skojarzona z zasobem. Jeśli masz więcej niż jedną subskrypcję, która należy do dzierżawy, wybierz żądaną wartość z listy rozwijanej.
   * **Nazwa zasobu tworzenia**: wybrana przez Ciebie Nazwa niestandardowa. Jest ona używana jako część adresu URL zapytań dotyczących tworzenia i przewidywania punktów końcowych. W przypadku tworzenia nowego zasobu tworzenia należy pamiętać, że nazwa zasobu może zawierać tylko znaki alfanumeryczne, `-` i nie może rozpoczynać się ani kończyć znakiem `-` . Jeśli wszystkie inne symbole są zawarte w nazwie, tworzenie zasobów i migracja nie powiedzie się.
   * **Nazwa grupy zasobów platformy Azure**: niestandardowa nazwa grupy zasobów wybrana z listy rozwijanej. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania. Jeśli obecnie nie masz grupy zasobów w subskrypcji, nie będzie można jej utworzyć w portalu LUIS. Przejdź do [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) , aby utworzyć jeden, a następnie przejdź do Luis, aby kontynuować proces logowania.

6. Po pomyślnym przeprowadzeniu migracji we wszystkich regionach kliknij przycisk Zakończ. Teraz będziesz mieć dostęp do aplikacji. Możesz kontynuować tworzenie i konserwowanie wszystkich aplikacji we wszystkich regionach w portalu.

## <a name="migration-notes"></a>Uwagi dotyczące migracji

* Przed migracją Współautorzy są znani jako _współpracownicy_ na poziomie aplikacji Luis. Po migracji rola _współautora_ jest używana na poziomie zasobów platformy Azure.
* Jeśli zalogowano się do więcej niż jednego [portalu Luis regionalnego](./luis-reference-regions.md#luis-authoring-regions), użytkownik zostanie poproszony o przeprowadzenie migracji w wielu regionach jednocześnie.
* Aplikacje zostaną automatycznie zmigrowane z Ciebie, jeśli jesteś właścicielem aplikacji. Aplikacje nie będą migrowane z Ciebie, jeśli jesteś współpracownikiem aplikacji. Jednak współpracownicy będą monitowani o wyeksportowanie aplikacji, których potrzebują.
* Właściciele aplikacji nie mogą wybrać podzestawu aplikacji do migracji i nie ma żadnego sposobu, aby właściciel mógł wiedzieć, czy współpracownicy przeprowadzili migrację.
* Migracja nie przenosi automatycznie ani nie dodaje współpracowników do zasobu tworzenia platformy Azure. Właściciel aplikacji to ten, który musi wykonać ten krok po migracji. Ten krok wymaga [uprawnień do zasobu tworzenia platformy Azure](./luis-how-to-collaborate.md).
* Po przypisaniu współautorów do zasobu platformy Azure konieczne będzie ich Migrowanie przed uzyskaniem dostępu do aplikacji. W przeciwnym razie użytkownicy nie będą mieli dostępu do tworzenia aplikacji.


## <a name="using-apps-after-migration"></a>Używanie aplikacji po migracji

Po zakończeniu migracji wszystkie Twoje aplikacje LUIS, dla których jesteś właścicielem, zostaną teraz przypisane do pojedynczego zasobu tworzenia LUIS.
Lista **Moje aplikacje** zawiera aplikacje migrowane do nowego zasobu tworzenia. Przed uzyskaniem dostępu do aplikacji wybierz opcję **Wybierz inny zasób tworzenia** , aby wybrać subskrypcję i zasób tworzenia, aby wyświetlić aplikacje, które mogą zostać utworzone.

> [!div class="mx-imgBorder"]
> ![Wybieranie zasobu subskrypcji i tworzenia](./media/migrate-authoring-key/select-sub-and-resource.png)


Jeśli planujesz edytować aplikacje programowo, będziesz potrzebować wartości klucza tworzenia. Te wartości są wyświetlane po kliknięciu pozycji **Zarządzaj** w górnej części ekranu w portalu Luis, a następnie wybraniu **zasobów platformy Azure**. Są one również dostępne w Azure Portal na stronie **klucz zasobu i punkty końcowe** . Możesz również utworzyć więcej zasobów autorstwa i przypisać je z tej samej strony.

## <a name="adding-contributors-to-authoring-resources"></a>Dodawanie współautorów do zasobów tworzenia

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Dowiedz się [, jak dodać współautorów](luis-how-to-collaborate.md) do zasobu tworzenia. Współautorzy będą mieli dostęp do wszystkich aplikacji w ramach tego zasobu.

Współautorzy można dodać do zasobu tworzenia z Azure Portal na stronie **Access Control (IAM)** dla tego zasobu. Aby uzyskać więcej informacji, zobacz [Dodawanie współautorów do aplikacji](luis-how-to-collaborate.md).

> [!Note]
> Jeśli właściciel aplikacji LUIS został zmigrowany i dodany do współpracownika jako współautor w ramach zasobu platformy Azure, współpracownik nadal nie będzie miał dostępu do aplikacji, o ile nie zostanie również zmigrowany.

## <a name="troubleshooting-the-migration-process"></a>Rozwiązywanie problemów z procesem migracji

Jeśli nie możesz znaleźć subskrypcji platformy Azure na liście rozwijanej:
* Upewnij się, że masz prawidłową subskrypcję platformy Azure, która jest uprawniona do tworzenia zasobów Cognitive Services. Przejdź do [Azure Portal](https://ms.portal.azure.com) i sprawdź stan subskrypcji. Jeśli go nie masz, [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/cognitive-services/).
* Upewnij się, że jesteś w odpowiedniej dzierżawie skojarzonej z prawidłową subskrypcją. Możesz przełączyć dzierżawców, wybierając awatar w prawym górnym rogu ekranu zawierającego inicjały.

  > [!div class="mx-imgBorder"]
  > ![Strona przełączania katalogów](./media/migrate-authoring-key/switch-directories.png)

Jeśli masz istniejący zasób tworzenia, ale nie można go znaleźć po wybraniu opcji **Użyj istniejącego zasobu tworzenia** :
* Zasób został prawdopodobnie utworzony w innym regionie niż ten, w którym próbujesz przeprowadzić migrację.
* Zamiast tego Utwórz nowy zasób z portalu LUIS.

W przypadku wybrania opcji **Utwórz nowy zasób tworzenia** i migracji kończy się niepowodzeniem z komunikatem o błędzie "nie powiodło się pobranie informacji o platformie Azure użytkownika, spróbuj ponownie później":
* Twoja subskrypcja może mieć 10 lub więcej zasobów autorstwa na region na subskrypcję. W takim przypadku nie będzie można utworzyć nowego zasobu tworzenia.
* Migruj przez wybranie opcji **Użyj istniejącego zasobu tworzenia** i wybranie jednego z istniejących zasobów w ramach subskrypcji.

## <a name="create-new-support-request"></a>Utwórz nowe żądanie obsługi

Jeśli masz problemy z migracją, które nie zostały uwzględnione w sekcji rozwiązywania problemów, [Utwórz temat pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i podaj poniższe informacje z następujących pól:

   * **Typ problemu**: techniczne
   * **Subskrypcja**: wybierz subskrypcję z listy rozwijanej
   * **Usługa**: Wyszukaj i wybierz pozycję "Cognitive Services"
   * **Zasób**: wybierz zasób Luis, jeśli istnieje. Jeśli nie, wybierz pozycję pytanie ogólne.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się [z pojęciami dotyczącymi tworzenia i kluczy czasu wykonywania](luis-how-to-azure-subscription.md)
* Przejrzyj sposób [przypisywania kluczy](luis-how-to-azure-subscription.md) i [Dodawanie współautorów](luis-how-to-collaborate.md)