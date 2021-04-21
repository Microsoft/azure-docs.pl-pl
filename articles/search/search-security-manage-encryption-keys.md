---
title: Szyfrowanie danych w spoczynku przy użyciu kluczy zarządzanych przez klienta
titleSuffix: Azure Cognitive Search
description: Uzupełnianie szyfrowania po stronie serwera przez indeksy i mapy synonimów w Azure Cognitive Search przy użyciu kluczy tworzyć i zarządzać w Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 9679157e7871b043711fff688a8cbb69cf9bb4d8
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813618"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania danych w Azure Cognitive Search

Azure Cognitive Search automatycznie szyfruje indeksowana zawartość w spoczynku przy użyciu [kluczy zarządzanych przez usługę.](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components) Jeśli potrzebna jest większa ochrona, można uzupełnić domyślne szyfrowanie dodatkową warstwą szyfrowania przy użyciu kluczy tworzyć i zarządzać nimi w Azure Key Vault. Ten artykuł zawiera instrukcje konfigurowania szyfrowania kluczy zarządzanego przez klienta.

Szyfrowanie klucza zarządzanego przez klienta zależy [od Azure Key Vault](../key-vault/general/overview.md). Możesz utworzyć własne klucze szyfrowania i zapisać je w magazynie kluczy lub użyć interfejsów API usługi Azure Key Vault do wygenerowania kluczy szyfrowania. Jeśli Azure Key Vault rejestrowanie, możesz również sprawdzić użycie [klucza.](../key-vault/general/logging.md)  

Szyfrowanie za pomocą kluczy zarządzanych przez klienta jest stosowane do poszczególnych indeksów lub map synonimów podczas tworzenia tych obiektów i nie jest określone na poziomie usługi wyszukiwania. Tylko nowe obiekty mogą być szyfrowane. Nie można zaszyfrować zawartości, która już istnieje.

Klucze nie muszą być w tym samym magazynie kluczy. Pojedyncza usługa wyszukiwania może hostować wiele zaszyfrowanych indeksów lub map synonimów, z których każdy jest zaszyfrowany przy użyciu własnych kluczy szyfrowania zarządzanych przez klienta, przechowywanych w różnych magazynach kluczy. Indeksy i mapy synonimów mogą być również przechowywane w tej samej usłudze, która nie jest szyfrowana przy użyciu kluczy zarządzanych przez klienta.

>[!Important]
> W przypadku implementowania kluczy zarządzanych przez klienta należy postępować zgodnie z rygorystycznymi procedurami podczas rutynowej rotacji kluczy magazynu kluczy oraz wpisów tajnych i rejestracji aplikacji usługi Active Directory. Zawsze aktualizuj całą zaszyfrowaną zawartość, aby używać nowych wpisów tajnych i kluczy przed usunięciem starych. W przypadku pominięcia tego kroku nie można odszyfrować zawartości.

## <a name="double-encryption"></a>Podwójne szyfrowanie

