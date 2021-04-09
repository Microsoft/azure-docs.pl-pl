---
title: Jak używać prywatnych punktów końcowych w usłudze Speech Services
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać usług mowy z prywatnymi punktami końcowymi udostępnionymi przez link prywatny platformy Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alexeyo
ms.openlocfilehash: 6971c6f0959135c7de1f41bcd49adde514f87941
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625487"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>Korzystanie z usług mowy za pomocą prywatnego punktu końcowego

[Link prywatny platformy Azure](../../private-link/private-link-overview.md) umożliwia łączenie się z usługami na platformie Azure przy użyciu [prywatnego punktu końcowego](../../private-link/private-endpoint-overview.md). Prywatny punkt końcowy to prywatny adres IP, który jest dostępny tylko w ramach określonej [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md) i podsieci.

W tym artykule wyjaśniono, jak skonfigurować i korzystać z prywatnego linku i prywatnych punktów końcowych za pomocą usług mowy w usłudze Azure Cognitive Services.
W tym artykule opisano sposób późniejszego usuwania prywatnych punktów końcowych, ale nadal używany jest zasób mowy.

> [!NOTE]
> Przed kontynuowaniem zapoznaj [się z tematem jak używać sieci wirtualnych z Cognitive Services](../cognitive-services-virtual-networks.md).



## <a name="create-a-custom-domain-name"></a>Utwórz niestandardową nazwę domeny

Prywatne punkty końcowe wymagają [niestandardowej nazwy domeny podrzędnej dla Cognitive Services](../cognitive-services-custom-subdomains.md). Wykonaj poniższe instrukcje, aby utworzyć jeden dla zasobu mowy.

