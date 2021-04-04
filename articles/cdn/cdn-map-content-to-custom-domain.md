---
title: 'Samouczek: Dodawanie domeny niestandardowej do punktu końcowego'
titleSuffix: Azure Content Delivery Network
description: Skorzystaj z tego samouczka, aby dodać domenę niestandardową do punktu końcowego usługi Azure Content Delivery Network, aby nazwa domeny była widoczna w adresie URL.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b0e8f2b14d506eb408660b939a7c925a33215cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537749"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>Samouczek: Dodawanie domeny niestandardowej do punktu końcowego

W tym samouczku przedstawiono sposób dodawania domeny niestandardowej do punktu końcowego usługi Azure Content Delivery Network (CDN). 

Nazwa punktu końcowego w profilu CDN jest poddomeną azureedge.net. Domyślnie podczas dostarczania zawartości domena profilu usługi CDN jest uwzględniana w adresie URL.

Przykładowy adres URL to **https://contoso.azureedge.net/photo.png**.

Azure CDN oferuje opcję kojarzenia domeny niestandardowej z punktem końcowym usługi CDN. Ta opcja dostarcza zawartość z domeną niestandardową w adresie URL zamiast w domenie domyślnej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME.
> - Kojarzenie domeny niestandardowej z punktem końcowym usługi CDN.
> - Weryfikowanie domeny niestandardowej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Przed wykonaniem kroków opisanych w tym samouczku Utwórz profil sieci CDN i co najmniej jeden punkt końcowy usługi CDN. 
    * Aby uzyskać więcej informacji, zobacz [Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN](cdn-create-new-endpoint.md).

* Jeśli nie masz domeny niestandardowej, Kup ją przy użyciu dostawcy domeny. 
    * Aby uzyskać więcej informacji, zobacz [kupowanie niestandardowej nazwy domeny](../app-service/manage-custom-dns-buy-domain.md).

* Jeśli używasz platformy Azure do hostowania [domen DNS](../dns/dns-overview.md), Deleguj domenę niestandardową do Azure DNS. 
    * Aby uzyskać więcej informacji, zobacz [delegowanie domeny do Azure DNS](../dns/dns-delegate-domain-azure-dns.md).

