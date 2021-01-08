---
title: Jak używać prywatnych punktów końcowych za pomocą usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać usługi mowy z prywatnymi punktami końcowymi udostępnionymi przez link prywatny platformy Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: d5822b6eeecfc61a5092519618ddfcaf88a625ae
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018534"
---
# <a name="use-speech-service-through-a-private-endpoint"></a>Korzystanie z usługi mowy za pomocą prywatnego punktu końcowego

[Link prywatny platformy Azure](../../private-link/private-link-overview.md) umożliwia łączenie się z usługami na platformie Azure przy użyciu [prywatnego punktu końcowego](../../private-link/private-endpoint-overview.md).
Prywatny punkt końcowy jest prywatnym adresem IP dostępnym tylko w ramach określonej [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md) i podsieci.

W tym artykule wyjaśniono, jak skonfigurować i korzystać z prywatnego linku i prywatnych punktów końcowych w usłudze Azure poznawcze usługi rozpoznawania mowy.

> [!NOTE]
> W tym artykule wyjaśniono sposób konfigurowania i korzystania z prywatnego linku do usługi Azure poznawczej. Przed kontynuowaniem zapoznaj się z tematem jak [używać sieci wirtualnych z Cognitive Services](../cognitive-services-virtual-networks.md).

Wykonaj następujące zadania, aby użyć usługi mowy za pomocą prywatnego punktu końcowego:

