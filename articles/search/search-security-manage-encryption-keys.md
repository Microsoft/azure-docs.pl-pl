---
title: Szyfrowanie — w spoczynku przy użyciu kluczy zarządzanych przez klienta
titleSuffix: Azure Cognitive Search
description: Uzupełniaj szyfrowanie po stronie serwera za pośrednictwem indeksów i map synonimów na platformie Azure Wyszukiwanie poznawcze przy użyciu kluczy tworzonych i zarządzanych w programie Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 2dc7458dd905ff84455927c81b4ea93765d4f5cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88928823"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania danych w usłudze Azure Wyszukiwanie poznawcze

Platforma Azure Wyszukiwanie poznawcze automatycznie szyfruje zawartość indeksowaną przy użyciu [kluczy zarządzanych przez usługę](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Jeśli jest wymagana większa ochrona, można uzupełnić domyślne szyfrowanie za pomocą dodatkowej warstwy szyfrowania przy użyciu kluczy tworzonych i zarządzanych w Azure Key Vault. W tym artykule przedstawiono kroki konfigurowania szyfrowania CMK.

Szyfrowanie CMK jest zależne od [Azure Key Vault](../key-vault/general/overview.md). Możesz tworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub używać interfejsów API Azure Key Vault do generowania kluczy szyfrowania. Za pomocą Azure Key Vault można również przeprowadzać inspekcję użycia klucza po [włączeniu rejestrowania](../key-vault/general/logging.md).  

Szyfrowanie przy użyciu kluczy zarządzanych przez klienta jest stosowane do poszczególnych indeksów lub mapowań synonimów, gdy te obiekty są tworzone i nie są określone na poziomie usługi wyszukiwania. Tylko nowe obiekty mogą być szyfrowane. Nie można zaszyfrować zawartości, która już istnieje.

Klucze nie muszą znajdować się w tym samym magazynie kluczy. Pojedyncza usługa wyszukiwania może obsługiwać wiele szyfrowanych indeksów lub map synonimów, z których każda została zaszyfrowana przy użyciu własnych kluczy szyfrowania zarządzanych przez klienta, przechowywanych w różnych magazynach kluczy. Można również mieć indeksy i mapy synonimów w tej samej usłudze, które nie są szyfrowane przy użyciu kluczy zarządzanych przez klienta. 

## <a name="double-encryption"></a>Podwójne szyfrowanie

W przypadku usług utworzonych po 1 sierpnia 2020 i w określonych regionach zakres szyfrowania CMK obejmuje dyski tymczasowe, osiągając [pełne podwójne szyfrowanie](search-security-overview.md#double-encryption), obecnie dostępne w tych regionach: 

+ Zachodnie stany USA 2
+ East US
+ South Central US
+ US Gov Wirginia
+ US Gov Arizona

Jeśli używasz innego regionu lub usługi utworzonej przed 1 sierpnia, CMK szyfrowanie jest ograniczone tylko do dysku z danymi, z wyłączeniem dysków tymczasowych używanych przez usługę.

## <a name="prerequisites"></a>Wymagania wstępne

W tym przykładzie użyto następujących usług i usług. 

+ [Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 

+ [Utwórz zasób Azure Key Vault](../key-vault/secrets/quick-create-portal.md#create-a-vault) lub Znajdź istniejący magazyn w tej samej subskrypcji co usługa Azure wyszukiwanie poznawcze. Ta funkcja ma to samo wymaganie dotyczące subskrypcji.

+ [Azure PowerShell](/powershell/azure/) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) jest używany na potrzeby zadań konfiguracyjnych.

+ Do wywoływania interfejsu API REST, który tworzy indeksy i mapy synonimów, które zawierają parametr klucza szyfrowania, można użyć programu [Poster](search-get-started-postman.md), [Azure PowerShell](./search-get-started-powershell.md) i [zestawu .NET SDK Preview](https://aka.ms/search-sdk-preview) . W tej chwili nie jest obsługiwane Dodawanie klucza do indeksów lub mapowań synonimów.

>[!Note]
> Ze względu na rodzaj szyfrowania z kluczami zarządzanymi przez klienta usługa Azure Wyszukiwanie poznawcze nie będzie mogła pobrać danych, jeśli klucz magazynu kluczy platformy Azure zostanie usunięty. Aby zapobiec utracie danych przez przypadkowe Key Vault usuwania kluczy, należy włączyć ochronę nietrwałego usuwania i przeczyszczania w magazynie kluczy. Funkcja usuwania nietrwałego jest domyślnie włączona, dzięki czemu w razie potrzeby zostaną napotkane tylko problemy. Ochrona przeczyszczania nie jest domyślnie włączona, ale jest wymagana w przypadku szyfrowania Azure Wyszukiwanie poznawcze CMK. Aby uzyskać więcej informacji, zobacz artykuł [usuwanie nietrwałe](../key-vault/general/soft-delete-overview.md) i [przeczyszczanie ochrony](../key-vault/general/soft-delete-overview.md#purge-protection) .

## <a name="1---enable-key-recovery"></a>1 — Włączanie odzyskiwania klucza

Magazyn kluczy musi mieć włączoną ochronę **nietrwałego usuwania** i **przeczyszczania** . Te funkcje można ustawić przy użyciu portalu lub następujących poleceń interfejsu wiersza polecenia platformy Azure.

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

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 — Tworzenie nowego klucza

Jeśli używasz istniejącego klucza do szyfrowania zawartości platformy Azure Wyszukiwanie poznawcze, Pomiń ten krok.

1. [Zaloguj się do Azure Portal](https://portal.azure.com) i Otwórz stronę omówienia magazynu kluczy.

1. Wybierz ustawienie **klucze** w lewym okienku nawigacji, a następnie kliknij pozycję **+ Generuj/Importuj**.

1. W okienku **Utwórz klucz** z listy **opcji**wybierz metodę, która ma zostać użyta do utworzenia klucza. Można **wygenerować** nowy klucz, **przekazać** istniejący klucz lub użyć **Przywróć kopię zapasową** , aby wybrać kopię zapasową klucza.

1. Wprowadź **nazwę** klucza i opcjonalnie wybierz inne właściwości klucza.

1. Kliknij przycisk **Utwórz** , aby rozpocząć wdrażanie.

Zanotuj identyfikator klucza — to jest składający się z **identyfikatora URI wartości klucza**, **nazwy klucza**i **wersji klucza**. Będą one potrzebne do zdefiniowania zaszyfrowanego indeksu w usłudze Azure Wyszukiwanie poznawcze.
 
![Utwórz nowy klucz magazynu kluczy](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Utwórz nowy klucz magazynu kluczy")

## <a name="3---create-a-service-identity"></a>3 — Tworzenie tożsamości usługi

Przypisanie tożsamości do usługi wyszukiwania umożliwia udzielenie Key Vault uprawnień dostępu do usługi wyszukiwania. Twoja usługa wyszukiwania będzie używać swojej tożsamości do uwierzytelniania w usłudze Azure Key magazyn.

Usługa Azure Wyszukiwanie poznawcze obsługuje dwa sposoby przypisywania tożsamości: zarządzaną tożsamością lub zewnętrznie zarządzaną aplikacją Azure Active Directory. 

Jeśli to możliwe, Użyj tożsamości zarządzanej. Jest to najprostszy sposób przypisywania tożsamości do usługi wyszukiwania i działania w większości scenariuszy. Jeśli używasz wielu kluczy w przypadku indeksów i map synonimów lub jeśli Twoje rozwiązanie znajduje się w rozproszonej architekturze, która nie kwalifikuje się do uwierzytelniania opartego na tożsamościach, użyj zaawansowanej [metody Azure Active Directory zarządzanej zewnętrznie](#aad-app) opisanej na końcu tego artykułu.

 Ogólnie rzecz biorąc, zarządzana tożsamość umożliwia usłudze wyszukiwania uwierzytelnianie Azure Key Vault bez zapisywania poświadczeń w kodzie. Cykl życia tego typu tożsamości zarządzanej jest powiązany z cyklem życia usługi wyszukiwania, która może mieć tylko jedną tożsamość zarządzaną. [Dowiedz się więcej o tożsamościach zarządzanych](../active-directory/managed-identities-azure-resources/overview.md).

1. [Zaloguj się do Azure Portal](https://portal.azure.com) i Otwórz stronę omówienia usługi wyszukiwania. 

1. Kliknij pozycję **tożsamość** w lewym okienku nawigacji, zmień jej stan na **włączone**, a następnie kliknij przycisk **Zapisz**.

![Włącz zarządzaną tożsamość](./media/search-enable-msi/enable-identity-portal.png "Włączanie zarządzanej tożsamości")

## <a name="4---grant-key-access-permissions"></a>4 — przyznaj uprawnienia dostępu do klucza

Aby umożliwić usłudze wyszukiwania korzystanie z klucza Key Vault, należy przyznać usłudze wyszukiwania określone uprawnienia dostępu.

Uprawnienia dostępu można odwołać w dowolnym momencie. Po odwołaniu każdy indeks usługi wyszukiwania lub mapa synonimu, która używa tego magazynu kluczy, stanie się bezużyteczny. Przywrócenie uprawnień dostępu do magazynu kluczy w późniejszym czasie spowoduje przywrócenie dostępu do mapy index\synonym. Aby uzyskać więcej informacji, zobacz [bezpieczny dostęp do magazynu kluczy](../key-vault/general/secure-your-key-vault.md).

1. [Zaloguj się do Azure Portal](https://portal.azure.com) i Otwórz stronę omówienia magazynu kluczy. 

1. Wybierz ustawienie **zasady dostępu** w lewym okienku nawigacji, a następnie kliknij pozycję **+ Dodaj nowe**.

   ![Dodawanie nowych zasad dostępu do magazynu kluczy](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Dodawanie nowych zasad dostępu do magazynu kluczy")

1. Kliknij pozycję **Wybierz podmiot zabezpieczeń** i wybierz usługę Azure wyszukiwanie poznawcze. Możesz wyszukać je według nazwy lub identyfikatora obiektu, który został wyświetlony po włączeniu tożsamości zarządzanej.

   ![Wybierz podmiot zabezpieczeń zasad dostępu magazynu kluczy](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Wybierz podmiot zabezpieczeń zasad dostępu magazynu kluczy")

1. Kliknij pozycję **uprawnienia klucza** i wybierz pozycję *Pobierz*, *Odpakuj klucz* i *Zawijaj klucz*. Możesz użyć szablonu *Azure Data Lake Storage lub Azure Storage* , aby szybko wybrać wymagane uprawnienia.

   Na platformie Azure Wyszukiwanie poznawcze należy udzielić następujących [uprawnień dostępu](../key-vault/keys/about-keys.md#key-operations):

   * *Get* — zezwala usłudze wyszukiwania na pobieranie publicznych części klucza w Key Vault
   * *Zawijanie klucza* — umożliwia usłudze wyszukiwania używanie klucza do ochrony wewnętrznego klucza szyfrowania
   * *Odpakuj klucz* — umożliwia usłudze wyszukiwania używanie klucza w celu odpakowania wewnętrznego klucza szyfrowania

   ![Wybieranie uprawnień klucza zasad dostępu do magazynu kluczy](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Wybieranie uprawnień klucza zasad dostępu do magazynu kluczy")

1. W przypadku uprawnień do wpisów **tajnych**wybierz pozycję *Pobierz*.

1. W obszarze **uprawnienia certyfikatów**wybierz pozycję *Pobierz*.

1. Kliknij przycisk **OK** i **Zapisz** zmiany zasad dostępu.

> [!Important]
> Zaszyfrowana zawartość w usłudze Azure Wyszukiwanie poznawcze jest skonfigurowana do używania określonego klucza Azure Key Vault z określoną **wersją**. W przypadku zmiany klucza lub wersji należy zaktualizować indeks lub mapę synonimu, aby korzystała z nowego key\version **przed** usunięciem poprzedniej key\version. Niewykonanie tej czynności spowoduje, że indeks lub mapa synonimu stanie się niezdatna, nie będzie można odszyfrować zawartości po utracie dostępu do klucza.   

## <a name="5---encrypt-content"></a>5 — Szyfruj zawartość

Aby dodać klucz zarządzany przez klienta na indeksie lub mapie synonimów, należy użyć [interfejsu API REST](/rest/api/searchservice/) lub zestawu SDK usługi Search. W portalu nie są ujawniane mapy synonimów ani właściwości szyfrowania. Jeśli używasz prawidłowego interfejsu API, oba indeksy i mapy synonimów obsługują właściwość **EncryptionKey** najwyższego poziomu. 

Za pomocą **identyfikatora URI magazynu kluczy**oraz **nazwy** klucza i **wersji klucza** magazynu kluczy Utwórz definicję **EncryptionKey** w następujący sposób:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Żadna z tych informacji nie jest uważana za klucz tajny i można ją łatwo pobrać, przechodząc do odpowiedniej strony klucza Azure Key Vault w Azure Portal.

Jeśli używasz aplikacji usługi AAD do uwierzytelniania Key Vault, a nie przy użyciu tożsamości zarządzanej, Dodaj **poświadczenia dostępu** do aplikacji usługi AAD do klucza szyfrowania: 
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

## <a name="example-index-encryption"></a>Przykład: szyfrowanie indeksu
Szczegóły dotyczące tworzenia nowego indeksu za pośrednictwem interfejsu API REST można znaleźć w artykule [Tworzenie indeksu (interfejs API REST platformy Azure wyszukiwanie poznawcze)](/rest/api/searchservice/create-index), gdzie jedyną różnicą jest określenie szczegółów klucza szyfrowania w ramach definicji indeksu: 

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
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Teraz możesz wysłać żądanie utworzenia indeksu, a następnie zacząć używać indeksu normalnie.

## <a name="example-synonym-map-encryption"></a>Przykład: szyfrowanie mapy synonimów

Szczegóły tworzenia nowej mapy synonimów za pośrednictwem interfejsu API REST można znaleźć na stronie [Tworzenie mapy synonimów (interfejs API REST platformy Azure wyszukiwanie poznawcze)](/rest/api/searchservice/create-synonym-map), gdzie jedyną różnicą jest określenie informacji o kluczu szyfrowania w ramach definicji mapy synonimów: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Teraz można wysłać żądanie utworzenia mapy synonimów, a następnie rozpocząć korzystanie z niej normalnie.

>[!Important] 
> Chociaż nie można dodać **EncryptionKey** do istniejących indeksów usługi Azure wyszukiwanie poznawcze lub mapowań synonimów, można je zaktualizować, podając różne wartości dla każdego z trzech szczegółów magazynu kluczy (na przykład aktualizacji wersji klucza). W przypadku zmiany na nowy klucz Key Vault lub nowej wersji klucza należy najpierw zaktualizować dowolny indeks Wyszukiwanie poznawcze systemu Azure lub mapę synonimów, która używa tego klucza do korzystania z nowego key\version **przed** usunięciem poprzedniej key\version. Niewykonanie tej czynności spowoduje renderowanie indeksu lub mapy synonimów, ponieważ nie będzie można odszyfrować zawartości po utracie dostępu do klucza.   
> Przywrócenie uprawnień dostępu do magazynu kluczy w późniejszym czasie spowoduje przywrócenie dostępu do zawartości.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a> Zaawansowane: Użyj zarządzanej zewnętrznie aplikacji Azure Active Directory

Jeśli zarządzana tożsamość nie jest możliwa, możesz utworzyć aplikację Azure Active Directory z podmiotem zabezpieczeń dla usługi Azure Wyszukiwanie poznawcze. W tym przypadku tożsamość zarządzana nie jest wykonalna w następujących warunkach:

* Nie można bezpośrednio udzielić uprawnień dostępu do usługi wyszukiwania do magazynu kluczy (na przykład jeśli usługa wyszukiwania znajduje się w innej dzierżawie Active Directory niż Azure Key Vault).

* Pojedyncza usługa wyszukiwania jest wymagana do hostowania wielu szyfrowanych map indexes\synonym, z których każdy korzysta z innego klucza niż inny magazyn kluczy, gdzie każdy Magazyn kluczy musi używać **innej tożsamości** do uwierzytelniania. Jeśli użycie innej tożsamości do zarządzania różnymi magazynami kluczy nie jest wymagane, należy rozważyć użycie opcji tożsamości zarządzanej powyżej.  

Aby można było obsłużyć takie topologie, usługa Azure Wyszukiwanie poznawcze obsługuje używanie aplikacji Azure Active Directory (AAD) do uwierzytelniania między usługą wyszukiwania i Key Vault.    
Aby utworzyć aplikację usługi AAD w portalu:

1. [Utworzenie aplikacji usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).

1. [Pobierz identyfikator aplikacji i klucz uwierzytelniania](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) , ponieważ będą one wymagane do utworzenia zaszyfrowanego indeksu. Wartości należy podać **Identyfikator aplikacji** i **klucz uwierzytelniania**.

>[!Important]
> Przy podejmowaniu decyzji o użyciu uwierzytelniania aplikacji usługi AAD zamiast tożsamości zarządzanej należy wziąć pod uwagę fakt, że usługa Azure Wyszukiwanie poznawcze nie jest autoryzowana do zarządzania swoją aplikacją w usłudze AAD w Twoim imieniu i służy do zarządzania swoją aplikacją w usłudze AAD, na przykład okresowego obrotu kluczem uwierzytelniania aplikacji.
> W przypadku zmiany aplikacji usługi AAD lub jej klucza uwierzytelniania należy najpierw zaktualizować indeks lub mapę synonimów Wyszukiwanie poznawcze platformy Azure, która używa tej aplikacji, aby użyć nowej aplikacji ID\key **przed** usunięciem poprzedniej aplikacji lub jej klucza autoryzacji oraz przed wywołaniem Key Vault dostępu do niej.
> Niewykonanie tej czynności spowoduje renderowanie indeksu lub mapy synonimów, ponieważ nie będzie można odszyfrować zawartości po utracie dostępu do klucza.

## <a name="work-with-encrypted-content"></a>Pracuj z zaszyfrowaną zawartością

Dzięki szyfrowaniu CMK można zauważyć opóźnienia zarówno dla indeksowania, jak i zapytań z powodu dodatkowej operacji szyfrowania/odszyfrowywania. Usługa Azure Wyszukiwanie poznawcze nie rejestruje aktywności szyfrowania, ale można monitorować dostęp do klucza za pomocą rejestrowania magazynu kluczy. Zalecamy [włączenie rejestrowania](../key-vault/general/logging.md) w ramach konfiguracji magazynu kluczy.

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