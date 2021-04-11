---
title: Sprawdzanie tekstu względem listy terminów niestandardowych w języku C# — Content Moderator
titleSuffix: Azure Cognitive Services
description: Jak moderować tekst przy użyciu list terminów niestandardowych za pomocą zestawu Content Moderator SDK dla języka C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: e32f18e7ff9e222586b1c9ca328e0f6c4eb3ba51
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055556"
---
# <a name="check-text-against-a-custom-term-list-in-c"></a>Sprawdzanie tekstu względem listy terminów niestandardowych w języku C#

Domyślna globalna lista terminów w usługach Azure Content Moderator wystarcza w przypadku większości potrzeb moderowania zawartości. Jednak może być konieczne sprawdzanie terminów, które są specyficzne dla organizacji. Na przykład warto oznaczyć tagiem nazwy konkurencji w celu dalszego przeglądu. 

[Zestaw SDK Content Moderator dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) może służyć do tworzenia niestandardowych list terminów do wykorzystania przy użyciu interfejsu API moderowania tekstów.

Ten artykuł zawiera informacje i przykłady kodu, aby początkowo ułatwić korzystanie z zestawu SDK Content Moderator dla platformy .NET do następujących zastosowań:
- Tworzenie listy.
- Dodawanie terminów do listy.
- Sprawdzanie terminów względem terminów na liście.
- Usuwanie terminów z listy.
- Usuwanie listy.
- Edycja informacji na liście.
- Odświeżanie indeksu, aby zmiany na liście były uwzględnione w nowym skanowaniu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/). 

## <a name="sign-up-for-content-moderator-services"></a>Zarejestruj się w usługach Content Moderator

Aby użyć usług Content Moderator za pomocą interfejsu API REST lub zestawu SDK, potrzebujesz klucza subskrypcji. Zasubskrybuj usługę Content Moderator w witrynie [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator), aby go uzyskać.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nowy projekt **Aplikacja konsoli (.NET Framework)** do rozwiązania.

1. Nazwij projekt **TermLists**. Wybierz ten projekt jako pojedynczy projekt startowy rozwiązania.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet do projektu TermLists:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizowanie programu za pomocą instrukcji

Dodaj następujące instrukcje `using`.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Tworzenie klienta usługi Content Moderator

Dodaj następujący kod, aby utworzyć klienta usługi Content Moderator dla Twojej subskrypcji. Zaktualizuj `AzureEndpoint` pola i `CMSubscriptionKey` przy użyciu wartości adresu URL punktu końcowego i klucza subskrypcji. Te informacje można znaleźć na karcie **Szybki Start** zasobu w Azure Portal.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="add-private-properties"></a>Dodawanie właściwości prywatnych

Dodaj następujące właściwości prywatnych do przestrzeni nazw TermLists w klasie Program.

