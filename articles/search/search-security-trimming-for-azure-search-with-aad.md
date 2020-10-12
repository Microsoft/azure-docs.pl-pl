---
title: Filtry zabezpieczeń do przycinania wyników przy użyciu Active Directory
titleSuffix: Azure Cognitive Search
description: Uprawnienia zabezpieczeń na poziomie dokumentu dla wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze przy użyciu filtrów zabezpieczeń i tożsamości Azure Active Directory (AAD).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/04/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 87337cf22bdb388c5873a2811bb9913c3e7f4d4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019780"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtry zabezpieczeń do przycinania wyników Wyszukiwanie poznawcze platformy Azure przy użyciu tożsamości Active Directory

W tym artykule pokazano, jak używać tożsamości zabezpieczeń usługi Azure Active Directory (AAD) wraz z filtrami w usłudze Azure Wyszukiwanie poznawcze do przycinania wyników wyszukiwania na podstawie przynależności do grupy użytkowników.

W tym artykule opisano następujące zadania:
> [!div class="checklist"]
> - Tworzenie grup i użytkowników usługi AAD
> - Skojarz użytkownika z utworzoną grupą
> - Buforuj nowe grupy
> - Indeksuj dokumenty ze skojarzonymi grupami
> - Wygeneruj żądanie wyszukiwania z filtrem identyfikatorów grup
> 
> [!NOTE]
> Przykładowe fragmenty kodu w tym artykule są zapisywane w języku C#. Pełny kod źródłowy można znaleźć [w usłudze GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Wymagania wstępne

Indeks na platformie Azure Wyszukiwanie poznawcze musi mieć [pole zabezpieczeń](search-security-trimming-for-azure-search.md) , aby można było przechowywać listę tożsamości grup z dostępem do odczytu do dokumentu. W tym przypadku użycia przyjęto, że założono, że jeden do jednego z elementów zabezpieczanych (takich jak aplikacja szkoły indywidualnej) i pole zabezpieczeń określają, kto ma dostęp do tego elementu (personel przyjęcia).

Musisz mieć uprawnienia administratora usługi AAD wymagane w tym instruktażu do tworzenia użytkowników, grup i skojarzeń w usłudze AAD.

Aplikacja musi być również zarejestrowana w usłudze AAD, zgodnie z opisem w poniższej procedurze.

### <a name="register-your-application-with-aad"></a>Zarejestruj swoją aplikację w usłudze AAD

Ten krok integruje aplikację z usługą AAD w celu zaakceptowania logowania użytkowników i kont grup. Jeśli nie jesteś administratorem usługi AAD w organizacji, może być konieczne [utworzenie nowej dzierżawy](../active-directory/develop/quickstart-create-new-tenant.md) w celu wykonania poniższych kroków.

