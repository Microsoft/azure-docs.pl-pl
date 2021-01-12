---
title: Szyfrowanie — w spoczynku przy użyciu kluczy zarządzanych przez klienta
titleSuffix: Azure Cognitive Search
description: Uzupełniaj szyfrowanie po stronie serwera za pośrednictwem indeksów i map synonimów na platformie Azure Wyszukiwanie poznawcze przy użyciu kluczy tworzonych i zarządzanych w programie Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 6b1079797f1a753fa8362d6e920f3394087d7e9f
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119292"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania danych w usłudze Azure Wyszukiwanie poznawcze

Platforma Azure Wyszukiwanie poznawcze automatycznie szyfruje zawartość indeksowaną przy użyciu [kluczy zarządzanych przez usługę](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Jeśli jest wymagana większa ochrona, można uzupełnić domyślne szyfrowanie za pomocą dodatkowej warstwy szyfrowania przy użyciu kluczy tworzonych i zarządzanych w Azure Key Vault. W tym artykule przedstawiono kroki konfigurowania szyfrowania kluczy zarządzanego przez klienta.

Szyfrowanie klucza zarządzanego przez klienta jest zależne od [Azure Key Vault](../key-vault/general/overview.md). Możesz tworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub używać interfejsów API Azure Key Vault do generowania kluczy szyfrowania. Za pomocą Azure Key Vault można również przeprowadzać inspekcję użycia klucza po [włączeniu rejestrowania](../key-vault/general/logging.md).  

Szyfrowanie przy użyciu kluczy zarządzanych przez klienta jest stosowane do poszczególnych indeksów lub mapowań synonimów, gdy te obiekty są tworzone i nie są określone na poziomie usługi wyszukiwania. Tylko nowe obiekty mogą być szyfrowane. Nie można zaszyfrować zawartości, która już istnieje.

Klucze nie muszą znajdować się w tym samym magazynie kluczy. Pojedyncza usługa wyszukiwania może obsługiwać wiele szyfrowanych indeksów lub map synonimów, z których każda została zaszyfrowana przy użyciu własnych kluczy szyfrowania zarządzanych przez klienta, przechowywanych w różnych magazynach kluczy. Można również mieć indeksy i mapy synonimów w tej samej usłudze, które nie są szyfrowane przy użyciu kluczy zarządzanych przez klienta.

>[!Important]
> W przypadku implementowania kluczy zarządzanych przez klienta należy przestrzegać ścisłych procedur podczas rutynowego rotacji kluczy magazynu kluczy oraz Active Directory wpisów tajnych i rejestracji aplikacji. Zawsze należy aktualizować całą zaszyfrowaną zawartość, aby użyć nowych kluczy tajnych i klucze przed usunięciem starych. Jeśli ten krok zostanie pominięty, nie będzie można odszyfrować zawartości.

## <a name="double-encryption"></a>Podwójne szyfrowanie

W przypadku usług utworzonych po 1 sierpnia 2020 i w określonych regionach zakres szyfrowania klucza zarządzanego przez klienta obejmuje dyski tymczasowe, które mają [pełne podwójne szyfrowanie](search-security-overview.md#double-encryption), obecnie dostępne w następujących regionach: 

+ Zachodnie stany USA 2
+ East US
+ South Central US
+ US Gov Wirginia
+ US Gov Arizona

Jeśli używasz innego regionu lub usługi utworzonej przed 1 sierpnia, szyfrowanie klucza zarządzanego jest ograniczone tylko do dysku danych, z wyłączeniem dysków tymczasowych używanych przez usługę.

## <a name="prerequisites"></a>Wymagania wstępne

W tym scenariuszu są używane następujące narzędzia i usługi.

+ [Platforma Azure wyszukiwanie poznawcze](search-create-service-portal.md) w ramach [warstwy rozliczeniowej](search-sku-tier.md#tier-descriptions) (podstawowa lub wyższa, w dowolnym regionie).
+ [Azure Key Vault](../key-vault/general/overview.md)można utworzyć magazyn kluczy przy użyciu [Azure Portal](../key-vault//general/quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](../key-vault//general/quick-create-cli.md)lub [Azure PowerShell](../key-vault//general/quick-create-powershell.md). w tej samej subskrypcji co usługa Azure Wyszukiwanie poznawcze. Magazyn kluczy musi mieć włączoną ochronę **nietrwałego usuwania** i **przeczyszczania** .
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Jeśli go nie masz, [Skonfiguruj nową dzierżawę](../active-directory/develop/quickstart-create-new-tenant.md).

Należy mieć aplikację wyszukiwania, która może utworzyć zaszyfrowany obiekt. W tym kodzie będziesz odwoływać się do klucza magazynu kluczy i Active Directory informacji o rejestracji. Ten kod może być działającą aplikacją lub kodem prototypu, takim jak [przykładowy kod C# DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> Do wywoływania interfejsów API REST, które tworzą indeksy i mapy synonimów, które zawierają parametr klucza szyfrowania, można użyć programu [Poster](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)lub [Azure PowerShell](./search-get-started-powershell.md). W tej chwili nie jest obsługiwane Dodawanie klucza do indeksów lub mapowań synonimów.

## <a name="1---enable-key-recovery"></a>1 — Włączanie odzyskiwania klucza

Ze względu na rodzaj szyfrowania z kluczami zarządzanymi przez klienta nikt nie może pobrać danych po usunięciu klucza magazynu kluczy platformy Azure. Aby zapobiec utracie danych przez przypadkowe Key Vault usuwania kluczy, należy włączyć ochronę nietrwałego usuwania i przeczyszczania w magazynie kluczy. Funkcja usuwania nietrwałego jest domyślnie włączona, dzięki czemu w razie potrzeby zostaną napotkane tylko problemy. Ochrona przed czyszczeniem nie jest domyślnie włączona, ale jest wymagana do szyfrowania klucza zarządzanego przez klienta w Wyszukiwanie poznawcze. Aby uzyskać więcej informacji, zobacz artykuł [usuwanie nietrwałe](../key-vault/general/soft-delete-overview.md) i [przeczyszczanie ochrony](../key-vault/general/soft-delete-overview.md#purge-protection) .

Obie właściwości można ustawić za pomocą poleceń portalu, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

1. [Zaloguj się do Azure Portal](https://portal.azure.com) i Otwórz stronę omówienia magazynu kluczy.

1. Na stronie **Przegląd** w obszarze **podstawowe** Włącz ochronę przed **usuwaniem** i **przeczyszczaniem**.

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

1. Uruchom `Connect-AzAccount` , aby skonfigurować Twoje poświadczenia platformy Azure.

1. Uruchom następujące polecenie, aby nawiązać połączenie z magazynem kluczy, zastępując je `<vault_name>` prawidłową nazwą:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault jest tworzony z włączonym niewygładzonym usuwaniem. Jeśli jest ona wyłączona w magazynie, uruchom następujące polecenie:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Włącz ochronę przed przeczyszczaniem:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Zapisz aktualizacje:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

+ Jeśli masz [instalację interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), możesz uruchomić następujące polecenie, aby włączyć wymagane właściwości.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 — Tworzenie klucza w Key Vault

Pomiń ten krok, jeśli masz już klucz w Azure Key Vault.

1. [Zaloguj się do Azure Portal](https://portal.azure.com) i Otwórz stronę omówienia magazynu kluczy.

1. Wybierz pozycję **klucze** po lewej stronie, a następnie wybierz pozycję **+ Generuj/Importuj**.

1. W okienku **Utwórz klucz** z listy **opcji** wybierz metodę, która ma zostać użyta do utworzenia klucza. Można **wygenerować** nowy klucz, **przekazać** istniejący klucz lub użyć **Przywróć kopię zapasową** , aby wybrać kopię zapasową klucza.

1. Wprowadź **nazwę** klucza i opcjonalnie wybierz inne właściwości klucza.

1. Wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie.

1. Zanotuj identyfikator klucza — składający się z **identyfikatora URI wartości klucza**, **nazwy klucza** i **wersji klucza**. Musisz mieć identyfikator, aby zdefiniować zaszyfrowany indeks w usłudze Azure Wyszukiwanie poznawcze.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Utwórz nowy klucz magazynu kluczy":::

## <a name="3---register-an-app-in-active-directory"></a>3 — rejestrowanie aplikacji w Active Directory

1. W [Azure Portal](https://portal.azure.com)znajdź zasób Azure Active Directory dla subskrypcji.

1. Po lewej stronie w obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.

1. Nadaj nazwę rejestracji, która jest podobna do nazwy aplikacji wyszukiwania. Wybierz pozycję **Zarejestruj**.

1. Po utworzeniu rejestracji aplikacji Skopiuj identyfikator aplikacji. Musisz podać ten ciąg dla swojej aplikacji. 

   Jeśli przechodzenie odbywa się przez [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), wklej tę wartość do **appsettings.jsna** pliku.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Identyfikator aplikacji w sekcji podstawowe informacje":::

1. Następnie wybierz pozycję **certyfikaty & wpisy tajne** po lewej stronie.

1. Wybierz pozycję **Nowy wpis tajny klienta**. Podaj klucz tajny z nazwą wyświetlaną i wybierz pozycję **Dodaj**.

1. Skopiuj klucz tajny aplikacji. W przypadku przechodzenia przez próbkę należy wkleić tę wartość do **appsettings.jsna** pliku.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Klucz tajny aplikacji":::

## <a name="4---grant-key-access-permissions"></a>4 — przyznaj uprawnienia dostępu do klucza

W tym kroku utworzysz zasady dostępu w Key Vault. Te zasady nadają aplikacji zarejestrowanej za pomocą Active Directory uprawnienia do korzystania z klucza zarządzanego przez klienta.

Uprawnienia dostępu można odwołać w dowolnym momencie. Po odwołaniu każdy indeks usługi wyszukiwania lub mapa synonimu, która używa tego magazynu kluczy, stanie się bezużyteczny. Przywrócenie uprawnień dostępu do magazynu kluczy w późniejszym czasie spowoduje przywrócenie dostępu do mapy index\synonym. Aby uzyskać więcej informacji, zobacz [bezpieczny dostęp do magazynu kluczy](../key-vault/general/secure-your-key-vault.md).

1. W Azure Portal Otwórz stronę **omówienia** magazynu kluczy. 

1. Wybierz pozycję **zasady dostępu** po lewej stronie, a następnie wybierz pozycję **+ Dodaj zasady dostępu**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Dodawanie nowych zasad dostępu do magazynu kluczy":::

1. Wybierz **pozycję Wybierz podmiot zabezpieczeń** i wybierz aplikację, która została zarejestrowana w Active Directory. Możesz wyszukać je według nazwy.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Wybierz podmiot zabezpieczeń zasad dostępu magazynu kluczy":::

1. W obszarze **uprawnienia klucza** wybierz pozycję *Pobierz*, *Odpakuj klucz* i *Zawijaj klucz*.

1. W obszarze **uprawnienia klucza tajnego** wybierz pozycję *Pobierz*.

1. W **uprawnienia certyfikatów** wybierz pozycję *Pobierz*.

1. Wybierz pozycję **Dodaj** , a następnie **Zapisz**.

> [!Important]
> Zaszyfrowana zawartość w usłudze Azure Wyszukiwanie poznawcze jest skonfigurowana do używania określonego klucza Azure Key Vault z określoną **wersją**. W przypadku zmiany klucza lub wersji należy zaktualizować indeks lub mapę synonimu, aby korzystała z nowego key\version **przed** usunięciem poprzedniej key\version. Niewykonanie tej czynności spowoduje, że indeks lub mapa synonimu stanie się niezdatna, nie będzie można odszyfrować zawartości po utracie dostępu do klucza.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 — Szyfruj zawartość

Aby dodać klucz zarządzany przez klienta do indeksu, źródła danych, zestawu umiejętności, indeksatora lub mapy synonimów, należy użyć [interfejsu API REST](/rest/api/searchservice/) lub zestawu SDK usługi Search. W portalu nie są ujawniane mapy synonimów ani właściwości szyfrowania. W przypadku używania prawidłowych indeksów interfejsu API, źródła danych, umiejętności, indeksatory i mapy synonimów obsługują właściwość **EncryptionKey** najwyższego poziomu.

W tym przykładzie jest używany interfejs API REST z wartościami dla Azure Key Vault i Azure Active Directory:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Żadna z tych informacji nie jest uważana za klucz tajny i można ją łatwo pobrać, przechodząc do odpowiedniej strony klucza Azure Key Vault w Azure Portal.

## <a name="example-index-encryption"></a>Przykład: szyfrowanie indeksu

Utwórz zaszyfrowany indeks przy użyciu [interfejsu API Rest tworzenia indeksu usługi Azure wyszukiwanie poznawcze](/rest/api/searchservice/create-index). Użyj `encryptionKey` właściwości, aby określić klucz szyfrowania do użycia.
> [!Note]
> Żadna z tych informacji nie jest uważana za klucz tajny i można ją łatwo pobrać, przechodząc do odpowiedniej strony klucza Azure Key Vault w Azure Portal.

## <a name="rest-examples"></a>Przykłady REST

W tej sekcji przedstawiono pełny kod JSON dla zaszyfrowanego indeksu i mapy synonimów

### <a name="index-encryption"></a>Szyfrowanie indeksu

Szczegółowe informacje na temat tworzenia nowego indeksu za pośrednictwem interfejsu API REST można znaleźć w artykule [Tworzenie indeksu (interfejs API REST)](/rest/api/searchservice/create-index), gdzie jedyną różnicą jest określenie informacji o kluczu szyfrowania w ramach definicji indeksu:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Teraz możesz wysłać żądanie utworzenia indeksu, a następnie zacząć używać indeksu normalnie.

### <a name="synonym-map-encryption"></a>Szyfrowanie mapy synonimów

Tworzenie zaszyfrowanej mapy synonimów przy użyciu [interfejsu API Rest tworzenia mapy synonimów platformy Azure wyszukiwanie poznawcze](/rest/api/searchservice/create-synonym-map). Użyj `encryptionKey` właściwości, aby określić klucz szyfrowania do użycia.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Teraz można wysłać żądanie utworzenia mapy synonimów, a następnie rozpocząć korzystanie z niej normalnie.

## <a name="example-data-source-encryption"></a>Przykład: szyfrowanie źródła danych

Utwórz zaszyfrowane źródło danych przy użyciu polecenia [Create Data Source (interfejs API REST platformy Azure wyszukiwanie poznawcze)](/rest/api/searchservice/create-data-source). Użyj `encryptionKey` właściwości, aby określić klucz szyfrowania do użycia.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Teraz można wysłać żądanie utworzenia źródła danych, a następnie zacząć używać go normalnie.

## <a name="example-skillset-encryption"></a>Przykład: szyfrowanie zestawu umiejętności

Utwórz zaszyfrowaną zestawu umiejętności za pomocą [interfejsu API REST zestawu umiejętności Azure wyszukiwanie poznawcze](/rest/api/searchservice/create-skillset). Użyj `encryptionKey` właściwości, aby określić klucz szyfrowania do użycia.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Możesz teraz wysłać żądanie utworzenia zestawu umiejętności, a następnie rozpocząć korzystanie z niego normalnie.

## <a name="example-indexer-encryption"></a>Przykład: szyfrowanie indeksatora

Utwórz zaszyfrowany indeksator przy użyciu [interfejsu API Rest tworzenia indeksatora usługi Azure wyszukiwanie poznawcze](/rest/api/searchservice/create-indexer). Użyj `encryptionKey` właściwości, aby określić klucz szyfrowania do użycia.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Teraz możesz wysyłać żądanie utworzenia indeksatora, a następnie zacząć używać go normalnie.

>[!Important]
> Chociaż `encryptionKey` nie można dodać do istniejących indeksów wyszukiwania lub mapowań synonimów, można je zaktualizować, podając różne wartości dla każdego z trzech szczegółów magazynu kluczy (na przykład aktualizacji wersji klucza). W przypadku zmiany na nowy klucz Key Vault lub nowej wersji klucza należy najpierw zaktualizować każdy indeks wyszukiwania lub mapę synonimów, które używają klucza, aby użyć nowego key\version **przed** usunięciem poprzedniej key\version. Niewykonanie tej czynności spowoduje renderowanie indeksu lub mapy synonimów, ponieważ nie będzie można odszyfrować zawartości po utracie dostępu do klucza. Chociaż przywrócenie uprawnień dostępu do magazynu kluczy w późniejszym czasie spowoduje przywrócenie dostępu do zawartości.

## <a name="simpler-alternative-trusted-service"></a>Prostsze alternatywa: usługa zaufana

W zależności od konfiguracji dzierżawy i wymagań dotyczących uwierzytelniania można wdrożyć prostsze podejście do uzyskiwania dostępu do klucza magazynu kluczy. Zamiast tworzyć i używać aplikacji Active Directory, można utworzyć usługę wyszukiwania jako zaufaną, włączając dla niej tożsamość zarządzaną przez system. W celu uzyskania dostępu do klucza magazynu kluczy można użyć zaufanej usługi wyszukiwania jako zasady zabezpieczeń, a nie aplikacji zarejestrowanej w usłudze AD.

Takie podejście umożliwia pominięcie kroków rejestracji aplikacji i wpisów tajnych aplikacji oraz uproszczenie definicji klucza szyfrowania tylko do składników magazynu kluczy (identyfikatora URI, nazwy magazynu, wersji klucza).

Ogólnie rzecz biorąc, zarządzana tożsamość umożliwia usłudze wyszukiwania uwierzytelnianie Azure Key Vault bez zapisywania poświadczeń (identyfikatora aplikacji lub ApplicationSecret) w kodzie. Cykl życia tego typu tożsamości zarządzanej jest powiązany z cyklem życia usługi wyszukiwania, która może mieć tylko jedną tożsamość zarządzaną. Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

1. Utwórz usługę wyszukiwania jako zaufaną.
   ![Włącz tożsamość zarządzaną przypisaną przez system](./media/search-managed-identities/turn-on-system-assigned-identity.png "Włącz tożsamość zarządzaną przypisaną przez system")

1. Podczas konfigurowania zasad dostępu w programie Azure Key Vault wybierz usługę zaufanego wyszukiwania jako zasadę (zamiast aplikacji zarejestrowanej w usłudze AD). Przypisz te same uprawnienia (wielokrotne pobieranie, ZAWIJAnie, cofanie ZAWIJAnia) zgodnie z instrukcjami w kroku Udziel uprawnień klucza dostępu.

1. Użyj uproszczonej konstrukcji `encryptionKey` , która pomija właściwości Active Directory.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Warunki, które uniemożliwią przyjęcie tego uproszczonego podejścia, obejmują:

+ Nie można bezpośrednio udzielić uprawnień dostępu do usługi wyszukiwania do magazynu kluczy (na przykład jeśli usługa wyszukiwania znajduje się w innej dzierżawie Active Directory niż Azure Key Vault).

+ Pojedyncza usługa wyszukiwania jest wymagana do hostowania wielu szyfrowanych map indexes\synonym, z których każdy korzysta z innego klucza niż inny magazyn kluczy, gdzie każdy Magazyn kluczy musi używać **innej tożsamości** do uwierzytelniania. Ponieważ usługa wyszukiwania może mieć tylko jedną tożsamość zarządzaną, wymagania dotyczące wielu tożsamości nie kwalifikują uproszczonego podejścia do danego scenariusza.  

## <a name="work-with-encrypted-content"></a>Pracuj z zaszyfrowaną zawartością

Przy użyciu szyfrowania klucza zarządzanego przez klienta można zauważyć opóźnienia zarówno dla indeksowania, jak i zapytań z powodu dodatkowej operacji szyfrowania/odszyfrowywania. Usługa Azure Wyszukiwanie poznawcze nie rejestruje aktywności szyfrowania, ale można monitorować dostęp do klucza za pomocą rejestrowania magazynu kluczy. Zalecamy [włączenie rejestrowania](../key-vault/general/logging.md) w ramach konfiguracji magazynu kluczy.

Oczekiwano rotacji kluczy w czasie. Za każdym razem, gdy zmieniasz klucze, ważne jest, aby postępować zgodnie z tą sekwencją:

1. [Określ klucz używany przez indeks lub mapę synonimów](search-security-get-encryption-keys.md).
1. [Utwórz nowy klucz w magazynie kluczy](../key-vault/keys/quick-create-portal.md), ale pozostaw oryginalny klucz dostępny.
1. [Zaktualizuj właściwości EncryptionKey](/rest/api/searchservice/update-index) na indeksie lub mapie synonimów, aby użyć nowych wartości. Tylko te obiekty, które zostały pierwotnie utworzone przy użyciu tej właściwości, można zaktualizować tak, aby używały innej wartości.
1. Wyłącz lub usuń poprzedni klucz w magazynie kluczy. Monitoruj dostęp do klucza, aby sprawdzić, czy nowy klucz jest używany.

Ze względu na wydajność usługa wyszukiwania buforuje klucz przez maksymalnie kilka godzin. Jeśli wyłączysz lub usuniesz klucz bez udostępniania nowego, zapytania będą nadal działały w sposób tymczasowy do momentu wygaśnięcia pamięci podręcznej. Jeśli jednak usługa wyszukiwania nie może odszyfrować zawartości, zostanie wyświetlony następujący komunikat: "dostęp zabroniony. Użyty klucz zapytania mógł zostać odwołany — spróbuj ponownie. 

## <a name="next-steps"></a>Następne kroki

Jeśli nie znasz architektury zabezpieczeń platformy Azure, zapoznaj się z [dokumentacją zabezpieczeń platformy Azure](../security/index.yml), a w szczególności w tym artykule:

> [!div class="nextstepaction"]
> [Szyfrowanie danych w spoczynku](../security/fundamentals/encryption-atrest.md)