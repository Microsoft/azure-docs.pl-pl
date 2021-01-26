---
title: 'Szybki Start: QnA Maker bibliotekę kliencką dla Node.js'
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z biblioteką kliencką QnA Maker Node.js.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: fba4354fb1aae19833790e166474008dc994cd79
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792400"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

QnA Maker Node.js do:

* Tworzenie bazy wiedzy
* Aktualizowanie bazy wiedzy
* Publikowanie bazy wiedzy
* Pobierz klucz punktu końcowego środowiska przewidywania
* Zaczekaj na długo uruchomione zadanie
* Pobierz baza wiedzy
* Uzyskaj odpowiedź z bazy wiedzy
* Usuń bazę wiedzy

[Dokumentacja](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [PrzykładyNode.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

QnA Maker Node.js do:

* Tworzenie bazy wiedzy
* Aktualizowanie bazy wiedzy
* Publikowanie bazy wiedzy
* Zaczekaj na długo uruchomione zadanie
* Pobierz baza wiedzy
* Uzyskaj odpowiedź z bazy wiedzy
* Usuń bazę wiedzy

[Dokumentacja](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [PrzykładyNode.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [Node.js](https://nodejs.org).
* Gdy masz subskrypcję platformy Azure, Utwórz [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w Azure Portal, aby uzyskać klucz i zasób tworzenia. Po wdrożeniu programu wybierz pozycję **Przejdź do zasobu**.
    * Musisz mieć nazwę klucza i zasobu z tworzonego zasobu, aby połączyć aplikację z interfejs API usługi QnA Maker. Będziesz wklejać swój klucz i nazwę zasobu do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [Node.js](https://nodejs.org).
* Gdy masz subskrypcję platformy Azure, Utwórz [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w Azure Portal, aby uzyskać klucz tworzenia i punkt końcowy.
    * Uwaga: Pamiętaj, aby zaznaczyć pole wyboru **zarządzane** .
    * Po wdrożeniu zasobów QnA Maker wybierz pozycję **Przejdź do zasobu**. Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API usługi QnA Maker. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

---

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Uruchom `npm init -y` polecenie, aby utworzyć aplikację Node z `package.json` plikiem.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

Zainstaluj następujące pakiety NPM:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Zainstaluj następujące pakiety NPM:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/ms-rest-js
```

---

`package.json`Plik aplikacji jest aktualizowany z zależnościami.

Utwórz plik o nazwie index.js i zaimportuj następujące biblioteki:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Dependencies)]

---

Utwórz zmienną dla nazwy zasobu i klucza platformy Azure.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

> [!IMPORTANT]
> Przejdź do Azure Portal i Znajdź klucz i punkt końcowy dla zasobu QnA Maker utworzonego w sekcji wymagania wstępne. Zostaną one umieszczone na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**.

- Utwórz zmienne środowiskowe o nazwach QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT i QNA_MAKER_RUNTIME_ENDPOINT, aby zapisać te wartości.
- Wartość QNA_MAKER_ENDPOINT ma format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . 
- Wartość QNA_MAKER_RUNTIME_ENDPOINT ma format `https://YOUR-RESOURCE-NAME.azurewebsites.net` .
- W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [usługa Azure Key](../../../key-vault/general/overview.md) Storage udostępnia bezpieczny Magazyn kluczy.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

> [!IMPORTANT]
> Przejdź do Azure Portal i Znajdź klucz i punkt końcowy dla zasobu QnA Maker utworzonego w sekcji wymagania wstępne. Zostaną one umieszczone na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**.

- Utwórz zmienne środowiskowe o nazwie QNA_MAKER_SUBSCRIPTION_KEY i QNA_MAKER_ENDPOINT, aby zapisać te wartości.
- Wartość QNA_MAKER_ENDPOINT ma format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . 
- W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [usługa Azure Key](../../../key-vault/general/overview.md) Storage udostępnia bezpieczny Magazyn kluczy.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Resourcevariables)]

---

## <a name="object-models"></a>Modele obiektów

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[QNA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) używa dwóch różnych modeli obiektów:
* **[QnAMakerClient](#qnamakerclient-object-model)** jest obiektem, który umożliwia tworzenie, publikowanie i pobieranie bazy wiedzy oraz zarządzanie nią.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** to obiekt służący do wykonywania zapytań względem bazy wiedzy z interfejsem API GenerateAnswer i wysyłania nowych sugerowanych pytań za pomocą interfejsu API uczenia (w ramach [aktywnego uczenia](../concepts/active-learning-suggestions.md)).

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[QNA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) używa następującego modelu obiektów:
* **[QnAMakerClient](#qnamakerclient-object-model)** jest obiektem do tworzenia, zarządzania, publikowania, pobierania i wykonywania zapytań dotyczących bazy wiedzy.

---

### <a name="qnamakerclient-object-model"></a>Model obiektów QnAMakerClient

QnA Maker tworzenia klienta jest obiektem [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) , który jest uwierzytelniany na platformie Azure przy użyciu poświadczeń, które zawierają klucz.

Po utworzeniu klienta należy użyć [bazy](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) wiedzy do tworzenia i publikowania bazy wiedzy oraz zarządzania nią.

Zarządzaj bazą wiedzy, wysyłając obiekt JSON. W przypadku operacji natychmiastowych Metoda zwykle zwraca obiekt JSON wskazujący stan. W przypadku długotrwałych operacji odpowiedź jest IDENTYFIKATORem operacji. Wywołaj metodę [Client. Operations. Details](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--msrest-requestoptionsbase-) z identyfikatorem operacji, aby określić [stan żądania](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest).

### <a name="qnamakerruntimeclient-object-model"></a>Model obiektów QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

QnA Maker predykcyjny jest obiektem QnAMakerRuntimeClient, który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz środowiska uruchomieniowego przewidywania, zwrócony z wywołania klienta tworzenia i [klienta. EndpointKeys. GetKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest#getkeys-msrest-requestoptionsbase-) po opublikowaniu bazy wiedzy.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Zasób zarządzany QnA Maker nie wymaga użycia obiektu QnAMakerRuntimeClient. Zamiast tego należy wywołać [generateAnswer](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#generateAnswer_string__QueryDTO__msRest_RequestOptionsBase_) bezpośrednio dla obiektu [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) .

---

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta QnA Maker dla platformy .NET:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

* [Uwierzytelnianie klienta tworzenia](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Tworzenie bazy wiedzy](#create-a-knowledge-base)
* [Aktualizowanie bazy wiedzy](#update-a-knowledge-base)
* [Pobieranie bazy wiedzy](#download-a-knowledge-base)
* [Publikowanie bazy wiedzy](#publish-a-knowledge-base)
* [Usuwanie bazy wiedzy](#delete-a-knowledge-base)
* [Pobierz klucz środowiska uruchomieniowego zapytania](#get-query-runtime-key)
* [Pobierz stan operacji](#get-status-of-an-operation)
* [Uwierzytelnianie klienta środowiska uruchomieniowego zapytań](#authenticate-the-runtime-for-generating-an-answer)
* [Generowanie odpowiedzi z bazy wiedzy](#generate-an-answer-from-the-knowledge-base)

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

* [Uwierzytelnianie klienta tworzenia](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Tworzenie bazy wiedzy](#create-a-knowledge-base)
* [Aktualizowanie bazy wiedzy](#update-a-knowledge-base)
* [Pobieranie bazy wiedzy](#download-a-knowledge-base)
* [Publikowanie bazy wiedzy](#publish-a-knowledge-base)
* [Usuwanie bazy wiedzy](#delete-a-knowledge-base)
* [Pobierz stan operacji](#get-status-of-an-operation)
* [Generowanie odpowiedzi z bazy wiedzy](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Uwierzytelnianie klienta w celu tworzenia bazy wiedzy

Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt serviceclientcredentials z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) .

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy zapisuje pary pytań i odpowiedzi dla obiektu [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) z trzech źródeł:

* W przypadku **zawartości redakcyjnej** Użyj obiektu [QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) .
    * Aby użyć metadanych i monitów uzupełniających, użyj kontekstu redakcyjnego, ponieważ te dane są dodawane na indywidualnym poziomie pary QnA.
* Dla **plików** Użyj obiektu [FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) . FileDTO zawiera nazwę pliku, a także publiczny adres URL w celu uzyskania dostępu do pliku.
* W przypadku **adresów URL** Użyj listy ciągów do reprezentowania publicznie dostępnych adresów URL.

Krok tworzenia obejmuje również właściwości bazy wiedzy:
* `defaultAnswerUsedForExtraction` -co jest zwracane, gdy nie zostanie znaleziona żadna odpowiedź
* `enableHierarchicalExtraction` -Automatycznie twórz relacje wiersza między wyodrębnionymi parami QnA
* `language` — podczas tworzenia pierwszej bazy wiedzy zasobu Ustaw język, który ma być używany w indeksie Azure Search.

Wywołaj metodę [Create](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) z informacjami bazy wiedzy. Informacje bazy wiedzy są zasadniczo obiektem JSON.

Gdy metoda Create zwraca wartość, Przekaż zwrócony identyfikator operacji do metody [wait_for_operation](#get-status-of-an-operation) , aby sondować stan. Metoda wait_for_operation zwraca po zakończeniu operacji. Przeanalizuj `resourceLocation` wartość nagłówka zwróconej operacji, aby uzyskać nowy identyfikator bazy wiedzy.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=CreateKBMethod)]

---

Upewnij się, że [`wait_for_operation`](#get-status-of-an-operation) Funkcja, do której odwołuje się powyższy kod, zostanie uwzględniona w celu pomyślnego utworzenia bazy wiedzy.

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Bazę wiedzy można zaktualizować, przekazując informacje o IDENTYFIKATORze bazy wiedzy i [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) zawierającym obiekty [Add](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [Update](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)i [delete](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO do metody [Update](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) . DTO są również zasadniczo obiektami JSON. Użyj metody [wait_for_operation](#get-status-of-an-operation) , aby określić, czy aktualizacja zakończyła się pomyślnie.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=UpdateKBMethod)]

---

Upewnij się, że Dołącz [`wait_for_operation`](#get-status-of-an-operation) funkcję, do której odwołuje się powyższy kod, aby pomyślnie zaktualizować bazę wiedzy.

## <a name="download-a-knowledge-base"></a>Pobieranie bazy wiedzy

Użyj metody [pobierania](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#download-string--models-environmenttype--msrest-requestoptionsbase-) , aby pobrać bazę danych jako listę [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto?view=azure-node-latest). _Nie_ jest to równoznaczne z eksportem QNA Maker portalu ze strony **ustawień** , ponieważ wynik tej metody nie jest plikiem TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy przy użyciu metody [Publish](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) . Obejmuje to bieżący zapisany i szkolony model, do którego odwołuje się identyfikator bazy wiedzy, i publikuje go w punkcie końcowym. Sprawdź kod odpowiedzi HTTP, aby sprawdzić, czy publikowanie zakończyło się pomyślnie.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Wykonywanie zapytań w bazie wiedzy

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Pobierz klucz środowiska uruchomieniowego zapytania

Po opublikowaniu bazy wiedzy potrzebny jest klucz środowiska uruchomieniowego zapytania, aby wykonać zapytanie dotyczące środowiska uruchomieniowego. Nie jest to ten sam klucz, który służy do tworzenia oryginalnego obiektu klienta.

Aby uzyskać klasę [EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto?view=azure-node-latest) , należy użyć metody [EndpointKeys. GetKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest) .

Użyj jednej z właściwości klucza zwracanego w obiekcie, aby wykonać zapytanie dotyczące bazy wiedzy.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Uwierzytelnianie środowiska uruchomieniowego w celu wygenerowania odpowiedzi

Utwórz QnAMakerRuntimeClient, aby wysłać zapytanie do bazy wiedzy w celu wygenerowania odpowiedzi lub pouczenia się z aktywnego uczenia.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

Skorzystaj z QnAMakerRuntimeClient, aby uzyskać odpowiedzi ze wiedzy lub wysłać nowe sugerowane pytania do bazy wiedzy w celu uzyskania [aktywnego uczenia](../concepts/active-learning-suggestions.md).

### <a name="generate-an-answer-from-the-knowledge-base"></a>Generowanie odpowiedzi z bazy wiedzy

Wygeneruj odpowiedź z opublikowanej bazy wiedzy przy użyciu metody RuntimeClient. Runtime. generateAnswer. Ta metoda akceptuje identyfikator bazy wiedzy i [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Uzyskuj dostęp do dodatkowych właściwości QueryDTO, takich jak Top i Context, które mają być używane w rozmowie bot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Generowanie odpowiedzi z bazy wiedzy

Wygeneruj odpowiedź z opublikowanej bazy wiedzy przy użyciu metody QnAMakerClient. Base. generateAnswer. Ta metoda akceptuje identyfikator bazy wiedzy i [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Uzyskuj dostęp do dodatkowych właściwości QueryDTO, takich jak Top i Context, które mają być używane w rozmowie bot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=GenerateAnswer)]

---

Jest to prosty przykład zapytania dotyczące bazy wiedzy. Aby zrozumieć zaawansowane scenariusze zapytań, zapoznaj się z [innymi przykładami zapytań](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń bazę wiedzy przy użyciu metody [delete](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) z parametrem identyfikatora bazy wiedzy.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Pobierz stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, mogą trwać wystarczająco długo, aby nie czekać na zakończenie procesu, zostanie zwrócona [operacja](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) . Użyj [identyfikatora operacji](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) z operacji do sondowania (z logiką ponownych prób), aby określić stan oryginalnej metody.

Wywołanie _delayTimer_ w poniższym bloku kodu służy do symulowania logiki ponawiania. Zamień ten program na własną logikę ponawiania.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node index.js` polecenia z katalogu aplikacji.

```console
node index.js
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js).

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js).

---