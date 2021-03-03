---
title: Konfigurowanie indeksatora usługi SharePoint Online (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Skonfiguruj indeksator usługi SharePoint Online do automatyzowania indeksowania zawartości biblioteki dokumentów w usłudze Azure Wyszukiwanie poznawcze.
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 558df115043d76acf865f19611e8c4cd322e00a7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680213"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>Jak skonfigurować indeksowanie online usługi SharePoint w Wyszukiwanie poznawcze (wersja zapoznawcza)

> [!IMPORTANT] 
> Pomoc techniczna usługi SharePoint Online jest obecnie w **publicznej wersji zapoznawczej**. Możesz zażądać dostępu do warunkowej wersji zapoznawczej, wypełniając [ten formularz](https://aka.ms/azure-cognitive-search/indexer-preview).
>
> Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> [Interfejs API REST w wersji 2020-06-30 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. Obecnie nie ma obsługi portalu lub zestawu SDK.

W tym artykule opisano, jak za pomocą usługi Azure Wyszukiwanie poznawcze indeksować dokumenty (takie jak pliki PDF, dokumenty Microsoft Office i inne popularne formaty) przechowywane w bibliotekach dokumentów usługi SharePoint Online w indeksie Wyszukiwanie poznawcze platformy Azure. Najpierw objaśnia podstawowe informacje na temat konfigurowania i konfigurowania indeksatora. Następnie oferuje dokładniejszą eksplorację zachowań i scenariuszy, które prawdopodobnie napotkasz.

## <a name="functionality"></a>Funkcjonalność
Indeksator w usłudze Azure Wyszukiwanie poznawcze to przeszukiwarka, która wyodrębnia dane z możliwością wyszukiwania i metadane ze źródła danych. Indeksator usługi SharePoint Online będzie łączyć się z witryną programu SharePoint Online i indeksować dokumenty z co najmniej jednej biblioteki dokumentów. Indeksator zapewnia następujące funkcje:
+ Indeksuj zawartość z co najmniej jednej biblioteki dokumentów usługi SharePoint Online.
+ Indeksuj zawartość z bibliotek dokumentów usługi SharePoint Online, które znajdują się w tej samej dzierżawie co usługa Wyszukiwanie poznawcze platformy Azure. Indeksator nie będzie działał z witrynami programu SharePoint, które znajdują się w innej dzierżawie niż usługa Wyszukiwanie poznawcze platformy Azure. 
+ Indeksator będzie obsługiwał indeksowanie przyrostowe, co oznacza, że będzie identyfikować zawartość biblioteki dokumentów, która została zmieniona i indeksować zaktualizowaną zawartość w przyszłych uruchomieniach indeksowania. Na przykład, jeśli 5 plików PDF jest pierwotnie indeksowane przez indeksator, to 1 zostanie zaktualizowany, a następnie indeksator zostanie uruchomiony ponownie, indeksator będzie indeksować tylko 1 plik PDF, który został zaktualizowany.
+ Tekst i znormalizowane obrazy będą domyślnie wyodrębniane z dokumentów, które są indeksowane. Opcjonalnie można dodać zestawu umiejętności do potoku w celu dalszej wzbogacania zawartości. Więcej informacji na temat umiejętności można znaleźć w artykule [zestawu umiejętności koncepcje na platformie Azure wyszukiwanie poznawcze](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Obsługiwane formaty dokumentów

Indeksator usługi Azure Wyszukiwanie poznawcze SharePoint Online może wyodrębnić tekst z następujących formatów dokumentu:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Wykrywanie przyrostowe i usuwanie
Domyślnie indeksator usługi SharePoint Online obsługuje indeksowanie przyrostowe, co oznacza, że będzie identyfikować zawartość biblioteki dokumentów, która została zmieniona, i indeksować zaktualizowaną zawartość tylko w przyszłych uruchomieniach indeksowania. Na przykład jeśli 5 dokumentów programu Word jest pierwotnie indeksowane przez indeksator, wówczas 1 zostanie zaktualizowany, a następnie indeksator zostanie uruchomiony ponownie, indeksator będzie ponownie indeksować 1 dokument programu Word, który został zaktualizowany.

Wykrywanie usuwania jest również domyślnie obsługiwane. Oznacza to, że jeśli dokument zostanie usunięty z biblioteki dokumentów usługi SharePoint Online, indeksator wykryje usunięcie podczas przyszłego uruchomienia indeksatora i usunie dokument z indeksu.

## <a name="setting-up-sharepoint-online-indexing"></a>Konfigurowanie indeksowania usługi SharePoint Online
Aby skonfigurować indeksator usługi SharePoint Online, należy wykonać pewne akcje w Azure Portal i niektóre akcje przy użyciu interfejsu API REST w wersji zapoznawczej. Ta wersja zapoznawcza nie jest obsługiwana przez zestaw SDK.

### <a name="step-1-enable-system-assigned-managed-identity"></a>Krok 1. Włączanie tożsamości zarządzanej przypisanej przez system
Gdy jest włączona tożsamość zarządzana przypisana przez system, platforma Azure tworzy tożsamość usługi wyszukiwania, która może być używana przez indeksator.

![Włącz tożsamość zarządzaną przypisaną przez system](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Włącz tożsamość zarządzaną przypisaną przez system")

Po wybraniu pozycji **Zapisz** zostanie wyświetlony identyfikator obiektu przypisany do usługi wyszukiwania.

![Tożsamość zarządzana przypisana przez system](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Tożsamość zarządzana przypisana przez system")

### <a name="step-2-create-an-aad-application"></a>Krok 2. Tworzenie aplikacji usługi AAD
Indeksator usługi SharePoint Online będzie używać tej aplikacji usługi AAD do uwierzytelniania. 

1.  Przejdź do [Azure Portal](https://portal.azure.com/).

1.  Otwórz menu po lewej stronie strony głównej, a następnie wybierz pozycję **Azure Active Directory** a następnie wybierz pozycję **rejestracje aplikacji**. Wybierz pozycję **+ Nowa rejestracja**.
    1.  Podaj nazwę aplikacji.
    2.  Wybierz **jedną dzierżawę**.
    3.  Nie jest wymagany identyfikator URI przekierowania.
    4.  Wybierz pozycję **zarejestruj**

1.  Wybierz pozycję **uprawnienia interfejsu API** z menu po lewej stronie, a następnie **Dodaj uprawnienie**, a następnie **Microsoft Graph** następnie **uprawnienia delegowane**. Dodaj następujące uprawnienia interfejsu API: 
    1.  **Delegowani-Files. Read. All** 
    2.  **Delegowane — sites. Read. All** 
    3.  **Delegowane — użytkownik. odczyt**

    ![Delegowane uprawnienia interfejsu API](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Delegowane uprawnienia interfejsu API")

    Użycie uprawnień delegowanych oznacza, że indeksator będzie miał dostęp do witryny programu SharePoint w kontekście użytkownika. W związku z tym po uruchomieniu indeksatora będzie miał dostęp tylko do zawartości, do której zalogowany użytkownik ma dostęp. Logowanie użytkownika odbywa się podczas tworzenia indeksatora lub aktualizowania źródła danych. Krok logowania został opisany w dalszej części tego artykułu.

1.  Wybierz kartę **uwierzytelnianie** . Ustaw wartość **Zezwalaj na przepływy publiczne klientów** na **wartość tak** , a następnie wybierz pozycję **Zapisz**.

1.  Wybierz pozycję **+ Dodaj platformę**, następnie **aplikacje mobilne i klasyczne**, a następnie sprawdź `https://login.microsoftonline.com/common/oauth2/nativeclient` , a następnie **Skonfiguruj**.

    ![Konfiguracja uwierzytelniania aplikacji usługi AAD](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "Konfiguracja uwierzytelniania aplikacji usługi AAD")

1.  Udziel zgody administratora (wymagane tylko dla niektórych dzierżawców).

    Niektóre dzierżawy są zablokowane w taki sposób, aby zgoda administratora była wymagana dla tych delegowanych uprawnień interfejsu API. Jeśli tak jest, przed utworzeniem indeksatora należy mieć uprawnienia administratora dla tej aplikacji usługi AAD. 

    Ponieważ nie wszyscy dzierżawcy mają to wymaganie, zalecamy najpierw Pominięcie tego kroku i kontynuowanie pracy z instrukcjami. Jeśli podczas tworzenia indeksatora potrzebujesz zgody administratora, uwierzytelnienie nie powiedzie się, informując o konieczności zatwierdzenia uwierzytelnienia przez administratora. W takim przypadku Administrator dzierżawy udzieli zgody przy użyciu przycisku poniżej.

    ![Zgoda administratora przyznania aplikacji usługi AAD](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "Zgoda administratora przyznania aplikacji usługi AAD")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>Krok 3. Tworzenie źródła danych
> [!IMPORTANT] 
> Począwszy od tej sekcji należy użyć interfejsu API REST w wersji zapoznawczej dla pozostałych kroków. Jeśli nie znasz interfejsu API REST usługi Azure Wyszukiwanie poznawcze, zalecamy zapoznanie się z tym [przewodnikiem Szybki Start](search-get-started-rest.md).

Źródło danych określa, które dane mają być indeksowane, które są potrzebne do uzyskiwania dostępu do danych, oraz zasady umożliwiające wydajne identyfikowanie zmian w danych (nowych, zmodyfikowanych lub usuniętych wierszy). Źródło danych może być używane przez wiele indeksatorów w tej samej usłudze wyszukiwania.

W przypadku indeksowania programu SharePoint źródło danych musi mieć następujące wymagane właściwości:
+ **Nazwa** jest unikatową nazwą źródła danych w ramach usługi wyszukiwania.
+ **typem** musi być "SharePoint". Uwzględnia wielkość liter.
+ **poświadczenia** zapewniają punkt końcowy usługi SharePoint Online i identyfikator aplikacji usługi AAD (klienta). Przykładowy punkt końcowy usługi SharePoint Online to `https://microsoft.sharepoint.com/teams/MySharePointSite` . Punkt końcowy usługi SharePoint Online można uzyskać, przechodząc do strony głównej witryny programu SharePoint i kopiując adres URL z przeglądarki.
+ **kontener** określa bibliotekę dokumentów do indeksowania. Więcej informacji na temat tworzenia kontenera można znaleźć w sekcji [kontrolowanie, które dokumenty są indeksowane](#controlling-which-documents-are-indexed) w tym dokumencie.

Aby utworzyć źródło danych:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>Krok 4. Tworzenie indeksu
Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania.

Poniżej przedstawiono sposób tworzenia indeksu z polem zawartości z możliwością wyszukiwania w celu przechowywania tekstu wyodrębnionego z dokumentów w bibliotece dokumentów:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

Aby uzyskać więcej informacji, zobacz [Tworzenie indeksu (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>Krok 5. Tworzenie indeksatora
Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i zawiera harmonogram do automatyzowania odświeżania danych. Gdy indeks i źródło danych zostały utworzone, możesz utworzyć indeksator.

W tej sekcji zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń organizacji, które mają dostęp do witryny programu SharePoint. Jeśli jest to możliwe, zalecamy utworzenie nowego konta użytkownika organizacji i nadanie nowemu użytkownikowi dokładnych uprawnień, które mają mieć indeksator.

Istnieje kilka kroków, które należy wykonać, aby utworzyć indeksator:

1.  Wyślij żądanie, aby utworzyć indeksator.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index"
        }
    
    ```

1.  Podczas tworzenia indeksatora po raz pierwszy zakończy się niepowodzeniem i zobaczysz następujący komunikat o błędzie. Przejdź do linku w komunikacie o błędzie. Jeśli nie możesz przejść do linku w ciągu 10 minut, kod wygaśnie i będzie konieczne ponowne utworzenie [źródła danych](#create-data-source).

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Podaj kod, który został podany w komunikacie o błędzie.

    ![Wprowadź kod urządzenia](media/search-howto-index-sharepoint-online/enter-device-code.png "Wprowadź kod urządzenia")

1.  Indeksator programu SharePoint będzie uzyskiwać dostęp do zawartości programu SharePoint jako zalogowany użytkownik. Użytkownik logujący się w tym kroku będzie miał ten zalogowany użytkownik. W przypadku zalogowania się przy użyciu konta użytkownika, które nie ma dostępu do dokumentu w bibliotece dokumentów, który ma być indeksowany, indeksator nie będzie miał dostępu do tego dokumentu.

    Jeśli to możliwe, zalecamy utworzenie nowego konta użytkownika i nadanie nowemu użytkownikowi dokładnych uprawnień, które mają mieć indeksator.

1.  Zatwierdź wymagane uprawnienia.

    ![Zatwierdź uprawnienia interfejsu API](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "Zatwierdź uprawnienia interfejsu API")

1.  Wyślij ponownie żądanie utworzenia indeksatora. Tym razem żądanie powinno zakończyć się pomyślnie. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>Krok 6. sprawdzenie stanu indeksatora
Po utworzeniu indeksatora można sprawdzić stan indeksatora, wykonując następujące żądanie.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Więcej informacji na temat stanu indeksatora można znaleźć tutaj: [pobieranie stanu indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Aktualizowanie źródła danych
Jeśli nie ma żadnych aktualizacji obiektu źródła danych, indeksator może być uruchamiany zgodnie z harmonogramem bez żadnej interakcji z użytkownikiem. Jednak za każdym razem, gdy obiekt źródła danych Wyszukiwanie poznawcze Azure zostanie zaktualizowany, należy ponownie zalogować się, aby indeksator mógł zostać uruchomiony. Na przykład w przypadku zmiany zapytania źródła danych należy ponownie zalogować się przy użyciu `https://microsoft.com/devicelogin` i nowego kodu.

Po zaktualizowaniu źródła danych wykonaj następujące czynności:

1.  Ręcznie uruchom indeksator.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Więcej informacji na temat żądania uruchomienia indeksatora można znaleźć tutaj: [Uruchom indeksator](https://docs.microsoft.com/rest/api/searchservice/run-indexer).

1.  Sprawdź stan indeksatora. Jeśli ostatni przebieg indeksatora zawiera błąd informujący o przejściu do `https://microsoft.com/devicelogin` strony, przejdź na Tę stronę i podaj nowy kod. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Więcej informacji na temat stanu indeksatora można znaleźć tutaj: [pobieranie stanu indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

1.  Zaloguj się

1.  Ręcznie uruchom ponownie indeksator i sprawdź stan indeksatora. Tym razem przebieg indeksatora powinien zostać pomyślnie uruchomiony.

## <a name="indexing-document-metadata"></a>Indeksowanie metadanych dokumentu
Jeśli ustawisz indeksator do indeksowania metadanych dokumentu, następujące metadane będą dostępne do indeksowania.

> [!NOTE]
> Metadane niestandardowe nie są uwzględnione w bieżącej wersji zapoznawczej.

| Identyfikator | Typ | Opis | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | Klucz kombinacji identyfikatora witryny, identyfikatora biblioteki i identyfikatora elementu, który jednoznacznie identyfikuje element w bibliotece dokumentów dla lokacji. |
| metadata_spo_site_id | Edm.String | Identyfikator witryny usługi SharePoint Online. |
| metadata_spo_library_id | Edm.String | Identyfikator biblioteki dokumentów. |
| metadata_spo_item_id | Edm.String | Identyfikator elementu (dokumentu) w bibliotece. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | Data/godzina ostatniej modyfikacji (UTC) elementu. |
| metadata_spo_item_name | Edm.String | Nazwa elementu . |
| metadata_spo_item_size | Edm.Int64 | Rozmiar (w bajtach) elementu. | 
| metadata_spo_item_content_type | Edm.String | Typ zawartości elementu. | 
| metadata_spo_item_extension | Edm.String | Rozszerzenie elementu. |
| metadata_spo_item_weburi | Edm.String | Identyfikator URI elementu. |
| metadata_spo_item_path | Edm.String | Kombinacja ścieżki nadrzędnej i nazwy elementu. | 

Indeksator usługi SharePoint Online obsługuje również metadane specyficzne dla każdego typu dokumentu. Więcej informacji można znaleźć we [właściwościach metadanych zawartości używanych w usłudze Azure wyszukiwanie poznawcze](search-blob-metadata-properties.md).

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Kontrolowanie, które dokumenty są indeksowane
Pojedynczy indeksator usługi SharePoint Online może indeksować zawartość z co najmniej jednej biblioteki dokumentów. Użyj parametru *Container* podczas tworzenia źródła danych, aby wskazać biblioteki dokumentów, które mają być indeksowane. *Kontener* źródła danych ma dwie właściwości: *name* i *Query*. 

### <a name="name"></a>Nazwa
Właściwość *name* jest wymagana i musi mieć jedną z trzech wartości:
+ *defaultSiteLibrary*
    + Indeksuj całą zawartość z domyślnej biblioteki dokumentów lokacji.
+   *allSiteLibraries*
    + Indeksuj całą zawartość ze wszystkich bibliotek dokumentów w lokacji. Nie spowoduje to indeksowania bibliotek dokumentów z podrzędnej lokacji. Można je określić w *zapytaniu* , chociaż.
+   *useQuery*
    + Tylko indeksowanie zawartości zdefiniowanej w *zapytaniu*.

### <a name="query"></a>Zapytanie
Właściwość *zapytania* składa się z par słowo kluczowe/wartość. Poniżej znajdują się słowa kluczowe, które mogą być używane. Wartości to adresy URL lokacji lub adresy URL biblioteki dokumentów.

> [!NOTE]
> Aby uzyskać wartość dla określonego słowa kluczowego, zalecamy otworzenie usługi SharePoint Online w przeglądarce, przechodzenie do biblioteki dokumentów, którą próbujesz uwzględnić/wykluczyć i skopiować identyfikator URI z przeglądarki. Jest to najprostszy sposób, aby uzyskać wartość do użycia z słowem kluczowym w zapytaniu.

| Słowo kluczowe | Opis zapytania | Przykład |
| ------------- | -------------- | ----------- |
| null | Jeśli wartość jest równa null lub pusta, Indeksuj domyślną bibliotekę dokumentów lub wszystkie biblioteki dokumentów w zależności od nazwy kontenera. | Indeksuj całą zawartość z domyślnej biblioteki lokacji: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | Indeksuj zawartość ze wszystkich bibliotek w zdefiniowanej lokacji w parametrach połączenia. Są one ograniczone do podwitryn lokacji <br><br> Wartość *zapytania* dla tego słowa kluczowego powinna być identyfikatorem URI witryny lub lokacji podrzędnej. | Indeksuj całą zawartość ze wszystkich bibliotek dokumentów w witrynie Moja witryna. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | Indeksuj zawartość z tej biblioteki. <br><br> Wartość *zapytania* dla tego słowa kluczowego powinna być w jednym z następujących formatów: <br><br> Przykład 1: <br><br> *includeLibrary = [lokacja lub podlokacja]/[Biblioteka dokumentów]* <br><br> Przykład 2: <br><br> Identyfikator URI skopiowany z przeglądarki. | Indeksuj całą zawartość z MyDocumentLibrary: <br><br> Przykład 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Przykład 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  Nie Indeksuj zawartości z tej biblioteki. <br><br> Wartość *zapytania* dla tego słowa kluczowego powinna być w jednym z następujących formatów: <br><br> Przykład 1: <br><br> *excludeLibrary = [identyfikator URI lokacji lub podwitryny]/[Biblioteka dokumentów]* <br><br> Przykład 2: <br><br> Identyfikator URI skopiowany z przeglądarki. | Indeksuj całą zawartość ze wszystkich moich bibliotek, z wyjątkiem MyDocumentLibrary: <br><br> Przykład 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Przykład 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>Indeksuj według typu pliku
Można kontrolować, które dokumenty są indeksowane i które są pomijane.

### <a name="include-documents-having-specific-file-extensions"></a>Uwzględnij dokumenty z określonymi rozszerzeniami plików
Można indeksować tylko dokumenty z rozszerzeniami nazw plików, które można określić za pomocą `indexedFileNameExtensions` parametru konfiguracji indeksatora. Wartość jest ciągiem zawierającym listę rozszerzeń plików rozdzielanych przecinkami (z kropką wiodącą). Na przykład, aby zindeksować tylko. PDF i. Dokumenty DOCX, wykonaj następujące czynności:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Wyklucz dokumenty z określonymi rozszerzeniami plików
Można wykluczyć dokumenty z określonymi rozszerzeniami nazw plików z indeksowania przy użyciu `excludedFileNameExtensions` parametru konfiguracji. Wartość jest ciągiem zawierającym listę rozszerzeń plików rozdzielanych przecinkami (z kropką wiodącą). Na przykład, aby indeksować całą zawartość z wyjątkiem tych z. PNG i. Rozszerzenia JPEG, wykonaj następujące czynności:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Jeśli oba `indexedFileNameExtensions` `excludedFileNameExtensions` Parametry i są obecne, platforma Azure wyszukiwanie poznawcze najpierw wyszukać `indexedFileNameExtensions` , a następnie na `excludedFileNameExtensions` . Oznacza to, że jeśli to samo rozszerzenie pliku znajduje się na obu listach, zostanie wyłączone z indeksowania.

## <a name="handling-errors"></a>Obsługa błędów
Domyślnie indeksator usługi SharePoint Online zostaje zatrzymany natychmiast po napotkaniu dokumentu z nieobsługiwanym typem zawartości (na przykład obrazem). Możesz użyć `excludedFileNameExtensions` parametru, aby pominąć niektóre typy zawartości. Jednak może być konieczne indeksowanie dokumentów bez znajomości wszystkich możliwych typów zawartości z wyprzedzeniem. Aby kontynuować indeksowanie, gdy zostanie napotkany nieobsługiwany typ zawartości, ustaw `failOnUnsupportedContentType` dla parametru konfiguracji wartość false:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

W przypadku niektórych dokumentów usługa Azure Wyszukiwanie poznawcze nie może określić typu zawartości lub nie może przetworzyć dokumentu z nieobsługiwanym typem zawartości. Aby zignorować ten tryb niepowodzenia, ustaw `failOnUnprocessableDocument` dla parametru konfiguracji wartość false:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Usługa Azure Wyszukiwanie poznawcze ogranicza rozmiar indeksowanych dokumentów. Limity te są udokumentowane w [limitach usługi w usłudze Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Zbyt duże dokumenty są domyślnie traktowane jako błędy. Można jednak nadal indeksować metadane magazynu z powiększonymi dokumentami, jeśli `indexStorageMetadataOnlyForOversizedDocuments` dla parametru konfiguracji określono wartość true:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Możesz również kontynuować indeksowanie w przypadku wystąpienia błędów w dowolnym momencie przetwarzania, podczas analizowania dokumentów lub podczas dodawania dokumentów do indeksu. Aby zignorować określoną liczbę błędów, należy ustawić `maxFailedItems` `maxFailedItemsPerBatch` wymagane wartości parametrów i konfiguracji. Na przykład:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Zobacz też
+ [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
+ [Właściwości metadanych zawartości używane w usłudze Azure Wyszukiwanie poznawcze](search-blob-metadata-properties.md)
