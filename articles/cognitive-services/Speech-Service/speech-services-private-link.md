---
title: Korzystanie z usług mowy z prywatnymi punktami końcowymi
titleSuffix: Azure Cognitive Services
description: Porady na korzystanie z usług mowy z prywatnymi punktami końcowymi udostępnionymi przez link prywatny platformy Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: alexeyo
ms.openlocfilehash: c88a7820518d0a73bfb0e93d3b364190207b8f90
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051219"
---
# <a name="using-speech-services-with-private-endpoints-provided-by-azure-private-link"></a>Korzystanie z usług mowy z prywatnymi punktami końcowymi udostępnionymi przez link prywatny platformy Azure

[Link prywatny platformy Azure](../../private-link/private-link-overview.md) umożliwia nawiązanie połączenia z różnymi usługami PaaS na platformie Azure za pośrednictwem [prywatnego punktu końcowego](../../private-link/private-endpoint-overview.md). Prywatny punkt końcowy to prywatny adres IP w ramach określonej [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md) i podsieci.

W tym artykule wyjaśniono, jak skonfigurować i korzystać z prywatnego linku i prywatnych punktów końcowych w usłudze Azure poznawcze usługi rozpoznawania mowy. 

> [!NOTE]
> W tym artykule wyjaśniono sposób konfigurowania i korzystania z prywatnego linku do usługi Azure poznawczej. Przed kontynuowaniem zapoznaj się z ogólnym artykułem dotyczącym [korzystania z sieci wirtualnych z Cognitive Services](../cognitive-services-virtual-networks.md).

