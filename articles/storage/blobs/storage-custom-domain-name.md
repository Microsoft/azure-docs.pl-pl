---
title: Mapowanie domeny niestandardowej na punkt końcowy usługi Azure Blob Storage
titleSuffix: Azure Storage
description: Zamapuj domenę niestandardową na Blob Storage lub punkt końcowy w sieci Web na koncie usługi Azure Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 903413b6ca00600e15ac3af0a93b98a8d67a1c28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88053630"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mapowanie domeny niestandardowej na punkt końcowy usługi Azure Blob Storage

Domenę niestandardową można zamapować na punkt końcowy usługi BLOB Service lub [statyczny punkt końcowy witryny sieci Web](storage-blob-static-website.md) . 

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> To mapowanie działa tylko dla poddomen (na przykład: `www.contoso.com` ). Jeśli chcesz, aby punkt końcowy sieci Web był dostępny w domenie głównej (na przykład: `contoso.com` ), musisz użyć Azure CDN. Aby uzyskać wskazówki, zobacz sekcję [Mapowanie domeny niestandardowej z włączonym protokołem HTTPS](#enable-https) w tym artykule. Ponieważ przechodzenie do tej sekcji tego artykułu, aby włączyć domenę główną domeny niestandardowej, krok w tej sekcji do włączenia protokołu HTTPS jest opcjonalny. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mapuj domenę niestandardową z włączonym protokołem HTTP

Takie podejście jest łatwiejsze, ale umożliwia dostęp tylko do protokołu HTTP. Jeśli konto magazynu jest skonfigurowane tak, aby [wymagało bezpiecznego transferu](../common/storage-require-secure-transfer.md) za pośrednictwem protokołu HTTPS, należy włączyć dostęp HTTPS dla domeny niestandardowej. 

Aby włączyć dostęp za pośrednictwem protokołu HTTPS, zobacz sekcję [Mapowanie domeny niestandardowej z włączonym protokołem HTTPS](#enable-https) w tym artykule. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Mapowanie domeny niestandardowej

> [!IMPORTANT]
> Domena niestandardowa będzie chwilowo niedostępna dla użytkowników podczas kończenia konfiguracji. Jeśli Twoja domena obsługuje obecnie aplikację z umową dotyczącą poziomu usług (SLA), która wymaga zero przestojów, wykonaj kroki opisane w sekcji [Mapowanie domeny niestandardowej o zero przestoju](#zero-down-time) w tym artykule, aby upewnić się, że użytkownicy będą mogli uzyskiwać dostęp do domeny podczas mapowania DNS.

Jeśli nie chcesz, aby dana domena była chwilowo niedostępna dla użytkowników, wykonaj następujące kroki.

: heavy_check_mark: Krok 1: pobieranie nazwy hosta punktu końcowego magazynu.

: heavy_check_mark: Krok 2. utworzenie rekordu nazwy kanonicznej (CNAME) z dostawcą domeny.

: heavy_check_mark: Krok 3: Zarejestruj domenę niestandardową na platformie Azure. 

: heavy_check_mark: Krok 4: testowanie domeny niestandardowej.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Krok 1. Pobieranie nazwy hosta punktu końcowego magazynu 

Nazwa hosta to adres URL punktu końcowego magazynu bez identyfikatora protokołu i końcowego ukośnika. 

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.

2. W okienku menu w obszarze **Ustawienia**wybierz pozycję **Właściwości**.  

3. Skopiuj wartość **podstawowego punktu końcowego usługi BLOB** lub **podstawowego statycznego punktu końcowego witryny sieci Web** do pliku tekstowego. 

4. Usuń identyfikator protokołu (*np.* https) i końcowy ukośnik z tego ciągu. Poniższa tabela zawiera przykłady.

   | Typ punktu końcowego |  endpoint | Nazwa hosta |
   |------------|-----------------|-------------------|
   |Usługa BLOB  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statyczna witryna internetowa  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ustaw tę wartość na później.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Krok 2. Tworzenie rekordu nazwy kanonicznej (CNAME) z dostawcą domeny

Utwórz rekord CNAME, aby wskazywał nazwę hosta. Rekord CNAME jest typem rekordu DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej.

1. Zaloguj się do witryny sieci Web rejestratora domen, a następnie przejdź do strony, aby zarządzać ustawieniami DNS.

   Stronę można znaleźć w sekcji Nazwa **domeny**, **DNS**lub **Zarządzanie serwerem nazw**.

2. Znajdź sekcję do zarządzania rekordami CNAME. 

   Może zajść potrzeba przejścia do strony ustawień zaawansowanych i poszukania **rekordu CNAME**, **aliasu**lub **poddomen**.

3. Utwórz rekord CNAME. W ramach tego rekordu podaj następujące elementy: 

   - Alias domeny podrzędnej, taki jak `www` lub `photos` . Poddomena jest wymagana, domeny główne nie są obsługiwane. 
      
   - Nazwa hosta uzyskana w sekcji [pobieranie nazwy hosta punktu końcowego magazynu](#endpoint) we wcześniejszej części tego artykułu. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Krok 3. Rejestrowanie domeny niestandardowej na platformie Azure

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.

2. W okienku menu w obszarze **Usługa BLOB**wybierz pozycję **domena niestandardowa**.  

   ![Opcja domeny niestandardowej](./media/storage-custom-domain-name/custom-domain-button.png "Domena niestandardowa")

   Zostanie otwarte okienko **domena niestandardowa** .

3. W polu tekstowym **nazwa domeny** wprowadź nazwę domeny niestandardowej, w tym poddomenę.  
   
   Na przykład jeśli Twoja domena to *contoso.com* , a alias domeny podrzędnej to *www*, wprowadź `www.contoso.com` . Jeśli poddomeną są *Zdjęcia*, wprowadź `photos.contoso.com` .

4. Aby zarejestrować domenę niestandardową, wybierz przycisk **Zapisz** .

   Po rozpropagowaniu rekordu CNAME za pośrednictwem serwerów nazw domen (DNS) i jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów BLOB przy użyciu domeny niestandardowej.

#### <a name="step-4-test-your-custom-domain"></a>Krok 4. Testowanie domeny niestandardowej

Aby upewnić się, że domena niestandardowa została zamapowana na punkt końcowy usługi BLOB Service, Utwórz obiekt BLOB w kontenerze publicznym na koncie magazynu. Następnie w przeglądarce internetowej Uzyskuj dostęp do obiektu BLOB przy użyciu identyfikatora URI w następującym formacie: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Na przykład aby uzyskać dostęp do formularza sieci Web w kontenerze *WebForms* w niestandardowej poddomenie *photos.contoso.com* , można użyć następującego identyfikatora URI: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mapowanie domeny niestandardowej o zero przestoju

> [!NOTE]
> Jeśli nie obawiasz się, że domena jest chwilowo niedostępna dla użytkowników, rozważ wykonanie kroków opisanych w sekcji [Mapowanie domeny niestandardowej](#map-a-domain) w tym artykule. Jest to prostsze podejście o mniejszej liczbie kroków.  

Jeśli domena obsługuje obecnie aplikację z umową dotyczącą poziomu usług (SLA), która wymaga zero przestojów, wykonaj następujące kroki, aby upewnić się, że użytkownicy będą mogli uzyskiwać dostęp do domeny podczas mapowania DNS. 

: heavy_check_mark: Krok 1: pobieranie nazwy hosta punktu końcowego magazynu.

: heavy_check_mark: Krok 2. Tworzenie pośredniego rekordu nazwy kanonicznej (CNAME) z dostawcą domeny.

: heavy_check_mark: Krok 3: wstępne Rejestrowanie domeny niestandardowej na platformie Azure.

: heavy_check_mark: Krok 4. Tworzenie rekordu CNAME z dostawcą domeny.

: heavy_check_mark: Krok 5: testowanie domeny niestandardowej.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Krok 1. Pobieranie nazwy hosta punktu końcowego magazynu 

Nazwa hosta to adres URL punktu końcowego magazynu bez identyfikatora protokołu i końcowego ukośnika. 

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.

2. W okienku menu w obszarze **Ustawienia**wybierz pozycję **Właściwości**.  

3. Skopiuj wartość **podstawowego punktu końcowego usługi BLOB** lub **podstawowego statycznego punktu końcowego witryny sieci Web** do pliku tekstowego. 

4. Usuń identyfikator protokołu (*np.* https) i końcowy ukośnik z tego ciągu. Poniższa tabela zawiera przykłady.

   | Typ punktu końcowego |  endpoint | Nazwa hosta |
   |------------|-----------------|-------------------|
   |Usługa BLOB  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statyczna witryna internetowa  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ustaw tę wartość na później.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Krok 2. Tworzenie pośredniego rekordu nazwy kanonicznej (CNAME) za pomocą dostawcy domeny

Utwórz tymczasowy rekord CNAME, aby wskazywał nazwę hosta. Rekord CNAME jest typem rekordu DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej.

1. Zaloguj się do witryny sieci Web rejestratora domen, a następnie przejdź do strony, aby zarządzać ustawieniami DNS.

   Stronę można znaleźć w sekcji Nazwa **domeny**, **DNS**lub **Zarządzanie serwerem nazw**.

2. Znajdź sekcję do zarządzania rekordami CNAME. 

   Może zajść potrzeba przejścia do strony ustawień zaawansowanych i poszukania **rekordu CNAME**, **aliasu**lub **poddomen**.

3. Utwórz rekord CNAME. W ramach tego rekordu podaj następujące elementy: 

   - Alias domeny podrzędnej, taki jak `www` lub `photos` . Poddomena jest wymagana, domeny główne nie są obsługiwane.

     Dodaj `asverify` poddomenę do aliasu. Na przykład: `asverify.www` lub `asverify.photos` .
       
   - Nazwa hosta uzyskana w sekcji [pobieranie nazwy hosta punktu końcowego magazynu](#endpoint) we wcześniejszej części tego artykułu. 

     Dodaj poddomenę `asverify` do nazwy hosta. Przykład: `asverify.mystorageaccount.blob.core.windows.net`.

4. Aby zarejestrować domenę niestandardową, wybierz przycisk **Zapisz** .

   Jeśli rejestracja zakończyła się pomyślnie, w portalu zostanie wyświetlone powiadomienie o pomyślnym zaktualizowaniu konta magazynu. Domena niestandardowa została zweryfikowana przez platformę Azure, ale ruch do domeny nie jest jeszcze kierowany do konta magazynu.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Krok 3. wstępne Rejestrowanie domeny niestandardowej na platformie Azure

Po wstępnym zarejestrowaniu domeny niestandardowej na platformie Azure zezwolisz platformie Azure na rozpoznawanie domeny niestandardowej bez konieczności modyfikowania rekordu DNS dla domeny. W ten sposób, gdy modyfikujesz rekord DNS dla domeny, zostanie ona zamapowana na punkt końcowy obiektu BLOB bez przestoju.

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.

2. W okienku menu w obszarze **Usługa BLOB**wybierz pozycję **domena niestandardowa**.  

   ![Opcja domeny niestandardowej](./media/storage-custom-domain-name/custom-domain-button.png "Domena niestandardowa")

   Zostanie otwarte okienko **domena niestandardowa** .

3. W polu tekstowym **nazwa domeny** wprowadź nazwę domeny niestandardowej, w tym poddomenę.  
   
   Na przykład jeśli Twoja domena to *contoso.com* , a alias domeny podrzędnej to *www*, wprowadź `www.contoso.com` . Jeśli poddomeną są *Zdjęcia*, wprowadź `photos.contoso.com` .

4. Zaznacz pole wyboru **Użyj pośredniego sprawdzania poprawności rekordu CNAME** .

5. Aby zarejestrować domenę niestandardową, wybierz przycisk **Zapisz** .
  
   Po rozpropagowaniu rekordu CNAME za pośrednictwem serwerów nazw domen (DNS) i jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów BLOB przy użyciu domeny niestandardowej.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Krok 4. Tworzenie rekordu CNAME z dostawcą domeny

Utwórz tymczasowy rekord CNAME, aby wskazywał nazwę hosta.

1. Zaloguj się do witryny sieci Web rejestratora domen, a następnie przejdź do strony, aby zarządzać ustawieniami DNS.

   Stronę można znaleźć w sekcji Nazwa **domeny**, **DNS**lub **Zarządzanie serwerem nazw**.

2. Znajdź sekcję do zarządzania rekordami CNAME. 

   Może zajść potrzeba przejścia do strony ustawień zaawansowanych i poszukania **rekordu CNAME**, **aliasu**lub **poddomen**.

3. Utwórz rekord CNAME. W ramach tego rekordu podaj następujące elementy: 

   - Alias domeny podrzędnej, taki jak `www` lub `photos` . Poddomena jest wymagana, domeny główne nie są obsługiwane.
      
   - Nazwa hosta uzyskana w sekcji [pobieranie nazwy hosta punktu końcowego magazynu](#endpoint-2) we wcześniejszej części tego artykułu. 

#### <a name="step-5-test-your-custom-domain"></a>Krok 5. Testowanie domeny niestandardowej

Aby upewnić się, że domena niestandardowa została zamapowana na punkt końcowy usługi BLOB Service, Utwórz obiekt BLOB w kontenerze publicznym na koncie magazynu. Następnie w przeglądarce internetowej Uzyskuj dostęp do obiektu BLOB przy użyciu identyfikatora URI w następującym formacie: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Na przykład aby uzyskać dostęp do formularza sieci Web w kontenerze *WebForms* w niestandardowej poddomenie *photos.contoso.com* , można użyć następującego identyfikatora URI: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Usuń niestandardowe mapowanie domeny

Aby usunąć niestandardowe mapowanie domeny, należy wyrejestrować domenę niestandardową. Użyj jednej z poniższych procedur.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć ustawienie domeny niestandardowej, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.

2. W okienku menu w obszarze **Usługa BLOB**wybierz pozycję **domena niestandardowa**.  
   Zostanie otwarte okienko **domena niestandardowa** .

3. Wyczyść zawartość pola tekstowego zawierającego swoją niestandardową nazwę domeny.

4. Wybierz ikonę **Zapisz**.

Po pomyślnym usunięciu domeny niestandardowej zobaczysz powiadomienie portalu, że konto magazynu zostało pomyślnie zaktualizowane

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby usunąć niestandardową rejestrację domeny, użyj polecenia [AZ Storage account Update](https://docs.microsoft.com/cli/azure/storage/account) CLI polecenie, a następnie określ pusty ciąg ( `""` ) dla `--custom-domain` wartości argumentu.

* Format polecenia:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Przykład polecenia:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby usunąć niestandardową rejestrację domeny, należy użyć polecenia cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) programu PowerShell, a następnie określić pusty ciąg ( `""` ) dla `-CustomDomainName` wartości argumentu.

* Format polecenia:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Przykład polecenia:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Mapuj domenę niestandardową z włączonym protokołem HTTPS

Takie podejście obejmuje więcej czynności, ale umożliwia dostęp do protokołu HTTPS. 

Jeśli nie chcesz, aby użytkownicy mieli dostęp do obiektu BLOB lub zawartości sieci Web przy użyciu protokołu HTTPS, zobacz sekcję [Mapowanie domeny niestandardowej tylko z włączonym protokołem HTTP](#enable-http) w tym artykule. 

Aby zmapować domenę niestandardową i włączyć dostęp do protokołu HTTPS, wykonaj następujące czynności:

1. Włącz [Azure CDN](../../cdn/cdn-overview.md) w obiekcie blob lub w punkcie końcowym sieci Web. 

   Aby uzyskać punkt końcowy Blob Storage, zobacz [integrowanie konta usługi Azure Storage z Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Aby uzyskać statyczny punkt końcowy witryny sieci Web, zobacz [integrowanie statycznej witryny sieci Web z Azure CDN](static-website-content-delivery-network.md).

2. [Mapuj zawartość Azure CDN na domenę niestandardową](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Włącz protokół https w domenie niestandardowej Azure CDN](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Podczas aktualizowania statycznej witryny sieci Web należy wyczyścić zawartość pamięci podręcznej na serwerach brzegowych usługi CDN, przeczyszczając punkt końcowy usługi CDN. Aby uzyskać więcej informacji, zobacz [Przeczyszczanie punktu końcowego usługi Azure CDN](../../cdn/cdn-purge-endpoint.md).

4. Obowiązkowe Zapoznaj się z następującymi wskazówkami:

   * [Tokeny sygnatury dostępu współdzielonego (SAS) z Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures).

   * [Przekierowywanie protokołu HTTP do protokołu HTTPS za pomocą Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

   * [Ceny i rozliczanie w przypadku korzystania z BLOB Storage z Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

## <a name="next-steps"></a>Następne kroki

* [Informacje o hostingu statycznej witryny sieci Web w usłudze Azure Blob Storage](storage-blob-static-website.md)
