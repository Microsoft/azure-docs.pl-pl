---
title: Mapowanie domeny niestandardowej na punkt Azure Blob Storage końcowego
titleSuffix: Azure Storage
description: Mapowanie domeny niestandardowej na punkt Blob Storage lub internetowy punkt końcowy na koncie usługi Azure Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 45ae3d80202bfb29074461f899798d278eb0895b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538355"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mapowanie domeny niestandardowej na punkt Azure Blob Storage końcowego

Domenę niestandardową można mapować na punkt końcowy usługi blob lub punkt [końcowy statycznej witryny](storage-blob-static-website.md) internetowej. 

> [!NOTE] 
> To mapowanie działa tylko w przypadku poddomen (na przykład: `www.contoso.com` ). Jeśli chcesz, aby internetowy punkt końcowy był dostępny w domenie głównej (na przykład : ), musisz `contoso.com` użyć Azure CDN. Aby uzyskać wskazówki, zobacz [sekcję Mapowanie domeny niestandardowej przy użyciu protokołu HTTPS](#enable-https) w tym artykule. Ponieważ zamierzasz włączyć domenę główną domeny niestandardowej w tej sekcji tego artykułu, krok w tej sekcji włączania protokołu HTTPS jest opcjonalny. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mapowanie domeny niestandardowej z włączonym tylko protokółem HTTP

Takie podejście jest łatwiejsze, ale umożliwia tylko dostęp HTTP. Jeśli konto magazynu jest skonfigurowane tak, aby wymagało [bezpiecznego transferu za](../common/storage-require-secure-transfer.md) pośrednictwem protokołu HTTPS, należy włączyć dostęp za pośrednictwem protokołu HTTPS dla domeny niestandardowej. 

Aby włączyć dostęp przy użyciu protokołu HTTPS, zobacz [sekcję Mapowanie domeny niestandardowej przy użyciu](#enable-https) protokołu HTTPS w tym artykule. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Mapowanie domeny niestandardowej

> [!IMPORTANT]
> Domena niestandardowa będzie przez krótki czas niedostępna dla użytkowników podczas konfigurowania. Jeśli domena obsługuje obecnie aplikację z umową poziomu usług (SLA), która nie [](#zero-down-time) wymaga przestoju, wykonaj kroki opisane w sekcji Mapowanie domeny niestandardowej bez przestojów w tym artykule, aby upewnić się, że użytkownicy mogą uzyskać dostęp do domeny podczas mapowania DNS.

Jeśli nie masz niezatwierdzonych informacji, że domena jest przez krótki czas niedostępna dla użytkowników, wykonaj następujące kroki.

:heavy_check_mark: Krok 1. Uzyskiwanie nazwy hosta punktu końcowego magazynu.

:heavy_check_mark: Krok 2. Utworzenie rekordu nazwy kanonicznej (CNAME) przy użyciu dostawcy domeny.

:heavy_check_mark: Krok 3. Rejestrowanie domeny niestandardowej na platformie Azure. 

:heavy_check_mark: Krok 4. Testowanie domeny niestandardowej.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Krok 1. Uzyskiwanie nazwy hosta punktu końcowego magazynu 

Nazwa hosta to adres URL punktu końcowego magazynu bez identyfikatora protokołu i końcowego ukośnika. 

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego konta magazynu.

2. W okienku menu w obszarze **Ustawienia wybierz** pozycję **Właściwości**.  

3. Skopiuj wartość podstawowego punktu **końcowego usługi Blob Service** lub punktu końcowego podstawowej statycznej witryny **internetowej** do pliku tekstowego. 
  
   > [!NOTE]
   > Punkt końcowy magazynu usługi Data Lake nie jest obsługiwany (na przykład: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Usuń identyfikator protokołu (na przykład : `HTTPS` ) i ukośnik nakońcowy z tego ciągu. W poniższej tabeli przedstawiono przykłady.

   | Typ punktu końcowego |  endpoint | nazwa hosta |
   |------------|-----------------|-------------------|
   |blob service  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statyczna witryna internetowa  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ustaw tę wartość na później.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Krok 2. Tworzenie rekordu nazwy kanonicznej (CNAME) przy użyciu dostawcy domeny

Utwórz rekord CNAME, aby wskazać nazwę hosta. Rekord CNAME jest typem rekordu systemu nazw domen (DNS), który mapuje nazwę domeny źródłowej na nazwę domeny docelowej.

1. Zaloguj się do witryny rejestratora domen, a następnie przejdź do strony zarządzania ustawieniem DNS.

   Stronę można znaleźć w sekcji o nazwie **Nazwa domeny,** **DNS** lub **Zarządzanie serwerem nazw.**

2. Znajdź sekcję zarządzania rekordami CNAME. 

   Może być konieczne przejdź do strony ustawień zaawansowanych i poszukaj nazwy **CNAME,** **aliasu** lub **poddomeny**.

3. Utwórz rekord CNAME. W ramach tego rekordu podaj następujące elementy: 

   - Alias poddomeny, taki `www` jak lub `photos` . Poddomena jest wymagana, domeny główne nie są obsługiwane. 
      
   - Nazwa hosta uzyskana w sekcji Uzyskiwanie [nazwy hosta punktu końcowego magazynu](#endpoint) wcześniej w tym artykule. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Krok 3. Rejestrowanie domeny niestandardowej na platformie Azure

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego konta magazynu.

2. W okienku menu w obszarze **Blob Service** wybierz pozycję **Domena niestandardowa**.

   > [!NOTE]
   > Ta opcja nie jest wyświetlana na kontach z włączoną funkcją hierarchicznej przestrzeni nazw. W przypadku tych kont wykonaj ten krok przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

   ![opcja domeny niestandardowej](./media/storage-custom-domain-name/custom-domain-button.png "domena niestandardowa")

   Zostanie **otwarte okienko Domena** niestandardowa.

3. W **polu tekstowym** Domain name (Nazwa domeny) wprowadź nazwę domeny niestandardowej, w tym domenę podrzędną  
   
   Jeśli na przykład domena jest *contoso.com* a aliasem poddomeny jest *www,* wprowadź `www.contoso.com` . Jeśli poddomena to *photos ,* wprowadź `photos.contoso.com` .

4. Aby zarejestrować domenę niestandardową, wybierz **przycisk** Zapisz.

   Po propagacji rekordu CNAME za pośrednictwem serwerów nazw domen (DNS) i jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów blob przy użyciu domeny niestandardowej.

##### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Uruchom następujące polecenie programu PowerShell

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- Zastąp `<resource-group-name>` symbol zastępczy nazwą grupy zasobów.

- Zastąp `<storage-account-name>` symbol zastępczy nazwą konta magazynu.

- Zastąp symbol `<custom-domain-name>` zastępczy nazwą domeny niestandardowej, w tym poddomeną.

  Jeśli na przykład domena jest *contoso.com* a aliasem poddomeny jest *www,* wprowadź `www.contoso.com` . Jeśli poddomena to *zdjęcia,* wprowadź `photos.contoso.com` .

Po propagacji rekordu CNAME za pośrednictwem serwerów nazw domen (DNS) i jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów blob przy użyciu domeny niestandardowej.

##### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Uruchom następujące polecenie programu PowerShell

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- Zastąp symbol `<resource-group-name>` zastępczy nazwą grupy zasobów.

- Zastąp `<storage-account-name>` symbol zastępczy nazwą konta magazynu.

- Zastąp symbol `<custom-domain-name>` zastępczy nazwą domeny niestandardowej, w tym poddomeną.

  Jeśli na przykład domena jest *contoso.com,* a alias domeny podrzędnej to *www,* wprowadź `www.contoso.com` . Jeśli poddomena to *zdjęcia,* wprowadź `photos.contoso.com` .

Po propagacji rekordu CNAME za pośrednictwem serwerów nazw domen (DNS) i jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów blob przy użyciu domeny niestandardowej.

---

#### <a name="step-4-test-your-custom-domain"></a>Krok 4. Testowanie domeny niestandardowej

Aby potwierdzić, że domena niestandardowa jest mapowana na punkt końcowy usługi blob, utwórz obiekt blob w kontenerze publicznym w ramach konta magazynu. Następnie w przeglądarce internetowej uzyskaj dostęp do obiektu blob przy użyciu URI w następującym formacie: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Aby na przykład uzyskać dostęp do formularza internetowego w kontenerze w photos.contoso.com poddomenie niestandardowej, możesz użyć `myforms` następującego URI: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mapowanie domeny niestandardowej bez przestojów

> [!NOTE]
> Jeśli nie masz niezatwierdzonych informacji, że domena jest krótko niedostępna dla użytkowników, rozważ użycie kroków w sekcji Mapowanie domeny [niestandardowej](#map-a-domain) w tym artykule. Jest to prostsze podejście z mniejszą liczbą kroków.  

Jeśli domena obsługuje obecnie aplikację z umową poziomu usług (SLA), która nie wymaga przestoju, wykonaj następujące kroki, aby upewnić się, że użytkownicy mogą uzyskać dostęp do twojej domeny podczas mapowania DNS. 

:heavy_check_mark: Krok 1. Uzyskiwanie nazwy hosta punktu końcowego magazynu.

:heavy_check_mark: Krok 2. Tworzenie rekordu pośredniczącej nazwy kanonicznej (CNAME) przy użyciu dostawcy domeny.

:heavy_check_mark: Krok 3. Wstępne zarejestrowanie domeny niestandardowej na platformie Azure.

:heavy_check_mark: Krok 4. Tworzenie rekordu CNAME przy użyciu dostawcy domeny.

:heavy_check_mark: Krok 5. Testowanie domeny niestandardowej.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Krok 1. Uzyskiwanie nazwy hosta punktu końcowego magazynu 

Nazwa hosta to adres URL punktu końcowego magazynu bez identyfikatora protokołu i końcowego ukośnika. 

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego konta magazynu.

2. W okienku menu w obszarze **Ustawienia** wybierz pozycję **Właściwości**.  

3. Skopiuj wartość podstawowego punktu **końcowego usługi Blob Service** lub punktu końcowego podstawowej statycznej witryny **internetowej** do pliku tekstowego. 

   > [!NOTE]
   > Punkt końcowy magazynu usługi Data Lake nie jest obsługiwany (na przykład: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Usuń identyfikator protokołu (na przykład : `HTTPS` ) i ukośnik nakońcowy z tego ciągu. W poniższej tabeli przedstawiono przykłady.

   | Typ punktu końcowego |  endpoint | nazwa hosta |
   |------------|-----------------|-------------------|
   |blob service  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statyczna witryna internetowa  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ustaw tę wartość na później.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Krok 2. Tworzenie pośredniczącego rekordu nazwy kanonicznej (CNAME) przy użyciu dostawcy domeny

Utwórz tymczasowy rekord CNAME, aby wskazać nazwę hosta. Rekord CNAME jest typem rekordu DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej.

1. Zaloguj się do witryny rejestratora domen, a następnie przejdź do strony zarządzania ustawieniem DNS.

   Stronę można znaleźć w sekcji o nazwie **Nazwa domeny,** **DNS** lub **Zarządzanie serwerem nazw.**

2. Znajdź sekcję zarządzania rekordami CNAME. 

   Może być konieczne przejdź do strony ustawień zaawansowanych i poszukaj nazwy **CNAME,** **aliasu** lub **poddomeny**.

3. Utwórz rekord CNAME. W ramach tego rekordu podaj następujące elementy: 

   - Alias poddomeny, taki `www` jak lub `photos` . Poddomena jest wymagana, domeny główne nie są obsługiwane.

     Dodaj `asverify` poddomenę do aliasu. Na przykład: `asverify.www` lub `asverify.photos` .
       
   - Nazwa hosta uzyskana w sekcji Uzyskiwanie [nazwy hosta punktu końcowego magazynu](#endpoint) wcześniej w tym artykule. 

     Dodaj poddomenę `asverify` do nazwy hosta. Na przykład: `asverify.mystorageaccount.blob.core.windows.net`.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Krok 3. Wstępne rejestrowanie domeny niestandardowej na platformie Azure

Wstępna rejestracja domeny niestandardowej na platformie Azure umożliwia platformie Azure rozpoznawanie domeny niestandardowej bez konieczności modyfikowania rekordu DNS dla domeny. Dzięki temu po zmodyfikowaniu rekordu DNS dla domeny zostanie on zamapowany do punktu końcowego obiektu blob bez przestoju.

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego konta magazynu.

2. W okienku menu w obszarze **Blob Service** wybierz pozycję **Domena niestandardowa**.

   > [!NOTE]
   > Ta opcja nie jest wyświetlana na kontach z włączoną funkcją hierarchicznej przestrzeni nazw. W przypadku tych kont wykonaj ten krok przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

   ![opcja domeny niestandardowej](./media/storage-custom-domain-name/custom-domain-button.png "domena niestandardowa")

   Zostanie **otwarte okienko Domena niestandardowa.**

3. W polu **tekstowym** Nazwa domeny wprowadź nazwę domeny niestandardowej, w tym domenę podrzędną  
   
   Jeśli na przykład domena jest contoso.com *alias* domeny podrzędnej to *www,* wprowadź `www.contoso.com` . Jeśli poddomena to *zdjęcia,* wprowadź `photos.contoso.com` .

4. Zaznacz pole **wyboru Użyj pośredniej walidacji rekordu CNAME.**

5. Aby zarejestrować domenę niestandardową, wybierz **przycisk** Zapisz.
  
   Jeśli rejestracja zakończy się pomyślnie, portal powiadomi Cię, że twoje konto magazynu zostało pomyślnie zaktualizowane. Domena niestandardowa została zweryfikowana przez platformę Azure, ale ruch do domeny nie jest jeszcze przekierowywane do konta magazynu do momentu utworzenia rekordu CNAME u dostawcy domeny. Zrobisz to w następnej sekcji.

##### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Uruchom następujące polecenie programu PowerShell

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- Zastąp `<resource-group-name>` symbol zastępczy nazwą grupy zasobów.

- Zastąp `<storage-account-name>` symbol zastępczy nazwą konta magazynu.

- Zastąp symbol `<custom-domain-name>` zastępczy nazwą domeny niestandardowej, w tym poddomeną.

  Jeśli na przykład domena jest *contoso.com* a aliasem poddomeny jest *www,* wprowadź `www.contoso.com` . Jeśli poddomena to *photos ,* wprowadź `photos.contoso.com` .

Ruch do domeny nie jest jeszcze przekierowywane do konta magazynu, dopóki nie utworzysz rekordu CNAME u dostawcy domeny. Zrobisz to w następnej sekcji.

##### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Uruchom następujące polecenie programu PowerShell

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- Zastąp `<resource-group-name>` symbol zastępczy nazwą grupy zasobów.

- Zastąp `<storage-account-name>` symbol zastępczy nazwą konta magazynu.

- Zastąp symbol `<custom-domain-name>` zastępczy nazwą domeny niestandardowej, w tym poddomeną.

  Jeśli na przykład domena jest *contoso.com* a aliasem poddomeny jest *www,* wprowadź `www.contoso.com` . Jeśli poddomena to *photos ,* wprowadź `photos.contoso.com` .

Ruch do domeny nie jest jeszcze przekierowywane do konta magazynu, dopóki nie utworzysz rekordu CNAME u dostawcy domeny. Zrobisz to w następnej sekcji.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Krok 4. Tworzenie rekordu CNAME za pomocą dostawcy domeny

Utwórz tymczasowy rekord CNAME, aby wskazać nazwę hosta.

1. Zaloguj się do witryny internetowej rejestratora domen, a następnie przejdź do strony zarządzania ustawieniem DNS.

   Stronę można znaleźć w sekcji o nazwie **Nazwa domeny,** **DNS** lub **Zarządzanie serwerem nazw.**

2. Znajdź sekcję zarządzania rekordami CNAME. 

   Być może trzeba będzie przejść do strony ustawień zaawansowanych i poszukać nazwy **CNAME,** **aliasu** lub **poddomeny.**

3. Utwórz rekord CNAME. W ramach tego rekordu podaj następujące elementy: 

   - Alias poddomeny, taki `www` jak lub `photos` . Poddomena jest wymagana, domeny główne nie są obsługiwane.
      
   - Nazwa hosta uzyskana w sekcji Uzyskiwanie [nazwy hosta punktu](#endpoint-2) końcowego magazynu wcześniej w tym artykule. 

#### <a name="step-5-test-your-custom-domain"></a>Krok 5. Testowanie domeny niestandardowej

Aby potwierdzić, że domena niestandardowa jest mapowana na punkt końcowy usługi blob, utwórz obiekt blob w kontenerze publicznym w ramach konta magazynu. Następnie w przeglądarce internetowej uzyskaj dostęp do obiektu blob przy użyciu URI w następującym formacie: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Aby na przykład uzyskać dostęp do formularza internetowego w kontenerze w photos.contoso.com poddomenie niestandardowej, możesz użyć `myforms` następującego URI: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Usuwanie niestandardowego mapowania domeny

Aby usunąć niestandardowe mapowanie domeny, wyrejestruj domenę niestandardową. Użyj jednej z poniższych procedur.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego konta magazynu.

2. W okienku menu w obszarze **Blob Service** wybierz pozycję **Domena niestandardowa**.  
   Zostanie **otwarte okienko Domena niestandardowa.**

3. Wyczyść zawartość pola tekstowego zawierającego niestandardową nazwę domeny.

4. Wybierz ikonę **Zapisz**.

Po pomyślnym usunięciu domeny niestandardowej zostanie wyświetlony komunikat w portalu z powiadomieniem o pomyślnym zaktualizowaniu konta magazynu.

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby usunąć niestandardową rejestrację domeny, użyj polecenia cmdlet programu PowerShell [Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) a następnie określ pusty ciąg ( ) dla `""` `-CustomDomainName` wartości argumentu.

* Format polecenia:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Przykładowe polecenie:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby usunąć niestandardową rejestrację domeny, użyj [polecenia az storage account update](/cli/azure/storage/account) CLI, a następnie podaj pusty ciąg ( ) dla wartości `""` `--custom-domain` argumentu.

* Format polecenia:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Przykładowe polecenie:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Mapowanie domeny niestandardowej z włączonym protokołem HTTPS

To podejście obejmuje więcej kroków, ale umożliwia dostęp za pośrednictwem protokołu HTTPS. 

Jeśli użytkownicy nie potrzebują dostępu do obiektu blob lub zawartości internetowej przy użyciu protokołu HTTPS, zobacz sekcję Mapowanie domeny niestandardowej z obsługą tylko protokołu [HTTP](#enable-http) w tym artykule. 

1. Włącz [Azure CDN](../../cdn/cdn-overview.md) w obiekcie blob lub internetowym punkcie końcowym. 

   Aby uzyskać Blob Storage punktu końcowego, zobacz [Integrate an Azure storage account with Azure CDN (Integrowanie konta usługi Azure Storage z Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md)). 

   Aby uzyskać informacje na temat punktu końcowego statycznej witryny internetowej, zobacz [Integrate a static website with Azure CDN (Integrowanie statycznej witryny internetowej z Azure CDN](static-website-content-delivery-network.md)).

2. [Mapuj Azure CDN na domenę niestandardową.](../../cdn/cdn-map-content-to-custom-domain.md)

3. [Włącz protokół HTTPS w domenie Azure CDN niestandardowej.](../../cdn/cdn-custom-ssl.md)

   > [!NOTE] 
   > Podczas aktualizowania statycznej witryny internetowej pamiętaj o wyczyszczeniu buforowanej zawartości na serwerach brzegowych usługi CDN przez wyczyszczenie punktu końcowego usługi CDN. Aby uzyskać więcej informacji, zobacz [Przeczyszczanie punktu końcowego usługi Azure CDN](../../cdn/cdn-purge-endpoint.md).

4. (Opcjonalnie) Zapoznaj się z następującymi wskazówkami:

   * [Tokeny sygnatury dostępu współdzielonego (SAS) z Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures).

   * [Przekierowywanie http-to-HTTPS za pomocą Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

   * [Cennik i rozliczenia w przypadku korzystania Blob Storage z Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#pricing-and-billing).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat hostowania statycznej witryny internetowej w usłudze Azure Blob Storage](storage-blob-static-website.md)