---
title: Konfigurowanie połączenia z kontem magazynu przy użyciu tożsamości zarządzanej
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak skonfigurować połączenie indeksatora z kontem usługi Azure Storage przy użyciu tożsamości zarządzanej
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 770ded494f050631cd1c373f4b3fa5846c65e01a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275136"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>Konfigurowanie połączenia z kontem usługi Azure Storage przy użyciu tożsamości zarządzanej

Na tej stronie opisano sposób konfigurowania połączenia indeksatora z kontem usługi Azure Storage przy użyciu tożsamości zarządzanej zamiast podawania poświadczeń w parametrach połączenia obiektu źródła danych.

Aby dowiedzieć się więcej o tej funkcji, zalecamy zapoznanie się z tym, co to jest indeksator i jak skonfigurować indeksator dla źródła danych. Więcej informacji można znaleźć w następujących linkach:
* [Omówienie indeksatora](search-indexer-overview.md)
* [Indeksator usługi Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 indeksator](search-howto-index-azure-data-lake-storage.md)
* [Indeksator tabeli platformy Azure](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>Skonfiguruj połączenie

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 — Włączanie tożsamości zarządzanej przypisanej przez system

Gdy jest włączona tożsamość zarządzana przypisana przez system, platforma Azure tworzy tożsamość usługi wyszukiwania, która może służyć do uwierzytelniania w innych usługach platformy Azure w ramach tej samej dzierżawy i subskrypcji. Tej tożsamości można następnie użyć w przypisaniach kontroli dostępu opartej na rolach (RBAC), które umożliwiają dostęp do danych podczas indeksowania.

![Włącz tożsamość zarządzaną przypisaną przez system](./media/search-managed-identities/turn-on-system-assigned-identity.png "Włącz tożsamość zarządzaną przypisaną przez system")

Po wybraniu pozycji **Zapisz** zostanie wyświetlony identyfikator obiektu przypisany do usługi wyszukiwania.

![Identyfikator obiektu](./media/search-managed-identities/system-assigned-identity-object-id.png "Identyfikator obiektu")
 
### <a name="2---add-a-role-assignment"></a>2 — Dodawanie przypisania roli

W tym kroku nadajesz usłudze Azure Wyszukiwanie poznawcze uprawnienia do odczytywania danych z konta magazynu.

1. W Azure Portal przejdź do konta magazynu zawierającego dane, które chcesz zindeksować.
2. Wybieranie **kontroli dostępu (IAM)**
3. Wybierz pozycję **Dodaj** , a następnie **Dodaj przypisanie roli**

    ![Dodaj przypisanie roli](./media/search-managed-identities/add-role-assignment-storage.png "Dodaj przypisanie roli")

4. Wybierz odpowiednie role w zależności od typu konta magazynu, które chcesz zindeksować:
    1. Magazyn obiektów blob platformy Azure wymaga dodania usługi wyszukiwania do roli **czytnika danych obiektów blob magazynu** .
    1. Azure Data Lake Storage Gen2 wymaga dodania usługi wyszukiwania do roli **czytnika danych obiektu blob magazynu** .
    1. Usługa Azure Table Storage wymaga dodania usługi wyszukiwania do roli **czytelnik i dostęp do danych** .
5.  Pozostaw **uprawnienia do przypisywania** jako **użytkownik usługi Azure AD, Grupa lub nazwa główna usługi**
6.  Wyszukaj swoją usługę wyszukiwania, wybierz ją, a następnie wybierz pozycję **Zapisz** .

    Przykład magazynu obiektów blob platformy Azure i Azure Data Lake Storage Gen2:

    ![Dodaj przypisanie roli czytnika danych obiektów blob magazynu](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Dodaj przypisanie roli czytnika danych obiektów blob magazynu")

    Przykład dotyczący usługi Azure Table Storage:

    ![Dodawanie przypisania roli czytnika i dostępu do danych](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Dodawanie przypisania roli czytnika i dostępu do danych")

### <a name="3---create-the-data-source"></a>3 — Tworzenie źródła danych

[Interfejs API REST](/rest/api/searchservice/create-data-source), Azure Portal i [zestaw SDK platformy .NET](/dotnet/api/microsoft.azure.search.models.datasource) obsługują parametry połączenia tożsamości zarządzanej. Poniżej przedstawiono przykład sposobu tworzenia źródła danych do indeksowania danych z konta magazynu przy użyciu [interfejsu API REST](/rest/api/searchservice/create-data-source) i parametrów połączenia zarządzanej tożsamości. Format parametrów połączenia tożsamości zarządzanej jest taki sam dla interfejsu API REST, zestawu .NET SDK i Azure Portal.

W przypadku indeksowania z konta magazynu źródło danych musi mieć następujące wymagane właściwości:

* **Nazwa** jest unikatową nazwą źródła danych w ramach usługi wyszukiwania.
* **Wprowadź**
    * Magazyn obiektów blob platformy Azure: `azureblob`
    * Azure Table Storage: `azuretable`
    * Azure Data Lake Storage Gen2: **Typ** zostanie udostępniony po zarejestrowaniu się w celu uzyskania podglądu przy użyciu [tego formularza](https://aka.ms/azure-cognitive-search/mi-preview-request).
* **uwierzytelniające**
    * Podczas uwierzytelniania przy użyciu tożsamości zarządzanej format **poświadczeń** różni się od czasu, gdy nie jest używana tożsamość zarządzana. Tutaj utworzysz identyfikator zasobu, który nie ma klucza konta ani hasła. ResourceId musi zawierać identyfikator subskrypcji konta magazynu, grupę zasobów konta magazynu oraz nazwę konta magazynu.
    * Format tożsamości zarządzanej: 
        * *ResourceId =/subscriptions/**Identyfikator subskrypcji**/resourceGroups/**nazwę grupy zasobów**/Providers/Microsoft.Storage/storageAccounts/**nazwy konta magazynu**/;*
* **kontener** określa nazwę kontenera lub tabeli na koncie magazynu. Domyślnie można pobrać wszystkie obiekty blob w kontenerze. Jeśli chcesz tylko indeksować obiekty blob w konkretnym katalogu wirtualnym, możesz określić ten katalog przy użyciu opcjonalnego parametru **zapytania** .

Przykład sposobu tworzenia obiektu źródła danych obiektu BLOB przy użyciu [interfejsu API REST](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

### <a name="4---create-the-index"></a>4 — Tworzenie indeksu

Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania.

Oto jak utworzyć indeks z polem z możliwością wyszukiwania, `content` Aby przechowywać tekst wyodrębniony z obiektów blob:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Aby uzyskać więcej informacji na temat tworzenia indeksów, zobacz [Tworzenie indeksu](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 — Tworzenie indeksatora

Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i zawiera harmonogram do automatyzowania odświeżania danych.

Po utworzeniu indeksu i źródła danych można przystąpić do tworzenia indeksatora.

Przykładowa definicja indeksatora dla indeksatora obiektu BLOB:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Ten indeksator będzie uruchamiany co dwie godziny (interwał harmonogramu jest ustawiony na wartość "PT2H"). Aby uruchomić indeksator co 30 minut, ustaw interwał na wartość "PT30M". Najkrótszy obsługiwany interwał to 5 minut. Harmonogram jest opcjonalny — w przypadku pominięcia, indeksator jest uruchamiany tylko raz, gdy zostanie utworzony. Można jednak uruchomić indeksator na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia indeksatora, zapoznaj się z tematem [Tworzenie indeksatora](/rest/api/searchservice/create-indexer).

Więcej informacji o definiowaniu harmonogramów indeksatorów znajduje się w temacie [jak zaplanować indeksatory dla platformy Azure wyszukiwanie poznawcze](search-howto-schedule-indexers.md).

## <a name="accessing-secure-data-in-storage-accounts"></a>Uzyskiwanie dostępu do zabezpieczonych danych na kontach magazynu

Konta usługi Azure Storage można zabezpieczyć za pomocą zapór i sieci wirtualnych. Jeśli chcesz zindeksować zawartość z konta usługi BLOB Storage lub Data Lake konto magazynu Gen2 zabezpieczone za pomocą zapory lub sieci wirtualnej, postępuj zgodnie z instrukcjami dotyczącymi [bezpiecznego uzyskiwania dostępu do danych w kontach magazynu za pomocą wyjątku usługi zaufanej](search-indexer-howto-access-trusted-service-exception.md).

## <a name="see-also"></a>Zobacz też

Dowiedz się więcej o indeksatorach usługi Azure Storage:

* [Indeksator usługi Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 indeksator](search-howto-index-azure-data-lake-storage.md)
* [Indeksator tabeli platformy Azure](search-howto-indexing-azure-tables.md)