Włączenie zasobu mowy dla scenariuszy prywatnych punktów końcowych wymaga wykonania następujących zadań:
- [Utwórz niestandardową nazwę domeny zasobu mowy](#create-custom-domain-name)
- [Utwórz i skonfiguruj prywatne punkty końcowe](#enabling-private-endpoints)
- [Dostosuj istniejące aplikacje i rozwiązania](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Jeśli później zdecydujesz się usunąć wszystkie prywatne punkty końcowe, ale nadal korzystasz z zasobu, w [tej sekcji](#using-speech-resource-with-custom-domain-name-without-private-endpoints)są opisane niezbędne akcje.

## <a name="create-custom-domain-name"></a>Utwórz niestandardową nazwę domeny

Prywatne punkty końcowe wymagają użycia [Cognitive Services niestandardowych nazw domen poddomen](../cognitive-services-custom-subdomains.md). Skorzystaj z poniższych instrukcji, aby utworzyć jeden dla zasobu mowy.

> [!WARNING]
> Zasób mowy z włączoną niestandardową nazwą domeny używa innego sposobu, aby móc korzystać z usługi Speech Services. Prawdopodobnie trzeba będzie dostosować kod aplikacji dla scenariuszy z włączonym [prywatnym punktem końcowym](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) , a [ **nie** z włączonymi prywatnymi punktami końcowymi](#using-speech-resource-with-custom-domain-name-without-private-endpoints) .
>
> Operacja włączania niestandardowej nazwy domeny [**nie jest odwracalna**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). Jedynym sposobem powrotu do [nazwy regionalnej](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) jest utworzenie nowego zasobu mowy. 
>
> Szczególnie w przypadku, gdy zasób mowy zawiera wiele skojarzonych modeli niestandardowych i projektów utworzonych za pośrednictwem programu [Speech Studio](https://speech.microsoft.com/) **zdecydowanie** zalecamy przeprowadzenie próby konfiguracji z zasobem testowym, a dopiero potem modyfikację używanego w środowisku produkcyjnym.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

- Przejdź do [Azure Portal](https://portal.azure.com/) i zaloguj się na koncie platformy Azure
- Wybierz wymagany zasób mowy
- Wybieranie *sieci* (Grupa *zarządzania zasobami* ) 
- Na karcie *zapory i sieci wirtualne* (domyślnie) kliknij przycisk **Generuj niestandardową nazwę domeny** .
- Zostanie wyświetlony nowy panel z instrukcjami, aby utworzyć unikatową niestandardową poddomenę dla zasobu
> [!WARNING]
> Po utworzeniu niestandardowej nazwy domeny **nie można** jej zmienić. Więcej informacji można znaleźć w powyższym ostrzeżeniu.
- Po zakończeniu operacji możesz wybrać pozycję *klucze i punkt końcowy* (Grupa *zarządzania zasobami* ) i zweryfikować nową nazwę punktu końcowego zasobu w formacie `{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Ta sekcja wymaga lokalnego uruchomienia programu PowerShell w wersji 7. x lub nowszej przy użyciu modułu Azure PowerShell w wersji 5.1.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps) .

Przed kontynuowaniem dalszego uruchamiania `Connect-AzAccount` , aby utworzyć połączenie z platformą Azure.

## <a name="verify-custom-domain-name-availability"></a>Sprawdź dostępność niestandardowej nazwy domeny

Musisz sprawdzić, czy domena niestandardowa, której chcesz użyć, jest bezpłatna. Będziemy używać metody [Sprawdź dostępność domeny](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) z Cognitive Services interfejsu API REST. Zobacz komentarze w bloku kodu poniżej objaśnienie kroków.

> [!TIP]
> Poniższy kod **nie** będzie działał w Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
Set-AzContext -SubscriptionId $subId

# Preparing OAuth token which is used in request
# to Cognitive Services REST API
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Preparing and executing the request to Cognitive Services REST API
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
Jeśli wymagana nazwa jest dostępna, otrzymasz odpowiedź w następujący sposób:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Jeśli nazwa jest już zajęta, otrzymasz następującą odpowiedź:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="enabling-custom-domain-name"></a>Włączanie niestandardowej nazwy domeny

Aby włączyć niestandardową nazwę domeny dla wybranego zasobu mowy, należy użyć polecenia cmdlet [Set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) . Zobacz komentarze w bloku kodu poniżej objaśnienie kroków.

> [!WARNING]
> Po pomyślnym wykonaniu kodu poniżej utworzysz niestandardową nazwę domeny dla zasobu mowy. **Nie można** zmienić tej nazwy. Więcej informacji można znaleźć w powyższym ostrzeżeniu.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource
# WARNING! THIS IS NOT REVERSIBLE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ta sekcja wymaga najnowszej wersji interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="verify-custom-domain-name-availability"></a>Sprawdź dostępność niestandardowej nazwy domeny

Musisz sprawdzić, czy domena niestandardowa, której chcesz użyć, jest bezpłatna. Będziemy używać metody [Sprawdź dostępność domeny](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) z Cognitive Services interfejsu API REST. 

Skopiuj poniższy blok kodu, Wstaw niestandardową nazwę domeny i Zapisz do pliku `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Skopiuj plik do bieżącego folderu lub Przekaż go do Azure Cloud Shell i wykonaj następujące polecenie. (Zastąp `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` Identyfikator subskrypcji platformy Azure).

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Jeśli wymagana nazwa jest dostępna, otrzymasz odpowiedź w następujący sposób:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Jeśli nazwa jest już zajęta, otrzymasz następującą odpowiedź:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enabling-custom-domain-name"></a>Włączanie niestandardowej nazwy domeny

Aby włączyć niestandardową nazwę domeny dla wybranego zasobu mowy, użyj polecenia [AZ cognitiveservices Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Wybierz subskrypcję platformy Azure zawierającą zasób mowy. Jeśli Twoje konto platformy Azure ma tylko jedną aktywną subskrypcję, możesz pominąć ten krok. (Zastąp `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` Identyfikator subskrypcji platformy Azure).
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Ustaw niestandardową nazwę domeny na wybrany zasób. Zastąp przykładowe wartości parametrów wartościami rzeczywistymi i wykonaj poniższe polecenie.
> [!WARNING]
> Po pomyślnym wykonaniu poniższego polecenia utworzysz niestandardową nazwę domeny dla zasobu mowy. **Nie można** zmienić tej nazwy. Więcej informacji można znaleźć w powyższym ostrzeżeniu.
```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enabling-private-endpoints"></a>Włączanie prywatnych punktów końcowych

Włącz prywatny punkt końcowy przy użyciu Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

Zalecamy używanie [prywatnej strefy DNS](../../dns/private-dns-overview.md) dołączonej do Virtual Network z aktualizacjami niezbędnymi dla prywatnych punktów końcowych, które są domyślnie tworzone podczas procesu aprowizacji. Jeśli jednak używasz własnego serwera DNS, może być konieczne wprowadzenie dodatkowych zmian w konfiguracji DNS. Zobacz sekcję [DNS dla prywatnych punktów końcowych](#dns-for-private-endpoints) . Najlepszym rozwiązaniem jest podjęcie decyzji dotyczącej strategii DNS _ *przed** aprowizacji prywatnych punktów końcowych dla zasobu mowy produkcyjnej. Zalecamy również wstępne testowanie, zwłaszcza jeśli używasz własnego serwera DNS.

Skorzystaj z poniższych artykułów, aby utworzyć prywatne punkty końcowe. Artykuły korzystają z aplikacji sieci Web jako przykładowego zasobu do włączania prywatnych punktów końcowych. Użyj zamiast tego następujących parametrów:

| Ustawienie             | Wartość                                    |
|---------------------|------------------------------------------|
| Typ zasobu       | **Microsoft. CognitiveServices/konta** |
| Zasób            | **\<your-speech-resource-name>**         |
| Docelowy zasób podrzędny | **organizacyjnego**                              |

- [Tworzenie prywatnego punktu końcowego przy użyciu witryny Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](../../private-link/create-private-endpoint-cli.md)

### <a name="dns-for-private-endpoints"></a>System DNS dla prywatnych punktów końcowych

Zapoznaj się z ogólnymi zasadami [usługi DNS dla prywatnych punktów końcowych w ramach zasobów Cognitive Services](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Następnie sprawdź, czy konfiguracja DNS działa prawidłowo (Zobacz następne podsekcje).

#### <a name="mandatory-check-dns-resolution-from-the-virtual-network"></a>(Obowiązkowa kontrola). Rozpoznawanie nazw DNS z Virtual Network

Zostanie użyta `my-private-link-speech.cognitiveservices.azure.com` jako Przykładowa nazwa DNS zasobu mowy dla tej sekcji.

Zaloguj się do maszyny wirtualnej znajdującej się w sieci wirtualnej, z którą został podłączony prywatny punkt końcowy. Otwórz wiersz polecenia systemu Windows lub powłokę bash, wykonaj polecenie "nslookup" i upewnij się, że pomyślnie rozpoznano niestandardową nazwę domeny zasobu:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```
Sprawdź, czy adres IP jest rozpoznawany jako adres prywatnego punktu końcowego.

#### <a name="optional-check-dns-resolution-from-other-networks"></a>(Sprawdzanie opcjonalne). Rozpoznawanie nazw DNS z innych sieci

Ten test jest niezbędny, jeśli planujesz używać zasobu mowy z włączonym punktem końcowym w trybie "hybrydowym", który ma włączoną opcję *wszystkie sieci* lub *wybrane sieci i dostęp do prywatnych punktów końcowych* w sekcji *Sieć* zasobu. Jeśli planujesz uzyskać dostęp do zasobu przy użyciu tylko prywatnego punktu końcowego, możesz pominąć tę sekcję.

Zostanie użyta `my-private-link-speech.cognitiveservices.azure.com` jako Przykładowa nazwa DNS zasobu mowy dla tej sekcji.

Na dowolnym komputerze podłączonym do sieci, z której zezwolisz na dostęp do zasobu Otwórz wiersz polecenia systemu Windows lub powłokę bash, wykonaj polecenie "nslookup" i upewnij się, że została pomyślnie rozpoznana niestandardowa nazwa domeny zasobu:
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

Należy zauważyć, że adres IP jest rozpoznawany jako punkt końcowy proxy sieci wirtualnej, który jest używany do wysyłania ruchu sieciowego do prywatnego punktu końcowego z włączonym Cognitive Services zasobem. To zachowanie będzie inne dla zasobu z włączoną niestandardową nazwą domeny, ale *bez* skonfigurowanych prywatnych punktów końcowych. Zapoznaj się z [tą sekcją](#dns-configuration).

## <a name="adjusting-existing-applications-and-solutions"></a>Dostosowywanie istniejących aplikacji i rozwiązań 

Zasób mowy z włączoną domeną niestandardową korzysta z innego sposobu, aby można było korzystać z usługi Speech Services. Jest to prawdziwe dla niestandardowego zasobu mowy z włączoną obsługą domeny zarówno [z](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) prywatnymi punktami końcowymi, jak i [bez](#using-speech-resource-with-custom-domain-name-without-private-endpoints) nich. Bieżąca sekcja zawiera informacje niezbędne do obu tych przypadków.

### <a name="using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Korzystanie z zasobu mowy z włączoną niestandardową nazwą domeny i prywatnym punktem końcowym

Zasób mowy z włączonym niestandardową nazwą domeny i prywatnym punktem końcowym używa innego sposobu, aby móc korzystać z usług mowy. W tej sekcji wyjaśniono, jak używać tego zasobu z interfejsem API REST usługi Speech Services i [zestawem Speech SDK](speech-sdk.md).

> [!NOTE]
> Należy pamiętać, że zasób mowy bez prywatnych punktów końcowych, ale z włączoną **niestandardową nazwą domeny** również ma specjalny sposób na korzystanie z usług mowy, ale w ten sposób różni się od scenariusza z włączonym prywatnym punktem końcowym. Jeśli masz taki zasób (Załóżmy, że masz zasób z prywatnymi punktami końcowymi, ale następnie postanowisz go usunąć), pamiętaj, aby zapoznać się z [sekcją korespondent](#using-speech-resource-with-custom-domain-name-without-private-endpoints).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Zasób mowy z niestandardową nazwą domeny i prywatnym punktem końcowym. Użycie przy użyciu interfejsu API REST

Zostanie użyta `my-private-link-speech.cognitiveservices.azure.com` jako Przykładowa nazwa DNS zasobu mowy (domena niestandardowa) dla tej sekcji.

##### <a name="note-on-speech-services-rest-api"></a>Uwaga dotycząca interfejsu API REST usługi Speech Services

Usługi mowy mają interfejs API REST dla [zamiany mowy na tekst](rest-speech-to-text.md) i [zamiany tekstu na mowę](rest-text-to-speech.md). Należy wziąć pod uwagę następujące kwestie dotyczące scenariusza z włączoną obsługą prywatnego punktu końcowego.

Zamiana mowy na tekst ma dwa różne interfejsy API REST. Każdy interfejs API pełni różne zastosowania, używa różnych punktów końcowych i wymaga innego podejścia, gdy jest używany pojedynczo w scenariuszu z włączoną obsługą prywatnego punktu końcowego.

Interfejsy API REST zamiany mowy na tekst są następujące:
- [wersja 1.0](rest-speech-to-text.md) jest używana na potrzeby transkrypcji w wierszu
- Wersja 3.0 jest używana na potrzeby [transkrypcji partii](batch-transcription.md) i [Custom Speech](custom-speech-overview.md). (Zobacz [pełne odwołanie](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0))

Użycie funkcji zamiany mowy na tekst w wersji 1.0 i interfejsu API REST zamiany tekstu na mowę w scenariuszu prywatnego punktu końcowego jest takie samo, jak w [przypadku zestawu Speech SDK](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) , opisanego w dalszej części tego artykułu. 

Funkcja zamiany mowy na tekst interfejsu API REST w wersji 3.0 korzysta z innego zestawu punktów końcowych i w ten sposób wymaga innego podejścia dla scenariusza z włączoną obsługą prywatnego punktu końcowego.

Oba przypadki są opisane w następnych podsekcjach.


##### <a name="speech-to-text-rest-api-v30"></a>Zamiana mowy na tekst interfejsu API REST v 3.0

Zazwyczaj zasoby mowy wykorzystują [Cognitive Services regionalne punkty końcowe](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) do komunikowania się z [INTERFEJSem API REST z funkcją zamiany mowy na tekst](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Te zasoby mają następujący format nazewnictwa: <p/>`{region}.api.cognitive.microsoft.com`

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
Ten adres URL powinien być dostępny z Virtual Network z dołączonym prywatnym punktem końcowym (pod warunkiem [poprawnego rozpoznawania nazw DNS](#mandatory-check-dns-resolution-from-the-virtual-network)).

Ogólnie mówiąc, po włączeniu niestandardowej nazwy domeny dla zasobu mowy należy zamienić nazwę hosta we wszystkich adresach URL żądań na nową nazwę hosta domeny niestandardowej. Wszystkie inne części żądania (takie jak ścieżka `/speechtotext/v3.0/transcriptions` w powyższym przykładzie) pozostają takie same.

> [!TIP]
> Niektórzy klienci opracowały aplikacje korzystające z regionu należącego do regionalnej nazwy DNS punktu końcowego (na przykład w celu wysłania żądania do zasobu mowy wdrożonego w określonym regionie platformy Azure).
>
> Niestandardowa nazwa domeny zasobu mowy **nie** zawiera informacji o regionie, w którym zasób jest wdrożony. Dlatego opisana powyżej logika aplikacji **nie** będzie działać i musi zostać zmieniona.

##### <a name="speech-to-text-rest-api-v10-and-text-to-speech-rest-api"></a>Interfejs API REST zamiany mowy na tekst, wersja 1.0 i interfejs API REST zamiany tekstu na mowę

Interfejs API REST [do zamiany mowy na tekst w wersji 1.0](rest-speech-to-text.md) i [interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md) wykorzystują dwa typy punktów końcowych:
- [Cognitive Services regionalne punkty końcowe](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) do komunikowania się z interfejsem API REST Cognitive Services w celu uzyskania tokenu autoryzacji
- Specjalne punkty końcowe dla wszystkich innych operacji

Szczegółowy opis specjalnych punktów końcowych i sposób przekształcania ich adresów URL dla zasobu mowy z włączoną obsługą prywatnego punktu końcowego znajduje się w [tej podsekcji](#general-principle) "użycie z zestawem Speech SDK" poniżej. Ta sama zasada opisana dla zestawu SDK ma zastosowanie do interfejsu API REST zamiany mowy na tekst v 1.0 i zamiany tekstu na mowę.

Zapoznaj się z materiałem znajdującym się w podsekcji wymienionym w poprzednim akapicie i zobacz Poniższy przykład. (W tym przykładzie opisano interfejs API REST zamiany tekstu na mowę; użycie interfejsu API REST zamiany mowy na tekst v 1.0 jest w pełni równoważne)

**Przykład użycia interfejsu API REST zamiany tekstu na mowę.**

Firma Microsoft będzie używać zachodniego Europy jako przykładowego regionu platformy Azure i przykładowej `my-private-link-speech.cognitiveservices.azure.com` nazwy DNS zasobu mowy (domena niestandardowa). Niestandardowa nazwa domeny `my-private-link-speech.cognitiveservices.azure.com` w naszym przykładzie należy do zasobu mowy utworzonego w regionie Europa Zachodnia.

Aby uzyskać listę głosów obsługiwanych w regionie, należy wykonać następujące dwie operacje:

- Uzyskaj Token autoryzacji za pośrednictwem
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Korzystanie z uzyskanego tokenu Pobieranie listy głosów za pośrednictwem
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

Korzystanie z zestawu Speech SDK z niestandardową nazwą domeny i z włączonymi prywatnymi punktami końcowymi wymaga przeglądu i możliwych zmian kodu aplikacji. Pracujemy nad bardziej bezproblemowe obsłudze scenariusza prywatnego punktu końcowego.

Zostanie użyta `my-private-link-speech.cognitiveservices.azure.com` jako Przykładowa nazwa DNS zasobu mowy (domena niestandardowa) dla tej sekcji.

##### <a name="general-principle"></a>Ogólna zasada

Zwykle w scenariuszach z zestawem SDK (a także w scenariuszach interfejsu API REST zamiany tekstu na mowę) zasoby mowy wykorzystują specjalne regionalne punkty końcowe dla różnych ofert usług. Format nazwy DNS dla tych punktów końcowych to: </p>`{region}.{speech service offering}.speech.microsoft.com`

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

W tym przykładzie powyżej ( `westeurope.stt.speech.microsoft.com` ) oznacza punkt końcowy zamiany mowy na tekst w regionie Europa Zachodnia.

Punkty końcowe z włączonym prywatnym punktem końcowym komunikują się z usługami mowy za pośrednictwem specjalnego serwera proxy i z tego powodu **należy zmienić adresy URL połączenia punktu końcowego**. Stosowana jest następująca zasada: adres URL punktu końcowego "standardowy" jest zgodny ze wzorcem <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Należy ją zmienić na: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Przykład 1.** Aplikacja komunikuje się za pomocą następującego adresu URL (rozpoznawanie mowy przy użyciu modelu podstawowego w języku angielskim USA w Europie Zachodniej): 
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Aby używać go w scenariuszu z włączonym prywatnym punktem końcowym, gdy niestandardowa nazwa domeny zasobu mowy to `my-private-link-speech.cognitiveservices.azure.com` ten adres URL należy zmodyfikować następująco:
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Przyjrzyjmy się bliżej:
- Nazwa hosta `westeurope.stt.speech.microsoft.com` jest zastępowana przez niestandardową nazwę hosta domeny `my-private-link-speech.cognitiveservices.azure.com`
- Drugi element oryginalnej nazwy DNS ( `stt` ) jest pierwszym elementem ścieżki URL i poprzedza oryginalną ścieżkę, która jest pierwotnym adresem URL. `/speech/recognition/conversation/cognitiveservices/v1?language=en-US``/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`
 
**Przykład 2.** Aplikacja komunikuje się za pomocą następującego adresu URL (funkcja syntezy mowy przy użyciu niestandardowego modelu głosu w Europie Zachodniej): 
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```
Aby używać go w scenariuszu z włączonym prywatnym punktem końcowym, gdy niestandardowa nazwa domeny zasobu mowy to `my-private-link-speech.cognitiveservices.azure.com` ten adres URL należy zmodyfikować następująco: 
```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Zostanie zastosowana taka sama zasada jak w przykładzie 1, ale kluczowym elementem tego czasu jest `voice` .

##### <a name="modifying-applications"></a>Modyfikowanie aplikacji

Aby zastosować zasadę opisaną w poprzedniej sekcji do kodu aplikacji, należy wykonać dwie główne rzeczy:

- Określ adres URL punktu końcowego używany przez aplikację
- Zmodyfikuj adres URL punktu końcowego zgodnie z opisem w poprzedniej sekcji i Utwórz `SpeechConfig` wystąpienie klasy, używając jawnie tego zmodyfikowanego adresu URL

###### <a name="determining-application-endpoint-url"></a>Określanie adresu URL punktu końcowego aplikacji

- [Włącz rejestrowanie dla aplikacji](how-to-use-logging.md) i uruchom ją w celu wygenerowania dziennika
- W pliku dziennika wyszukaj `SPEECH-ConnectionUrl` . Ciąg będzie zawierać `value` parametr, który z kolei będzie zawierać pełny adres URL używany przez aplikację

Przykład wiersza pliku dziennika z adresem URL punktu końcowego:
```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```
W tym przykładzie adres URL używany przez aplikację jest następujący:
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
###### <a name="creating-speechconfig-instance-using-full-endpoint-url"></a>Tworzenie `SpeechConfig` wystąpienia przy użyciu pełnego adresu URL punktu końcowego

Zmodyfikuj punkt końcowy określony w poprzedniej sekcji, zgodnie z opisem w [ogólnej zasadzie](#general-principle) powyżej.

Teraz należy zmodyfikować sposób tworzenia wystąpienia `SpeechConfig` . Najprawdopodobniej dzisiejsza aplikacja korzysta z podobnej do tego:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Nie będzie to możliwe w przypadku zasobu mowy z włączonym prywatnym punktem końcowym ze względu na zmiany nazwy hosta i adresu URL opisane w poprzednich sekcjach. Jeśli spróbujesz uruchomić istniejącą aplikację bez żadnych modyfikacji przy użyciu klucza zasobu z włączonym prywatnym punktem końcowym, zostanie wyświetlony komunikat o błędzie uwierzytelniania (401).

Aby to umożliwić, należy zmodyfikować sposób tworzenia wystąpienia `SpeechConfig` klasy i używania "z punktu końcowego"/"z inicjacją punktu końcowego". Załóżmy, że mamy zdefiniowane dwie zmienne:
- `subscriptionKey` zawierający klucz zasobu mowy z włączonym prywatnym punktem końcowym
- `endPoint` zawierający pełny, **zmodyfikowany** adres URL punktu końcowego (przy użyciu typu wymaganego przez język programowania korespondenta). W naszym przykładzie ta zmienna powinna zawierać
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
Następnie musimy utworzyć wystąpienie `SpeechConfig` klasy podobnej do:
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
> Parametry zapytania określone w identyfikatorze URI punktu końcowego nie są zmieniane, nawet jeśli są ustawione przez inne interfejsy API. Na przykład jeśli język rozpoznawania jest zdefiniowany w identyfikatorze URI jako parametr zapytania "language = EN-US" i jest również ustawiony na wartość "ru-RU" za pośrednictwem właściwości korespondent, ustawienie języka w identyfikatorze URI ma pierwszeństwo, a obowiązujący język to "pl-US". Inne interfejsy API można ustawić tylko dla parametrów, które nie są określone w identyfikatorze URI punktu końcowego.

Po tej modyfikacji aplikacja powinna współpracować z prywatnymi włączonymi zasobami mowy. Pracujemy nad bardziej bezproblemowe obsłudze scenariusza prywatnego punktu końcowego.

### <a name="using-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Używanie zasobu mowy z niestandardową nazwą domeny bez prywatnych punktów końcowych

W tym artykule opisano kilka razy, że włączenie niestandardowej domeny dla zasobu mowy jest **nieodwracalne** i taki zasób będzie używać innego sposobu komunikacji z usługami mowy, porównując je z "typowymi" (to są te, które używają [regionalnych nazw punktów końcowych](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)).

W tej sekcji wyjaśniono, jak używać zasobu mowy z włączoną niestandardową nazwą domeny, ale **bez** żadnych prywatnych punktów końcowych za pomocą interfejsu API REST usługi Speech Services i [zestawu Speech SDK](speech-sdk.md). Może to być zasób, który został raz użyty w scenariuszu prywatnego punktu końcowego, ale jego prywatne punkty końcowe zostały usunięte.

#### <a name="dns-configuration"></a>Konfiguracja usługi DNS

Należy pamiętać, jak nazwa DNS domeny niestandardowej z włączonym prywatnym punktem końcowym jest [rozpoznawana z sieci publicznych](#optional-check-dns-resolution-from-other-networks). W tym przypadku adres IP jest rozpoznawany jako punkt końcowy serwera proxy sieci wirtualnej, który jest używany do wysyłania ruchu sieciowego do prywatnego punktu końcowego z włączonym Cognitive Services zasobem.

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
Porównaj ją z danymi wyjściowymi z [tej sekcji](#optional-check-dns-resolution-from-other-networks).

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Zasób mowy z niestandardową nazwą domeny bez prywatnych punktów końcowych. Użycie przy użyciu interfejsu API REST

##### <a name="speech-to-text-rest-api-v30"></a>Zamiana mowy na tekst interfejsu API REST v 3.0

Użycie funkcji zamiany mowy na tekst interfejsu API REST w wersji 3.0 jest w pełni równoważne z przypadkiem [prywatnych zasobów mowy z włączonym punktem końcowym](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-v10-and-text-to-speech-rest-api"></a>Interfejs API REST zamiany mowy na tekst, wersja 1.0 i interfejs API REST zamiany tekstu na mowę

W tym przypadku funkcja zamiany mowy na tekst w interfejsie API REST w wersji 1.0 oraz użycie interfejsu API REST zamiany tekstu na mowę nie ma żadnych różnic w ogólnym przypadku i powinna być używana zgodnie z opisem w dokumentacji interfejsu API REST w [trybie zamiany mowy na tekst w wersji 1.0](rest-speech-to-text.md) i w interfejsie API [rest zamiany tekstu na mowę](rest-text-to-speech.md) .


#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Zasób mowy z niestandardową nazwą domeny bez prywatnych punktów końcowych. Użycie przy użyciu zestawu Speech SDK

Korzystanie z zestawu Speech SDK z niestandardowymi nazwami domen z włączonymi zasobami mowy **bez** prywatnych punktów końcowych wymaga przeglądu i możliwych zmian kodu aplikacji. Należy zauważyć, że te zmiany **różnią** się w porównaniu do wielkości liter [zasobu mowy z włączoną obsługą prywatnego punktu końcowego](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk). Pracujemy nad bardziej bezproblemowe obsłudze prywatnego punktu końcowego/domeny niestandardowej.

Zostanie użyta `my-private-link-speech.cognitiveservices.azure.com` jako Przykładowa nazwa DNS zasobu mowy (domena niestandardowa) dla tej sekcji.

W sekcji dotyczącej [zasobu mowy z włączoną funkcją prywatnego punktu końcowego](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) wyjaśniono, jak określić używany adres URL punktu końcowego, zmodyfikować go i przeznaczyć na "od punktu końcowego"/"z punktem końcowym" Inicjowanie `SpeechConfig` wystąpienia klasy.

Jednak jeśli spróbujesz uruchomić tę samą aplikację po usunięciu wszystkich prywatnych punktów końcowych (co pozwoli na jakiś czas na ponowne Inicjowanie obsługi rekordu DNS), zostanie wyświetlony wewnętrzny błąd usługi (404). Przyczyną jest [rekord DNS](#dns-configuration) , który wskazuje punkt końcowy Cognitive Services regionalny zamiast serwera proxy sieci wirtualnej, a ścieżki URL takie jak `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` nie zostaną tam znalezione, dlatego nie znaleziono błędu (404).

W przypadku "wycofywania" aplikacji do "standardowego" tworzenia wystąpienia `SpeechConfig` w stylu
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Aplikacja zostanie zakończona z błędem uwierzytelniania (401).

##### <a name="modifying-applications"></a>Modyfikowanie aplikacji

Aby umożliwić aplikacji scenariusz zasobu mowy z niestandardową nazwą domeny bez prywatnych punktów końcowych, należy wykonać następujące czynności:
- Żądanie tokenu autoryzacji za pośrednictwem interfejsu API REST Cognitive Services
- Tworzenie wystąpienia `SpeechConfig` klasy przy użyciu metody "from Token autoryzacji"/"z tokenem autoryzacji" 

###### <a name="requesting-authorization-token"></a>Żądanie tokenu autoryzacji

Zapoznaj się z [tym artykułem](../authentication.md#authenticate-with-an-authentication-token) , jak uzyskać token za pośrednictwem interfejsu API REST Cognitive Services. 

Użyj niestandardowej nazwy domeny w adresie URL punktu końcowego, który jest w naszym przykładzie następujący adres URL to:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> Ten adres URL można znaleźć w sekcji *klucze i punkt końcowy* (Grupa *zarządzania zasobami* ) w zasobie mowy w Azure Portal.

###### <a name="creating-speechconfig-instance-using-authorization-token"></a>Tworzenie `SpeechConfig` wystąpienia przy użyciu tokenu autoryzacji

Należy utworzyć wystąpienie `SpeechConfig` klasy przy użyciu tokenu autoryzacji uzyskanego w poprzedniej sekcji. Załóżmy, że zdefiniowano następujące zmienne:

- `token` zawierający token autoryzacji uzyskany w poprzedniej sekcji
- `azureRegion` zawierający nazwę [regionu](regions.md) zasobów mowy (przykład: `westeurope` )
- `outError` (tylko w przypadku [celu C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) )

Następnie musimy utworzyć wystąpienie `SpeechConfig` klasy podobnej do:
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
> Obiekt wywołujący musi upewnić się, że token autoryzacji jest prawidłowy. Przed wygaśnięciem tokenu autoryzacji wywołujący musi odświeżyć go przez wywołanie tej metody ustawiającej z nowym prawidłowym tokenem. Ponieważ wartości konfiguracji są kopiowane podczas tworzenia nowego aparatu rozpoznawania/syntezatora, Nowa wartość tokenu nie będzie stosowana do aparatów rozpoznawania, które zostały już utworzone. W przypadku aparatów rozpoznawania/syntezatorów, które zostały utworzone przed, należy ustawić Token autoryzacji odpowiedniego aparatu rozpoznawania/syntezatora w celu odświeżenia tokenu. W przeciwnym razie aparaty rozpoznawania/syntezatory napotkają błędy podczas rozpoznawania/syntezy.

Po tej modyfikacji aplikacja powinna współpracować z niestandardowymi zasobami mowy z włączoną obsługą nazw domen bez prywatnych punktów końcowych. Pracujemy nad bardziej bezproblemowe obsłudze scenariusza niestandardowej domeny/prywatnego punktu końcowego.

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [prywatnym łączu platformy Azure](../../private-link/private-link-overview.md)
* Dowiedz się więcej o [zestawie mowy SDK](speech-sdk.md)
* Dowiedz się więcej o [interfejsie API REST zamiany mowy na tekst](rest-speech-to-text.md)
* Dowiedz się więcej o [interfejsie API REST zamiany tekstu na mowę](rest-text-to-speech.md)