* Jeśli używasz dostawcy domeny do obsługi domeny DNS, Kontynuuj [Tworzenie rekordu DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Tworzenie rekordu CNAME usługi DNS

Aby można było użyć domeny niestandardowej z punktem końcowym Azure CDN, należy najpierw utworzyć rekord nazwy kanonicznej (CNAME) z Azure DNS lub dostawcą DNS, aby wskazać punkt końcowy usługi CDN. 

Rekord CNAME jest rekordem DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej. 

W przypadku usługi Azure CDN nazwa domeny źródłowej to nazwa domeny niestandardowej, a nazwa domeny docelowej to nazwa hosta punktu końcowego usługi CDN. 

Azure CDN kieruje ruchem skierowanym do źródłowej domeny niestandardowej do docelowej nazwy hosta punktu końcowego usługi CDN po zweryfikowaniu rekordu CNAME.

Domena niestandardowa i jej poddomena można skojarzyć z jednym punktem końcowym w danym momencie. 

Używaj wielu rekordów CNAME dla różnych poddomen z tej samej domeny niestandardowej dla różnych usług platformy Azure.

Możesz zmapować domenę niestandardową z różnymi domenami poddomeny do tego samego punktu końcowego usługi CDN.

> [!NOTE]
> Ten samouczek używa typu rekordu CNAME. Jeśli używasz typów rekordów AAAA lub, wykonaj te same kroki i Zastąp rekord CNAME wybranym typem.

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

Azure DNS używa rekordów aliasów dla zasobów platformy Azure w ramach tej samej subskrypcji.

Aby dodać rekord aliasu dla punktu końcowego Azure CDN:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W menu po lewej stronie wybierz pozycję **wszystkie zasoby** , a następnie Azure DNS strefy dla domeny niestandardowej.

3. W strefie DNS dla domeny niestandardowej wybierz pozycję **+ zestaw rekordów**.

4. W obszarze **Dodaj zestaw rekordów** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa  | Wprowadź alias, którego chcesz użyć dla punktu końcowego usługi CDN. Na przykład **www**. |
    | Typ  | Wybierz pozycję **CNAME**. |
    | Zestaw rekordów aliasu | Wybierz pozycję **Tak**. |
    | Typ aliasu | Wybierz pozycję **zasób platformy Azure**. |
    | Wybierz subskrypcję | Wybierz subskrypcję. |
    | Zasób platformy Azure | Wybierz punkt końcowy Azure CDN. |

5. Zmień **czas wygaśnięcia** rekordu na wartość.

6. Wybierz przycisk **OK**.

# <a name="dns-provider"></a>[**Dostawca DNS**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Mapowanie tymczasowej domeny podrzędnej cdnverify

W przypadku mapowania istniejącej domeny, która znajduje się w środowisku produkcyjnym, należy wziąć pod uwagę zagadnienia specjalne. 

Krótki okres przestoju domeny może wystąpić podczas rejestrowania domeny niestandardowej w Azure Portal.

Aby uniknąć przerwy w działaniu sieci Web, należy zmapować domenę niestandardową na nazwę hosta punktu końcowego usługi CDN przy użyciu domeny podrzędnej Azure **cdnverify** . Ten proces tworzy tymczasowe mapowanie CNAME. 

Przy użyciu tej metody użytkownicy mogą uzyskiwać dostęp do domeny bez zakłóceń w trakcie mapowania DNS. 

Jeśli zagadnienia przestoju produkcji nie są istotne, można bezpośrednio zmapować domenę niestandardową na punkt końcowy usługi CDN. Kontynuuj [Mapowanie trwałej domeny niestandardowej](#map-the-permanent-custom-domain).

Aby utworzyć rekord CNAME z poziomu poddomeny cdnverify:

1. Zaloguj się do witryny sieci Web dostawcy DNS dla domeny niestandardowej.

2. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy                    | Typ  | Element docelowy                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Źródło: Wprowadź nazwę domeny niestandardowej, w tym poddomenę cdnverify, w następującym formacie: **cdnverify. \<custom-domain-name>**
        - Na przykład: **cdnverify.www.contoso.com**

    - Wpisz: wprowadź lub wybierz **CNAME**.

    - Miejsce docelowe: Wprowadź nazwę hosta punktu końcowego usługi CDN, w tym poddomenę cdnverify, w następującym formacie: **cdnverify. \<endpoint-name> . azureedge.net**. 
        - Na przykład: **cdnverify.contoso.azureedge.NET**

3. Zapisz zmiany.

## <a name="map-the-permanent-custom-domain"></a>Mapowanie trwałej domeny niestandardowej

W tej sekcji zamapujesz stałą domenę niestandardową na punkt końcowy usługi CDN. 

Aby utworzyć rekord CNAME dla domeny niestandardowej:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy          | Typ  | Element docelowy           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Źródło: wprowadź niestandardową nazwę domeny.
        - Na przykład: **www.contoso.com**

    - Wpisz: wprowadź lub wybierz **CNAME**.

    - Miejsce docelowe: Wprowadź nazwę hosta punktu końcowego usługi CDN w następującym formacie: **\<endpoint-name> . azureedge.NET**. 
        - Na przykład: **contoso.azureedge.NET**

3. Zapisz zmiany.

4. Jeśli wcześniej utworzono rekord CNAME tymczasowej domeny podrzędnej cdnverify, usuń go. 

5. Jeśli używasz tej domeny niestandardowej w środowisku produkcyjnym po raz pierwszy, postępuj zgodnie z instrukcjami dotyczącymi [kojarzenia domeny niestandardowej z punktem końcowym usługi CDN](#associate-the-custom-domain-with-your-cdn-endpoint) i [Sprawdź domenę niestandardową](#verify-the-custom-domain).

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Tworzenie skojarzenia domeny niestandardowej z punktem końcowym usługi CDN

Po zarejestrowaniu domeny niestandardowej można dodać ją do punktu końcowego usługi CDN. 


---
# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) i przejdź do profilu usługi CDN zawierającego punkt końcowy, który ma być mapowany na domenę niestandardową.
    
2. Na stronie **Profil CDN** wybierz punkt końcowy usługi CDN do skojarzenia z domeną niestandardową.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="Wybór punktu końcowego usługi CDN" border="true":::
    
3. Wybierz pozycję **+ domena niestandardowa**. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="Przycisk Dodaj domenę niestandardową" border="true":::

4. W obszarze **Dodaj domenę niestandardową** **Nazwa hosta punktu końcowego** jest wstępnie wypełniona i pochodzi od adresu URL punktu końcowego usługi CDN: **\<endpoint-hostname>** . azureedge.NET. Nie można zmienić tej nazwy.

5. W obszarze **Niestandardowa nazwa hosta** wprowadź domenę niestandardową (razem z poddomeną), która ma być używana jako domena źródłowa rekordu CNAME. 
    1. Na przykład **www.contoso.com** lub **CDN.contoso.com**. **Nie używaj nazwy domeny** podrzędnej cdnverify.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="Dodaj domenę niestandardową" border="true":::

6. Wybierz pozycję **Dodaj**.

   Platforma Azure sprawdzi, czy dla wprowadzonej nazwy domeny niestandardowej istnieje rekord CNAME. Jeśli rekord CNAME jest poprawny, domena niestandardowa zostanie zweryfikowana. 

   Propagacja ustawień nowej domeny niestandardowej do wszystkich węzłów brzegowych usługi CDN może zająć trochę czasu: 
    - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
    - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
    - W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut.   

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

1. Zaloguj się do Azure PowerShell:

```azurepowershell-interactive
    Connect-AzAccount

```
2. Użyj [New-AzCdnCustomDomain](/powershell/module/az.cdn/new-azcdncustomdomain) , aby zmapować domenę niestandardową do punktu końcowego usługi CDN. 

    * Zastąp **myendpoint8675.azureedge.NET** adresem URL punktu końcowego.
    * Zastąp **myendpoint8675** nazwą punktu końcowego usługi CDN.
    * Zastąp **www.contoso.com** nazwą domeny niestandardowej.
    * Zastąp **myCDN** nazwą profilu CDN.
    * Zastąp **myResourceGroupCDN** nazwą grupy zasobów.

```azurepowershell-interactive
    $parameters = @{
        Hostname = 'myendpoint8675.azureedge.net'
        EndPointName = 'myendpoint8675'
        CustomDomainName = 'www.contoso.com'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    New-AzCdnCustomDomain @parameters
```

Platforma Azure sprawdzi, czy dla wprowadzonej nazwy domeny niestandardowej istnieje rekord CNAME. Jeśli rekord CNAME jest poprawny, domena niestandardowa zostanie zweryfikowana. 

   Propagacja ustawień nowej domeny niestandardowej do wszystkich węzłów brzegowych usługi CDN może zająć trochę czasu: 

- W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
- W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
- W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut.   


---
## <a name="verify-the-custom-domain"></a>Weryfikowanie domeny niestandardowej

Po zakończeniu rejestracji domeny niestandardowej upewnij się, że domena niestandardowa odwołuje się do punktu końcowego usługi CDN.
 
1. Upewnij się, że masz publiczną zawartość buforowaną w punkcie końcowym. Jeśli na przykład punkt końcowy usługi CDN został skojarzony z kontem magazynu, usługa Azure CDN będzie buforować zawartość w kontenerze publicznym. Ustaw kontener tak, aby zezwalał na dostęp publiczny i zawiera co najmniej jeden plik do przetestowania domeny niestandardowej.

2. W przeglądarce przejdź do adresu pliku przy użyciu domeny niestandardowej. Na przykład jeśli domena niestandardowa to `www.contoso.com` , adres URL pliku w pamięci podręcznej powinien wyglądać podobnie do następującego adresu URL: `http://www.contoso.com/my-public-container/my-file.jpg` . Sprawdź, czy wynik jest taki sam jak w przypadku uzyskiwania dostępu do punktu końcowego usługi CDN bezpośrednio pod adresem **\<endpoint-hostname>** . azureedge.NET.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

---
# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal-cleanup)

Jeśli nie chcesz już kojarzyć punktu końcowego z domeną niestandardową, Usuń domenę niestandardową, wykonując następujące czynności:
 
1. W profilu usługi CDN wybierz punkt końcowy z domeną niestandardową, która ma zostać usunięta.

2. Na stronie **Punkt końcowy** w obszarze domen niestandardowych kliknij prawym przyciskiem myszy domenę niestandardową, którą chcesz usunąć, a następnie wybierz polecenie **Usuń** z menu kontekstowego. Wybierz pozycję **Tak**.

   Skojarzenie domeny niestandardowej z punktem końcowym zostanie usunięte.

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell-cleanup)

Jeśli nie chcesz już kojarzyć punktu końcowego z domeną niestandardową, Usuń domenę niestandardową, wykonując następujące czynności:

1. Użyj [Remove-AzCdnCustomDomain](/powershell/module/az.cdn/remove-azcdncustomdomain) , aby usunąć domenę niestandardową z punktu końcowego:

    * Zastąp **myendpoint8675** nazwą punktu końcowego usługi CDN.
    * Zastąp **www.contoso.com** nazwą domeny niestandardowej.
    * Zastąp **myCDN** nazwą profilu CDN.
    * Zastąp **myResourceGroupCDN** nazwą grupy zasobów.


```azurepowershell-interactive
    $parameters = @{
        CustomDomainName = 'www.contoso.com'
        EndPointName = 'myendpoint8675'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    Remove-AzCdnCustomDomain @parameters
```

---
## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME.
> - Kojarzenie domeny niestandardowej z punktem końcowym usługi CDN.
> - Weryfikowanie domeny niestandardowej.

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować protokół HTTPS w domenie niestandardowej usługi Azure CDN.

> [!div class="nextstepaction"]
> [Samouczek: konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](cdn-custom-ssl.md)