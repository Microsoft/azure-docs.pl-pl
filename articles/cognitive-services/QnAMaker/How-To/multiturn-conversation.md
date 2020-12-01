---
title: Konwersacje wieloetapowe — QnA Maker
description: Użyj wskazówek i kontekstu, aby zarządzać wielokrotnymi przepisaniami, nazywanymi wieloma przełączanymi, dla bot z jednego pytania do innego. Funkcja wielodostępna umożliwia konwersację z poprzednią i przechodzącą, w której kontekście poprzedniego pytania ma wpływ na następne pytanie i odpowiedź.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 26fc976983fc08857e7771d58f15d0abcd9a1d3c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353225"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Tworzenie wielu etapów konwersacji za pomocą dodatkowych monitów

Użyj monitów uzupełniających i kontekstu, aby zarządzać wielokrotnymi _przełączami_, nazywanymi wieloma ruchami dla bot z jednego pytania do innego.

Aby zobaczyć, jak działa wiele przełączania, Wyświetl następujące wideo demonstracyjne:

[![Obsługa wieloskładnikowej rozmowy w QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Co to jest konwersacja wieloletnia?

Niektórych pytań nie można udzielić na jednym z nich. Gdy projektujesz konwersacje aplikacji klienckiej (Chat bot), użytkownik może zadawać pytanie, które musi zostać przefiltrowane lub ulepszone, aby określić poprawną odpowiedź. Ten przepływ można wykonać za pomocą pytań możliwych do przesłania użytkownikowi z *monitami*.

Gdy użytkownik prosi o pytanie, QnA Maker zwróci odpowiedź _i_ wszelkie monity monitujące. Ta odpowiedź umożliwia zaprezentowanie pytań uzupełniających jako wyborów.

> [!CAUTION]
> Monity o wiele wyłączeń nie są wyodrębniane z dokumentów często zadawanych pytań. Jeśli potrzebujesz wyodrębniania wieloskładnikowego, Usuń znaki zapytania, które wyznaczają pary QnA jako często zadawane pytania.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Przykład wieloletniej rozmowy z bot rozmowy

Dzięki wielokrotnemu przeniesieniu rozmowa bot zarządza konwersacją z użytkownikiem w celu określenia odpowiedzi końcowej, jak pokazano na poniższej ilustracji:

![Wieloosiowe okno dialogowe z instrukcjami, które prowadzą użytkownika przez konwersację](../media/conversational-context/conversation-in-bot.png)

Na poprzedniej ilustracji użytkownik rozpoczął konwersację, wprowadzając **Moje konto**. Baza wiedzy ma trzy połączone pary pytań i odpowiedzi. Aby uściślić odpowiedź, użytkownik wybiera jedną z trzech opcji w bazie wiedzy. Pytanie (#1) ma trzy monity monitujące, które są prezentowane w rozmowie bot jako trzy opcje (#2).

Gdy użytkownik wybierze opcję (#3), zostanie wyświetlona Następna lista opcji rafinacji (#4). Ta sekwencja będzie kontynuowana (#5), dopóki użytkownik nie ustali poprawnej, końcowej odpowiedzi (#6).


### <a name="use-multi-turn-in-a-bot"></a>Używanie wieloskładnikowego bot

Po opublikowaniu bazy wiedzy możesz wybrać przycisk **Utwórz bot** , aby wdrożyć QNA Maker Bot w usłudze Azure bot. Pojawią się na nich komunikaty, które zostały włączone dla bot.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Tworzenie wieloskładnikowej konwersacji z struktury dokumentu

Po utworzeniu bazy wiedzy w sekcji Wypełnij w **bazie** wiedzy zostanie wyświetlona pole wyboru **Włącz funkcję wyodrębniania z adresów URL, plików PDF lub DOCX** .

![Pole wyboru umożliwiające włączenie wyodrębniania wieloskładnikowego](../media/conversational-context/enable-multi-turn.png)

Po wybraniu tej opcji QnA Maker wyodrębnia hierarchię obecną w strukturze dokumentu. Hierarchia jest konwertowana w programie w celu wykonania monitów, a katalog główny hierarchii służy jako nadrzędny QnA. W niektórych dokumentach w katalogu głównym hierarchii nie ma zawartości, która może służyć jako odpowiedź, możesz podać "domyślny tekst odpowiedzi" jako zastępczy tekst odpowiedzi, aby wyodrębnić takie hierarchie.

Strukturę wielostopniową można wywnioskować tylko na podstawie adresów URL, plików PDF lub plików DOCX. Aby zapoznać się z przykładem struktury, Wyświetl obraz [ręcznego pliku PDF użytkownika powierzchniowego](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf).

:::image type="content" source="../media/conversational-context/import-file-with-conversational-structure.png" alt-text="Zrzut ekranu przedstawia przykład struktury w podręczniku użytkownika." lightbox="../media/conversational-context/import-file-with-conversational-structure.png":::

### <a name="building-your-own-multi-turn-document"></a>Tworzenie własnego dokumentu wielostopniowego

W przypadku tworzenia dokumentu z obsługą wielodostępności należy wziąć pod uwagę następujące wytyczne:

* Użyj nagłówków i nagłówków podrzędnych do określenia hierarchii. Na przykład można H1 do określenia elementu Parent QnA i H2, aby zauważyć QnA, które należy wykonać jako monit. Użyj małego rozmiaru nagłówka, aby zauważyć dalszą hierarchię. Nie używaj stylu, koloru ani innego mechanizmu, aby oznaczać strukturę w dokumencie, QnA Maker nie Wyodrębnij wyświetlonych przez siebie wierszy.

* Pierwszy znak nagłówka musi być wielkimi literami.

* Nie kończyj nagłówka ze znakiem zapytania `?` .

* Możesz użyć przykładowego [dokumentu](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) w celu utworzenia własnego dokumentu wieloskładnikowego.

### <a name="adding-files-to-a-multi-turn-kb"></a>Dodawanie plików do wieloskładnikowej bazy wiedzy

Po dodaniu dokumentu hierarchicznego, QnA Maker określa monity o instrukcje ze struktury, aby utworzyć przepływ konwersacji.

1. W QnA Maker wybierz istniejącą bazę wiedzy, która została utworzona z opcją **Włącz wyodrębnianie wieloskładnikowe z adresów URL, plików PDF lub DOCX.** dostępny.
1. Przejdź do strony **Ustawienia** , wybierz plik lub adres URL do dodania.
1. **Zapisz i Wyszkol** bazę wiedzy.

> [!Caution]
> Obsługa wyeksportowanych plików TSV lub XLS z użyciem wieloskładnikowej bazy wiedzy jako źródła danych dla nowej lub pustej bazy wiedzy nie jest obsługiwana. Należy **zaimportować** ten typ pliku z poziomu strony **ustawienia** w portalu QNA Maker, aby dodać do bazy wiedzy wyeksportowane komunikaty z obsługą wielowymiarową.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Tworzenie bazy wiedzy z instrukcjami wielodostępnymi za pomocą interfejsu API tworzenia

Można utworzyć przypadek wiedzy z instrukcjami wieloletnimi przy użyciu [QNA Maker tworzenia interfejsu API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Monity są dodawane w `context` `prompts` tablicy właściwości.

## <a name="show-questions-and-answers-with-context"></a>Pokaż pytania i odpowiedzi z kontekstem

Zmniejsz liczbę wyświetlanych par pytań i odpowiedzi tylko do tych, które są kontekstowe konwersacje.

Wybierz pozycję **Wyświetl opcje**, a następnie wybierz pozycję **Pokaż kontekst**. Na liście są wyświetlane pary pytania i odpowiedzi, które zawierają monity monitujące.

![Filtrowanie par pytań i odpowiedzi według kontekstowych konwersacji](../media/conversational-context/filter-question-and-answers-by-context.png)

Kontekst wielowłączany jest wyświetlany w pierwszej kolumnie.

:::image type="content" source="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png" alt-text="Zrzut ekranu przedstawia podświetloną sekcję kontekstową." lightbox="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png":::

Na powyższym obrazie **#1** oznacza pogrubiony tekst w kolumnie, co oznacza bieżące pytanie. Pytanie nadrzędne to pierwszy element w wierszu. Wszystkie pytania poniżej są połączonymi parami pytań i odpowiedzi. Elementy te są wybierane, dzięki czemu można od razu przejść do innych elementów kontekstu.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Dodaj istniejącą parę pytanie-odpowiedź jako monit

Oryginalne pytanie, **Moje konto** zawiera monity monitujące, takie jak **konta i logowanie**.

![Odpowiedzi "konta i logowania" oraz monity monitujące](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Dodawanie monitu monitujące do istniejącej pary pytań i odpowiedzi, która nie jest aktualnie połączona. Ponieważ pytanie nie jest połączone z żadną parą pytań i odpowiedzi, należy zmienić ustawienie bieżącego widoku.

1. Aby połączyć istniejącą parę pytanie-odpowiedź jako monit, wybierz wiersz dla pary pytania i odpowiedzi. Aby uzyskać ręczną powierzchnię, Wyszukaj pozycję **Wyloguj** się, aby zmniejszyć listę.
1. W wierszu dla **wylogowaniu**, w kolumnie **odpowiedź** wybierz pozycję **Dodaj monit** uzupełniający.
1. W polach w oknie podręcznym **monitu krok po prawej stronie** wprowadź następujące wartości:

    |Pole|Wartość|
    |--|--|
    |Tekst wyświetlany|Wprowadź Wyłącz **urządzenie**. Jest to niestandardowy tekst do wyświetlenia w wierszu monitu.|
    |Tylko kontekstowe| Zaznacz to pole wyboru. Odpowiedź jest zwracana tylko wtedy, gdy pytanie określa kontekst.|
    |Połącz z odpowiedzią|Wprowadź **Użyj ekranu logowania,** aby znaleźć istniejącą parę pytania i odpowiedzi.|


1.  Zwracane jest jedno dopasowanie. Zaznacz tę odpowiedź jako monit, a następnie wybierz pozycję **Zapisz**.

    ![Strona "monit o zaobserwuj" (wersja zapoznawcza)](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Po dodaniu monitu kliknij pozycję **Zapisz i pouczenie** w górnym obszarze nawigacji.

### <a name="edit-the-display-text"></a>Edytuj tekst wyświetlany

Po utworzeniu monitu o podanie i istniejącej pary pytań i odpowiedzi jako **linku do odpowiedzi** możesz wprowadzić nowy **tekst wyświetlany**. Ten tekst nie zastępuje istniejącego pytania i nie dodaje nowego pytania alternatywnego. Jest ona oddzielona od tych wartości.

1. Aby edytować tekst wyświetlany, Wyszukaj i wybierz pytanie w polu **kontekst** .
1. W wierszu dla tego pytania wybierz wiersz monitu w kolumnie odpowiedź.
1. Wybierz tekst wyświetlany, który chcesz edytować, a następnie wybierz pozycję **Edytuj**.

    ![Polecenie edycji dla tekstu wyświetlanego](../media/conversational-context/edit-existing-display-text.png)

1. W oknie podręcznym **monitu o zapowiedź** Zmień istniejący tekst wyświetlany.
1. Po zakończeniu edycji wyświetlanego tekstu wybierz pozycję **Zapisz**.
1. Na górnym pasku nawigacyjnym **Zapisz i poszkol**.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Dodaj nową parę pytanie-odpowiedź jako monit

Po dodaniu nowej pary pytań i odpowiedzi do bazy wiedzy każda para powinna być połączona z istniejącym pytaniem jako monitem.

1. Na pasku narzędzi bazy wiedzy, Wyszukaj i wybierz istniejącą parę pytanie-odpowiedź dla **kont i zaloguj się**.

1. W kolumnie **odpowiedź** dla tego pytania wybierz pozycję **Dodaj monit** uzupełniający.
1. W obszarze **monitu krok po prawej stronie (wersja ZApoznawcza)** Utwórz nowy monit uzupełniający, wprowadzając następujące wartości:

    |Pole|Wartość|
    |--|--|
    |Tekst wyświetlany|*Utwórz konto systemu Windows*. Niestandardowy tekst do wyświetlenia w wierszu monitu.|
    |Tylko kontekstowe|Zaznacz to pole wyboru. Ta odpowiedź jest zwracana tylko wtedy, gdy pytanie określa kontekst.|
    |Połącz z odpowiedzią|Wprowadź następujący tekst jako odpowiedź:<br>*[Utwórz](https://account.microsoft.com/) konto systemu Windows przy użyciu nowego lub istniejącego konta e-mail*.<br>Podczas zapisywania i uczenia bazy danych ten tekst zostanie przekonwertowany. |
    |||

    ![Utwórz nowe pytanie i odpowiedź monitu](../media/conversational-context/create-child-prompt-from-parent.png)


1. Wybierz pozycję **Utwórz nowy**, a następnie wybierz pozycję **Zapisz**.

    Ta akcja powoduje utworzenie nowej pary pytanie-odpowiedź i połączenie wybranego pytania jako monitu. Kolumna **kontekst** dla obu pytań wskazuje na relację monitu o kolejne instrukcje.

1. Wybierz pozycję **Wyświetl opcje**, a następnie wybierz pozycję [**Pokaż kontekst (wersja zapoznawcza)**](#show-questions-and-answers-with-context).

    Nowe pytanie pokazuje, jak to jest połączone.

    ![Utwórz nowy monit uzupełniający](../media/conversational-context/new-qna-follow-up-prompt.png)

    Pytanie nadrzędne wyświetla nowe pytanie jako jedną z opcji.

    :::image type="content" source="../media/conversational-context/child-prompt-created.png" alt-text="Zrzut ekranu przedstawia kolumnę kontekstową dla obu pytań wskazuje, że jest to relacja monitu." lightbox="../media/conversational-context/child-prompt-created.png":::

1. Po dodaniu monitu kliknij pozycję **Zapisz i pouczenie** na górnym pasku nawigacyjnym.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Wyświetl wiele przełączanych podczas testowania monitów o kolejne instrukcje

Po przetestowaniu pytania z monitami kolejnych w okienku **test** odpowiedzi zawiera monity monitujące.

![Odpowiedź zawiera monity monitujące](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Żądanie JSON zwracające początkową odpowiedź i monity monitujące

Użyj pustego `context` obiektu, aby zażądać odpowiedzi na pytanie użytkownika i dołączyć monity monitujące.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Odpowiedź w formacie JSON zwracająca wstępną odpowiedź i monity monitujące

Poprzednia sekcja zażądała odpowiedzi i wszelkie monity monitujące dotyczące **kont i logowania**. Odpowiedź zawiera informacje o monitach, które znajdują się w *odpowiedzi [0]. Context* i tekst, który ma być wyświetlany użytkownikowi.

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts`Tablica zawiera tekst we `displayText` właściwości i `qnaId` wartość. Możesz wyświetlić te odpowiedzi jako kolejne wyświetlane wybory w przepływie konwersacji, a następnie wysłać wybrane z `qnaId` powrotem do QNA Maker w poniższym żądaniu.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Żądanie JSON zwracające niepoczątkową odpowiedź i monity monitujące

Wypełnij `context` obiekt, aby uwzględnić poprzedni kontekst.

W poniższym żądaniu JSON bieżące pytanie *służy do logowania* się, a poprzednie pytanie dotyczyło *kont i zalogowanie* się.

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Odpowiedź JSON zwracająca odpowiedź niepoczątkową i monity monitujące

Odpowiedź QnA Maker _GenerateAnswer_ JSON zawiera monity monitujące we `context` Właściwości pierwszego elementu w `answers` obiekcie:

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Zbadaj bazę wiedzy przy użyciu identyfikatora QnA Maker

Jeśli tworzysz aplikację niestandardową przy użyciu funkcji wieloskładnikowej. W odpowiedzi na pytanie początkowe zostanie zwrócona prośba o wyświetlenie wszystkich monitów i skojarzonych z nią danych `qnaId` . Teraz, gdy masz identyfikator, możesz przekazać ten komunikat w treści żądania monitowania. Jeśli treść żądania zawiera `qnaId` , a obiekt kontekstu (zawierający poprzednie właściwości QNA Maker), GenerateAnswer zwróci dokładne pytanie według identyfikatora, zamiast używać algorytmu klasyfikacji do znajdowania odpowiedzi według tekstu pytania.


## <a name="display-order-is-supported-in-the-update-api"></a>Kolejność wyświetlania jest obsługiwana w interfejsie API aktualizacji

[Wyświetlany tekst i porządek wyświetlania](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)zwrócone w odpowiedzi JSON są obsługiwane do edycji przez [interfejs API aktualizacji](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Dodawanie lub usuwanie wieloskładnikowych wierszy przy użyciu interfejsu API aktualizacji

Możesz dodawać lub usuwać wieloosiowe polecenia przy użyciu [interfejsu API aktualizacji QNA Maker](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Monity są dodawane w `context` `promptsToAdd` tablicy właściwości i `promptsToDelete` tablicy.

## <a name="export-knowledge-base-for-version-control"></a>Eksportuj bazę wiedzy na potrzeby kontroli wersji

QnA Maker obsługuje kontrolę wersji przez dołączenie do wyeksportowanego pliku kroków konwersacji wielokierunkowej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o konwersacjach kontekstowych z tego [okna dialogowego](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/07.qnamaker/QnAMaker.csproj) lub Dowiedz się więcej [na temat koncepcyjnych projektów bot w przypadku konwersacji z wieloma](/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)zmianami.

> [!div class="nextstepaction"]
> [Migrowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)