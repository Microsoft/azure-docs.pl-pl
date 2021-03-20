---
title: Filtry zabezpieczeń do przycinania wyników przy użyciu Active Directory
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak zaimplementować uprawnienia zabezpieczeń na poziomie dokumentu dla wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze przy użyciu filtrów zabezpieczeń i tożsamości Azure Active Directory (AD).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97629514"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtry zabezpieczeń do przycinania wyników Wyszukiwanie poznawcze platformy Azure przy użyciu tożsamości Active Directory

W tym artykule pokazano, jak używać tożsamości zabezpieczeń programu Azure Active Directory (AD) wraz z filtrami w usłudze Azure Wyszukiwanie poznawcze do przycinania wyników wyszukiwania na podstawie przynależności do grupy użytkowników.

W tym artykule opisano następujące zadania:
> [!div class="checklist"]
> - Tworzenie grup i użytkowników usługi Azure AD
> - Skojarz użytkownika z utworzoną grupą
> - Buforuj nowe grupy
> - Indeksuj dokumenty ze skojarzonymi grupami
> - Wygeneruj żądanie wyszukiwania z filtrem identyfikatorów grup
> 
> [!NOTE]
> Przykładowe fragmenty kodu w tym artykule są zapisywane w języku C#. Pełny kod źródłowy można znaleźć [w usłudze GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Wymagania wstępne

Indeks na platformie Azure Wyszukiwanie poznawcze musi mieć [pole zabezpieczeń](search-security-trimming-for-azure-search.md) , aby można było przechowywać listę tożsamości grup z dostępem do odczytu do dokumentu. W tym przypadku użycia przyjęto, że założono, że jeden do jednego z elementów zabezpieczanych (takich jak aplikacja szkoły indywidualnej) i pole zabezpieczeń określają, kto ma dostęp do tego elementu (personel przyjęcia).

Musisz mieć uprawnienia administratora usługi Azure AD, które są wymagane w tym instruktażu do tworzenia użytkowników, grup i skojarzeń. 

Aplikacja musi być również zarejestrowana w usłudze Azure AD jako aplikacja wielodostępna, zgodnie z opisem w poniższej procedurze.

### <a name="register-your-application-with-azure-active-directory"></a>Zarejestruj swoją aplikację za pomocą Azure Active Directory

Ten krok integruje aplikację z usługą Azure AD w celu zaakceptowania logowania użytkowników i kont grup. Jeśli nie jesteś administratorem dzierżawy w organizacji, może być konieczne [utworzenie nowej dzierżawy](../active-directory/develop/quickstart-create-new-tenant.md) w celu wykonania poniższych kroków.

