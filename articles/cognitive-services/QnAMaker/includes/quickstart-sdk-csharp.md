---
title: 'Szybki Start: QnA Makera Biblioteka kliencka dla platformy .NET'
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z biblioteką klienta QnA Maker dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 6170d9fc7f630d44fd7c1f3bfff5ed581dbf9cdd
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386662"
---
Użyj biblioteki klienta QnA Maker dla platformy .NET, aby:

 * Tworzenie bazy wiedzy
 * Aktualizowanie bazy wiedzy
 * Publikowanie bazy wiedzy
 * Pobierz klucz punktu końcowego środowiska przewidywania
 * Zaczekaj na długo uruchomione zadanie
 * Pobierz baza wiedzy
 * Uzyskiwanie odpowiedzi
 * Usuwanie bazy wiedzy

[Dokumentacja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [Przykłady w języku C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/) lub bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Gdy masz subskrypcję platformy Azure, Utwórz [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w Azure Portal, aby uzyskać swój klucz tworzenia i nazwę zasobu. Po wdrożeniu programu wybierz pozycję **Przejdź do zasobu**.
    * Musisz mieć nazwę klucza i zasobu z tworzonego zasobu, aby połączyć aplikację z interfejs API usługi QnA Maker. Będziesz wklejać swój klucz i nazwę zasobu do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Za pomocą programu Visual Studio Utwórz aplikację .NET Core i zainstaluj bibliotekę kliencką, klikając rozwiązanie prawym przyciskiem myszy w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj** , zaznacz pozycję **Uwzględnij wersję wstępną** i Wyszukaj `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker` . Wybierz wersję `2.0.0-preview.1` , a następnie **Zainstaluj**.

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `qna-maker-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu aplikacji zainstaluj QnA Makerą bibliotekę kliencką dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.0-preview.1
```


---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), która zawiera przykłady kodu w tym przewodniku Szybki Start.

W katalogu projektu Otwórz plik *program.cs* i Dodaj następujące `using` dyrektywy:

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

W `Main` metodzie aplikacji Dodaj zmienne i kod, które przedstawiono w poniższej sekcji, aby użyć typowych zadań w tym przewodniku Szybki Start.

> [!IMPORTANT]
> Przejdź do Azure Portal i Znajdź klucz i punkt końcowy dla zasobu QnA Maker utworzonego w sekcji wymagania wstępne. Zostaną one umieszczone na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**.
> Do utworzenia bazy danych jest potrzebny cały klucz. Potrzebna jest tylko nazwa zasobu z punktu końcowego. Format to `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` .
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [usługa Azure Key](https://docs.microsoft.com/azure/key-vault/key-vault-overview) Storage udostępnia bezpieczny Magazyn kluczy.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


## <a name="object-models"></a>Modele obiektów

QnA Maker używa dwóch różnych modeli obiektów:
* **[QnAMakerClient](#qnamakerclient-object-model)** jest obiektem, który umożliwia tworzenie, publikowanie i pobieranie bazy wiedzy oraz zarządzanie nią.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** to obiekt służący do wykonywania zapytań względem bazy wiedzy z interfejsem API GenerateAnswer i wysyłania nowych sugerowanych pytań za pomocą interfejsu API uczenia (w ramach [aktywnego uczenia](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Model obiektów QnAMakerClient

QnA Maker tworzenia klienta jest obiektem [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) , który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz.

Po utworzeniu klienta Użyj właściwości [Baza wiedzy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) , aby utworzyć i opublikować bazę wiedzy oraz zarządzać nią.

Zarządzaj bazą wiedzy, wysyłając obiekt JSON. W przypadku operacji natychmiastowych Metoda zwykle zwraca obiekt JSON wskazujący stan. W przypadku długotrwałych operacji odpowiedź jest IDENTYFIKATORem operacji. Wywoływanie [klienta. Operations. GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) — Metoda z identyfikatorem operacji w celu określenia [stanu żądania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).

### <a name="qnamakerruntimeclient-object-model"></a>Model obiektów QnAMakerRuntimeClient

Prognoza QnA Maker jest obiektem [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) , który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz środowiska uruchomieniowego przewidywania, zwrócony z wywołania klienta tworzenia `client.EndpointKeys.GetKeys` po opublikowaniu bazy wiedzy.

Użyj metody [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) , aby uzyskać odpowiedź z środowiska uruchomieniowego zapytań.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta QnA Maker dla platformy .NET:

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



## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Uwierzytelnianie klienta w celu tworzenia bazy wiedzy

Utwórz wystąpienie obiektu klienta z kluczem i użyj go razem z zasobem, aby utworzyć punkt końcowy w celu utworzenia [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) z punktem końcowym i kluczem. Utwórz obiekt [Serviceclientcredentials](https://docs.microsoft.com/dotnet/api/microsoft.rest.serviceclientcredentials?view=azure-dotnet) .

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy zapisuje pary pytań i odpowiedzi dla obiektu [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) z trzech źródeł:

* W przypadku **zawartości redakcyjnej** Użyj obiektu [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) .
    * Aby użyć metadanych i monitów uzupełniających, użyj kontekstu redakcyjnego, ponieważ te dane są dodawane na indywidualnym poziomie pary QnA.
* Dla **plików** Użyj obiektu [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) . FileDTO zawiera nazwę pliku, a także publiczny adres URL w celu uzyskania dostępu do pliku.
* W przypadku **adresów URL** Użyj listy ciągów do reprezentowania publicznie dostępnych adresów URL.

Krok tworzenia obejmuje również właściwości bazy wiedzy:
* `defaultAnswerUsedForExtraction` -co jest zwracane, gdy nie zostanie znaleziona żadna odpowiedź
* `enableHierarchicalExtraction` -Automatycznie twórz relacje wiersza między wyodrębnionymi parami QnA
* `language` — podczas tworzenia pierwszej bazy wiedzy zasobu Ustaw język, który ma być używany w indeksie Azure Search.

Wywołaj metodę [Noasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) , a następnie Przekaż identyfikator zwróconej operacji do metody [MonitorOperation](#get-status-of-an-operation) w celu sondowania stanu.

Ostatni wiersz poniższego kodu zwraca identyfikator bazy wiedzy z odpowiedzi z MonitorOperation.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=31)]

Upewnij się, że Dołącz [`MonitorOperation`](#get-status-of-an-operation) funkcję, do której odwołuje się powyższy kod, w celu pomyślnego utworzenia bazy wiedzy.

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Bazę wiedzy można zaktualizować, przekazując informacje o IDENTYFIKATORze bazy wiedzy i [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) zawierającym obiekty [Add](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [Update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)i [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO do metody [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) . Użyj metody [MonitorOperation](#get-status-of-an-operation) , aby określić, czy aktualizacja zakończyła się pomyślnie.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

Upewnij się, że Dołącz [`MonitorOperation`](#get-status-of-an-operation) funkcję, do której odwołuje się powyższy kod, aby pomyślnie zaktualizować bazę wiedzy.

## <a name="download-a-knowledge-base"></a>Pobieranie bazy wiedzy

Użyj metody [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) , aby pobrać bazę danych jako listę [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). _Nie_ jest to równoznaczne z eksportem QNA Maker portalu ze strony **ustawień** , ponieważ wynik tej metody nie jest plikiem.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3)]

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy przy użyciu metody [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) . Obejmuje to bieżący zapisany i szkolony model, do którego odwołuje się identyfikator bazy wiedzy, i publikuje go w punkcie końcowym. Jest to konieczne, aby wykonać zapytanie dotyczące bazy danych.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]



## <a name="get-query-runtime-key"></a>Pobierz klucz środowiska uruchomieniowego zapytania

Po opublikowaniu bazy wiedzy potrzebny jest klucz środowiska uruchomieniowego zapytania, aby wykonać zapytanie dotyczące środowiska uruchomieniowego. Nie jest to ten sam klucz, który służy do tworzenia oryginalnego obiektu klienta.

Aby uzyskać klasę [EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet) , należy użyć metody [EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) .

Użyj jednej z właściwości klucza zwracanego w obiekcie, aby wykonać zapytanie dotyczące bazy wiedzy.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

Klucz czasu wykonywania jest niezbędny do przeszukiwania bazy danych.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Uwierzytelnianie środowiska uruchomieniowego w celu wygenerowania odpowiedzi

Utwórz [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) , aby wysłać zapytanie do bazy wiedzy w celu wygenerowania odpowiedzi lub pouczenia się z aktywnego uczenia.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Użyj QnAMakerRuntimeClient do:
* uzyskaj odpowiedź z bazy wiedzy
* Aby wysłać nowe sugerowane pytania do bazy wiedzy na potrzeby [aktywnej nauki](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generowanie odpowiedzi z bazy wiedzy

### <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Generuj odpowiedź z opublikowanej bazy wiedzy przy użyciu [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) . Ta metoda akceptuje identyfikator bazy wiedzy i [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Uzyskuj dostęp do dodatkowych właściwości QueryDTO, takich jak [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) i [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) , które mają być używane w rozmowie bot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]


### <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Generuj odpowiedź z opublikowanej bazy wiedzy przy użyciu [QnAMakerClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet-preview)Knowledge Base. [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync?view=azure-dotnet-preview) . Ta metoda akceptuje identyfikator bazy wiedzy i [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet-preview). Uzyskuj dostęp do dodatkowych właściwości QueryDTO, takich jak [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) i [AnswerSpanRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest) , które mają być używane w programie Chat bot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswerPreview&highlight=3)]

Jest to prosty przykład zapytania dotyczącego bazy wiedzy. Aby zrozumieć zaawansowane scenariusze zapytań, zapoznaj się z [innymi przykładami zapytań](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).


## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń bazę wiedzy przy użyciu metody [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) z parametrem identyfikatora bazy wiedzy.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Pobierz stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, mogą trwać wystarczająco długo, aby nie czekać na zakończenie procesu, zostanie zwrócona [operacja](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) . Użyj [identyfikatora operacji](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) z operacji do sondowania (z logiką ponownych prób), aby określić stan oryginalnej metody.

_Pętla_ i _Task. Delay_ w poniższym bloku kodu służą do symulowania logiki ponawiania. Należy je zastąpić własną logiką ponownych prób.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `dotnet run` polecenia z katalogu aplikacji.

```dotnetcli
dotnet run
```

Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).