---
title: 'Szybki Start: QnA Makera Biblioteka kliencka dla platformy .NET'
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z biblioteką klienta QnA Maker dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: d853f4729d126590df711554f60efe6d9f2194cc
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569496"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

Użyj biblioteki klienta QnA Maker dla platformy .NET, aby:

 * Tworzenie bazy wiedzy
 * Aktualizowanie bazy wiedzy
 * Publikowanie bazy wiedzy
 * Pobierz klucz punktu końcowego środowiska przewidywania
 * Zaczekaj na długo uruchomione zadanie
 * Pobierz baza wiedzy
 * Uzyskaj odpowiedź z bazy wiedzy
 * Usuwanie bazy wiedzy

[Dokumentacja](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/2.0.1)  |  [Przykłady w języku C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Użyj biblioteki klienta QnA Maker dla platformy .NET, aby:

 * Tworzenie bazy wiedzy
 * Aktualizowanie bazy wiedzy
 * Publikowanie bazy wiedzy
 * Zaczekaj na długo uruchomione zadanie
 * Pobierz baza wiedzy
 * Uzyskaj odpowiedź z bazy wiedzy
 * Usuwanie bazy wiedzy

[Dokumentacja](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1)  |  [Przykłady w języku C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/) lub bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Gdy masz subskrypcję platformy Azure, Utwórz [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w Azure Portal, aby uzyskać swój klucz tworzenia i nazwę zasobu. Po wdrożeniu programu wybierz pozycję **Przejdź do zasobu**.
    * Musisz mieć nazwę klucza i zasobu z tworzonego zasobu, aby połączyć aplikację z interfejs API usługi QnA Maker. Będziesz wklejać swój klucz i nazwę zasobu do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/) lub bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Gdy masz subskrypcję platformy Azure, Utwórz [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w Azure Portal, aby uzyskać klucz tworzenia i punkt końcowy.
    * Uwaga: Pamiętaj, aby zaznaczyć pole wyboru **zarządzane** .
    * Po wdrożeniu zasobów QnA Maker wybierz pozycję **Przejdź do zasobu**. Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API usługi QnA Maker. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

---

## <a name="setting-up"></a>Konfigurowanie

### <a name="visual-studio-ide"></a>Visual Studio IDE

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

Za pomocą programu Visual Studio Utwórz aplikację .NET Core i zainstaluj bibliotekę kliencką, klikając rozwiązanie prawym przyciskiem myszy w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj**, a następnie wyszukaj `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker` . Wybierz wersję `2.0.1` , a następnie **Zainstaluj**.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Za pomocą programu Visual Studio Utwórz aplikację .NET Core i zainstaluj bibliotekę kliencką, klikając rozwiązanie prawym przyciskiem myszy w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj**, zaznacz pozycję **Uwzględnij wersję wstępną** i Wyszukaj `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker` . Wybierz wersję `3.0.0-preview.1` , a następnie **Zainstaluj**.

---

### <a name="cli"></a>Interfejs wiersza polecenia

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

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.1
```

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), która zawiera przykłady kodu w tym przewodniku Szybki Start.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs), która zawiera przykłady kodu w tym przewodniku Szybki Start.

---

### <a name="using-directives"></a>Dyrektywy using

W katalogu projektu Otwórz plik *program.cs* i Dodaj następujące `using` dyrektywy:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

---

### <a name="subscription-key-and-resource-endpoints"></a>Klucz subskrypcji i punkty końcowe zasobów

W `Main` metodzie aplikacji Dodaj zmienne i kod, które przedstawiono w poniższej sekcji, aby użyć typowych zadań w tym przewodniku Szybki Start.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

> [!IMPORTANT]
> Przejdź do Azure Portal i Znajdź klucz i punkt końcowy dla zasobu QnA Maker utworzonego w sekcji wymagania wstępne. Zostaną one umieszczone na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. Korzystamy z klucza subskrypcji i tworzenia klucza interchangably. Aby uzyskać więcej informacji na temat klucza tworzenia, należy użyć [klawiszy w QNA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/azure-resources?tabs=v1#keys-in-qna-maker).

- Utwórz zmienne środowiskowe o nazwach QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT i QNA_MAKER_RUNTIME_ENDPOINT, aby zapisać te wartości.
- Wartość QNA_MAKER_ENDPOINT ma format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . 
- Wartość QNA_MAKER_RUNTIME_ENDPOINT ma format `https://YOUR-RESOURCE-NAME.azurewebsites.net` .
- W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [usługa Azure Key](../../../key-vault/general/overview.md) Storage udostępnia bezpieczny Magazyn kluczy.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

> [!IMPORTANT]
> Przejdź do Azure Portal i Znajdź klucz i punkt końcowy dla zasobu QnA Maker utworzonego w sekcji wymagania wstępne. Zostaną one umieszczone na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. Korzystamy z klucza subskrypcji i tworzenia klucza interchangably. Aby uzyskać więcej informacji na temat klucza tworzenia, należy użyć [klawiszy w QNA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/azure-resources?tabs=v2#keys-in-qna-maker-managed-preview).

- Utwórz zmienne środowiskowe o nazwie QNA_MAKER_SUBSCRIPTION_KEY i QNA_MAKER_ENDPOINT, aby zapisać te wartości.
- Wartość QNA_MAKER_ENDPOINT ma format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . 
- W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [usługa Azure Key](../../../key-vault/general/overview.md) Storage udostępnia bezpieczny Magazyn kluczy.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

---


## <a name="object-models"></a>Modele obiektów

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[QNA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) używa dwóch różnych modeli obiektów:
* **[QnAMakerClient](#qnamakerclient-object-model)** jest obiektem, który umożliwia tworzenie, publikowanie i pobieranie bazy wiedzy oraz zarządzanie nią.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** to obiekt służący do wykonywania zapytań względem bazy wiedzy z interfejsem API GenerateAnswer i wysyłania nowych sugerowanych pytań za pomocą interfejsu API uczenia (w ramach [aktywnego uczenia](../concepts/active-learning-suggestions.md)).

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[QNA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) używa następującego modelu obiektów:
* **[QnAMakerClient](#qnamakerclient-object-model)** jest obiektem do tworzenia, zarządzania, publikowania, pobierania i wykonywania zapytań dotyczących bazy wiedzy.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Model obiektów QnAMakerClient

QnA Maker tworzenia klienta jest obiektem [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) , który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz.

Po utworzeniu klienta Użyj właściwości [Baza wiedzy](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) , aby utworzyć i opublikować bazę wiedzy oraz zarządzać nią.

Zarządzaj bazą wiedzy, wysyłając obiekt JSON. W przypadku operacji natychmiastowych Metoda zwykle zwraca obiekt JSON wskazujący stan. W przypadku długotrwałych operacji odpowiedź jest IDENTYFIKATORem operacji. Wywoływanie [klienta. Operations. GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync) — Metoda z identyfikatorem operacji w celu określenia [stanu żądania](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype).

### <a name="qnamakerruntimeclient-object-model"></a>Model obiektów QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

Prognoza QnA Maker jest obiektem [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) , który jest uwierzytelniany na platformie Azure przy użyciu elementu Microsoft. Rest. serviceclientcredentials, który zawiera klucz środowiska uruchomieniowego przewidywania, zwrócony z wywołania klienta tworzenia `client.EndpointKeys.GetKeys` po opublikowaniu bazy wiedzy.

Użyj metody [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) , aby uzyskać odpowiedź z środowiska uruchomieniowego zapytań.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Zasób zarządzany QnA Maker nie wymaga użycia obiektu **QnAMakerRuntimeClient** . Zamiast tego należy wywołać [QnAMakerClient.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase)Knowledge Base. [GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) .

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

Utwórz wystąpienie obiektu klienta z kluczem i użyj go razem z zasobem, aby utworzyć punkt końcowy w celu utworzenia [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) z punktem końcowym i kluczem. Utwórz obiekt [Serviceclientcredentials](/dotnet/api/microsoft.rest.serviceclientcredentials) .

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy zapisuje pary pytań i odpowiedzi dla obiektu [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) z trzech źródeł:

* W przypadku **zawartości redakcyjnej** Użyj obiektu [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto) .
    * Aby użyć metadanych i monitów uzupełniających, użyj kontekstu redakcyjnego, ponieważ te dane są dodawane na indywidualnym poziomie pary QnA.
* Dla **plików** Użyj obiektu [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto) . FileDTO zawiera nazwę pliku, a także publiczny adres URL w celu uzyskania dostępu do pliku.
* W przypadku **adresów URL** Użyj listy ciągów do reprezentowania publicznie dostępnych adresów URL.

Krok tworzenia obejmuje również właściwości bazy wiedzy:
* `defaultAnswerUsedForExtraction` -co jest zwracane, gdy nie zostanie znaleziona żadna odpowiedź
* `enableHierarchicalExtraction` -Automatycznie twórz relacje wiersza między wyodrębnionymi parami QnA
* `language` — podczas tworzenia pierwszej bazy wiedzy zasobu Ustaw język, który ma być używany w indeksie Azure Search.

Wywołaj metodę [Noasync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync) , a następnie Przekaż identyfikator zwróconej operacji do metody [MonitorOperation](#get-status-of-an-operation) w celu sondowania stanu.

Ostatni wiersz poniższego kodu zwraca identyfikator bazy wiedzy z odpowiedzi z MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

---

Upewnij się, że Dołącz [`MonitorOperation`](#get-status-of-an-operation) funkcję, do której odwołuje się powyższy kod, w celu pomyślnego utworzenia bazy wiedzy.

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Bazę wiedzy można zaktualizować, przekazując informacje o IDENTYFIKATORze bazy wiedzy i [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) zawierającym obiekty [Add](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [Update](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate)i [delete](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) DTO do metody [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync) . Użyj metody [MonitorOperation](#get-status-of-an-operation) , aby określić, czy aktualizacja zakończyła się pomyślnie.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

---

Upewnij się, że Dołącz [`MonitorOperation`](#get-status-of-an-operation) funkcję, do której odwołuje się powyższy kod, aby pomyślnie zaktualizować bazę wiedzy.

## <a name="download-a-knowledge-base"></a>Pobieranie bazy wiedzy

Użyj metody [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync) , aby pobrać bazę danych jako listę [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto). _Nie_ jest to równoznaczne z eksportem QNA Maker portalu ze strony **ustawień** , ponieważ wynik tej metody nie jest plikiem.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy przy użyciu metody [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync) . Obejmuje to bieżący zapisany i szkolony model, do którego odwołuje się identyfikator bazy wiedzy, i publikuje go w punkcie końcowym. Jest to konieczne, aby wykonać zapytanie dotyczące bazy danych.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

## <a name="get-query-runtime-key"></a>Pobierz klucz środowiska uruchomieniowego zapytania

Po opublikowaniu bazy wiedzy potrzebny jest klucz środowiska uruchomieniowego zapytania, aby wykonać zapytanie dotyczące środowiska uruchomieniowego. Nie jest to ten sam klucz, który służy do tworzenia oryginalnego obiektu klienta.

Aby uzyskać klasę [EndpointKeysDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto) , należy użyć metody [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) .

Użyj jednej z właściwości klucza zwracanego w obiekcie, aby wykonać zapytanie dotyczące bazy wiedzy.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey)]

Klucz czasu wykonywania jest niezbędny do przeszukiwania bazy danych.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Uwierzytelnianie środowiska uruchomieniowego w celu wygenerowania odpowiedzi

Utwórz [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) , aby wysłać zapytanie do bazy wiedzy w celu wygenerowania odpowiedzi lub pouczenia się z aktywnego uczenia.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Użyj QnAMakerRuntimeClient do:
* uzyskaj odpowiedź z bazy wiedzy
* Aby wysłać nowe sugerowane pytania do bazy wiedzy na potrzeby [aktywnej nauki](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generowanie odpowiedzi z bazy wiedzy

Generuj odpowiedź z opublikowanej bazy wiedzy przy użyciu [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). [GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync) . Ta metoda akceptuje identyfikator bazy wiedzy i [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Uzyskuj dostęp do dodatkowych właściwości QueryDTO, takich jak [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) i [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context) , które mają być używane w rozmowie bot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generowanie odpowiedzi z bazy wiedzy

Generuj odpowiedź z opublikowanej bazy wiedzy przy użyciu [QnAMakerClient.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase)Knowledge Base. [GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) . Ta metoda akceptuje identyfikator bazy wiedzy i [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Uzyskuj dostęp do dodatkowych właściwości QueryDTO, takich jak [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) i [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest) , które mają być używane w programie Chat bot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

---

Jest to prosty przykład zapytania dotyczącego bazy wiedzy. Aby zrozumieć zaawansowane scenariusze zapytań, zapoznaj się z [innymi przykładami zapytań](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń bazę wiedzy przy użyciu metody [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) z parametrem identyfikatora bazy wiedzy.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Pobierz stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, mogą trwać wystarczająco długo, aby nie czekać na zakończenie procesu, zostanie zwrócona [operacja](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation) . Użyj [identyfikatora operacji](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) z operacji do sondowania (z logiką ponownych prób), aby określić stan oryginalnej metody.

_Pętla_ i _Task. Delay_ w poniższym bloku kodu służą do symulowania logiki ponawiania. Należy je zastąpić własną logiką ponownych prób.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `dotnet run` polecenia z katalogu aplikacji.

```dotnetcli
dotnet run
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart).

---
