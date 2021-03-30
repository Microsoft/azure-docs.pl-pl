---
title: Uaktualnianie do Azure Search .NET SDK w wersji 3
titleSuffix: Azure Cognitive Search
description: Migruj kod do Azure Search .NET SDK wersja 3 ze starszych wersji. Dowiedz się, co nowego i jakie zmiany w kodzie są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e9f4602582a8c24f74d6603b732e7b91d97fa8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002695"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Uaktualnianie do Azure Search .NET SDK w wersji 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

W przypadku korzystania z wersji 2,0-Preview lub starszej [Azure Search .NET SDK](/dotnet/api/overview/azure/search)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 3.

Aby zapoznać się z bardziej ogólnym przewodnikiem dotyczącym zestawu SDK, łącznie z przykładami, zobacz [jak używać Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

Wersja 3 zestawu SDK Azure Search .NET zawiera pewne zmiany z wcześniejszych wersji. Są one w większości drobnych, więc zmiana kodu powinna wymagać tylko minimalnego nakładu pracy. Zobacz [procedurę uaktualniania](#UpgradeSteps) , aby uzyskać instrukcje dotyczące zmiany kodu w celu użycia nowej wersji zestawu SDK.

> [!NOTE]
> Jeśli używasz wersji 1.0.2-Preview lub starszej, należy najpierw uaktualnić do wersji 1,1, a następnie uaktualnić do wersji 3. Aby uzyskać instrukcje [, zobacz Uaktualnianie do Azure Search zestawu SDK platformy .NET w wersji 1,1](search-dotnet-sdk-migration-version-1.md) .
>
> Wystąpienie usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Możesz nadal korzystać z wersji, gdy nie jest już Najnowsza, ale zalecamy przeprowadzenie migracji kodu w celu użycia najnowszej wersji. W przypadku korzystania z interfejsu API REST należy określić wersję interfejsu API w każdym żądaniu za pośrednictwem parametru API-Version. Podczas korzystania z zestawu SDK platformy .NET wersja zestawu SDK określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego zestawu SDK, możesz nadal uruchamiać ten kod bez zmian, nawet jeśli usługa zostanie uaktualniona w celu obsługi nowszej wersji interfejsu API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Co nowego w wersji 3
Wersja 3 zestawu SDK Azure Search .NET jest przeznaczona dla najnowszej ogólnie dostępnej wersji interfejsu API REST Azure Search, w tym 2016-09-01. Dzięki temu można korzystać z wielu nowych funkcji Azure Search z aplikacji .NET, w tym:

* [Analizatory niestandardowe](index-add-custom-analyzers.md)
* Obsługa [usługi azure BLOB Storage](search-howto-indexing-azure-blob-storage.md) i [usługi Azure Table Storage](search-howto-indexing-azure-tables.md) Indexer
* Dostosowywanie indeksatora za pośrednictwem [mapowań pól](search-indexer-field-mappings.md)
* Elementy ETag obsługują bezpieczną współbieżną aktualizację definicji indeksu, indeksatorów i źródeł danych
* Obsługa tworzenia definicji pól indeksu w sposób deklaratywny przez dekorowania nazwy klasy modelu i przy użyciu nowej `FieldBuilder` klasy.
* Obsługa profilu przenośnego .NET Core i .NET 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki do uaktualnienia
Najpierw zaktualizuj odwołanie do programu NuGet `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub kliknij prawym przyciskiem myszy odwołania do projektu i wybierz polecenie "Zarządzaj pakietami NuGet..." w programie Visual Studio.

Po pobraniu przez program NuGet nowych pakietów i ich zależności, należy ponownie skompilować projekt. W zależności od tego, jak kod jest strukturalny, może zostać pomyślnie ponownie odbudowany. Jeśli tak, wszystko jest gotowe!

Jeśli kompilacja nie powiedzie się, powinien pojawić się błąd kompilacji podobny do następującego:

```output
Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)
```

Następnym krokiem jest poprawienie tego błędu kompilacji. Zobacz istotne [zmiany w wersji 3](#ListOfChanges) , aby uzyskać szczegółowe informacje na temat tego, co powoduje błąd i sposób jego rozwiązania.

Mogą pojawić się dodatkowe ostrzeżenia kompilacji dotyczące przestarzałych metod lub właściwości. Ostrzeżenia będą zawierać instrukcje dotyczące użycia zamiast przestarzałej funkcji. Na przykład jeśli aplikacja używa `IndexingParameters.Base64EncodeKeys` właściwości, należy otrzymać ostrzeżenie z informacją o tym, że `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Po naprawieniu błędów kompilacji możesz wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji. Nowe funkcje zestawu SDK opisano w temacie [co nowego w wersji 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Istotne zmiany w wersji 3
Istnieje niewielka liczba istotnych zmian w wersji 3, które mogą wymagać zmiany kodu oprócz ponownego kompilowania aplikacji.

### <a name="indexesgetclient-return-type"></a>Indexes. GetClient zwraca typ
`Indexes.GetClient`Metoda ma nowy zwracany typ. Wcześniej `SearchIndexClient` został zwrócony, ale został zmieniony na `ISearchIndexClient` w wersji 2,0-Preview, a zmiana ta przeniesie do wersji 3. Jest to pomoc dla klientów, którzy chcą zasymulować `GetClient` metodę dla testów jednostkowych, zwracając implementację makiety `ISearchIndexClient` .

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Można to zmienić w celu usunięcia błędów kompilacji:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>Analizatorname, DataType i inne nie są już niejawnie konwertowane na ciągi
Istnieje wiele typów w zestawie SDK Azure Search .NET, który pochodzi od `ExtensibleEnum` . Wcześniej te typy były niejawnie konwertowane na typ `string` . Jednak usterka została odnaleziona w `Object.Equals` implementacji dla tych klas i Naprawianie błędu wymaganego do wyłączenia tej niejawnej konwersji. Jawna konwersja na `string` jest nadal dozwolona.

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Można to zmienić w celu usunięcia błędów kompilacji:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Usunięto przestarzałe składowe

Możesz zobaczyć błędy kompilacji związane z metodami lub właściwościami, które zostały oznaczone jako przestarzałe w wersji 2,0-Preview, a następnie usunięte w wersji 3. W przypadku wystąpienia takich błędów poniżej przedstawiono sposób ich rozwiązywania:

- Jeśli używasz tego konstruktora: `ScoringParameter(string name, string value)` , użyj tego: `ScoringParameter(string name, IEnumerable<string> values)`
- Jeśli używasz `ScoringParameter.Value` właściwości, `ScoringParameter.Values` zamiast tego użyj właściwości lub `ToString` metody.
- Jeśli używasz `SearchRequestOptions.RequestId` właściwości, użyj `ClientRequestId` zamiast niej właściwości.

### <a name="removed-preview-features"></a>Usunięte funkcje w wersji zapoznawczej

W przypadku uaktualniania z wersji 2,0 — wersja zapoznawcza do wersji 3 należy pamiętać, że obsługa kodu JSON i woluminu CSV dla indeksatorów obiektów BLOB została usunięta, ponieważ te funkcje są nadal w wersji zapoznawczej. W szczególnych przypadkach następujące metody klasy zostały `IndexingParametersExtensions` usunięte:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Jeśli aplikacja ma twardą zależność od tych funkcji, nie będzie można przeprowadzić uaktualnienia do wersji 3 zestawu Azure Search .NET SDK. Możesz nadal używać wersji 2,0-Preview. Należy jednak pamiętać, że **nie zalecamy korzystania z wersji zapoznawczej zestawów SDK w aplikacjach produkcyjnych**. Funkcje w wersji zapoznawczej służą wyłącznie do oceny i mogą ulec zmianie.

## <a name="conclusion"></a>Podsumowanie
Jeśli potrzebujesz więcej szczegółowych informacji na temat korzystania z zestawu SDK Azure Search platformy .NET, zobacz [temat jak to zrobić](search-howto-dotnet-sdk.md).

Poznamy Twoją opinię na temat zestawu SDK. Jeśli wystąpią problemy, skontaktuj się z nami, aby uzyskać pomoc dotyczącą [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub zestawu SDK platformy Azure](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest prefiksem "[Azure Search]".

Dziękujemy za korzystanie z Azure Search!