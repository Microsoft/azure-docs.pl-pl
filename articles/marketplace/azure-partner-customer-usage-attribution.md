---
title: Przypisanie użycia klienta platformy Azure
description: Zapoznaj się z omówieniem śledzenia użycia klientów dla aplikacji platformy Azure w komercyjnej witrynie Marketplace i innym wdrażanym adresem IP opracowanym przez partnerów.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 03/19/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 79f3276347aa64655f0c9086db5f152c4ff5fbcf
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771094"
---
# <a name="azure-customer-usage-attribution"></a>Przypisanie użycia klienta platformy Azure

Przypisanie użycia klientów kojarzy użycie zasobów platformy Azure w ramach subskrypcji klientów utworzonych podczas wdrażania adresu IP jako partnera. Takie skojarzenia w wewnętrznych systemach firmy Microsoft zapewniają większą widoczność platformy Azure, na której działa oprogramowanie. [W przypadku ofert aplikacji platformy Azure w komercyjnej witrynie Marketplace](#commercial-marketplace-azure-apps)ta funkcja śledzenia pomaga w wyrównaniu z zespołami sprzedaży firmy Microsoft i uzyskać środki na korzystanie z programów partnerskich firmy Microsoft.

Przypisanie użycia klienta obsługuje trzy opcje wdrażania:

1. Szablony Azure Resource Manager (typowe podnoszenie poziomu aplikacji platformy Azure, nazywane również w komercyjnym portalu Marketplace jako "szablony rozwiązań" lub "aplikacje zarządzane"): Partnerzy tworzą szablony Menedżer zasobów w celu zdefiniowania infrastruktury i konfiguracji rozwiązań platformy Azure. Szablon Menedżer zasobów umożliwia klientom wdrażanie zasobów rozwiązania w stanie spójny i powtarzalny.
1. Interfejsy API Azure Resource Manager: partnerzy mogą wywoływać interfejsy API Menedżer zasobów, aby wdrożyć szablon Menedżer zasobów lub bezpośrednio zainicjować usługi platformy Azure.
1. Terraform: partnerzy mogą używać Terraform do wdrażania szablonu Menedżer zasobów lub bezpośrednio wdrażać usługi platformy Azure.

Istnieją pomocnicze przypadki użycia dotyczące przypisywania użycia klienta poza rynkiem komercyjnym opisanym [w dalszej części tego artykułu](#other-use-cases).

>[!IMPORTANT]
>- Przypisanie użytkowania przez klienta nie jest przeznaczone do śledzenia pracy integratorów systemów, dostawców usług zarządzanych lub narzędzi przeznaczonych głównie do wdrażania zasobów platformy Azure i zarządzania nimi.
>- Przypisanie użycia klienta dotyczy nowych wdrożeń i nie obsługuje śledzenia zasobów, które zostały już wdrożone.
>- Nie wszystkie usługi platformy Azure są zgodne z przyznanymi użyciem klientów. Usługi Azure Kubernetes Services (AKS), VM Scale Sets i Azure Batch mają znane problemy powodujące raportowanie użycia.

## <a name="commercial-marketplace-azure-apps"></a>Komercyjne aplikacje platformy Azure Marketplace

Śledzenie użycia platformy Azure z poziomu aplikacji platformy Azure opublikowanych na komercyjnej witrynie Marketplace jest w dużym stopniu automatyczne. Po przekazaniu szablonu Menedżer zasobów w ramach [konfiguracji technicznej planu aplikacji platformy Azure Marketplace](https://docs.microsoft.com/azure/marketplace/create-new-azure-apps-offer-solution#define-the-technical-configuration)centrum partnerskie doda identyfikator śledzenia możliwy do odczytania przez Azure Resource Manager.

Jeśli używasz Azure Resource Manager interfejsów API, musisz dodać identyfikator śledzenia zgodnie z [poniższymi instrukcjami](#use-resource-manager-apis) , aby przekazać go do Azure Resource Manager, ponieważ kod wdraża zasoby. Ten identyfikator jest widoczny w centrum partnerskim na stronie konfiguracji technicznej Twojego planu. 

> [!NOTE]
> W przypadku istniejących aplikacji platformy Azure migracja jednorazowa została przeprowadzona w marcu 2021, aby zaktualizować identyfikatory śledzenia w konfiguracji technicznej każdego planu. Użycie z wcześniejszych wdrożeń tych ofert pozostanie śledzone w systemach firmy Microsoft.

## <a name="other-use-cases"></a>Inne przypadki użycia 

Możesz użyć przypisywania użycia klientów do śledzenia użycia rozwiązań na platformie Azure, które nie są dostępne w komercyjnej witrynie Marketplace. Te rozwiązania zwykle znajdują się w repozytorium szybkiego startu, prywatnych repozytoriach usługi GitHub lub pochodzą z 1:1 zaangażowania klientów, którzy tworzą trwały adres IP (na przykład aplikacje wdrożone i skalowalne).

Wymagana jest kilka czynności ręcznych:

1. Utwórz co najmniej jeden identyfikator GUID, który ma być używany jako identyfikator śledzenia.
1. Zarejestruj te identyfikatory GUID w centrum partnerskim.
1. Dodaj zarejestrowane identyfikatory GUID do ciągów aplikacji platformy Azure i/lub agenta użytkownika.

### <a name="create-guids"></a>Tworzenie identyfikatorów GUID

W przeciwieństwie do identyfikatorów śledzenia tworzonych przez centrum partnerskie w imieniu aplikacji platformy Azure w komercyjnej witrynie Marketplace inne zastosowania CUA wymagają utworzenia identyfikatora GUID, który będzie używany jako identyfikator śledzenia. Identyfikator GUID jest unikatowym identyfikatorem odwołania, który ma 32 cyfr szesnastkowych. Aby utworzyć identyfikatory GUID do śledzenia, należy użyć generatora GUID, na przykład, za pomocą programu PowerShell:

```powershell
[guid]::NewGuid()
```

Należy utworzyć unikatowy identyfikator GUID dla każdego produktu i kanału dystrybucji. Jeśli nie chcesz, aby raporty były dzielone, możesz użyć jednego identyfikatora GUID dla wielu kanałów dystrybucji produktu. Raportowanie odbywa się według identyfikatora Microsoft Partner Network i identyfikatora GUID.

### <a name="register-guids"></a>Zarejestruj identyfikatory GUID

Identyfikatory GUID muszą zostać zarejestrowane w centrum partnerskim, aby można je było skojarzyć z użytkownikiem jako partner:

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard).

1. Zarejestruj się jako [komercyjny wydawca portalu Marketplace](https://aka.ms/JoinMarketplace).

1. Wybierz pozycję **Ustawienia** (ikona koła zębatego) w prawym górnym rogu, a następnie **Ustawienia konta**.

1. Wybierz **identyfikatory profilów organizacji**  >    >  **Dodaj identyfikator GUID śledzenia**.

1. W polu **GUID** wprowadź identyfikator GUID śledzenia. Wprowadź tylko identyfikator GUID bez `pid-` prefiksu. W polu **Opis** wprowadź nazwę lub opis rozwiązania.

1. Aby zarejestrować więcej niż jeden identyfikator GUID, ponownie wybierz pozycję **Dodaj identyfikator GUID śledzenia** . Na stronie są wyświetlane dodatkowe pola.

1. Wybierz pozycję **Zapisz**.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Dodawanie identyfikatora GUID do szablonu Menedżer zasobów

Aby dodać zarejestrowany identyfikator GUID do szablonu Menedżer zasobów, należy dokonać pojedynczej modyfikacji pliku szablonu głównego:

1. Otwórz szablon Menedżer zasobów.

1. Dodaj nowy zasób typu [Microsoft. resources/Deployments](/azure/templates/microsoft.resources/deployments) w głównym pliku szablonu. Zasób musi znajdować się w **mainTemplate.js** lubazuredeploy.jstylko **dla** pliku, a nie w żadnych zagnieżdżonych lub połączonych szablonach.

1. Wprowadź wartość identyfikatora GUID po `pid-` prefiksie jako nazwę zasobu. Na przykład jeśli identyfikator GUID to eb7927c8-dd66-43e1-b0cf-c346a422063, nazwą zasobu będzie **PID-eb7927c8-dd66-43e1-b0cf-c346a422063**. Przykład:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. Sprawdź, czy szablon nie ma błędów.

1. Opublikuj ponownie szablon w odpowiednich repozytoriach.

1. [Sprawdź sukces identyfikatora GUID we wdrożeniu szablonu](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> Aby uzyskać więcej informacji na temat tworzenia i publikowania szablonów Menedżer zasobów, zobacz: [Tworzenie i wdrażanie pierwszego szablonu Menedżer zasobów](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>Weryfikuj wdrożenia śledzone przy użyciu identyfikatora GUID

Po zmodyfikowaniu szablonu i uruchomieniu wdrożenia testowego należy użyć następującego skryptu programu PowerShell, aby pobrać wdrożone i otagowane zasoby.

Możesz użyć skryptu, aby sprawdzić, czy identyfikator GUID został pomyślnie dodany do szablonu Menedżer zasobów. Skrypt nie ma zastosowania do Menedżer zasobów wdrożeń API lub Terraform.

Zaloguj się do platformy Azure. Wybierz subskrypcję z wdrożeniem, które chcesz zweryfikować przed uruchomieniem skryptu. Uruchom skrypt w kontekście subskrypcji wdrożenia.

**Identyfikator GUID** (poniżej "deploymentname") i nazwa **resourceGroupName** wdrożenia są wymagane parametry.

Możesz uzyskać [oryginalny skrypt](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) w serwisie GitHub.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Powiadamiaj klientów

Partnerzy powinni poinformować klientów o wdrożeniach korzystających z przypisywania użycia przez klienta. Firma Microsoft zgłasza do partnera użycie platformy Azure skojarzone z tymi wdrożeniami. Poniższe przykłady obejmują zawartość, której można użyć do powiadamiania klientów o tych wdrożeniach. W przykładach Zastąp ciąg \<PARTNER> nazwą swojej firmy. Partnerzy powinni upewnić się, że powiadomienia są wyrównane do zasad zachowania poufności danych i ich zbierania, w tym opcji dla klientów, którzy mają być wykluczeni ze śledzenia.

#### <a name="notification-for-resource-manager-template-deployments"></a>Powiadomienie dotyczące wdrożeń szablonów Menedżer zasobów

Po wdrożeniu tego szablonu firma Microsoft może zidentyfikować instalację \<PARTNER> oprogramowania przy użyciu wdrożonych zasobów platformy Azure. Firma Microsoft może skorelować te zasoby, które są używane do obsługi oprogramowania. Firma Microsoft zbiera te informacje, aby zapewnić najlepsze środowisko dla swoich produktów i prowadzić działalność biznesową. Dane są zbierane i podlegają zasadom zachowania poufności informacji firmy Microsoft, które znajdują się w witrynie [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

#### <a name="notification-for-sdk-or-api-deployments"></a>Powiadomienie o wdrożeniach zestawu SDK lub interfejsu API

Podczas wdrażania \<PARTNER> oprogramowania firma Microsoft może zidentyfikować instalację \<PARTNER> oprogramowania przy użyciu wdrożonych zasobów platformy Azure. Firma Microsoft może skorelować te zasoby, które są używane do obsługi oprogramowania. Firma Microsoft zbiera te informacje, aby zapewnić najlepsze środowisko dla swoich produktów i prowadzić działalność biznesową. Dane są zbierane i podlegają zasadom zachowania poufności informacji firmy Microsoft, które znajdują się w witrynie [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="use-resource-manager-apis"></a>Korzystanie z Menedżer zasobów interfejsów API

W niektórych przypadkach można nawiązywać wywołania bezpośrednio do Menedżer zasobów interfejsów API REST w celu wdrożenia usług platformy Azure. [Platforma Azure obsługuje wiele zestawów SDK](../index.yml?pivot=sdkstools) , aby umożliwić te wywołania. Można użyć jednego z zestawów SDK lub wywołać interfejsy API REST bezpośrednio w celu wdrożenia zasobów.

Aby włączyć przypisanie użycia klienta, podczas projektowania wywołań interfejsu API należy uwzględnić identyfikator śledzenia w nagłówku agenta użytkownika w żądaniu. Sformatuj ciąg z `pid-` prefiksem. Przykłady:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Jeśli używasz Menedżer zasobów interfejsów API z aplikacją platformy Azure w komercyjnej witrynie Marketplace, użyj identyfikatora śledzenia podanego w centrum partnerskim. NIE należy używać identyfikatora GUID.

Różne zestawy SDK współdziałają z Menedżer zasobów interfejsów API w różny sposób i będą wymagały pewnych różnic w kodzie. Poniższe przykłady przedstawiają niekomercyjne podejście do witryny Marketplace przy użyciu identyfikatora GUID i obejmują różne bardziej popularne zestawy SDK platformy Azure.

#### <a name="example-python-sdk"></a>Przykład: zestaw SDK języka Python

Dla języka Python Użyj atrybutu **config** . Możesz dodać atrybut tylko do UserAgent. Przykład:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Dodaj atrybut dla każdego klienta. Nie istnieje globalna Konfiguracja statyczna. Można oznaczyć fabrykę klienta, aby upewnić się, że każdy klient śledzi śledzenie. Aby uzyskać więcej informacji, zobacz ten [przykład fabryki klienta w serwisie GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-net-sdk"></a>Przykład: zestaw SDK platformy .NET

W przypadku platformy .NET upewnij się, że ustawiono agenta użytkownika. Użyj biblioteki [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) , aby ustawić agenta użytkownika przy użyciu następującego kodu (przykład w języku C#):

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Przykład: Azure PowerShell

W przypadku wdrażania zasobów za pośrednictwem Azure PowerShell należy dołączyć identyfikator GUID przy użyciu tej metody:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Przykład: interfejs wiersza polecenia platformy Azure

Korzystając z interfejsu wiersza polecenia platformy Azure, aby dołączyć identyfikator GUID, należy ustawić zmienną środowiskową **AZURE_HTTP_USER_AGENT** w zakresie skryptu. Możesz również ustawić zmienną globalnie dla zakresu powłoki:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Aby uzyskać więcej informacji, zobacz [Azure SDK dla języka go](/azure/developer/go/).

## <a name="use-terraform"></a>Użyj Terraform

Pomoc techniczna dla usługi Terraform jest dostępna za pomocą wersji 1.21.0 dostawcy platformy Azure: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) . Dotyczy to wszystkich partnerów, którzy wdrażają swoje rozwiązania za pośrednictwem Terraform oraz wszystkich zasobów wdrożonych i naliczanych przez dostawcę platformy Azure (wersja 1.21.0 lub nowsza).

Dostawca platformy Azure dla Terraform dodał nowe opcjonalne pole o nazwie [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) do określania identyfikatora GUID śledzenia używanego dla Twojego rozwiązania. Wartość tego pola może być również źródłem *ARM_PARTNER_ID* zmiennej środowiskowej.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Ustaw wartość *partner_id* na zarejestrowany identyfikator GUID. NIE należy prefiksować identyfikatora GUID z parametrem "PID", po prostu ustaw go na rzeczywisty identyfikator GUID.

> [!IMPORTANT]
> Jeśli używasz Terraform z aplikacją platformy Azure w komercyjnej witrynie Marketplace, użyj całego identyfikatora śledzenia podanego w centrum partnerskim. NIE należy używać identyfikatora GUID.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Dowiedz się więcej o opcjach pomocy technicznej w portalu komercyjnym na [potrzeby obsługi komercyjnego programu Marketplace w centrum partnerskim](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Jak przesłać żądanie konsultacji technicznych

1. Odwiedź [usługę partner Services](https://aka.ms/TechnicalJourney).
1. Wybierz pozycję **infrastruktura i zarządzanie chmurą** , aby wyświetlić drogę techniczną.
1. Wybierz pozycję **usługi wdrażania**  >  **Prześlij żądanie**.
1. Zaloguj się przy użyciu konta MSA (MPN) lub usługi AAD (konto pulpitu nawigacyjnego partnera).
1. Ukończ/Przejrzyj informacje kontaktowe w formularzu, który zostanie otwarty. Szczegóły konsultacji mogą być wstępnie wypełnione lub mogą być dostępne opcje listy rozwijanej.
1. Wprowadź tytuł i szczegółowy opis problemu.
1. Wybierz pozycję **Prześlij**.

Zapoznaj się z instrukcjami krok po kroku dotyczącymi zrzutów ekranu [korzystających z usług presprzedaży i wdrażania](https://aka.ms/TechConsultInstructions).

Skontaktujesz się z przedstawicielem firmy Microsoft dotyczącym partnerów technicznych w celu skonfigurowania połączenia w celu określenia zakresu Twoich potrzeb.

## <a name="report"></a>Raport
Raportowanie dotyczące użycia platformy Azure śledzone za pośrednictwem przypisywania użycia klienta nie jest obecnie dostępne dla partnerów niezależnego dostawcy oprogramowania. Dodanie raportowania do komercyjnego programu w witrynie Marketplace w centrum partnerskim w celu pokrycia przypisywania użycia klientów jest przeznaczone dla drugiej połowy 2021.

## <a name="faq"></a>Często zadawane pytania

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>Czy po dodaniu identyfikatora śledzenia można go zmienić?

Identyfikatory śledzenia dla aplikacji platformy Azure w komercyjnej witrynie Marketplace są zarządzane automatycznie przez centrum partnerskie. Klient może jednak pobrać szablon i zmienić lub usunąć identyfikator śledzenia. Partnerzy powinni aktywnie opisać rolę identyfikatora śledzenia dla swoich klientów, aby zapobiec usunięciu lub zmianom. Zmiana identyfikatora śledzenia ma wpływ tylko na nowe wdrożenia i zasoby, a nie istniejące.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>Czy mogę śledzić szablony wdrożone z repozytorium innego niż Microsoft, takiego jak GitHub?

Tak, pod warunkiem, że podczas wdrażania szablonu jest obecny identyfikator śledzenia, użycie jest śledzone. Aby zachować skojarzenie między Twoim wydawcą i szablonem wdrożonym z repozytorium innym niż Microsoft, należy najpierw pobrać kopię opublikowanego szablonu (która będzie zawierać identyfikator śledzenia) z komercyjnej listy rynkowej oferty w Azure Portal. Opublikuj tę wersję w serwisie GitHub lub innym repozytorium innym niż Microsoft.

Jeśli szablon nie znajduje się na liście komercyjna witryna Marketplace i zawiera zarejestrowany identyfikator GUID, upewnij się, że identyfikator GUID jest obecny w wersji publikowanej w usłudze GitHub lub innym repozytorium firmy innej niż Microsoft.

#### <a name="does-the-customer-receive-reporting-as-well"></a>Czy klient otrzymuje także raportowanie?

Nie. Klienci mogą śledzić użycie wszystkich zasobów lub grup zasobów w ramach Azure Portal. Klienci nie widzą użycia rozdzielonym przez identyfikator śledzenia CUA.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>Czy przypisanie użycia klienta jest podobne do partnera Digital Partner of Record (DPOR) lub linku administratora partnera (PAL)?

Przypisanie użycia klienta jest mechanizmem do kojarzenia użycia platformy Azure z powtarzalnym, wdrożonym przez partnera adresem IP tworzącym skojarzenie w czasie wdrażania. DPOR i PAL są przeznaczone do kojarzenia partnera konsultingowego (integratora systemów) lub zarządzania (dostawca usług zarządzanych) z odpowiednim okresem platformy Azure klienta przez czas, w którym partner jest zaangażowany z klientem.