1. Przejdź do [**portalu rejestracji aplikacji**](https://apps.dev.microsoft.com)  >   **zbieżność aplikacji**  >  **Dodaj aplikację**.
2. Wprowadź nazwę aplikacji, a następnie kliknij przycisk **Utwórz**. 
3. Wybierz nowo zarejestrowanej aplikacji na stronie Moje aplikacje.
4. Na stronie Rejestracja aplikacji > **platformy**  >  **Dodaj platformę**, wybierz pozycję **interfejs API sieci Web**.
5. Na stronie Rejestracja aplikacji przejdź do pozycji > **Microsoft Graph uprawnienia**  >  **Dodaj**.
6. W obszarze Wybierz uprawnienia Dodaj następujące delegowane uprawnienia, a następnie kliknij przycisk **OK**:

   + **Katalog. ReadWrite. All**
   + **Group.ReadWrite.All**
   + **User. ReadWrite. All**

Microsoft Graph udostępnia interfejs API, który umożliwia programistyczny dostęp do usługi AAD za pomocą interfejsu API REST. Przykładowy kod dla tego instruktażu używa uprawnień do wywoływania interfejsu API Microsoft Graph do tworzenia grup, użytkowników i skojarzeń. Interfejsy API są również używane do buforowania identyfikatorów grup do szybszego filtrowania.

## <a name="create-users-and-groups"></a>Tworzenie użytkowników i grup

W przypadku dodawania wyszukiwania do ustalonej aplikacji mogą istnieć identyfikatory użytkowników i grup w usłudze AAD. W takim przypadku można pominąć trzy następne kroki. 

Jeśli jednak nie masz istniejących użytkowników, możesz użyć Microsoft Graph interfejsów API do utworzenia podmiotów zabezpieczeń. Poniższe fragmenty kodu pokazują, jak generować identyfikatory, które stają się wartościami danych dla pola zabezpieczenia w indeksie usługi Azure Wyszukiwanie poznawcze. W naszej hipotetycznej aplikacji do naliczania uczelni będą to identyfikatory zabezpieczeń dla personelu usługi Admission Control.

Członkostwo użytkowników i grup może być bardzo płynne, szczególnie w dużych organizacjach. Kod, który kompiluje tożsamości użytkowników i grup, powinien być uruchamiany często, aby można było wybrać zmiany w członkostwie w organizacji. Podobnie indeks Wyszukiwanie poznawcze platformy Azure wymaga podobnego harmonogramu aktualizacji w celu odzwierciedlenia bieżącego stanu dozwolonych użytkowników i zasobów.

### <a name="step-1-create-aad-group"></a>Krok 1. Tworzenie [grupy usługi AAD](/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-user"></a>Krok 2. Tworzenie [użytkownika usługi AAD](/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Krok 3. Skojarz użytkownika i grupę
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Krok 4. buforowanie grup identyfikatorów
Opcjonalnie, aby zmniejszyć opóźnienie sieci, można buforować skojarzenia grupy użytkowników, aby podczas wystawiania żądania wyszukiwania grupy były zwracane z pamięci podręcznej, co pozwala zaoszczędzić dostęp do usługi AAD. Korzystając z [interfejsu API usługi AAD Batch](/graph/json-batching) , można wysyłać pojedyncze żądanie HTTP z wieloma użytkownikami i tworzyć pamięć podręczną.

Microsoft Graph został zaprojektowany z myślą o obsłudze dużej liczby żądań. W przypadku przeciążania liczby żądań Microsoft Graph nie można wykonać żądania z kodem stanu HTTP 429. Aby uzyskać więcej informacji, zobacz [Microsoft Graph ograniczanie przepustowości](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indeksowanie dokumentu z ich dozwolonymi grupami

Operacje zapytań w usłudze Azure Wyszukiwanie poznawcze są wykonywane za pośrednictwem indeksu Wyszukiwanie poznawcze platformy Azure. W tym kroku Operacja indeksowania importuje dane z możliwością wyszukiwania do indeksu, w tym identyfikatory używane jako filtry zabezpieczeń. 

Usługa Azure Wyszukiwanie poznawcze nie uwierzytelnia tożsamości użytkowników ani nie oferuje logiki do określania zawartości, do której użytkownik ma uprawnienia do wyświetlania. W przypadku użycia w przypadku przycinania zabezpieczeń przyjęto założenie, że podajesz skojarzenie między poufnym dokumentem a identyfikatorem grupy mającym dostęp do tego dokumentu, który został zaimportowany do indeksu wyszukiwania. 

W hipotetycznym przykładzie treść żądania PUT na indeksie usługi Azure Wyszukiwanie poznawcze będzie zawierać opis lub transkrypcję kolegium kandydata wraz z identyfikatorem grupy mającym uprawnienia do wyświetlania tej zawartości. 

W ogólnym przykładzie używanym w przykładowym kodzie dla tego przewodnika akcja indeksu może wyglądać następująco:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Wydaj żądanie Search

W celu przycinania zabezpieczeń wartości w polu zabezpieczeń w indeksie są wartościami statycznymi używanymi do dołączania lub wykluczania dokumentów w wynikach wyszukiwania. Na przykład, jeśli identyfikator grupy dla przyjmowania to "A11B22C33D44-E55F66G77-H88I99JKK", wszystkie dokumenty w indeksie usługi Azure Wyszukiwanie poznawcze, które mają ten identyfikator w zgłoszonych zabezpieczeniach, są uwzględniane (lub wykluczone) w wynikach wyszukiwania wysłanych z powrotem do osoby żądającej.

Aby odfiltrować dokumenty zwrócone w wynikach wyszukiwania na podstawie grup użytkownika wystawiającego żądanie, należy zapoznać się z poniższymi instrukcjami.

### <a name="step-1-retrieve-users-group-identifiers"></a>Krok 1. pobieranie identyfikatorów grup użytkowników

Jeśli grupy użytkownika nie zostały już zapisane w pamięci podręcznej lub pamięć podręczna wygasła, wystaw żądanie [grup](/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Krok 2. redagowanie żądania wyszukiwania

Przy założeniu, że masz członkostwo w grupach użytkowników, możesz wydać żądanie wyszukiwania z odpowiednimi wartościami filtru.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Krok 3. Obsługa wyników

Odpowiedź obejmuje przefiltrowaną listę dokumentów składającą się z tych, do których użytkownik ma uprawnienia do wyświetlania. W zależności od sposobu konstruowania strony wyników wyszukiwania możesz chcieć uwzględnić podpowiedzi wizualne, aby odzwierciedlić filtrowany zestaw wyników.

## <a name="conclusion"></a>Podsumowanie

W tym instruktażu przedstawiono metody używania logowań usługi AAD do filtrowania dokumentów w usłudze Azure Wyszukiwanie poznawcze wyniki, przycinania wyników dokumentów, które nie pasują do filtru dostarczonego w żądaniu.

## <a name="see-also"></a>Zobacz też

+ [Kontrola dostępu oparta na tożsamościch za pomocą filtrów Wyszukiwanie poznawcze platformy Azure](search-security-trimming-for-azure-search.md)
+ [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
+ [Zabezpieczenia danych i kontrola dostępu w operacjach usługi Azure Wyszukiwanie poznawcze](search-security-overview.md)