> [!WARNING]
> Zasób mowy, który używa niestandardowej nazwy domeny współdziała z usługami mowy w inny sposób.
> Być może trzeba będzie dostosować kod aplikacji w taki sposób, aby korzystał z zasobu mowy z prywatnym punktem końcowym, a także używać zasobu mowy _bez_ prywatnego punktu końcowego.
> Oba scenariusze mogą być konieczne, ponieważ przełączanie do niestandardowej nazwy domeny _nie_ jest odwracalne.
>
> Po włączeniu niestandardowej nazwy domeny operacja jest [nieodwracalna](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). Jedynym sposobem powrotu do [nazwy regionalnej](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) jest utworzenie nowego zasobu mowy.
>
> Jeśli zasób mowy zawiera wiele skojarzonych modeli niestandardowych i projektów utworzonych za pośrednictwem programu [Speech Studio](https://speech.microsoft.com/), zdecydowanie zalecamy podjęcie próby konfiguracji z zasobem testowym przed zmodyfikowaniem zasobu używanego w środowisku produkcyjnym.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby utworzyć niestandardową nazwę domeny przy użyciu Azure Portal, wykonaj następujące kroki:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/) i zaloguj się na swoje konto platformy Azure.
1. Wybierz wymagany zasób mowy.
1. W grupie **Zarządzanie zasobami** w okienku po lewej stronie wybierz pozycję **Sieć**.
1. Na karcie **zapory i sieci wirtualne** wybierz opcję **Generuj niestandardową nazwę domeny**. Zostanie wyświetlony nowy prawy panel z instrukcjami, aby utworzyć unikatową niestandardową poddomenę dla zasobu.
1. Na panelu **Generuj niestandardową nazwę domeny** , wprowadź niestandardową nazwę domeny. Pełna domena niestandardowa będzie wyglądać następująco: `https://{your custom name}.cognitiveservices.azure.com` . 
    
    Należy pamiętać, że po utworzeniu niestandardowej nazwy domeny _nie można_ jej zmienić.
    
    Po wprowadzeniu niestandardowej nazwy domeny wybierz pozycję **Zapisz**.
1. Po zakończeniu operacji w grupie **Zarządzanie zasobami** wybierz pozycję **klucze i punkt końcowy**. Upewnij się, że nowa nazwa punktu końcowego zasobu jest uruchamiana w następujący sposób: `https://{your custom name}.cognitiveservices.azure.com` .

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby utworzyć niestandardową nazwę domeny przy użyciu programu PowerShell, upewnij się, że na komputerze jest zainstalowany program PowerShell w wersji 7. x lub nowszej przy użyciu modułu Azure PowerShell w wersji 5.1.0 lub nowszej. Aby wyświetlić wersje tych narzędzi, wykonaj następujące kroki:

1. W oknie programu PowerShell wprowadź:

    `$PSVersionTable`

    Upewnij się, że `PSVersion` wartość to 7. x lub nowsza. Aby uaktualnić program PowerShell, postępuj zgodnie z instrukcjami dotyczącymi [instalowania różnych wersji programu PowerShell](/powershell/scripting/install/installing-powershell).

1. W oknie programu PowerShell wprowadź:

    `Get-Module -ListAvailable Az`

    Jeśli nic się nie pojawia lub jeśli ta wersja modułu Azure PowerShell jest wcześniejsza niż 5.1.0, postępuj zgodnie z instrukcjami w części [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps) w celu uaktualnienia.

Przed kontynuowaniem Uruchom polecenie, `Connect-AzAccount` Aby utworzyć połączenie z platformą Azure.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Sprawdź, czy jest dostępna niestandardowa nazwa domeny

Sprawdź, czy domena niestandardowa, która ma być używana, jest dostępna. Poniższy kod potwierdza, że domena jest dostępna przy użyciu operacji [Sprawdź dostępność domeny](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) w interfejsie API REST Cognitive Services.

> [!TIP]
> Poniższy kod *nie* będzie działał w Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Jeśli żądana nazwa jest dostępna, zobaczysz odpowiedź w następujący sposób:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Jeśli nazwa jest już zajęta, zobaczysz następującą odpowiedź:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Utwórz niestandardową nazwę domeny

Aby włączyć niestandardową nazwę domeny dla wybranego zasobu mowy, użyj polecenia cmdlet [Set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) .

> [!WARNING]
> Po pomyślnym uruchomieniu poniższego kodu utworzysz niestandardową nazwę domeny dla zasobu mowy. Należy pamiętać, że tej nazwy *nie można* zmienić.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Ta sekcja wymaga najnowszej wersji interfejsu wiersza polecenia platformy Azure. Jeśli używasz Azure Cloud Shell, Najnowsza wersja jest już zainstalowana.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Sprawdź, czy nazwa domeny niestandardowej jest dostępna

Sprawdź, czy domena niestandardowa, której chcesz używać, jest bezpłatna. Użyj metody [Sprawdź dostępność domeny](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) w interfejsie API REST Cognitive Services.

Skopiuj następujący blok kodu, Wstaw preferowaną niestandardową nazwę domeny i Zapisz w pliku `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Skopiuj plik do bieżącego folderu lub Przekaż go do Azure Cloud Shell i uruchom następujące polecenie. Zamień wartość `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` na identyfikator swojej subskrypcji platformy Azure.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Jeśli żądana nazwa jest dostępna, zobaczysz odpowiedź w następujący sposób:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Jeśli nazwa jest już zajęta, zobaczysz następującą odpowiedź:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="turn-on-a-custom-domain-name"></a>Włącz niestandardową nazwę domeny

Aby użyć niestandardowej nazwy domeny z wybranym zasobem mowy, użyj polecenia [AZ cognitiveservices Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Wybierz subskrypcję platformy Azure, która zawiera zasób mowy. Jeśli Twoje konto platformy Azure ma tylko jedną aktywną subskrypcję, możesz pominąć ten krok. Zamień wartość `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` na identyfikator swojej subskrypcji platformy Azure.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Ustaw niestandardową nazwę domeny na wybrany zasób. Zastąp przykładowe wartości parametrów wartościami rzeczywistymi i uruchom następujące polecenie.

> [!WARNING]
> Po pomyślnym wykonaniu poniższego polecenia utworzysz niestandardową nazwę domeny dla zasobu mowy. Należy pamiętać, że tej nazwy *nie można* zmienić.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="turn-on-private-endpoints"></a>Włącz prywatne punkty końcowe

Zalecamy używanie [prywatnej strefy DNS](../../dns/private-dns-overview.md) dołączonej do sieci wirtualnej z aktualizacjami niezbędnymi dla prywatnych punktów końcowych. Podczas procesu aprowizacji można utworzyć prywatną strefę DNS. W przypadku korzystania z własnego serwera DNS może być również konieczna zmiana konfiguracji DNS.

*Przed* udostępnieniem prywatnych punktów końcowych dla produkcyjnego zasobu mowy należy podjąć decyzję dotyczącą strategii DNS. I przetestuj zmiany DNS, zwłaszcza jeśli używasz własnego serwera DNS.

Użyj jednego z poniższych artykułów, aby utworzyć prywatne punkty końcowe.
Te artykuły używają aplikacji sieci Web jako przykładowego zasobu, aby udostępnić je za pomocą prywatnych punktów końcowych.

- [Tworzenie prywatnego punktu końcowego za pomocą Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego za pomocą Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Tworzenie prywatnego punktu końcowego za pomocą interfejsu wiersza polecenia platformy Azure](../../private-link/create-private-endpoint-cli.md)

Użyj tych parametrów zamiast parametrów w wybranym artykule:

| Ustawienie             | Wartość                                    |
|---------------------|------------------------------------------|
| Typ zasobu       | **Microsoft. CognitiveServices/konta** |
| Zasób            | **\<your-speech-resource-name>**         |
| Docelowy zasób podrzędny | **organizacyjnego**                              |

**System DNS dla prywatnych punktów końcowych:** Zapoznaj się z ogólnymi zasadami [usługi DNS dla prywatnych punktów końcowych w ramach zasobów Cognitive Services](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Następnie upewnij się, że konfiguracja DNS działa prawidłowo, wykonując testy opisane w poniższych sekcjach.

### <a name="resolve-dns-from-the-virtual-network"></a>Rozpoznawanie systemu DNS z sieci wirtualnej

To sprawdzenie jest *wymagane*.

Wykonaj następujące kroki, aby przetestować niestandardowy wpis DNS z sieci wirtualnej:

1. Zaloguj się do maszyny wirtualnej znajdującej się w sieci wirtualnej, do której został podłączony prywatny punkt końcowy. 
1. Otwórz wiersz polecenia systemu Windows lub powłokę bash, uruchom `nslookup` polecenie i upewnij się, że pomyślnie rozpozna niestandardową nazwę domeny zasobu.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. Upewnij się, że adres IP jest zgodny z adresem IP prywatnego punktu końcowego.

### <a name="resolve-dns-from-other-networks"></a>Rozpoznawanie systemu DNS z innych sieci

To sprawdzenie należy wykonać tylko wtedy, gdy włączono opcję **wszystkie sieci** lub opcję dostęp do **wybranych sieci i prywatnych punktów końcowych** w sekcji **Sieć** zasobu. 

Jeśli planujesz uzyskać dostęp do zasobu za pomocą tylko prywatnego punktu końcowego, możesz pominąć tę sekcję.

1. Zaloguj się na komputerze podłączonym do sieci, która może uzyskać dostęp do zasobu.
2. Otwórz wiersz polecenia systemu Windows lub powłokę bash, uruchom `nslookup` polecenie i upewnij się, że pomyślnie rozpozna niestandardową nazwę domeny zasobu.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

> [!NOTE]
> Rozpoznany adres IP wskazuje punkt końcowy proxy sieci wirtualnej, który wysyła ruch sieciowy do prywatnego punktu końcowego dla zasobu Cognitive Services. Zachowanie będzie inne dla zasobu z niestandardową nazwą domeny, ale *bez* prywatnych punktów końcowych. Szczegółowe informacje znajdują się w [tej sekcji](#dns-configuration) .

## <a name="adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint"></a>Dostosowanie aplikacji do korzystania z zasobu mowy z prywatnym punktem końcowym

Zasób mowy z domeną niestandardową współdziała z usługami mowy w inny sposób. Jest to prawdziwe dla zasobu mowy z włączoną obsługą domeny niestandardowej zarówno z prywatnymi punktami końcowymi, jak i bez nich. Informacje w tej sekcji dotyczą obu scenariuszy.

Postępuj zgodnie z instrukcjami w tej sekcji, aby dostosować istniejące aplikacje i rozwiązania do korzystania z zasobu mowy z niestandardową nazwą domeny i włączonym prywatnym punktem końcowym.

Zasób mowy z niestandardową nazwą domeny i prywatnym punktem końcowym z włączonym innym sposobem korzystania z usługi Speech Services. W tej sekcji wyjaśniono, jak używać takiego zasobu z interfejsami API REST usług mowy i [zestawem SDK mowy](speech-sdk.md).

> [!NOTE]
> Zasób mowy bez prywatnych punktów końcowych, który używa niestandardowej nazwy domeny, również ma specjalny sposób na korzystanie z usług mowy.
> W ten sposób różni się od scenariusza zasobu mowy korzystającego z prywatnego punktu końcowego. Jest to ważne do uwzględnienia, ponieważ użytkownik może zdecydować się na późniejsze usunięcie prywatnych punktów końcowych.
> Zobacz _Dostosowywanie aplikacji, aby używać zasobu mowy bez prywatnych punktów końcowych_ w dalszej części tego artykułu.

### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Zasób mowy z niestandardową nazwą domeny i prywatnym punktem końcowym: użycie z interfejsami API REST

Użyjemy `my-private-link-speech.cognitiveservices.azure.com` jako przykładowej nazwy DNS zasobu mowy (domena niestandardowa) dla tej sekcji.

Usługi mowy mają interfejsy API REST dla [zamiany mowy na tekst](rest-speech-to-text.md) i [zamiany tekstu na mowę](rest-text-to-speech.md). Rozważ następujące informacje dotyczące scenariusza z obsługą prywatnego punktu końcowego.

Zamiana mowy na tekst ma dwa interfejsy API REST. Każdy interfejs API pełni różne zastosowania, używa różnych punktów końcowych i wymaga innego podejścia, gdy jest używany w scenariuszu z obsługą prywatnego punktu końcowego.

Interfejsy API REST zamiany mowy na tekst są następujące:
- [Zamiana mowy na tekst API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30), która jest używana na potrzeby [transkrypcji partii](batch-transcription.md) i [Custom Speech](custom-speech-overview.md). v 3.0 jest [następnikiem w wersji 2.0](./migrate-v2-to-v3.md)
- [Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio), który jest używany na potrzeby transkrypcji online 

Użycie interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku i interfejsu API REST zamiany tekstu na mowę w scenariuszu prywatnego punktu końcowego jest takie samo. Jest to odpowiednik [przypadku zestawu Speech SDK](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) opisanego w dalszej części tego artykułu. 

Funkcja zamiany mowy na tekst interfejsu API REST w wersji 3.0 używa innego zestawu punktów końcowych, dlatego wymaga innego podejścia do scenariusza z obsługą punktu końcowego.

W następnych podsekcjach opisano oba przypadki.

#### <a name="speech-to-text-rest-api-v30"></a>Zamiana mowy na tekst interfejsu API REST v 3.0

Zazwyczaj zasoby mowy wykorzystują [Cognitive Services regionalne punkty końcowe](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) do komunikowania się z [INTERFEJSem API REST z funkcją zamiany mowy na tekst](rest-speech-to-text.md#speech-to-text-rest-api-v30). Te zasoby mają następujący format nazewnictwa: <p/>`{region}.api.cognitive.microsoft.com`.

Jest to przykładowy adres URL żądania:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> Zobacz [ten artykuł](sovereign-clouds.md) , aby uzyskać Azure Government i punkty końcowe platformy Azure w Chinach.

Po włączeniu niestandardowej domeny dla zasobu mowy (który jest konieczny dla prywatnych punktów końcowych) ten zasób będzie używał następującego wzorca nazwy DNS dla podstawowego punktu końcowego interfejsu API REST: <p/>`{your custom name}.cognitiveservices.azure.com`

Oznacza to, że w naszym przykładzie nazwa punktu końcowego interfejsu API REST będzie: <p/>`my-private-link-speech.cognitiveservices.azure.com`

I przykładowy adres URL żądania musi zostać przekonwertowany na:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Ten adres URL powinien być dostępny z sieci wirtualnej z dołączonym prywatnym punktem końcowym (pod warunkiem [poprawnego rozpoznawania nazw DNS](#resolve-dns-from-the-virtual-network)).

Po włączeniu niestandardowej nazwy domeny dla zasobu mowy, zazwyczaj zastępujesz nazwę hosta we wszystkich adresach URL żądania nową nazwą hosta domeny niestandardowej. Wszystkie inne części żądania (takie jak ścieżka `/speechtotext/v3.0/transcriptions` w poprzednim przykładzie) pozostają takie same.

> [!TIP]
> Niektórzy klienci opracowują aplikacje korzystające z regionu należącego do regionalnej nazwy DNS punktu końcowego (na przykład w celu wysłania żądania do zasobu mowy wdrożonego w określonym regionie platformy Azure).
>
> Domena niestandardowa dla zasobu mowy *nie* zawiera informacji o regionie, w którym jest wdrożony zasób. Dlatego opisana wcześniej logika aplikacji *nie* będzie działać i musi zostać zmieniona.

#### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku i interfejsu API REST zamiany tekstu na mowę

[Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) i [interfejsu API REST zamiany tekstu na mowę](rest-text-to-speech.md) wykorzystują dwa typy punktów końcowych:
- [Cognitive Services regionalne punkty końcowe](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) do komunikowania się z interfejsem API REST Cognitive Services w celu uzyskania tokenu autoryzacji
- Specjalne punkty końcowe dla wszystkich innych operacji

> [!NOTE]
> Zobacz [ten artykuł](sovereign-clouds.md) , aby uzyskać Azure Government i punkty końcowe platformy Azure w Chinach.

Szczegółowy opis specjalnych punktów końcowych i sposób przekształcania adresu URL dla zasobu mowy z obsługą prywatnego punktu końcowego jest dostępny w [tej podsekcji](#construct-endpoint-url) dotyczącej użycia za pomocą zestawu Speech SDK. Ta sama zasada opisana dla zestawu SDK dotyczy interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku i interfejsu API REST zamiany tekstu na mowę.

Zapoznaj się z materiałem znajdującym się w podsekcji wymienionym w poprzednim akapicie i zobacz Poniższy przykład. W tym przykładzie opisano interfejs API REST zamiany tekstu na mowę. Użycie interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku jest w pełni równoważne.

> [!NOTE]
> Gdy korzystasz z interfejsu API REST zamiany mowy na tekst dla krótkiego interfejsu API REST i zamiany tekstu na mowę w scenariuszach prywatnych punktów końcowych, użyj klucza subskrypcji przeprzesyłanego za pośrednictwem `Ocp-Apim-Subscription-Key` nagłówka. (Zobacz szczegóły dotyczące [interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku](rest-speech-to-text.md#request-headers) i [interfejsu API REST zamiany tekstu na mowę](rest-text-to-speech.md#request-headers))
>
> Użycie tokenu autoryzacji i przekazanie go do specjalnego punktu końcowego za pośrednictwem `Authorization` nagłówka będzie możliwe *tylko* wtedy, gdy włączono opcję dostępu **wszystkie sieci** w sekcji **Sieć** zasobu mowy. W innych przypadkach podczas `Forbidden` `BadRequest` próby uzyskania tokenu autoryzacji wystąpi błąd lub komunikat o błędzie.

**Przykład użycia interfejsu API REST zamiany tekstu na mowę**

Użyjemy Europa Zachodnia jako przykładowego regionu platformy Azure i `my-private-link-speech.cognitiveservices.azure.com` jako przykładowej nazwy DNS zasobu mowy (domena niestandardowa). Niestandardowa nazwa domeny `my-private-link-speech.cognitiveservices.azure.com` w naszym przykładzie należy do zasobu mowy utworzonego w regionie Europa Zachodnia.

Aby uzyskać listę głosów obsługiwanych w regionie, należy wykonać następujące żądanie:

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
Więcej szczegółów znajduje się w [dokumentacji interfejsu API REST zamiany tekstu na mowę](rest-text-to-speech.md).

W przypadku zasobu mowy z obsługą prywatnego punktu końcowego należy zmodyfikować adres URL punktu końcowego dla tej samej operacji. To samo żądanie będzie wyglądać następująco:

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Zapoznaj się ze szczegółowymi wyjaśnieniami w podsekcji [konstrukcja adresu URL punktu końcowego](#construct-endpoint-url) dla zestawu Speech SDK.

### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Zasób mowy z niestandardową nazwą domeny i prywatnym punktem końcowym: użycie z zestawem Speech SDK

Korzystanie z zestawu Speech SDK z niestandardową nazwą domeny i zasobami mowy z obsługą prywatnych punktów końcowych wymaga przejrzenia i możliwie zmiany kodu aplikacji.

Użyjemy `my-private-link-speech.cognitiveservices.azure.com` jako przykładowej nazwy DNS zasobu mowy (domena niestandardowa) dla tej sekcji.

#### <a name="construct-endpoint-url"></a>Adres URL punktu końcowego konstruowania

Zwykle w scenariuszach z zestawem SDK (a także w interfejsie API REST zamiany mowy na tekst na potrzeby krótkich i zamiany tekstu na mowę), zasoby mowy wykorzystują dedykowane regionalne punkty końcowe dla różnych ofert usług. Format nazwy DNS dla tych punktów końcowych to:

`{region}.{speech service offering}.speech.microsoft.com`

Przykładowa nazwa DNS:

`westeurope.stt.speech.microsoft.com`

Wszystkie możliwe wartości dla regionu (pierwszy element nazwy DNS) są wymienione w obszarze [Obsługiwane regiony usługi mowy](regions.md). (Zobacz [ten artykuł](sovereign-clouds.md) , aby uzyskać Azure Government i punkty końcowe platformy Azure w Chinach). W poniższej tabeli przedstawiono możliwe wartości oferty usługi Speech Services (drugi element nazwy DNS):

| Wartość nazwy DNS | Oferta usługi mowy                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Polecenia niestandardowe](custom-commands.md)                       |
| `convai`       | [Transkrypcja konwersacji](conversation-transcription.md) |
| `s2s`          | [Tłumaczenie mowy](speech-translation.md)                 |
| `stt`          | [Zamiana mowy na tekst](speech-to-text.md)                         |
| `tts`          | [Zamiana tekstu na mowę](text-to-speech.md)                         |
| `voice`        | [Niestandardowy głos](how-to-custom-voice.md)                      |

Dlatego wcześniejszy przykład ( `westeurope.stt.speech.microsoft.com` ) oznacza punkt końcowy zamiany mowy na tekst w regionie Europa Zachodnia.

Punkty końcowe z obsługą punktów końcowych komunikują się z usługami mowy za pośrednictwem specjalnego serwera proxy. Z tego powodu *należy zmienić adresy URL połączeń punktów końcowych*. 

Adres URL punktu końcowego "standardowy" wygląda następująco: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Adres URL prywatnego punktu końcowego wygląda następująco: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Przykład 1.** Aplikacja komunikuje się za pomocą następującego adresu URL (rozpoznawanie mowy przy użyciu modelu podstawowego w języku angielskim USA w Europie Zachodniej):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Aby użyć jej w scenariuszu z obsługą prywatnego punktu końcowego, gdy niestandardowa nazwa domeny zasobu mowy to `my-private-link-speech.cognitiveservices.azure.com` , należy zmodyfikować adres URL podobny do tego:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Zwróć uwagę na następujące szczegóły:

- Nazwa hosta `westeurope.stt.speech.microsoft.com` jest zastępowana przez niestandardową nazwę hosta domeny `my-private-link-speech.cognitiveservices.azure.com` .
- Drugi element oryginalnej nazwy DNS ( `stt` ) jest pierwszym elementem ścieżki URL i poprzedza oryginalną ścieżkę. Oryginalny adres URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` zmieni się `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Przykład 2.** Aplikacja używa następującego adresu URL, aby wypróbować mowę w Europie zachodniej przy użyciu niestandardowego modelu głosu:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Następujący równoważny adres URL używa prywatnego punktu końcowego, gdzie niestandardowa nazwa domeny zasobu mowy `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Zostanie zastosowana ta sama zasada z przykładu 1, ale kluczowym elementem tego czasu jest `voice` .

#### <a name="modifying-applications"></a>Modyfikowanie aplikacji

Wykonaj następujące kroki, aby zmodyfikować kod:

1. Określ adres URL punktu końcowego aplikacji:

   - [Włącz rejestrowanie dla aplikacji](how-to-use-logging.md) i uruchom ją, aby rejestrować działania.
   - W pliku dziennika wyszukaj pozycję `SPEECH-ConnectionUrl` . W pasujących wierszach `value` parametr zawiera pełny adres URL używany przez aplikację do uzyskiwania dostępu do usług mowy.

   Przykład:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   Dlatego adres URL używany przez aplikację w tym przykładzie:

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. Utwórz `SpeechConfig` wystąpienie przy użyciu pełnego adresu URL punktu końcowego:

   1. Zmodyfikuj już ustalony punkt końcowy, zgodnie z opisem w sekcji wcześniejszy [adres URL punktu końcowego konstruowania](#construct-endpoint-url) .

   1. Zmodyfikuj sposób tworzenia wystąpienia `SpeechConfig` . Najprawdopodobniej aplikacja korzysta z podobnej do tego:
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      Nie będzie to możliwe w przypadku zasobu mowy z obsługą prywatnego punktu końcowego z powodu zmiany nazwy hosta i adresu URL, które zostały opisane w poprzednich sekcjach. Jeśli spróbujesz uruchomić istniejącą aplikację bez żadnych modyfikacji przy użyciu klucza zasobu z obsługą prywatnego punktu końcowego, zostanie wyświetlony komunikat o błędzie uwierzytelniania (401).

      Aby to zrobić, zmodyfikuj sposób tworzenia wystąpienia `SpeechConfig` klasy i Użyj "z punktu końcowego"/"z inicjacją punktu końcowego". Załóżmy, że mamy zdefiniowane dwie zmienne:
      - `subscriptionKey` zawiera klucz zasobu mowy z obsługą prywatnego punktu końcowego.
      - `endPoint` zawiera pełny *zmodyfikowany* adres URL punktu końcowego (przy użyciu typu wymaganego przez odpowiedni język programowania). W naszym przykładzie ta zmienna powinna zawierać:
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Utwórz `SpeechConfig` wystąpienie:
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> Parametry zapytania określone w identyfikatorze URI punktu końcowego nie są zmieniane, nawet jeśli są ustawiane przez inne interfejsy API. Na przykład jeśli język rozpoznawania jest zdefiniowany w identyfikatorze URI jako parametr zapytania `language=en-US` i jest również ustawiony na `ru-RU` za pośrednictwem odpowiedniej właściwości, używany jest ustawienie języka w identyfikatorze URI. Następnie obowiązuje język obowiązujący `en-US` .
>
> Parametry ustawione w identyfikatorze URI punktu końcowego zawsze mają pierwszeństwo. Inne interfejsy API mogą przesłonić tylko parametry, które nie są określone w identyfikatorze URI punktu końcowego.

Po tej modyfikacji aplikacja powinna współpracować z zasobami mowy z obsługą prywatnych punktów końcowych. Pracujemy nad bardziej bezproblemowe wsparciem scenariuszy prywatnych punktów końcowych.

## <a name="adjust-an-application-to-use-a-speech-resource-without-private-endpoints"></a>Dostosowanie aplikacji do korzystania z zasobu mowy bez prywatnych punktów końcowych

W tym artykule opisano kilka razy, gdy włączenie niestandardowej domeny dla zasobu mowy jest *nieodwracalne*. Taki zasób będzie używać innego sposobu komunikowania się z usługami mowy w porównaniu z tymi, które korzystają z [regionalnych nazw punktów końcowych](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

W tej sekcji wyjaśniono, jak używać zasobu mowy z niestandardową nazwą domeny, ale *bez* żadnych prywatnych punktów końcowych za pomocą interfejsów API REST usługi mowy i [zestawu Speech SDK](speech-sdk.md). Może to być zasób, który został raz użyty w scenariuszu prywatnego punktu końcowego, ale jego prywatne punkty końcowe zostały usunięte.

### <a name="dns-configuration"></a>Konfiguracja usługi DNS

Pamiętaj, jak nazwa DNS domeny niestandardowej zasobu mowy z obsługą prywatnego punktu końcowego jest [rozpoznawana z sieci publicznych](#resolve-dns-from-other-networks). W takim przypadku adres IP jest rozpoznawany jako punkt końcowy serwera proxy dla sieci wirtualnej. Ten punkt końcowy jest używany do wysyłania ruchu sieciowego do zasobu Cognitive Services z włączonym prywatnym punktem końcowym.

Jednak po usunięciu *wszystkich* prywatnych punktów końcowych zasobów (lub po włączeniu nazwy domeny niestandardowej) rekord CNAME zasobu mowy zostanie ponownie zainicjowany. Teraz wskazuje adres IP odpowiadającego [Cognitive Services regionalnego punktu końcowego](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Dlatego dane wyjściowe `nslookup` polecenia będą wyglądać następująco:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Porównaj ją z danymi wyjściowymi z [tej sekcji](#resolve-dns-from-other-networks).

### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Zasób mowy z niestandardową nazwą domeny i bez prywatnych punktów końcowych: użycie przy użyciu interfejsów API REST

#### <a name="speech-to-text-rest-api-v30"></a>Zamiana mowy na tekst interfejsu API REST v 3.0

Użycie funkcji zamiany mowy na tekst w interfejsie API v 3.0 jest w pełni równoważne w przypadku [zasobów mowy z obsługą punktu końcowego](#speech-to-text-rest-api-v30).

#### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku i interfejsu API REST zamiany tekstu na mowę

W takim przypadku użycie interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku i użycia interfejsu API REST zamiany tekstu na mowę nie ma żadnych różnic między ogólnym przypadkiem a jednym wyjątkiem. (Zobacz poniższą uwagę). Należy używać obu interfejsów API, jak opisano w [interfejsie API REST zamiany mowy na tekst dla krótkiej](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) dokumentacji [interfejsu API REST audio i zamiany tekstu na mowę](rest-text-to-speech.md) .

> [!NOTE]
> W przypadku korzystania z interfejsu API REST zamiany mowy na tekst dla krótkiego interfejsu API REST audio i zamiany tekstu na mowę w scenariuszach domen niestandardowych należy użyć klucza subskrypcji przenoszonego przez `Ocp-Apim-Subscription-Key` nagłówek. (Zobacz szczegóły dotyczące [interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku](rest-speech-to-text.md#request-headers) i [interfejsu API REST zamiany tekstu na mowę](rest-text-to-speech.md#request-headers))
>
> Użycie tokenu autoryzacji i przekazanie go do specjalnego punktu końcowego za pośrednictwem `Authorization` nagłówka będzie możliwe *tylko* wtedy, gdy włączono opcję dostępu **wszystkie sieci** w sekcji **Sieć** zasobu mowy. W innych przypadkach podczas `Forbidden` `BadRequest` próby uzyskania tokenu autoryzacji wystąpi błąd lub komunikat o błędzie.

### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Zasób mowy z niestandardową nazwą domeny i bez prywatnych punktów końcowych: użycie z zestawem Speech SDK

Używanie zestawu Speech SDK z niestandardowymi zasobami mowy z obsługą domeny *bez* prywatnych punktów końcowych jest równoznaczne z ogólnym przypadkiem opisanym w [dokumentacji zestawu Speech SDK](speech-sdk.md).

W przypadku zmodyfikowania kodu do użycia z [zasobem mowy z obsługą prywatnego punktu końcowego](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)należy wziąć pod uwagę następujące kwestie.

W sekcji dotyczącej [zasobów mowy z obsługą punktów końcowych](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk), wyjaśniono, jak określić adres URL punktu końcowego, zmodyfikować go i przeznaczyć na "od punktu końcowego"/"z punktem końcowym" Inicjowanie `SpeechConfig` wystąpienia klasy.

Jednak jeśli spróbujesz uruchomić tę samą aplikację po usunięciu wszystkich prywatnych punktów końcowych (co pozwoli na jakiś czas na ponowne zainicjowanie odpowiedniego rekordu DNS), zostanie wyświetlony wewnętrzny błąd usługi (404). Przyczyną jest to, że [rekord DNS](#dns-configuration) wskazuje punkt końcowy Cognitive Services regionalny zamiast serwera proxy sieci wirtualnej, a ścieżki URL, takie jak `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` nie można znaleźć w tym miejscu.

Należy wycofać swoją aplikację do standardowego wystąpienia programu `SpeechConfig` w stylu następującego kodu:

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

## <a name="pricing"></a>Ceny

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Więcej tutaj

* [Link prywatny platformy Azure](../../private-link/private-link-overview.md)
* [Zestaw SDK rozpoznawania mowy](speech-sdk.md)
* [Interfejs API REST zamiany mowy na tekst](rest-speech-to-text.md)
* [Interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md)