1. [Utwórz niestandardową nazwę domeny zasobu mowy](#create-a-custom-domain-name)
2. [Utwórz i skonfiguruj prywatne punkty końcowe](#enable-private-endpoints)
3. [Dostosuj istniejące aplikacje i rozwiązania](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Aby później usunąć prywatne punkty końcowe, ale nadal korzystać z zasobu mowy, zostaną wykonane zadania znajdujące się w [tej sekcji](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Utwórz niestandardową nazwę domeny

Prywatne punkty końcowe wymagają [Cognitive Services niestandardowej nazwy domeny](../cognitive-services-custom-subdomains.md)podrzędnej. Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć ten zasób mowy.

> [!WARNING]
> Zasób mowy z włączoną niestandardową nazwą domeny używa innego sposobu na współdziałanie z usługą mowy.
> Prawdopodobnie musisz dostosować kod aplikacji dla scenariuszy z włączonym [prywatnym punktem końcowym](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) , a [ **nie** z włączonymi prywatnymi punktami końcowymi](#use-speech-resource-with-custom-domain-name-without-private-endpoints) .
>
> Po włączeniu niestandardowej nazwy domeny operacja jest [**nieodwracalna**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). Jedynym sposobem powrotu do [nazwy regionalnej](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) jest utworzenie nowego zasobu mowy.
>
> Jeśli zasób mowy zawiera wiele skojarzonych modeli niestandardowych i projektów utworzonych za pośrednictwem programu [Speech Studio](https://speech.microsoft.com/) **zdecydowanie** zalecamy podjęcie próby konfiguracji z zasobem testowym przed zmodyfikowaniem zasobu używanego w środowisku produkcyjnym.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby utworzyć niestandardową nazwę domeny przy użyciu Azure Portal, wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://portal.azure.com/) i zaloguj się na koncie platformy Azure.
1. Wybierz wymagany zasób mowy.
1. W grupie **Zarządzanie zasobami** w okienku nawigacji po lewej stronie kliknij pozycję **Sieć**.
1. W obszarze **zapory i sieci wirtualne** kliknij pozycję **Generuj niestandardową nazwę domeny**. Zostanie wyświetlony nowy prawy panel z instrukcjami, aby utworzyć unikatową niestandardową poddomenę dla zasobu.
1. Na panelu Generuj niestandardową nazwę domeny wprowadź część nazwy domeny niestandardowej. Pełna domena niestandardowa będzie wyglądać następująco: `https://{your custom name}.cognitiveservices.azure.com` . 
    **Po utworzeniu niestandardowej nazwy domeny _nie można_ jej zmienić. Odczytaj powyżej alertu ostrzegawczego.** Po wprowadzeniu niestandardowej nazwy domeny kliknij pozycję **Zapisz**.
1. Po zakończeniu operacji w grupie **Zarządzanie zasobami** kliknij pozycję **klucze i punkt końcowy**. Potwierdź, że nowa nazwa punktu końcowego zasobu jest uruchamiana w następujący sposób:

    `https://{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby utworzyć niestandardową nazwę domeny przy użyciu programu PowerShell, upewnij się, że na komputerze jest zainstalowany program PowerShell w wersji 7. x lub nowszej przy użyciu modułu Azure PowerShell w wersji 5.1.0 lub nowszej. Aby wyświetlić wersje tych narzędzi, wykonaj następujące kroki:

1. W oknie programu PowerShell wpisz:

    `$PSVersionTable`

    Upewnij się, że wartość PSVersion jest większa niż 7. x. Aby uaktualnić program PowerShell, postępuj zgodnie z instrukcjami dotyczącymi [instalowania różnych wersji programu PowerShell](/powershell/scripting/install/installing-powershell) , które mają zostać uaktualnione.

1. W oknie programu PowerShell wpisz:

    `Get-Module -ListAvailable Az`

    Jeśli nic nie występuje lub jeśli Azure PowerShell wersja modułu jest niższa niż 5.1.0, postępuj zgodnie z instrukcjami w instrukcji [Install Azure PowerShell module](/powershell/azure/install-Az-ps) , aby przeprowadzić uaktualnienie.

Przed kontynuowaniem Uruchom polecenie, `Connect-AzAccount` Aby utworzyć połączenie z platformą Azure.

## <a name="verify-custom-domain-name-is-available"></a>Sprawdź, czy nazwa domeny niestandardowej jest dostępna

Sprawdź, czy domena niestandardowa, której chcesz używać, jest dostępna. Wykonaj następujące kroki, aby upewnić się, że domena jest dostępna przy użyciu operacji [Sprawdź dostępność domeny](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) w interfejsie API REST Cognitive Services.

> [!TIP]
> Poniższy kod **nie** będzie działał w Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare OAuth token to use in request to Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to Cognitive Services REST API.
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
Jeśli wymagana nazwa jest dostępna, zostanie wyświetlona odpowiedź:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Jeśli nazwa jest już zajęta, zostanie wyświetlona następująca odpowiedź:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Utwórz niestandardową nazwę domeny

Aby włączyć niestandardową nazwę domeny dla wybranego zasobu mowy, należy użyć polecenia cmdlet [Set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) .

> [!WARNING]
> Po pomyślnym uruchomieniu kodu zostanie utworzona niestandardowa nazwa domeny dla zasobu mowy.
> **Nie można** zmienić tej nazwy. Więcej informacji można znaleźć w powyższym alercie **ostrzegawczym** .

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
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

- Ta sekcja wymaga najnowszej wersji interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="verify-the-custom-domain-name-is-available"></a>Sprawdź, czy nazwa domeny niestandardowej jest dostępna

Sprawdź, czy domena niestandardowa, której chcesz użyć, jest bezpłatna. Będziemy używać metody [Sprawdź dostępność domeny](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) z Cognitive Services interfejsu API REST.

Skopiuj poniższy blok kodu, Wstaw preferowaną niestandardową nazwę domeny i Zapisz plik `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Skopiuj plik do bieżącego folderu lub Przekaż go do Azure Cloud Shell i uruchom następujące polecenie. (Zastąp `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` Identyfikator subskrypcji platformy Azure).

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Jeśli wymagana nazwa jest dostępna, zostanie wyświetlona odpowiedź:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Jeśli nazwa jest już zajęta, zostanie wyświetlona następująca odpowiedź:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-custom-domain-name"></a>Włącz niestandardową nazwę domeny

Aby włączyć niestandardową nazwę domeny dla wybranego zasobu mowy, użyj polecenia [AZ cognitiveservices Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Wybierz subskrypcję platformy Azure zawierającą zasób mowy. Jeśli Twoje konto platformy Azure ma tylko jedną aktywną subskrypcję, możesz pominąć ten krok. (Zastąp `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` Identyfikator subskrypcji platformy Azure).
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Ustaw niestandardową nazwę domeny na wybrany zasób. Zastąp przykładowe wartości parametrów wartościami rzeczywistymi i uruchom poniższe polecenie.

> [!WARNING]
> Po pomyślnym wykonaniu poniższego polecenia utworzysz niestandardową nazwę domeny dla zasobu mowy. **Nie można** zmienić tej nazwy. Więcej informacji znajduje się w powyższym alercie ostrzegawczym.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enable-private-endpoints"></a>Włącz prywatne punkty końcowe

Zalecamy używanie [prywatnej strefy DNS](../../dns/private-dns-overview.md) dołączonej do sieci wirtualnej z aktualizacjami niezbędnymi dla prywatnych punktów końcowych, które są domyślnie tworzone podczas procesu aprowizacji. Jednak w przypadku korzystania z własnego serwera DNS może być również konieczna zmiana konfiguracji DNS, jak pokazano w _systemie DNS dla prywatnych punktów końcowych_ poniżej. Należy podjąć decyzję dotyczącą strategii DNS _ *przed** aprowizacji prywatnych punktów końcowych dla zasobu mowy produkcyjnej i PRZETESTOWAĆ zmiany DNS, zwłaszcza jeśli używasz własnego serwera DNS.

Użyj jednego z poniższych artykułów, aby utworzyć prywatne punkty końcowe. W artykułach użyto aplikacji sieci Web jako przykładowego zasobu do włączenia prywatnych punktów końcowych. Te parametry będą używane zamiast tych w artykule:

| Ustawienie             | Wartość                                    |
|---------------------|------------------------------------------|
| Typ zasobu       | **Microsoft. CognitiveServices/konta** |
| Zasób            | **\<your-speech-resource-name>**         |
| Docelowy zasób podrzędny | **organizacyjnego**                              |

- [Tworzenie prywatnego punktu końcowego przy użyciu witryny Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](../../private-link/create-private-endpoint-cli.md)

**System DNS dla prywatnych punktów końcowych:** Zapoznaj się z ogólnymi zasadami [usługi DNS dla prywatnych punktów końcowych w ramach zasobów Cognitive Services](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Następnie upewnij się, że konfiguracja DNS działa prawidłowo, wykonując następujące testy:

### <a name="resolve-dns-from-the-virtual-network"></a>Rozpoznawanie systemu DNS z sieci wirtualnej

To sprawdzenie jest **wymagane**.

Wykonaj następujące kroki, aby przetestować niestandardowy wpis DNS z sieci wirtualnej.

1. Zaloguj się do maszyny wirtualnej znajdującej się w sieci wirtualnej, z którą został podłączony prywatny punkt końcowy. 
1. Otwórz wiersz polecenia systemu Windows lub powłokę bash, uruchom `nslookup` i Potwierdź, że została pomyślnie rozpoznana niestandardowa nazwa domeny zasobu.

```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```

3. Upewnij się, że adres IP jest zgodny z adresem IP prywatnego punktu końcowego.

### <a name="resolve-dns-from-other-networks"></a>Rozpoznawanie systemu DNS z innych sieci

Ten test należy wykonać tylko wtedy, gdy planujesz używać zasobu mowy z włączonym prywatnym punktem końcowym w trybie "hybrydowym", w którym włączono opcję **wszystkie sieci** lub **wybrane sieci i dostęp do prywatnych punktów końcowych** w sekcji **Sieć** zasobu. Jeśli planujesz uzyskać dostęp do zasobu przy użyciu tylko prywatnego punktu końcowego, możesz pominąć tę sekcję.

1. Zaloguj się na komputerze podłączonym do sieci, aby uzyskać dostęp do zasobu.
2. Otwórz wiersz polecenia systemu Windows lub powłokę bash, uruchom `nslookup` i Potwierdź, że została pomyślnie rozpoznana niestandardowa nazwa domeny zasobu.

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

3. Upewnij się, że adres IP jest zgodny z adresem IP prywatnego punktu końcowego.

> [!NOTE]
> Rozpoznany adres IP wskazuje punkt końcowy proxy sieci wirtualnej, który wysyła ruch sieciowy do prywatnego punktu końcowego dla zasobu Cognitive Services. Zachowanie będzie inne dla zasobu z niestandardową nazwą domeny, ale *bez* prywatnych punktów końcowych. Szczegółowe informacje znajdują się w [tej sekcji](#dns-configuration) .

## <a name="adjust-existing-applications-and-solutions"></a>Dostosuj istniejące aplikacje i rozwiązania

Zasób mowy z włączoną domeną niestandardową korzysta z innego sposobu, aby można było korzystać z usługi Speech Services. Jest to prawdziwe dla niestandardowego zasobu mowy z włączoną obsługą domeny zarówno z prywatnymi punktami końcowymi, jak i bez nich. Informacje w tej sekcji dotyczą obu scenariuszy.

### <a name="use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Korzystanie z zasobu mowy z włączoną niestandardową nazwą domeny i prywatnym punktem końcowym

Zasób mowy z włączonym niestandardową nazwą domeny i prywatnym punktem końcowym używa innego sposobu, aby móc korzystać z usług mowy. W tej sekcji wyjaśniono, jak używać tego zasobu z interfejsem API REST usługi Speech Services i [zestawem Speech SDK](speech-sdk.md).

> [!NOTE]
> Należy pamiętać, że zasób mowy bez prywatnych punktów końcowych, ale z włączoną **niestandardową nazwą domeny** również ma specjalny sposób na korzystanie z usług mowy, ale w ten sposób różni się od scenariusza z włączonym prywatnym punktem końcowym. Jeśli masz taki zasób (Załóżmy, że masz zasób z prywatnymi punktami końcowymi, ale następnie postanowisz go usunąć), pamiętaj, aby zapoznać się z [sekcją korespondent](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Zasób mowy z niestandardową nazwą domeny i prywatnym punktem końcowym. Użycie przy użyciu interfejsu API REST

Zostanie użyta `my-private-link-speech.cognitiveservices.azure.com` jako Przykładowa nazwa DNS zasobu mowy (domena niestandardowa) dla tej sekcji.

##### <a name="note-on-speech-services-rest-api"></a>Uwaga dotycząca interfejsu API REST usługi Speech Services

Usługi mowy mają interfejs API REST dla [zamiany mowy na tekst](rest-speech-to-text.md) i [zamiany tekstu na mowę](rest-text-to-speech.md). Należy wziąć pod uwagę następujące kwestie dotyczące scenariusza z włączoną obsługą prywatnego punktu końcowego.

Zamiana mowy na tekst ma dwa różne interfejsy API REST. Każdy interfejs API pełni różne zastosowania, używa różnych punktów końcowych i wymaga innego podejścia, gdy jest używany pojedynczo w scenariuszu z włączoną obsługą prywatnego punktu końcowego.

Interfejsy API REST zamiany mowy na tekst są następujące:
- Funkcja [zamiany mowy na tekst "API REST v 3.0"](rest-speech-to-text.md#speech-to-text-rest-api-v30) jest używana na potrzeby [transkrypcji](batch-transcription.md) i [Custom Speech](custom-speech-overview.md)partii. v 3.0 jest [następnikiem w wersji 2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- [Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) jest używany do transkrypcji online. 

Użycie interfejsu API REST zamiany mowy na tekst dla krótkiego interfejsu API REST audio i zamiany tekstu na mowę w scenariuszu prywatnego punktu końcowego jest takie samo, jak w [przypadku zestawu mowy SDK](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) opisanego w dalszej części tego artykułu. 

Funkcja zamiany mowy na tekst interfejsu API REST w wersji 3.0 korzysta z innego zestawu punktów końcowych i w ten sposób wymaga innego podejścia dla scenariusza z włączoną obsługą prywatnego punktu końcowego.

Oba przypadki są opisane w następnych podsekcjach.


##### <a name="speech-to-text-rest-api-v30"></a>Zamiana mowy na tekst interfejsu API REST v 3.0

Zazwyczaj zasoby mowy wykorzystują [Cognitive Services regionalne punkty końcowe](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) do komunikowania się z [INTERFEJSem API REST z funkcją zamiany mowy na tekst](rest-speech-to-text.md#speech-to-text-rest-api-v30). Te zasoby mają następujący format nazewnictwa: <p/>`{region}.api.cognitive.microsoft.com`

Jest to przykładowy adres URL żądania:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Po włączeniu domeny niestandardowej dla zasobu mowy (który jest niezbędny dla prywatnych punktów końcowych) ten zasób będzie używać następującego wzorca nazw DNS dla podstawowego punktu końcowego interfejsu API REST: <p/>`{your custom name}.cognitiveservices.azure.com`

Oznacza to, że w naszym przykładzie nazwa punktu końcowego interfejsu API REST będzie: <p/>`my-private-link-speech.cognitiveservices.azure.com`

A przykładowy adres URL żądania powyżej musi zostać przekonwertowany na:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Ten adres URL powinien być dostępny z sieci wirtualnej z dołączonym prywatnym punktem końcowym (pod warunkiem [poprawnego rozpoznawania nazw DNS](#resolve-dns-from-the virtual-network)).

Zwykle po włączeniu niestandardowej nazwy domeny dla zasobu mowy zamienisz nazwę hosta we wszystkich adresach URL żądań z nową nazwą hosta domeny niestandardowej. Wszystkie inne części żądania (takie jak ścieżka `/speechtotext/v3.0/transcriptions` w powyższym przykładzie) pozostają takie same.

> [!TIP]
> Niektórzy klienci opracowały aplikacje korzystające z regionu należącego do regionalnej nazwy DNS punktu końcowego (na przykład w celu wysłania żądania do zasobu mowy wdrożonego w określonym regionie platformy Azure).
>
> Niestandardowa nazwa domeny zasobu mowy **nie** zawiera informacji o regionie, w którym zasób jest wdrożony. Dlatego opisana powyżej logika aplikacji **nie** będzie działać i musi zostać zmieniona.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku i interfejsu API REST zamiany tekstu na mowę

[Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) i [interfejsu API REST tekstu na mowę](rest-text-to-speech.md) użyj dwóch typów punktów końcowych:
- [Cognitive Services regionalne punkty końcowe](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) do komunikowania się z interfejsem API REST Cognitive Services w celu uzyskania tokenu autoryzacji
- Specjalne punkty końcowe dla wszystkich innych operacji

Szczegółowy opis specjalnych punktów końcowych i sposób przekształcania ich adresów URL dla zasobu mowy z włączoną obsługą prywatnego punktu końcowego znajduje się w [tej podsekcji](#general-principle) "użycie z zestawem Speech SDK" poniżej. Ta sama zasada opisana dla zestawu SDK ma zastosowanie do interfejsu API REST zamiany mowy na tekst v 1.0 i zamiany tekstu na mowę.

Zapoznaj się z materiałem znajdującym się w podsekcji wymienionym w poprzednim akapicie i zobacz Poniższy przykład. (W tym przykładzie przedstawiono interfejs API REST zamiany tekstu na mowę; użycie interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku jest w pełni równoważne)

> [!NOTE]
> W przypadku korzystania z **interfejsu API REST zamiany mowy na tekst w przypadku krótkiego dźwięku** w scenariuszach prywatnych punktów końcowych należy użyć tokenu autoryzacji [przekazaną przez](rest-speech-to-text.md#request-headers) `Authorization` [nagłówek](rest-speech-to-text.md#request-headers). Przekazywanie klucza subskrypcji mowy do specjalnego punktu końcowego za pośrednictwem `Ocp-Apim-Subscription-Key` nagłówka **nie** będzie działało i spowoduje to wygenerowanie błędu 401.

**Przykład użycia interfejsu API REST zamiany tekstu na mowę.**

Firma Microsoft będzie używać zachodniego Europy jako przykładowego regionu platformy Azure i przykładowej `my-private-link-speech.cognitiveservices.azure.com` nazwy DNS zasobu mowy (domena niestandardowa). Niestandardowa nazwa domeny `my-private-link-speech.cognitiveservices.azure.com` w naszym przykładzie należy do zasobu mowy utworzonego w regionie Europa Zachodnia.

Aby uzyskać listę głosów obsługiwanych w regionie, należy wykonać następujące dwie operacje:

- Uzyskaj Token autoryzacji:
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Przy użyciu tokenu Pobierz listę głosów:
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
(Zobacz więcej szczegółów na temat powyższych kroków w [dokumentacji interfejsu API REST zamiany tekstu na mowę](rest-text-to-speech.md))

Dla zasobu mowy z włączonym prywatnym punktem końcowym należy zmodyfikować adresy URL punktów końcowych dla tej samej sekwencji operacji. Ta sama sekwencja będzie wyglądać następująco:
- Uzyskaj Token autoryzacji za pośrednictwem
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
(Zobacz szczegółowe wyjaśnienie w sekcji [Zamiana mowy na tekst REST API v 3.0](#speech-to-text-rest-api-v30) powyżej)
- Korzystanie z uzyskanego tokenu Pobieranie listy głosów za pośrednictwem
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
(Zobacz szczegółowe wyjaśnienie w sekcji [ogólne zasady](#general-principle) w sekcji "użycie za pomocą zestawu Speech SDK" poniżej).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>Zasób mowy z niestandardową nazwą domeny i prywatnym punktem końcowym. Użycie przy użyciu zestawu Speech SDK

Korzystanie z zestawu Speech SDK z niestandardową nazwą domeny i z włączonymi prywatnymi punktami końcowymi wymaga przeglądu i możliwych zmian kodu aplikacji.

Zostanie użyta `my-private-link-speech.cognitiveservices.azure.com` jako Przykładowa nazwa DNS zasobu mowy (domena niestandardowa) dla tej sekcji.

##### <a name="general-principle"></a>Ogólna zasada

Zwykle w scenariuszach z zestawem SDK (a także w scenariuszach interfejsu API REST zamiany tekstu na mowę) zasoby mowy używają dedykowanych regionalnych punktów końcowych dla różnych ofert usług. Format nazwy DNS dla tych punktów końcowych to: </p>`{region}.{speech service offering}.speech.microsoft.com`

Przykład: </p>`westeurope.stt.speech.microsoft.com`

Wszystkie możliwe wartości dla regionu (pierwszy element nazwy DNS) [są wymienione w poniższej tabeli](regions.md) poniżej przedstawia możliwe wartości oferty usługi Speech Services (drugi element nazwy DNS):

| Wartość nazwy DNS | Oferta usługi Speech Services                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Polecenia niestandardowe](custom-commands.md)                       |
| `convai`       | [Transkrypcja konwersacji](conversation-transcription.md) |
| `s2s`          | [Tłumaczenie mowy](speech-translation.md)                 |
| `stt`          | [Zamiana mowy na tekst](speech-to-text.md)                         |
| `tts`          | [Zamiana tekstu na mowę](text-to-speech.md)                         |
| `voice`        | [Niestandardowy głos](how-to-custom-voice.md)                      |

Dlatego powyższy przykład ( `westeurope.stt.speech.microsoft.com` ) oznacza punkt końcowy zamiany mowy na tekst w regionie Europa Zachodnia.

Punkty końcowe z włączonym prywatnym punktem końcowym komunikują się z usługami mowy za pośrednictwem specjalnego serwera proxy i z tego powodu **należy zmienić adresy URL połączeń punktów końcowych**. 

Adres URL punktu końcowego "standardowy" wygląda następująco: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Adres URL prywatnego punktu końcowego wygląda następująco: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Przykład 1.** Aplikacja komunikuje się za pomocą następującego adresu URL (rozpoznawanie mowy przy użyciu modelu podstawowego w języku angielskim USA w Europie Zachodniej):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Aby używać go w scenariuszu z włączoną obsługą prywatnego punktu końcowego, gdy niestandardowa nazwa domeny zasobu mowy jest `my-private-link-speech.cognitiveservices.azure.com` konieczna, należy zmodyfikować adres URL podobny do tego:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Zwróć uwagę na następujące szczegóły:

- Nazwa hosta `westeurope.stt.speech.microsoft.com` jest zastępowana przez niestandardową nazwę hosta domeny `my-private-link-speech.cognitiveservices.azure.com` .
- Drugi element oryginalnej nazwy DNS ( `stt` ) jest pierwszym elementem ścieżki URL i poprzedza oryginalną ścieżkę. Oryginalny adres URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` zmieni się `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Przykład 2.** Aplikacja korzysta z następującego adresu URL, aby wypróbować mowę w Europie zachodniej przy użyciu niestandardowego modelu głosu):
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Poniżej znajduje się równoważny adres URL, który korzysta z włączonego prywatnego punktu końcowego, w którym jest włączona niestandardowa nazwa domeny zasobu mowy `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Zostanie zastosowana taka sama zasada jak w przykładzie 1, ale kluczowym elementem tego czasu jest `voice` .

##### <a name="modify-applications"></a>Modyfikuj aplikacje

Wykonaj następujące kroki, aby zmodyfikować kod:

**1. Określ adres URL punktu końcowego aplikacji**

- [Włącz rejestrowanie dla aplikacji](how-to-use-logging.md) i uruchom ją, aby rejestrować działania.
- W pliku dziennika wyszukaj pozycję `SPEECH-ConnectionUrl` . W pasujących wierszach `value` parametr zawiera pełny adres URL używany przez aplikację do uzyskiwania dostępu do usługi mowy.

Przykład:

```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```

W tym przykładzie adres URL używany przez aplikację jest następujący:

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

**2. Utwórz `SpeechConfig` wystąpienie przy użyciu pełnego adresu URL punktu końcowego**

Zmodyfikuj punkt końcowy określony w poprzedniej sekcji, zgodnie z opisem w [ogólnej zasadzie](#general-principle) powyżej.

Teraz zmodyfikuj sposób tworzenia wystąpienia `SpeechConfig` . Najprawdopodobniej dzisiejsza aplikacja korzysta z podobnej do tego:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Nie będzie to możliwe w przypadku zasobu mowy z włączonym prywatnym punktem końcowym ze względu na zmiany nazwy hosta i adresu URL opisane w poprzednich sekcjach. Jeśli spróbujesz uruchomić istniejącą aplikację bez żadnych modyfikacji przy użyciu klucza zasobu z włączonym prywatnym punktem końcowym, zostanie wyświetlony komunikat o błędzie uwierzytelniania (401).

Aby to zrobić, zmodyfikuj sposób tworzenia wystąpienia `SpeechConfig` klasy i używania "z punktu końcowego"/"z inicjacją punktu końcowego". Załóżmy, że mamy zdefiniowane dwie zmienne:
- `subscriptionKey` zawierający klucz zasobu mowy z włączonym prywatnym punktem końcowym
- `endPoint` zawierający pełny, **zmodyfikowany** adres URL punktu końcowego (przy użyciu typu wymaganego przez język programowania korespondenta). W naszym przykładzie ta zmienna powinna zawierać
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Następnie Utwórz `SpeechConfig` wystąpienie:
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
> Parametry zapytania określone w identyfikatorze URI punktu końcowego nie są zmieniane, nawet jeśli są ustawione przez inne interfejsy API. Na przykład, jeśli język rozpoznawania jest zdefiniowany w identyfikatorze URI jako parametr zapytania "language = EN-US" i jest również ustawiony na wartość "ru-RU" za pośrednictwem właściwości korespondent, używane jest ustawienie języka w identyfikatorze URI, a obowiązujący język to "pl-US". Parametry ustawione w identyfikatorze URI punktu końcowego zawsze przyjmują precidence. Inne interfejsy API mogą przesłonić tylko parametry, które nie są określone w identyfikatorze URI punktu końcowego.

Po tej modyfikacji aplikacja powinna współpracować z prywatnymi włączonymi zasobami mowy. Pracujemy nad bardziej bezproblemowe obsłudze scenariusza prywatnego punktu końcowego.

### <a name="use-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Używanie zasobu mowy z niestandardową nazwą domeny bez prywatnych punktów końcowych

W tym artykule opisano kilka razy, że włączenie niestandardowej domeny dla zasobu mowy jest **nieodwracalne** i taki zasób będzie używać innego sposobu komunikacji z usługami mowy, porównując je z "typowymi" (to są te, które używają [regionalnych nazw punktów końcowych](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)).

W tej sekcji wyjaśniono, jak używać zasobu mowy z włączoną niestandardową nazwą domeny, ale **bez** żadnych prywatnych punktów końcowych za pomocą interfejsu API REST usługi Speech Services i [zestawu Speech SDK](speech-sdk.md). Może to być zasób, który został raz użyty w scenariuszu prywatnego punktu końcowego, ale jego prywatne punkty końcowe zostały usunięte.

#### <a name="dns-configuration"></a>Konfiguracja usługi DNS

Należy pamiętać, jak nazwa DNS domeny niestandardowej z włączonym prywatnym punktem końcowym jest [rozpoznawana z sieci publicznych](#resolve-dns-from-other-networks). W tym przypadku adres IP jest rozpoznawany jako punkt końcowy serwera proxy sieci wirtualnej, który jest używany do wysyłania ruchu sieciowego do prywatnego punktu końcowego z włączonym Cognitive Services zasobem.

Jednak po usunięciu **wszystkich** prywatnych punktów końcowych zasobów (lub po włączeniu rekordu CNAME dla nazwy domeny niestandardowej) zasób mowy jest ponownie inicjowany, a teraz wskazuje adres IP korespondenta [Cognitive Services regionalnego punktu końcowego](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

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

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Zasób mowy z niestandardową nazwą domeny bez prywatnych punktów końcowych. Użycie przy użyciu interfejsu API REST

##### <a name="speech-to-text-rest-api-v30"></a>Zamiana mowy na tekst interfejsu API REST v 3.0

Użycie funkcji zamiany mowy na tekst interfejsu API REST w wersji 3.0 jest w pełni równoważne z przypadkiem [prywatnych zasobów mowy z włączonym punktem końcowym](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku i interfejsu API REST zamiany tekstu na mowę

W tym przypadku interfejs API REST zamiany mowy na tekst dla krótkiego użycia interfejsu API REST z dźwiękiem i zamiany tekstu na mowę nie ma żadnych różnic w ogólnym przypadku z jednym wyjątkiem dla interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku (patrz Uwaga poniżej). Należy używać obu interfejsów API, jak opisano w [interfejsie API REST zamiany mowy na tekst dla krótkiej](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) dokumentacji [interfejsu API REST audio i zamiany tekstu na mowę](rest-text-to-speech.md) .

> [!NOTE]
> W przypadku korzystania z **interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku** w scenariuszach domen niestandardowych Użyj tokenu autoryzacji [przekazaną przez](rest-speech-to-text.md#request-headers) `Authorization` [nagłówek](rest-speech-to-text.md#request-headers). Przekazywanie klucza subskrypcji mowy do specjalnego punktu końcowego za pośrednictwem `Ocp-Apim-Subscription-Key` nagłówka **nie** będzie działało i spowoduje to wygenerowanie błędu 401.

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Zasób mowy z niestandardową nazwą domeny bez prywatnych punktów końcowych. Użycie przy użyciu zestawu Speech SDK

Korzystanie z zestawu Speech SDK z niestandardowymi nazwami domen z włączonymi zasobami mowy **bez** prywatnych punktów końcowych wymaga przeglądu i możliwych zmian kodu aplikacji. Należy zauważyć, że te zmiany **różnią** się w porównaniu do wielkości liter [zasobu mowy z włączoną obsługą prywatnego punktu końcowego](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk). Pracujemy nad bardziej bezproblemowe obsłudze scenariusza prywatnego punktu końcowego/domeny niestandardowej.

Zostanie użyta `my-private-link-speech.cognitiveservices.azure.com` jako Przykładowa nazwa DNS zasobu mowy (domena niestandardowa) dla tej sekcji.

W sekcji dotyczącej [zasobu mowy z włączoną funkcją prywatnego punktu końcowego](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) wyjaśniono, jak określić używany adres URL punktu końcowego, zmodyfikować go i przeznaczyć na "od punktu końcowego"/"z punktem końcowym" Inicjowanie `SpeechConfig` wystąpienia klasy.

Jednak jeśli spróbujesz uruchomić tę samą aplikację po usunięciu wszystkich prywatnych punktów końcowych (co pozwoli na jakiś czas na ponowne Inicjowanie obsługi rekordu DNS), zostanie wyświetlony wewnętrzny błąd usługi (404). Przyczyną jest [rekord DNS](#dns-configuration) , który wskazuje punkt końcowy Cognitive Services regionalny zamiast serwera proxy sieci wirtualnej, a ścieżki URL takie jak `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` nie zostaną tam znalezione, dlatego nie znaleziono błędu (404).

W przypadku "wycofywania" aplikacji do "standardowego" tworzenia wystąpienia `SpeechConfig` w stylu
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Aplikacja zostanie zakończona z błędem uwierzytelniania (401).

##### <a name="modifying-applications"></a>Modyfikowanie aplikacji

Aby umożliwić aplikacji używanie zasobu mowy z niestandardową nazwą domeny i bez prywatnych punktów końcowych, wykonaj następujące kroki:

**1. Żądaj tokenu autoryzacji z interfejsu API REST Cognitive Services**

W [tym artykule](../authentication.md#authenticate-with-an-authentication-token) pokazano, jak uzyskać token przy użyciu interfejsu API REST Cognitive Services.

Użyj niestandardowej nazwy domeny w adresie URL punktu końcowego, który jest w naszym przykładzie następujący adres URL to:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> Ten adres URL można znaleźć w Azure Portal. Na stronie zasobów mowy w obszarze w grupie **Zarządzanie zasobami** wybierz pozycję **klucze i punkt końcowy**.

**2. Utwórz `SpeechConfig` wystąpienie przy użyciu metody "from Token autoryzacji"/"z tokenem autoryzacji".**

Utwórz `SpeechConfig` wystąpienie przy użyciu tokenu autoryzacji uzyskanego w poprzedniej sekcji. Załóżmy, że zdefiniowano następujące zmienne:

- `token`: Token autoryzacji uzyskany w poprzedniej sekcji
- `azureRegion`: nazwa [regionu](regions.md) zasobów mowy (przykład: `westeurope` )
- `outError`: (tylko dla przypadku [celu C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) )

Następnie Utwórz `SpeechConfig` wystąpienie:

```csharp
var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
```
```cpp
auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
```
```java
SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
```
> [!NOTE]
> Obiekt wywołujący musi upewnić się, że token autoryzacji jest prawidłowy.
> Przed wygaśnięciem tokenu autoryzacji wywołujący musi odświeżyć go przez wywołanie tej metody ustawiającej z nowym prawidłowym tokenem.
> Ponieważ wartości konfiguracji są kopiowane podczas tworzenia nowego aparatu rozpoznawania lub syntezatora, Nowa wartość tokenu nie będzie stosowana do aparatów rozpoznawania lub syntezatorów, które zostały już utworzone.
> W tym celu należy ustawić Token autoryzacji odpowiedniego aparatu rozpoznawania lub syntezatora w celu odświeżenia tokenu.
> Jeśli token nie zostanie odświeżony, aparat rozpoznawania lub syntezator napotka błędy podczas pracy.

Po tej modyfikacji aplikacja powinna współpracować z zasobami mowy, które używają niestandardowej nazwy domeny bez prywatnych punktów końcowych.

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Dowiedz się więcej

* [Link prywatny platformy Azure](../../private-link/private-link-overview.md)
* [Zestaw SDK rozpoznawania mowy](speech-sdk.md)
* [Interfejs API REST zamiany mowy na tekst](rest-speech-to-text.md)
* [Interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md)