W przypadku usług utworzonych po 1 sierpnia 2020 r. i w określonych regionach zakres szyfrowania kluczy zarządzanych przez klienta obejmuje dyski tymczasowe, które osiągają pełne podwójne szyfrowanie [,](search-security-overview.md#double-encryption)obecnie dostępne w tych regionach: 

+ Zachodnie stany USA 2
+ East US
+ South Central US
+ US Gov Wirginia
+ US Gov Arizona

Jeśli używasz innego regionu lub usługi utworzonej przed 1 sierpnia, szyfrowanie klucza zarządzanego jest ograniczone tylko do dysku danych, z wyłączeniem dysków tymczasowych używanych przez usługę.

## <a name="prerequisites"></a>Wymagania wstępne

W tym scenariuszu są używane następujące narzędzia i usługi.

+ [Azure Cognitive Search](search-create-service-portal.md) w [warstwie rozliczanej](search-sku-tier.md#tier-descriptions) (Podstawowa lub wyższych, w dowolnym regionie).
+ [Azure Key Vault](../key-vault/general/overview.md)można utworzyć magazyn kluczy przy użyciu interfejsu [Azure Portal,](../key-vault//general/quick-create-portal.md) [interfejsu](../key-vault//general/quick-create-cli.md)wiersza polecenia platformy Azure [lub Azure PowerShell](../key-vault//general/quick-create-powershell.md). w tej samej subskrypcji co Azure Cognitive Search. Magazyn kluczy musi mieć **włączoną ochronę usuwania nie soft-delete** **i przeczyszczania.**
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Jeśli jej nie masz, [skonfiguruj nową dzierżawę](../active-directory/develop/quickstart-create-new-tenant.md).

Powinna być tam aplikacja wyszukiwania, która może utworzyć zaszyfrowany obiekt. W tym kodzie będziesz odwoływać się do klucza magazynu kluczy i informacji o rejestracji usługi Active Directory. Ten kod może być roboczą aplikacją lub prototypowym kodem, takim jak przykładowy kod [języka C# DotNetHowToEncryptionUsingCMK.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)

> [!TIP]
> Za pomocą metod [Postman,](search-get-started-rest.md) [Visual Studio Code](search-get-started-vs-code.md)lub [Azure PowerShell](./search-get-started-powershell.md)można wywołać interfejsy API REST tworzące indeksy i mapy synonimów, które zawierają parametr klucza szyfrowania. W tej chwili nie jest dostępne dodawanie klucza do indeksów lub map synonimów w portalu.

## <a name="1---enable-key-recovery"></a>1 — Włączanie odzyskiwania kluczy

Ze względu na charakter szyfrowania kluczami zarządzanymi przez klienta nikt nie może pobrać danych w przypadku usunięcia klucza usługi Azure Key Vault. Aby zapobiec utracie danych spowodowanej Key Vault przez usunięcie klucza, należy włączyć ochronę przed usunięciem i przeczyszczaniem w magazynie kluczy. Usuwanie nieuprawniane jest domyślnie włączone, więc problemy będą napotykane tylko wtedy, gdy celowo je wyłączysz. Ochrona przed przeczyszczaniem nie jest domyślnie włączona, ale jest wymagana do szyfrowania kluczy zarządzanych przez klienta w Cognitive Search. Aby uzyskać więcej informacji, zobacz Omówienie ochrony [przed usuwaniem](../key-vault/general/soft-delete-overview.md) nieuruchomiania [i przeczyszczania.](../key-vault/general/soft-delete-overview.md#purge-protection)

Obie właściwości można ustawić przy użyciu portalu, programu PowerShell lub poleceń interfejsu wiersza polecenia platformy Azure.

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

1. [Zaloguj się do Azure Portal](https://portal.azure.com) i otwórz stronę przeglądu magazynu kluczy.

1. Na stronie **Przegląd** w obszarze **Podstawowe elementy** włącz opcję Usuwanie **nie softowe** i **Ochrona przed przeczyszczaniem.**

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

1. Uruchom `Connect-AzAccount` , aby skonfigurować poświadczenia platformy Azure.

1. Uruchom następujące polecenie, aby nawiązać połączenie z magazynem kluczy, `<vault_name>` zastępując element prawidłową nazwą:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault jest tworzony z włączoną obsługą usuwania nie soft. Jeśli magazyn jest wyłączony, uruchom następujące polecenie:

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

+ Jeśli masz instalację [interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli)możesz uruchomić następujące polecenie, aby włączyć wymagane właściwości.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 — Tworzenie klucza w Key Vault

Pomiń ten krok, jeśli masz już klucz w Azure Key Vault.

1. [Zaloguj się do Azure Portal](https://portal.azure.com) i otwórz stronę przeglądu magazynu kluczy.

1. Wybierz **pozycję Klucze** po lewej stronie, a następnie wybierz pozycję + **Generuj/Importuj.**

1. W **okienku Tworzenie** klucza z listy **Opcje** wybierz metodę, której chcesz użyć do utworzenia klucza. Możesz wygenerować **nowy** **klucz,** przekazać istniejący klucz  lub użyć opcji Przywróć kopię zapasową, aby wybrać kopię zapasową klucza.

1. Wprowadź **nazwę** klucza i opcjonalnie wybierz inne właściwości klucza.

1. Wybierz **pozycję Utwórz,** aby rozpocząć wdrażanie.

1. Zanotuj identyfikator klucza — składa się on z identyfikatora  **URI** wartości klucza, nazwy klucza i **wersji klucza**. Identyfikator będzie potrzebny do zdefiniowania zaszyfrowanego indeksu w Azure Cognitive Search.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Tworzenie nowego klucza magazynu kluczy":::

## <a name="3---register-an-app-in-active-directory"></a>3 — Rejestrowanie aplikacji w usłudze Active Directory

1. W [Azure Portal](https://portal.azure.com)znajdź Azure Active Directory zasobu subskrypcji.

1. Po lewej stronie w obszarze **Zarządzanie** wybierz **pozycję Rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja.**

1. Nadaj rejestracji nazwę, być może nazwę podobną do nazwy aplikacji wyszukiwania. Wybierz pozycję **Zarejestruj**.

1. Po utworzeniu rejestracji aplikacji skopiuj identyfikator aplikacji. Ten ciąg należy podać w aplikacji. 

   Jeśli przechodzisz krok po kroku przez [dotNetHowToEncryptionUsingCMK,](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)wklej tę wartość **appsettings.jspliku.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Identyfikator aplikacji w sekcji Podstawowe elementy":::

1. Następnie wybierz **pozycję Certyfikaty & wpisów tajnych** po lewej stronie.

1. Wybierz pozycję **Nowy wpis tajny klienta**. Nadaj kluczowi tajnego nazwę wyświetlaną i wybierz **pozycję Dodaj**.

1. Skopiuj klucz tajny aplikacji. Jeśli przechodzisz krok po kroku przez przykład, wklej tę wartość do **appsettings.jspliku.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Klucz tajny aplikacji":::

## <a name="4---grant-key-access-permissions"></a>4 — Udzielanie uprawnień dostępu do klucza

W tym kroku utworzysz zasady dostępu w Key Vault. Te zasady nadaj aplikacji zarejestrowanej w usłudze Active Directory uprawnienie do używania klucza zarządzanego przez klienta.

W dowolnym momencie można odwołać uprawnienia dostępu. Po odwołaniu dowolny indeks usługi wyszukiwania lub mapa synonimów, która korzysta z tego magazynu kluczy, stanie się bezużytecznym. Przywrócenie uprawnień dostępu do magazynu kluczy w późniejszym czasie spowoduje przywrócenie dostępu do mapy index\synonym. Aby uzyskać więcej informacji, zobacz [Bezpieczny dostęp do magazynu kluczy.](../key-vault/general/security-features.md)

1. Nadal w Azure Portal otwórz stronę Przegląd **magazynu** kluczy. 

1. Wybierz pozycję **Zasady dostępu po** lewej stronie, a następnie wybierz pozycję + Dodaj zasady **dostępu.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Dodawanie nowych zasad dostępu magazynu kluczy":::

1. Wybierz **pozycję Wybierz podmiot** zabezpieczeń i wybierz aplikację zarejestrowaną w usłudze Active Directory. Możesz wyszukać ją według nazwy.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Wybieranie jednostki zasad dostępu magazynu kluczy":::

1. W **uprawnieniach klucza** wybierz pozycję *Pobierz,* *Odpakuj klucz* i *Opakuj klucz.*

1. W **skrypcie Uprawnienia** do tajnych wybierz *pozycję Pobierz*.

1. W **uprawnieniach certyfikatów** wybierz pozycję *Pobierz*.

1. Wybierz **pozycję Dodaj,** a następnie **pozycję Zapisz.**

> [!Important]
> Zaszyfrowana zawartość w Azure Cognitive Search jest skonfigurowana do używania określonego klucza Azure Key Vault z określoną **wersją**. Jeśli zmienisz klucz lub wersję, mapa indeksu lub synonimów musi zostać  zaktualizowana w celu używania nowego klucza\wersji przed usunięciem poprzedniego klucza\wersji. Jeśli tego nie zrobisz, mapa indeksu lub synonimów stanie się bezużytetyczna. Po utracie dostępu do klucza nie będzie można odszyfrować zawartości.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 — Szyfrowanie zawartości

Aby dodać klucz zarządzany przez klienta do indeksu, źródła danych, zestawu umiejętności, indeksatora lub mapy synonimów, należy użyć interfejsu [API REST](/rest/api/searchservice/) wyszukiwania lub zestawu SDK. Portal nie uwidacznia map synonimów ani właściwości szyfrowania. Jeśli używasz prawidłowych indeksów interfejsu API, źródła danych, umiejętności, indeksery i mapy synonimów obsługują właściwość **encryptionKey najwyższego** poziomu.

W tym przykładzie użyto interfejsu API REST z wartościami Azure Key Vault i Azure Active Directory:

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
> Żadne z tych szczegółów magazynu kluczy nie są uznawane za tajne i można je łatwo pobrać, przeglądając odpowiednią stronę Azure Key Vault klucza w Azure Portal.

## <a name="example-index-encryption"></a>Przykład: Szyfrowanie indeksu

Utwórz indeks zaszyfrowany przy użyciu [interfejsu API REST create index Azure Cognitive Search](/rest/api/searchservice/create-index). Użyj właściwości `encryptionKey` , aby określić, którego klucza szyfrowania użyć.
> [!Note]
> Żadne z tych szczegółów magazynu kluczy nie są uznawane za tajne i można je łatwo pobrać, przeglądając odpowiednią Azure Key Vault klucza w Azure Portal.

## <a name="rest-examples"></a>Przykłady rest

W tej sekcji przedstawiono pełny kod JSON dla zaszyfrowanego indeksu i mapy synonimów

### <a name="index-encryption"></a>Szyfrowanie indeksu

Szczegółowe informacje dotyczące tworzenia nowego indeksu za pomocą interfejsu API REST można znaleźć na stronie [Create Index (REST API) (Tworzenie indeksu (interfejs API REST),](/rest/api/searchservice/create-index)gdzie jedyną różnicą w tym przypadku jest określenie szczegółów klucza szyfrowania w ramach definicji indeksu:

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

Teraz możesz wysłać żądanie utworzenia indeksu, a następnie zacząć normalnie korzystać z indeksu.

### <a name="synonym-map-encryption"></a>Szyfrowanie mapy synonimów

Utwórz zaszyfrowaną mapę synonimów przy użyciu interfejsu [API REST Create Synonym Map Azure Cognitive Search](/rest/api/searchservice/create-synonym-map). Użyj właściwości `encryptionKey` , aby określić, którego klucza szyfrowania użyć.

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

Teraz możesz wysłać żądanie utworzenia mapy synonimów, a następnie zacząć z niego korzystać normalnie.

## <a name="example-data-source-encryption"></a>Przykład: Szyfrowanie źródła danych

Utwórz zaszyfrowane źródło danych przy użyciu [interfejsu API REST create data source (Azure Cognitive Search).](/rest/api/searchservice/create-data-source) Użyj właściwości `encryptionKey` , aby określić, którego klucza szyfrowania użyć.

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

Teraz możesz wysłać żądanie utworzenia źródła danych, a następnie zacząć z niego korzystać normalnie.

## <a name="example-skillset-encryption"></a>Przykład: Szyfrowanie skillset

Utwórz zaszyfrowany grupy umiejętności przy użyciu interfejsu API REST create [skillset Azure Cognitive Search skillset](/rest/api/searchservice/create-skillset). Użyj właściwości `encryptionKey` , aby określić, którego klucza szyfrowania użyć.

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

Teraz możesz wysłać żądanie utworzenia skillset i zacząć z niego korzystać normalnie.

## <a name="example-indexer-encryption"></a>Przykład: Szyfrowanie indeksatora

Utwórz zaszyfrowany indeksator przy użyciu [interfejsu API REST create indexer Azure Cognitive Search](/rest/api/searchservice/create-indexer). Użyj właściwości `encryptionKey` , aby określić, którego klucza szyfrowania użyć.

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

Teraz możesz wysłać żądanie utworzenia indeksatora, a następnie zacząć z niego korzystać normalnie.

>[!Important]
> Nie można dodać do istniejących indeksów wyszukiwania ani map synonimów, ale można je zaktualizować, podając różne wartości dla dowolnego z trzech szczegółów magazynu kluczy (na przykład aktualizując `encryptionKey` wersję klucza). Podczas zmiany na nowy klucz Key Vault lub nową wersję klucza, wszystkie indeks wyszukiwania lub mapa synonimów, która  używa klucza, należy najpierw zaktualizować, aby użyć nowego klucza\wersji przed usunięciem poprzedniego klucza\wersji. Jeśli tego nie zrobisz, mapa indeksu lub synonimów stanie się bezużytetyczna, ponieważ nie będzie w stanie odszyfrować zawartości po utracie dostępu do klucza. Przywrócenie uprawnień dostępu magazynu kluczy w późniejszym czasie spowoduje przywrócenie dostępu do zawartości.

## <a name="simpler-alternative-trusted-service"></a>Prostsza alternatywa: Zaufana usługa

W zależności od konfiguracji dzierżawy i wymagań dotyczących uwierzytelniania może być możliwe zaimplementowanie prostszego podejścia do uzyskiwania dostępu do klucza magazynu kluczy. Zamiast tworzyć i używać aplikacji usługi Active Directory, można ustawić usługę wyszukiwania jako zaufaną usługę, włączając dla niego tożsamość zarządzaną przez system. Następnie należy użyć zaufanej usługi wyszukiwania jako zasady zabezpieczeń, a nie aplikacji zarejestrowanej w usłudze AD, aby uzyskać dostęp do klucza magazynu kluczy.

Takie podejście pozwala pominąć kroki rejestracji aplikacji i wpisów tajnych aplikacji oraz uprościć definicję klucza szyfrowania tylko do składników magazynu kluczy (URI, nazwa magazynu, wersja klucza).

Ogólnie rzecz biorąc, tożsamość zarządzana umożliwia usłudze wyszukiwania uwierzytelnianie w usłudze Azure Key Vault bez przechowywania poświadczeń (ApplicationID lub ApplicationSecret) w kodzie. Cykl życia tożsamości zarządzanej tego typu jest powiązany z cyklem życia usługi wyszukiwania, który może mieć tylko jedną tożsamość zarządzaną. Aby uzyskać więcej informacji na temat sposobu działania tożsamości zarządzanych, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)

1. Ułać usługę wyszukiwania jako usługę zaufaną.
   ![Włączanie tożsamości zarządzanej przypisanej przez system](./media/search-managed-identities/turn-on-system-assigned-identity.png "Włączanie tożsamości zarządzanej przypisanej przez system")

1. Podczas konfigurowania zasad dostępu w Azure Key Vault należy wybrać usługę wyszukiwania zaufanego jako zasadę (zamiast aplikacji zarejestrowanej w usłudze AD). Przypisz te same uprawnienia (wiele GET, WRAP, UNWRAP) zgodnie z instrukcjami w kroku udzielanie uprawnień klucza dostępu.

1. Użyj uproszczonej konstrukcji obiektu `encryptionKey` , która pomija właściwości usługi Active Directory.

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

+ Nie można bezpośrednio udzielić usłudze wyszukiwania uprawnień dostępu do magazynu kluczy (na przykład jeśli usługa wyszukiwania znajduje się w innej dzierżawie usługi Active Directory niż Azure Key Vault).

+ Pojedyncza usługa wyszukiwania jest wymagana do hostowania wielu zaszyfrowanych indeksów\map synonimów, z których  każdy używa innego klucza z innego magazynu kluczy, w którym każdy magazyn kluczy musi używać innej tożsamości do uwierzytelniania. Ponieważ usługa wyszukiwania może mieć tylko jedną tożsamość zarządzaną, wymagania dotyczące wielu tożsamości dyskwalifikuje uproszczone podejście w twoim scenariuszu.  

## <a name="work-with-encrypted-content"></a>Praca z zaszyfrowaną zawartością

Dzięki szyfrowaniu klucza zarządzanego przez klienta zauważysz opóźnienie zarówno indeksowania, jak i zapytań ze względu na dodatkową pracę szyfrowania/odszyfrowywania. Azure Cognitive Search nie rejestruje aktywności szyfrowania, ale można monitorować dostęp do klucza za pośrednictwem rejestrowania magazynu kluczy. Zalecamy włączenie [rejestrowania w](../key-vault/general/logging.md) ramach konfiguracji magazynu kluczy.

Oczekuje się, że rotacja kluczy będzie występować w czasie. Zawsze, gdy obracasz klucze, ważne jest, aby postępować zgodnie z tą sekwencją:

1. [Określ klucz używany przez indeks lub mapę synonimów](search-security-get-encryption-keys.md).
1. [Utwórz nowy klucz w magazynie kluczy,](../key-vault/keys/quick-create-portal.md)ale pozostaw oryginalny klucz dostępny.
1. [Zaktualizuj właściwości encryptionKey w](/rest/api/searchservice/update-index) indeksie lub mapie synonimów, aby użyć nowych wartości. Tylko obiekty, które zostały pierwotnie utworzone za pomocą tej właściwości, można zaktualizować w celu użycia innej wartości.
1. Wyłącz lub usuń poprzedni klucz w magazynie kluczy. Monitoruj dostęp do klucza, aby sprawdzić, czy nowy klucz jest używany.

Ze względu na wydajność usługa wyszukiwania buforuje klucz przez maksymalnie kilka godzin. Jeśli wyłączysz lub usuniesz klucz bez podaniem nowego klucza, zapytania będą nadal działać tymczasowo do momentu wygaśnięcia pamięci podręcznej. Jeśli jednak usługa wyszukiwania nie może odszyfrować zawartości, zostanie wyświetlony komunikat: "Dostęp zabroniony. Użyty klucz zapytania mógł zostać odwołany — ponów próbę". 

## <a name="next-steps"></a>Następne kroki

Jeśli nie masz informacji o architekturze zabezpieczeń platformy Azure, zapoznaj się z dokumentacją zabezpieczeń platformy [Azure,](../security/index.yml)a w szczególności z tym artykułem:

> [!div class="nextstepaction"]
> [Szyfrowanie danych w spoczynku](../security/fundamentals/encryption-atrest.md)