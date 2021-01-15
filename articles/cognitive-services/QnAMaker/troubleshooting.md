---
title: Rozwiązywanie problemów — QnA Maker
description: Zanadzorowana lista najbardziej często zadawanych pytań dotyczących usługi QnA Maker pomoże Ci szybciej wdrożyć usługę i uzyskać lepsze wyniki.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: aea205bee41aed232b8453417dca521d2dfc83ab
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233784"
---
# <a name="troubleshooting-for-qna-maker"></a>Rozwiązywanie problemów dotyczących QnA Maker

Zanadzorowana lista najbardziej często zadawanych pytań dotyczących usługi QnA Maker pomoże Ci szybciej wdrożyć usługę i uzyskać lepsze wyniki.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Zarządzanie przewidywaniami

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

<details>
<summary><b>Jak można poprawić przepływność dla prognoz zapytania?</b></summary>

**Odpowiedź**: problemy z wydajnością przepływności wskazują, że konieczne jest skalowanie w górę zarówno dla usługi App Service, jak i dla wyszukiwanie poznawcze. Rozważ dodanie repliki do Wyszukiwanie poznawcze, aby zwiększyć wydajność.

Dowiedz się więcej o [warstwach cenowych](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Jak uzyskać punkt końcowy usługi QnAMaker</b></summary>

**Odpowiedź**: punkt końcowy usługi QnAMaker jest przydatny do celów debugowania podczas kontaktowania się z pomocą techniczną QnAMaker lub UserVoice. Punkt końcowy jest adresem URL w tej formie: `https://your-resource-name.azurewebsites.net` .

1. Przejdź do usługi QnAMaker (Grupa zasobów) w [Azure Portal](https://portal.azure.com)

    ![QnAMaker grupę zasobów platformy Azure w Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Wybierz App Service skojarzoną z zasobem QnA Maker. Zazwyczaj nazwy są takie same.

     ![Wybierz QnAMaker App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Adres URL punktu końcowego jest dostępny w sekcji Przegląd

    ![Punkt końcowy QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

<details>
<summary><b>Jak można poprawić przepływność dla prognoz zapytania?</b></summary>

**Odpowiedź**: problemy z wydajnością przepływności wskazują, że potrzebujesz skalować w górę wyszukiwanie poznawcze. Rozważ dodanie repliki do Wyszukiwanie poznawcze, aby zwiększyć wydajność.

Dowiedz się więcej o [warstwach cenowych](Concepts/azure-resources.md).
</details>

---

## <a name="manage-the-knowledge-base"></a>Zarządzanie bazą wiedzy

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

<details>
<summary><b>Przypadkowo usunięto część QnA Maker, co mam zrobić?</b></summary>

**Odpowiedź**: nie usuwaj żadnych utworzonych usług platformy Azure wraz z zasobem QNA Maker, takich jak Search lub Web App. Są one niezbędne do pracy QnA Maker, jeśli zostaną usunięte, QnA Maker przestaną działać poprawnie.

Wszystkie usunięcia są trwałe, w tym pary pytań i odpowiedzi, pliki, adresy URL, niestandardowe pytania i odpowiedzi, bazy wiedzy lub zasoby platformy Azure. Przed usunięciem jakiejkolwiek części bazy wiedzy upewnij się, że została wyeksportowana z bazy wiedzy na stronie **Ustawienia** .

</details>

<details>
<summary><b>Dlaczego moje adresy URL nie wyodrębniają par pytań i odpowiedzi?</b></summary>

**Odpowiedź**: jest możliwe, że QNA Maker nie może pobrać zawartości z ważnych często zadawanych pytań i odpowiedzi (QNA). W takich przypadkach można wkleić zawartość QnA w pliku txt i sprawdzić, czy narzędzie może go pozyskać. Alternatywnie można dodać zawartość do bazy wiedzy za pomocą [portalu QNA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Jak duża baza wiedzy można utworzyć?</b></summary>

**Odpowiedź**: rozmiar bazy wiedzy zależy od jednostki SKU usługi Azure Search wybranej podczas tworzenia usługi QNA Maker. Przeczytaj [tutaj](./concepts/azure-resources.md) , aby uzyskać więcej szczegółów.

</details>

<details>
<summary><b>Dlaczego nie widzę niczego na liście rozwijanej podczas próby utworzenia nowej bazy wiedzy?</b></summary>

**Odpowiedź**: nie utworzono jeszcze żadnych usług QNA Maker na platformie Azure. Przeczytaj [tutaj](./How-To/set-up-qnamaker-service-azure.md) , aby dowiedzieć się, jak to zrobić.

</details>

<details>
<summary><b>Jak mogę udostępnić bazę wiedzy innym osobom?</b></summary>

**Odpowiedź**: udostępnianie działa na poziomie usługi QNA Maker, czyli wszystkie bazy wiedzy w usłudze zostaną udostępnione. Przeczytaj [tutaj](./index.yml) , jak współpracować z bazą wiedzy.

</details>

<details>
<summary><b>Czy można udostępnić bazę wiedzy z współautorem, który nie znajduje się w tej samej dzierżawie usługi AAD, aby zmodyfikować bazę wiedzy?</b></summary>

**Odpowiedź**: udostępnianie odbywa się na podstawie kontroli dostępu opartej na rolach (Azure RBAC). Jeśli możesz udostępnić _dowolny_ zasób na platformie Azure innym użytkownikom, możesz również udostępnić QNA Maker.

</details>

<details>
<summary><b>Jeśli masz plan App Service z 5 QnAMaker bazami wiedzy. Czy można przypisywać prawa do odczytu i zapisu do 5 różnych użytkowników, aby każdy z nich miał dostęp tylko do 1 QnAMaker bazy wiedzy?</b></summary>

**Odpowiedź**: możesz udostępnić całą usługę QnAMaker, a nie poszczególne bazy wiedzy.

</details>

<details>
<summary><b>Jak zmienić domyślny komunikat, gdy nie znaleziono dobrego dopasowania?</b></summary>

**Odpowiedź**: domyślna wiadomość jest częścią ustawień w usłudze App Service.
- Przejdź do zasobu usługi App Service w Azure Portal

![qnamaker appService](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kliknij opcję **Ustawienia**

![Ustawienia appService qnamaker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Zmień wartość ustawienia **DefaultAnswer**
- Uruchom ponownie usługę App Service

![qnamaker appService ponownie](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Dlaczego mój link programu SharePoint nie jest wyodrębniany?</b></summary>

**Odpowiedź**: zobacz [lokalizacje źródła danych](./concepts/data-sources-and-content.md#data-source-locations) , aby uzyskać więcej informacji.

</details>

<details>
<summary><b>Aktualizacje wprowadzone w bazie wiedzy nie są uwzględniane podczas publikowania. Dlaczego nie?</b></summary>

**Odpowiedź**: Każda operacja edycji, niezależnie od tego, czy w tabeli aktualizacja, test lub ustawienie, musi zostać zapisana, aby można było ją opublikować. Pamiętaj, aby kliknąć przycisk **Zapisz i pouczenie** po każdej operacji edycji.

</details>

<details>
<summary><b>Czy baza wiedzy obsługuje bogate dane czy multimedia?</b></summary>

**Odpowiedź**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Funkcja autowyodrębniania multimediów dla plików i adresów URL

* Adresy URL — ograniczona możliwość konwersji z formatu HTML do promocji.
* Pliki — nieobsługiwane

#### <a name="answer-text-in-markdown"></a>Tekst odpowiedzi w promocji
Gdy pary QnA znajdują się w bazie wiedzy, można edytować tekst w promocji na podstawie odpowiedzi, aby uwzględnić linki do multimediów dostępnych z publicznych adresów URL.


</details>

<details>
<summary><b>Czy QnA Maker obsługuje języki inne niż angielskie?</b></summary>

**Odpowiedź**: Zobacz więcej szczegółów dotyczących [obsługiwanych języków](./overview/language-support.md).

Jeśli masz zawartość z wielu języków, pamiętaj o utworzeniu oddzielnej usługi dla każdego języka.

</details>

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

<details>
<summary><b>Dlaczego moje adresy URL nie wyodrębniają par pytań i odpowiedzi?</b></summary>

**Odpowiedź**: jest możliwe, że QNA Maker nie może pobrać zawartości z ważnych często zadawanych pytań i odpowiedzi (QNA). W takich przypadkach można wkleić zawartość QnA w pliku txt i sprawdzić, czy narzędzie może go pozyskać. Alternatywnie można dodać zawartość do bazy wiedzy za pomocą [portalu QNA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Jak duża baza wiedzy można utworzyć?</b></summary>

**Odpowiedź**: rozmiar bazy wiedzy zależy od jednostki SKU usługi Azure Search wybranej podczas tworzenia usługi QNA Maker. Przeczytaj [tutaj](./concepts/azure-resources.md) , aby uzyskać więcej szczegółów.

</details>

<details>
<summary><b>Dlaczego nie widzę niczego na liście rozwijanej podczas próby utworzenia nowej bazy wiedzy?</b></summary>

**Odpowiedź**: nie utworzono jeszcze żadnych usług QNA Maker na platformie Azure. Przeczytaj [tutaj](./How-To/set-up-qnamaker-service-azure.md) , aby dowiedzieć się, jak to zrobić.

</details>

<details>
<summary><b>Jak mogę udostępnić bazę wiedzy innym osobom?</b></summary>

**Odpowiedź**: udostępnianie działa na poziomie usługi QNA Maker, czyli wszystkie bazy wiedzy w usłudze zostaną udostępnione. Przeczytaj [tutaj](./index.yml) , jak współpracować z bazą wiedzy.

</details>

<details>
<summary><b>Czy można udostępnić bazę wiedzy z współautorem, który nie znajduje się w tej samej Azure Active Directory dzierżawie, aby zmodyfikować bazę wiedzy?</b></summary>

**Odpowiedź**: udostępnianie odbywa się na podstawie kontroli dostępu opartej na rolach (Azure RBAC). Jeśli możesz udostępnić _dowolny_ zasób na platformie Azure innym użytkownikom, możesz również udostępnić QNA Maker.

</details>

<details>
<summary><b>Czy można przypisywać prawa do odczytu i zapisu do 5 różnych użytkowników, aby każdy z nich miał dostęp tylko do 1 QnAMaker bazy wiedzy?</b></summary>

**Odpowiedź**: możesz udostępnić całą usługę QnAMaker, a nie poszczególne bazy wiedzy.

</details>

<details>
<summary><b>Dlaczego mój link programu SharePoint nie jest wyodrębniany?</b></summary>

**Odpowiedź**: zobacz [lokalizacje źródła danych](./concepts/data-sources-and-content.md#data-source-locations) , aby uzyskać więcej informacji.

</details>

<details>
<summary><b>Aktualizacje wprowadzone w bazie wiedzy nie są uwzględniane podczas publikowania. Dlaczego nie?</b></summary>

**Odpowiedź**: Każda operacja edycji, niezależnie od tego, czy w tabeli aktualizacja, test lub ustawienie, musi zostać zapisana, aby można było ją opublikować. Pamiętaj, aby kliknąć przycisk **Zapisz i pouczenie** po każdej operacji edycji.

</details>

<details>
<summary><b>Czy baza wiedzy obsługuje bogate dane czy multimedia?</b></summary>

**Odpowiedź**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Funkcja autowyodrębniania multimediów dla plików i adresów URL

* Adresy URL — ograniczona możliwość konwersji z formatu HTML do promocji.
* Pliki — nieobsługiwane

#### <a name="answer-text-in-markdown"></a>Tekst odpowiedzi w promocji
Gdy pary QnA znajdują się w bazie wiedzy, można edytować tekst w promocji na podstawie odpowiedzi, aby uwzględnić linki do multimediów dostępnych z publicznych adresów URL.


</details>

<details>
<summary><b>Czy QnA Maker obsługuje języki inne niż angielskie?</b></summary>

**Odpowiedź**: Zobacz więcej szczegółów dotyczących [obsługiwanych języków](./overview/language-support.md).

Jeśli masz zawartość z wielu języków, pamiętaj o utworzeniu oddzielnej usługi dla każdego języka.

</details>

---

## <a name="manage-service"></a>Zarządzanie usługą

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

<details>
<summary><b>Kiedy należy uruchomić ponownie usługę App Service?</b></summary>

**Odpowiedź**: Odśwież usługę App Service, gdy ikona przestroga znajduje się obok wartości wersja bazy wiedzy w tabeli **klucze punktów końcowych** na [stronie](https://www.qnamaker.ai/UserSettings) **Ustawienia użytkownika** .

</details>

<details>
<summary><b>Usunięto istniejącą usługę wyszukiwania. Jak można to naprawić?</b></summary>

**Odpowiedź**: Jeśli usuniesz indeks usługi Azure wyszukiwanie poznawcze, operacja jest końcowa i nie będzie można odzyskać indeksu.

</details>

<details>
<summary><b>Mój indeks został usunięty `testkb` w usłudze wyszukiwania. Jak można to naprawić?</b></summary>

**Odpowiedź**: nie można odzyskać starych danych. Utwórz nowy zasób QnA Maker i ponownie utwórz bazę wiedzy.

</details>

<details>
<summary><b>Kiedy należy odświeżyć klucze punktów końcowych?</b></summary>

**Odpowiedź**: Odśwież klucze punktów końcowych, jeśli podejrzewasz, że zostały naruszone.

</details>

<details>
<summary><b>Czy można używać tego samego zasobu Wyszukiwanie poznawcze platformy Azure dla baz wiedzy przy użyciu wielu języków?</b></summary>

**Odpowiedź**: Aby korzystać z wielu języków i wielu baz wiedzy, użytkownik musi utworzyć zasób QNA Maker dla każdego języka. Spowoduje to utworzenie oddzielnej usługi Azure Search dla każdego języka. Mieszanie różnych baz wiedzy w ramach jednej usługi Azure Search spowoduje spadek wydajności wyników.

</details>

<details>
<summary><b>Jak mogę zmienić nazwę zasobu usługi Azure Wyszukiwanie poznawcze używanego przez QnA Maker?</b></summary>

**Odpowiedź**: nazwa zasobu usługi Azure wyszukiwanie poznawcze to nazwa zasobu QNA Maker z kilkoma losowymi literami dołączanymi na końcu. Dzięki temu trudno jest rozróżnić wiele zasobów wyszukiwania dla QnA Maker. Utwórz oddzielną usługę wyszukiwania (nazywaną w pożądany sposób) i połącz ją z usługą QnA. Kroki są podobne do kroków, które należy wykonać, aby [uaktualnić usługę Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>Kiedy QnA Maker zwraca `Runtime core is not initialized,` , jak rozwiązać ten problem?</b></summary>

**Odpowiedź**: ilość miejsca na dysku dla usługi App Service może być pełna. Procedura naprawy miejsca na dysku:

1. W [Azure Portal](https://portal.azure.com)wybierz usługę App Service QNA Maker, a następnie Zatrzymaj usługę.
1. Gdy nadal znajduje się w usłudze App Service, wybierz pozycję **Narzędzia programistyczne**, a następnie **Narzędzia zaawansowane**, a następnie pozycję **Przejdź**. Spowoduje to otwarcie nowego okna przeglądarki.
1. Wybierz pozycję **konsola debugowania**, a następnie polecenie **cmd** , aby otworzyć narzędzie wiersza polecenia.
1. Przejdź do _lokalizacji site/wwwroot/Data/QnAMaker/_ Directory.
1. Usuń wszystkie foldery, których nazwy zaczynają się od `rd` .

    **Nie usuwaj** następujących danych:

    * Plik KbIdToRankerMappings.txt
    * EndpointSettings.jspliku
    * Folder EndpointKeys

1. Uruchom usługę App Service.
1. Uzyskaj dostęp do bazy wiedzy, aby sprawdzić, czy teraz działa.

</details>
<details>
<summary><b>Dlaczego moje Application Insights nie działają?</b></summary>

**Odpowiedź**: Sprawdź krzyżowo i zaktualizuj poniższe kroki, aby rozwiązać ten problem:

1. W obszarze App Service-> ustawienia > sekcja konfiguracji — > ustawienia aplikacji-> nazwa "UserAppInsightsKey" jest prawidłowo skonfigurowana i ustawiana na odpowiednie karty Przegląd usługi Application Insights ("klucz Instrumentacji"). 

1. W sekcji App Service > > "Application Insights" — > upewnij się, że usługi App Insights są włączone i połączone z odpowiednim zasobem usługi Application Insights.

</details>

<details>
<summary><b>My Application Insights jest włączona, ale dlaczego nie działa prawidłowo?</b></summary>

**Odpowiedź**: wykonaj poniższe czynności: 

1.  Skopiuj wartość "" APPINSIGHTS_INSTRUMENTATIONKEY "name" na nazwę "UserAppInsightsKey", zastępując ją, jeśli istnieje już obecna wartość. 

1.  Jeśli klucz "UserAppInsightsKey" nie istnieje w ustawieniach aplikacji, Dodaj nowy klucz o tej nazwie i skopiuj wartość.

1.  Zapisz go. spowoduje to automatyczne ponowne uruchomienie usługi App Service. Powinno to rozwiązać problem. 

</details>

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)


<details>
<summary><b>Usunięto istniejącą usługę wyszukiwania. Jak można to naprawić?</b></summary>

**Odpowiedź**: Jeśli usuniesz indeks usługi Azure wyszukiwanie poznawcze, operacja jest końcowa i nie będzie można odzyskać indeksu.

</details>

<details>
<summary><b>Mój indeks został usunięty `testkb` w usłudze wyszukiwania. Jak można to naprawić?</b></summary>

**Odpowiedź**: nie można odzyskać starych danych. Utwórz nowy zasób QnA Maker i ponownie utwórz bazę wiedzy.

</details>

<details>
<summary><b>Czy można używać tego samego zasobu Wyszukiwanie poznawcze platformy Azure dla baz wiedzy przy użyciu wielu języków?</b></summary>

**Odpowiedź**: Aby korzystać z wielu języków i wielu baz wiedzy, użytkownik musi utworzyć zasób QNA Maker dla każdego języka. Spowoduje to utworzenie oddzielnej usługi Azure Search dla każdego języka. Mieszanie różnych baz wiedzy w ramach jednej usługi Azure Search spowoduje spadek wydajności wyników.

</details>

<details>
<summary><b>Jak mogę zmienić nazwę zasobu usługi Azure Wyszukiwanie poznawcze używanego przez QnA Maker?</b></summary>

**Odpowiedź**: nazwa zasobu usługi Azure wyszukiwanie poznawcze to nazwa zasobu QNA Maker z kilkoma losowymi literami dołączanymi na końcu. Dzięki temu trudno jest rozróżnić wiele zasobów wyszukiwania dla QnA Maker. Utwórz oddzielną usługę wyszukiwania (nazywaną w pożądany sposób) i połącz ją z usługą QnA. Kroki są podobne do kroków, które należy wykonać, aby [uaktualnić usługę Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Integracja z innymi usługami, w tym botów

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

<details>
<summary><b>Czy muszę używać bot Framework, aby używać QnA Maker?</b></summary>

**Odpowiedź**: nie, nie trzeba używać [platformy bot](https://github.com/Microsoft/botbuilder-dotnet) z QNA Maker. QnA Maker jest jednak oferowany jako jeden z kilku szablonów w programie [Azure bot Service](/azure/bot-service/?preserve-view=true&view=azure-bot-service-4.0). Usługa bot umożliwia szybkie inteligentne programowanie bot przy użyciu programu Microsoft bot Framework i działa w środowisku bez serwera.

</details>

<details>
<summary><b>Jak utworzyć nowy Bot z QnA Maker?</b></summary>

**Odpowiedź**: Postępuj zgodnie z instrukcjami w [tej](./Quickstarts/create-publish-knowledge-base.md) dokumentacji, aby utworzyć bot za pomocą Azure bot Service.

</details>

<details>
<summary><b>Jak mogę użyć innej bazy wiedzy z istniejącą usługą Azure bot?</b></summary>

**Odpowiedź**: musisz mieć następujące informacje na temat bazy wiedzy:

* Identyfikator bazy wiedzy.
* Niestandardowa nazwa domeny podrzędnej punktu końcowego bazy wiedzy, znana jako `host` znaleziona na stronie **Ustawienia** po opublikowaniu.
* Klucz punktu końcowego opublikowany w bazie wiedzy — znajduje się na stronie **Ustawienia** po opublikowaniu.

Korzystając z tych informacji, przejdź do usługi App Service bot w Azure Portal. W obszarze **ustawienia > konfiguracja — > ustawienia aplikacji** Zmień te wartości.

Klucz punktu końcowego bazy wiedzy jest oznaczony jako `QnAAuthkey` Usługa ABS.

</details>

<details>
<summary><b>Czy co najmniej dwie aplikacje klienckie współdzielą bazę wiedzy?</b></summary>

**Odpowiedź**: tak, baza wiedzy może być wysyłana z dowolnej liczby klientów. Jeśli odpowiedź z bazy wiedzy wydaje się powolnić lub przekroczyć limit czasu, rozważ uaktualnienie warstwy usług dla usługi App Service skojarzonej z bazą wiedzy.

</details>

<details>
<summary><b>Jak mogę osadzić usługę QnA Maker w mojej witrynie sieci Web?</b></summary>

**Odpowiedź**: wykonaj następujące kroki, aby osadzić usługę QNA Maker jako kontrolkę rozmowy w sieci Web w witrynie internetowej:

1. Utwórz bot często zadawanych pytań, postępując zgodnie z instrukcjami znajdującymi się [tutaj](./Quickstarts/create-publish-knowledge-base.md).
2. Włącz rozmowę internetową, wykonując kroki opisane [tutaj](/azure/bot-service/bot-service-channel-connect-webchat) .

</details>

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)


<details>
<summary><b>Czy muszę używać bot Framework, aby używać QnA Maker?</b></summary>

**Odpowiedź**: nie, nie trzeba używać [platformy bot](https://github.com/Microsoft/botbuilder-dotnet) z QNA Maker. QnA Maker jest jednak oferowany jako jeden z kilku szablonów w programie [Azure bot Service](/azure/bot-service/?preserve-view=true&view=azure-bot-service-4.0). Usługa bot umożliwia szybkie inteligentne programowanie bot przy użyciu programu Microsoft bot Framework i działa w środowisku bez serwera.

</details>

<details>
<summary><b>Jak utworzyć nowy Bot z QnA Maker?</b></summary>

**Odpowiedź**: Postępuj zgodnie z instrukcjami w [tej](./Quickstarts/create-publish-knowledge-base.md) dokumentacji, aby utworzyć bot za pomocą Azure bot Service.

</details>

<details>
<summary><b>Jak mogę użyć innej bazy wiedzy z istniejącą usługą Azure bot?</b></summary>

**Odpowiedź**: musisz mieć następujące informacje na temat bazy wiedzy:

* Identyfikator bazy wiedzy.
* Niestandardowa nazwa domeny podrzędnej punktu końcowego bazy wiedzy, znana jako `host` znaleziona na stronie **Ustawienia** po opublikowaniu.
* Klucz punktu końcowego opublikowany w bazie wiedzy — znajduje się na stronie **Ustawienia** po opublikowaniu.

Korzystając z tych informacji, przejdź do usługi App Service bot w Azure Portal. W obszarze **ustawienia > konfiguracja — > ustawienia aplikacji** Zmień te wartości.

Klucz punktu końcowego bazy wiedzy jest oznaczony jako `QnAAuthkey` Usługa ABS.

</details>

<details>
<summary><b>Czy co najmniej dwie aplikacje klienckie współdzielą bazę wiedzy?</b></summary>

**Odpowiedź**: tak, baza wiedzy może być wysyłana z dowolnej liczby klientów. Jeśli odpowiedź z bazy wiedzy wydaje się powolnić lub przekroczyć limit czasu, rozważ uaktualnienie warstwy usług dla usługi App Service skojarzonej z bazą wiedzy.

</details>

<details>
<summary><b>Jak mogę osadzić usługę QnA Maker w mojej witrynie sieci Web?</b></summary>

**Odpowiedź**: wykonaj następujące kroki, aby osadzić usługę QNA Maker jako kontrolkę rozmowy w sieci Web w witrynie internetowej:

1. Utwórz bot często zadawanych pytań, postępując zgodnie z instrukcjami znajdującymi się [tutaj](./Quickstarts/create-publish-knowledge-base.md).
2. Włącz rozmowę internetową, wykonując kroki opisane [tutaj](/azure/bot-service/bot-service-channel-connect-webchat) .

---

## <a name="data-storage"></a>Magazyn danych

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

<details>
<summary><b>Jakie dane są przechowywane i gdzie są przechowywane?</b></summary>

**Odpowiedź**:

Po utworzeniu usługi QnA Maker wybrano region platformy Azure. Bazy wiedzy i pliki dzienników są przechowywane w tym regionie.

</details>

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

<details>
<summary><b>Jakie dane są przechowywane i gdzie są przechowywane?</b></summary>

**Odpowiedź**:

Po utworzeniu usługi QnA Maker wybrano region platformy Azure. Bazy wiedzy i pliki dzienników są przechowywane w tym regionie.

</details>

---