1. W [Azure Portal](https://portal.azure.com)znajdź zasób Azure Active Directory dla subskrypcji.

1. Po lewej stronie w obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.

1. Nadaj nazwę rejestracji, która jest podobna do nazwy aplikacji wyszukiwania. Wybierz pozycję **Zarejestruj**.

1. Po utworzeniu rejestracji aplikacji Skopiuj identyfikator aplikacji. Musisz podać ten ciąg dla swojej aplikacji.

   Jeśli przechodzenie odbywa się przez [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), wklej tę wartość do pliku **app.config** .

   Powtórz tę czynność dla identyfikatora dzierżawy.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Identyfikator aplikacji w sekcji podstawowe informacje":::

1. Po lewej stronie wybierz pozycję **uprawnienia interfejsu API** , a następnie wybierz pozycję **Dodaj uprawnienie**. 

1. Wybierz pozycję **Microsoft Graph** a następnie wybierz pozycję **uprawnienia delegowane**.

1. Wyszukaj, a następnie Dodaj następujące delegowane uprawnienia:

   - **Katalog. ReadWrite. All**
   - **Group.ReadWrite.All**
   - **User. ReadWrite. All**

Microsoft Graph udostępnia interfejs API, który umożliwia programistyczny dostęp do usługi Azure AD za pomocą interfejsu API REST. Przykładowy kod dla tego instruktażu używa uprawnień do wywoływania interfejsu API Microsoft Graph do tworzenia grup, użytkowników i skojarzeń. Interfejsy API są również używane do buforowania identyfikatorów grup do szybszego filtrowania.

## <a name="create-users-and-groups"></a>Tworzenie użytkowników i grup

Jeśli dodajesz wyszukiwanie do ustalonej aplikacji, możesz mieć istniejące identyfikatory użytkowników i grup w usłudze Azure AD. W takim przypadku można pominąć trzy następne kroki. 

Jeśli jednak nie masz istniejących użytkowników, możesz użyć Microsoft Graph interfejsów API do utworzenia podmiotów zabezpieczeń. Poniższe fragmenty kodu pokazują, jak generować identyfikatory, które stają się wartościami danych dla pola zabezpieczenia w indeksie usługi Azure Wyszukiwanie poznawcze. W naszej hipotetycznej aplikacji do naliczania uczelni będą to identyfikatory zabezpieczeń dla personelu usługi Admission Control.

Członkostwo użytkowników i grup może być bardzo płynne, szczególnie w dużych organizacjach. Kod, który kompiluje tożsamości użytkowników i grup, powinien być uruchamiany często, aby można było wybrać zmiany w członkostwie w organizacji. Podobnie indeks Wyszukiwanie poznawcze platformy Azure wymaga podobnego harmonogramu aktualizacji w celu odzwierciedlenia bieżącego stanu dozwolonych użytkowników i zasobów.

### <a name="step-1-create-group"></a>Krok 1. [Tworzenie grupy](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>Krok 2. [Tworzenie użytkownika](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>Krok 3. Skojarz użytkownika i grupę

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>Krok 4. buforowanie grup identyfikatorów

Opcjonalnie, aby zmniejszyć opóźnienie sieci, można buforować skojarzenia grupy użytkowników, aby po wydaniu żądania wyszukiwania grupy były zwracane z pamięci podręcznej, co umożliwia zapisanie w trybie komunikacji jednokrotnej w usłudze Azure AD. Za pomocą [interfejsu API usługi Azure AD Batch](/graph/json-batching) można wysyłać pojedyncze żądanie HTTP z wieloma użytkownikami i tworzyć pamięć podręczną.

Microsoft Graph został zaprojektowany z myślą o obsłudze dużej liczby żądań. W przypadku przeciążania liczby żądań Microsoft Graph nie można wykonać żądania z kodem stanu HTTP 429. Aby uzyskać więcej informacji, zobacz [Microsoft Graph ograniczanie przepustowości](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indeksowanie dokumentu z ich dozwolonymi grupami

Operacje zapytań w usłudze Azure Wyszukiwanie poznawcze są wykonywane za pośrednictwem indeksu Wyszukiwanie poznawcze platformy Azure. W tym kroku Operacja indeksowania importuje dane z możliwością wyszukiwania do indeksu, w tym identyfikatory używane jako filtry zabezpieczeń. 

Usługa Azure Wyszukiwanie poznawcze nie uwierzytelnia tożsamości użytkowników ani nie oferuje logiki do określania zawartości, do której użytkownik ma uprawnienia do wyświetlania. W przypadku użycia w przypadku przycinania zabezpieczeń przyjęto założenie, że podajesz skojarzenie między poufnym dokumentem a identyfikatorem grupy mającym dostęp do tego dokumentu, który został zaimportowany do indeksu wyszukiwania. 

W hipotetycznym przykładzie treść żądania PUT na indeksie usługi Azure Wyszukiwanie poznawcze będzie zawierać opis lub transkrypcję kolegium kandydata wraz z identyfikatorem grupy mającym uprawnienia do wyświetlania tej zawartości. 

W ogólnym przykładzie używanym w przykładowym kodzie dla tego przewodnika akcja indeksu może wyglądać następująco:

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>Wydaj żądanie Search

W celu przycinania zabezpieczeń wartości w polu zabezpieczeń w indeksie są wartościami statycznymi używanymi do dołączania lub wykluczania dokumentów w wynikach wyszukiwania. Na przykład, jeśli identyfikator grupy dla przyjmowania to "A11B22C33D44-E55F66G77-H88I99JKK", wszystkie dokumenty w indeksie usługi Azure Wyszukiwanie poznawcze, które mają ten identyfikator w zgłoszonych zabezpieczeniach, są uwzględniane (lub wykluczone) w wynikach wyszukiwania wysłanych z powrotem do osoby żądającej.

Aby odfiltrować dokumenty zwrócone w wynikach wyszukiwania na podstawie grup użytkownika wystawiającego żądanie, należy zapoznać się z poniższymi instrukcjami.

### <a name="step-1-retrieve-users-group-identifiers"></a>Krok 1. pobieranie identyfikatorów grup użytkowników

Jeśli grupy użytkownika nie zostały już zapisane w pamięci podręcznej lub pamięć podręczna wygasła, wystaw żądanie [grup](/graph/api/directoryobject-getmembergroups) .

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>Krok 2. redagowanie żądania wyszukiwania

Przy założeniu, że masz członkostwo w grupach użytkowników, możesz wydać żądanie wyszukiwania z odpowiednimi wartościami filtru.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>Krok 3. Obsługa wyników

Odpowiedź obejmuje przefiltrowaną listę dokumentów składającą się z tych, do których użytkownik ma uprawnienia do wyświetlania. W zależności od sposobu konstruowania strony wyników wyszukiwania możesz chcieć uwzględnić podpowiedzi wizualne, aby odzwierciedlić filtrowany zestaw wyników.

## <a name="next-steps"></a>Następne kroki

W tym instruktażu przedstawiono wzorzec korzystania z logowania do usługi Azure AD w celu filtrowania dokumentów w usłudze Azure Wyszukiwanie poznawcze wyniki, przycinania wyników dokumentów, które nie pasują do filtru dostarczonego w żądaniu. Aby uzyskać alternatywny wzorzec, który może być prostszy lub aby ponownie odwiedzić inne funkcje zabezpieczeń, zobacz poniższe linki.

- [Filtry zabezpieczeń do przycinania wyników](search-security-trimming-for-azure-search.md)
- [Zabezpieczenia w usłudze Azure Wyszukiwanie poznawcze](search-security-overview.md)