```csharp
/// <summary>
/// The language of the terms in the term lists.
/// </summary>
private const string lang = "eng";

/// <summary>
/// The minimum amount of time, in milliseconds, to wait between calls
/// to the Content Moderator APIs.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>Tworzenie listy terminów

Do tworzenia listy terminów służy metoda **ContentModeratorClient.ListManagementTermLists.Create**. Pierwszy parametr metody **Create** to ciąg zawierający typ MIME, który powinien mieć wartość „application/json”. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). Drugi parametr to obiekt **Body**, który zawiera nazwę i opis nowej listy terminów.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list terminów**, a poszczególne listy **nie mogą przekraczać 10 000 terminów**.

Dodaj następującą definicję metody do przestrzeni nazw TermLists w klasie Program.

> [!NOTE]
> Klucz usługi Content Moderator ma limit liczby żądań na sekundę (RPS), a w razie przekroczenia tego limitu zestaw SDK zgłasza wyjątek z kodem błędu 429. Limit klucza warstwy bezpłatnej wynosi 1 RPS.

```csharp
/// <summary>
/// Creates a new term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The term list ID.</returns>
static string CreateTermList (ContentModeratorClient client)
{
    Console.WriteLine("Creating term list.");

    Body body = new Body("Term list name", "Term list description");
    TermList list = client.ListManagementTermLists.Create("application/json", body);
    if (false == list.Id.HasValue)
    {
        throw new Exception("TermList.Id value missing.");
    }
    else
    {
        string list_id = list.Id.Value.ToString();
        Console.WriteLine("Term list created. ID: {0}.", list_id);
        Thread.Sleep(throttleRate);
        return list_id;
    }
}
```

## <a name="update-term-list-name-and-description"></a>Aktualizacja nazwy i opisu listy terminów

Do aktualizacji informacji o liście terminów służy metoda **ContentModeratorClient.ListManagementTermLists.Update**. Pierwszy parametr metody **Update** to identyfikator listy terminów. Drugi parametr to typ MIME, który powinien mieć wartość „application/json”. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). Trzeci parametr to obiekt **Body**, który zawiera nową nazwę i nowy opis.

Dodaj następującą definicję metody do przestrzeni nazw TermLists w klasie Program.

```csharp
/// <summary>
/// Update the information for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="name">The new name for the term list.</param>
/// <param name="description">The new description for the term list.</param>
static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
{
    Console.WriteLine("Updating information for term list with ID {0}.", list_id);
    Body body = new Body(name, description);
    client.ListManagementTermLists.Update(list_id, "application/json", body);
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-term-to-a-term-list"></a>Dodawanie terminu do listy terminów

Dodaj następującą definicję metody do przestrzeni nazw TermLists w klasie Program.

```csharp
/// <summary>
/// Add a term to the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="term">The term to add to the term list.</param>
static void AddTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
    client.ListManagementTerm.AddTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

## <a name="get-all-terms-in-a-term-list"></a>Pobieranie wszystkich terminów z listy terminów

Dodaj następującą definicję metody do przestrzeni nazw TermLists w klasie Program.

```csharp
/// <summary>
/// Get all terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to get all terms.</param>
static void GetAllTerms(ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
    Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
    TermsData data = terms.Data;
    foreach (TermsInList term in data.Terms)
    {
        Console.WriteLine(term.Term);
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="add-code-to-refresh-the-search-index"></a>Dodawanie kodu do odświeżania indeksu wyszukiwania

Po wprowadzeniu zmian w liście terminów należy odświeżyć jej indeks wyszukiwania, aby zmiany zostały uwzględnione przy następnym użyciu listy terminów do sprawdzania tekstu. Przypomina to działanie wyszukiwarki na komputerze (jeśli jest włączona) lub wyszukiwarki internetowej, które ciągle odświeżają swój indeks w celu uwzględnienia nowych plików lub stron.

Do odświeżania indeksu wyszukiwania listy terminów służy metoda **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod**.

Dodaj następującą definicję metody do przestrzeni nazw TermLists w klasie Program.

```csharp
/// <summary>
/// Refresh the search index for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to refresh.</param>
static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
    client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
    Thread.Sleep((int)(latencyDelay * 60 * 1000));
}
```

## <a name="screen-text-using-a-term-list"></a>Sprawdzanie tekstu przy użyciu listy terminów

Do sprawdzania tekstu przy użyciu listy terminów służy metoda **ContentModeratorClient.TextModeration.ScreenText**, która przyjmuje następujące parametry.

- Język terminów na liście terminów.
- Typ MIME, który może mieć wartość „text/html”, „text/xml”, „text/markdown” lub „text/plain”.
- Tekst do sprawdzenia.
- Wartość logiczna. Ustaw wartość **true** w tym polu w celu autokorekty tekstu przed jego sprawdzaniem.
- Wartość logiczna. Ustaw to pole na **wartość true** , aby wykrywać dane osobowe w tekście.
- Identyfikator listy terminów.

Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** zwraca obiekt **Screen**, który ma właściwość **Terms** zawierającą listę terminów wykrytych przez usługi Content Moderator podczas sprawdzania. Należy pamiętać, że jeśli usługi Content Moderator nie wykryją żadnych warunków podczas sprawdzania, właściwość **Terms** będzie miała wartość **null**.

Dodaj następującą definicję metody do przestrzeni nazw TermLists w klasie Program.

```csharp
/// <summary>
/// Screen the indicated text for terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to use to screen the text.</param>
/// <param name="text">The text to screen.</param>
static void ScreenText (ContentModeratorClient client, string list_id, string text)
{
    Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
    Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
    if (null == screen.Terms)
    {
        Console.WriteLine("No terms from the term list were detected in the text.");
    }
    else
    {
        foreach (DetectedTerms term in screen.Terms)
        {
            Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
        }
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="delete-terms-and-lists"></a>Usuwanie terminów i list

Usuwanie terminu lub listy jest bardzo proste. Zestaw SDK pozwala wykonać następuje zadania:

- Usuwanie terminu. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- Usuwanie wszystkich terminów z listy, bez usuwania listy. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Usuwanie listy i całej jej zawartości. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Usuwanie terminu

Dodaj następującą definicję metody do przestrzeni nazw TermLists w klasie Program.

```csharp
/// <summary>
/// Delete a term from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete the term.</param>
/// <param name="term">The term to delete.</param>
static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
    client.ListManagementTerm.DeleteTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-all-terms-in-a-term-list"></a>Usuwanie wszystkich terminów z listy terminów

Dodaj następującą definicję metody do przestrzeni nazw TermLists w klasie Program.

```csharp
/// <summary>
/// Delete all terms from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete all terms.</param>
static void DeleteAllTerms (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
    client.ListManagementTerm.DeleteAllTerms(list_id, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-a-term-list"></a>Usuwanie listy terminów

Dodaj następującą definicję metody do przestrzeni nazw TermLists w klasie Program.

```csharp
/// <summary>
/// Delete the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to delete.</param>
static void DeleteTermList (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Deleting term list with ID {0}.", list_id);
    client.ListManagementTermLists.Delete(list_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="compose-the-main-method"></a>Redaguj metodę Main

Dodaj definicję metody **Main** do przestrzeni nazw **TermLists** w klasie **Program**. Na koniec zamknij klasę **Program** i przestrzeń nazw **TermLists**.

```csharp
static void Main(string[] args)
{
    using (var client = Clients.NewClient())
    {
        string list_id = CreateTermList(client);

        UpdateTermList(client, list_id, "name", "description");
        AddTerm(client, list_id, "term1");
        AddTerm(client, list_id, "term2");

        GetAllTerms(client, list_id);

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        string text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteTerm(client, list_id, "term1");

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteAllTerms(client, list_id);
        DeleteTermList(client, list_id);

        Console.WriteLine("Press ENTER to close the application.");
        Console.ReadLine();
    }
}
```

## <a name="run-the-application-to-see-the-output"></a>Uruchom aplikację, aby zobaczyć dane wyjściowe

Dane wyjściowe konsoli będą wyglądać następująco:

```console
Creating term list.
Term list created. ID: 252.
Updating information for term list with ID 252.

Adding term "term1" to term list with ID 252.
Adding term "term2" to term list with ID 252.

Getting terms in term list with ID 252.
term1
term2

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term1" from list ID 252 at index 32.
Found term: "term2" from list ID 252 at index 46.

Removed term "term1" from term list with ID 252.

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term2" from list ID 252 at index 46.

Removing all terms from term list with ID 252.
Deleting term list with ID 252.
Press ENTER to close the application.
```

## <a name="next-steps"></a>Następne kroki

Pobierz [zestaw SDK Content Moderator dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązanie Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego i innych przewodników Szybki start dotyczących usług Content Moderator dla platformy .NET i rozpocznij pracę nad integracją.
