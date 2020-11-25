---
title: Komercyjny partner Marketplace i przypisanie użytkowania przez klienta
description: Zapoznaj się z omówieniem śledzenia użycia klientów na potrzeby rozwiązań w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 11/4/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 3728fdd07172f2402ddb1dc7434ddb1993b984b1
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95999879"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Komercyjny partner Marketplace i przypisanie użytkowania przez klienta

Przypisanie użycia klientów to metoda kojarzenia zasobów platformy Azure działających w ramach subskrypcji klientów, wdrożonych w celu uruchomienia rozwiązania, z partnerem. Takie skojarzenia w wewnętrznych systemach firmy Microsoft zapewniają większą widoczność platformy Azure, na której działa oprogramowanie. Po przyjęciu tej funkcji śledzenia należy dostosować ją do zespołów Microsoft Sales Teams i uzyskać środki na korzystanie z programów partnerskich firmy Microsoft.

Można utworzyć skojarzenie za pośrednictwem portalu Azure Marketplace, repozytorium szybkiego startu, prywatnych repozytoriów GitHub i 1:1 zaangażowania klientów, którzy tworzą trwały adres IP (na przykład rozwój aplikacji).

Przypisanie użycia klienta obsługuje trzy opcje wdrażania:

- Szablony Azure Resource Manager: partnerzy mogą używać szablonów Menedżer zasobów do wdrażania usług platformy Azure w celu uruchamiania oprogramowania partnera. Partnerzy mogą utworzyć szablon Menedżer zasobów, aby zdefiniować infrastrukturę i konfigurację rozwiązania platformy Azure. Szablon Menedżer zasobów umożliwia klientom wdrażanie rozwiązania w całym cyklu życia. Możesz mieć pewność, że zasoby są wdrażane w spójnym stanie.
- Interfejsy API Azure Resource Manager: partnerzy mogą wywoływać interfejsy API Menedżer zasobów bezpośrednio, aby wdrożyć szablon Menedżer zasobów lub wygenerować wywołania interfejsu API w celu bezpośredniego udostępnienia usług platformy Azure.
- Terraform: partnerzy mogą używać Terraform do wdrażania szablonu Menedżer zasobów lub bezpośrednio wdrażać usługi platformy Azure.

>[!IMPORTANT]
>- Przypisanie użytkowania przez klienta nie jest przeznaczone do śledzenia pracy integratorów systemów, dostawców usług zarządzanych lub narzędzi przeznaczonych do wdrażania oprogramowania działającego na platformie Azure i zarządzania nim.
>
>- Przypisanie użycia klienta dotyczy nowych wdrożeń i nie obsługuje tagowania istniejących już wdrożonych zasobów.
>
>- Przypisanie użycia klienta jest wymagane dla ofert [aplikacji platformy Azure](./partner-center-portal/create-new-azure-apps-offer.md) opublikowanych w portalu Azure Marketplace.
>
>- Nie wszystkie usługi platformy Azure są zgodne z przyznanymi użyciem klientów. Usługi Azure Kubernetes Services (AKS) i VM Scale Sets mają znane problemy, które powodują, że w ramach raportowania użycia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Tworzenie identyfikatorów GUID

Identyfikator GUID jest unikatowym identyfikatorem odwołania, który ma 32 cyfr szesnastkowych. Aby utworzyć identyfikatory GUID do śledzenia, należy użyć generatora GUID, na przykład za pośrednictwem programu PowerShell.

```powershell
[guid]::NewGuid()
```

Zalecamy utworzenie unikatowego identyfikatora GUID dla każdej oferty i kanału dystrybucji dla każdego produktu. Można wybrać opcję użycia jednego identyfikatora GUID dla wielu kanałów dystrybucji produktu, jeśli nie chcesz, aby raportowanie było podzielone.

Jeśli produkt zostanie wdrożony przy użyciu szablonu, który jest dostępny zarówno w portalu Azure Marketplace, jak i w witrynie GitHub, można utworzyć i zarejestrować dwa unikatowe identyfikatory GUID:

- Produkt A w portalu Azure Marketplace
- Produkt A w serwisie GitHub

Raportowanie odbywa się według identyfikatora Microsoft Partner Network i identyfikatora GUID.

Możesz również śledzić użycie na bardziej szczegółowym poziomie, rejestrując dodatkowe identyfikatory GUID i zmieniając identyfikatory GUID między planami, gdzie plany są wariantem oferty.

## <a name="register-guids"></a>Zarejestruj identyfikatory GUID

Identyfikatory GUID muszą być zarejestrowane w centrum partnerskim, aby umożliwić przypisanie użycia klienta.

Po dodaniu identyfikatora GUID do szablonu lub w agencie użytkownika i zarejestrowaniu identyfikatora GUID w centrum partnerskim są śledzone przyszłe wdrożenia.

> [!NOTE]
> W przypadku publikowania oferty [aplikacji platformy Azure](./partner-center-portal/create-new-azure-apps-offer.md) w portalu Azure Marketplace za pomocą Centrum partnerskiego każdy nowy identyfikator GUID używany wewnątrz szablonu zostanie automatycznie zarejestrowany w profilu Centrum partnerskiego podczas przekazywania szablonu.  

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard).

1. Zarejestruj się jako [komercyjny wydawca portalu Marketplace](https://aka.ms/JoinMarketplace).

   * Partnerzy muszą [mieć profil w centrum partnerskim](./partner-center-portal/create-account.md). Zachęcamy do korzystania z oferty w witrynie Azure Marketplace lub AppSource.
   * Partnerzy mogą rejestrować wiele identyfikatorów GUID.
   * Partnerzy mogą rejestrować identyfikatory GUID dla szablonów rozwiązań i ofert spoza witryny Marketplace.

1. Wybierz pozycję **Ustawienia** (ikona koła zębatego) w prawym górnym rogu, > **Ustawienia konta**.

1. W obszarze identyfikatory **profilów organizacji**  >  **Identifiers** wybierz pozycję **Dodaj identyfikator GUID śledzenia**.

1. W polu **GUID** wprowadź identyfikator GUID śledzenia. Wprowadź tylko identyfikator GUID bez `pid-` prefiksu. W polu **Opis** wprowadź nazwę lub opis oferty.

1. Aby zarejestrować więcej niż jeden identyfikator GUID, ponownie wybierz pozycję **Dodaj identyfikator GUID śledzenia** . Na stronie są wyświetlane dodatkowe pola.

1. Wybierz pozycję **Zapisz**.

## <a name="use-resource-manager-templates"></a>Używanie szablonów usługi Resource Manager
Wiele rozwiązań partnerskich jest wdrażanych przy użyciu szablonów Azure Resource Manager. Jeśli masz szablon Menedżer zasobów, który jest dostępny w witrynie Azure Marketplace, w witrynie GitHub lub w ramach przewodnika Szybki Start, proces modyfikacji szablonu, który umożliwi przypisanie użycia klienta, jest prosty do przodu.

> [!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia i publikowania szablonów rozwiązań, zobacz
> * [Utwórz i Wdróż pierwszy szablon Menedżer zasobów](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
>* [Oferta aplikacji platformy Azure](./partner-center-portal/create-new-azure-apps-offer.md).
>* Wideo: [Tworzenie szablonów rozwiązań i aplikacji zarządzanych dla portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Aby dodać unikatowy identyfikator globalny (GUID), należy dokonać pojedynczej modyfikacji pliku szablonu głównego:

1. [Utwórz identyfikator GUID](#create-guids) przy użyciu sugerowanej metody i [zarejestruj identyfikator GUID](#register-guids).

1. Otwórz szablon Menedżer zasobów.

1. Dodaj nowy zasób typu [Microsoft. resources/Deployments](/azure/templates/microsoft.resources/deployments) w głównym pliku szablonu. Zasób musi znajdować się w **mainTemplate.js** lubazuredeploy.jstylko **dla** pliku, a nie w żadnych zagnieżdżonych lub połączonych szablonach.

1. Wprowadź wartość identyfikatora GUID po `pid-` prefiksie jako nazwę zasobu. Na przykład jeśli identyfikator GUID to eb7927c8-dd66-43e1-b0cf-c346a422063, nazwą zasobu będzie _PID-eb7927c8-dd66-43e1-b0cf-c346a422063_.

1. Sprawdź, czy szablon nie ma błędów.

1. Opublikuj ponownie szablon w odpowiednich repozytoriach.

1. [Sprawdź sukces identyfikatora GUID we wdrożeniu szablonu](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Przykładowy kod szablonu Menedżer zasobów

Aby włączyć śledzenie zasobów dla szablonu, należy dodać do sekcji Resources następujący dodatkowy zasób. Pamiętaj o zmodyfikowaniu poniższego przykładowego kodu przy użyciu własnych danych wejściowych po dodaniu go do głównego pliku szablonu.
Zasób należy dodać do **mainTemplate.js** lub **azuredeploy.jstylko dla** pliku, a nie w żadnych zagnieżdżonych lub połączonych szablonach.

```json
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
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

## <a name="use-the-resource-manager-apis"></a>Korzystanie z Menedżer zasobów interfejsów API

W niektórych przypadkach warto wykonać wywołania bezpośrednio do Menedżer zasobów interfejsów API REST w celu wdrożenia usług platformy Azure. [Platforma Azure obsługuje wiele zestawów SDK](../index.yml?pivot=sdkstools) , aby umożliwić te wywołania. Można użyć jednego z zestawów SDK lub wywołać interfejsy API REST bezpośrednio w celu wdrożenia zasobów.

Jeśli używasz szablonu Menedżer zasobów, należy oznaczyć rozwiązanie, postępując zgodnie z instrukcjami opisanymi wcześniej. Jeśli nie korzystasz z szablonu Menedżer zasobów i tworzysz bezpośrednie wywołania interfejsu API, możesz oznaczyć wdrożenie, aby skojarzyć użycie zasobów platformy Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Dodawanie tagów do wdrożenia za pomocą interfejsów API Menedżer zasobów

Aby włączyć przypisanie użycia klienta, podczas projektowania wywołań interfejsu API Dołącz identyfikator GUID w nagłówku agenta użytkownika w żądaniu. Dodaj identyfikator GUID dla każdej oferty lub jednostki SKU. Sformatuj ciąg z `pid-` prefiksem i Uwzględnij wygenerowany przez partnera identyfikator GUID. Oto przykład formatu identyfikatora GUID do wstawienia do agenta użytkownika:

![Przykładowy format identyfikatora GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> Format ciągu jest ważny. Jeśli `pid-` prefiks nie jest uwzględniony, nie jest możliwe wykonywanie zapytań dotyczących danych. Różne zestawy SDK śledzą się inaczej. Aby zaimplementować tę metodę, zapoznaj się z podejściem do pomocy technicznej i śledzenia dla preferowanego zestawu Azure SDK.

#### <a name="example-the-python-sdk"></a>Przykład: zestaw SDK języka Python

Dla języka Python Użyj atrybutu **config** . Możesz dodać atrybut tylko do UserAgent. Oto przykład:

![Dodawanie atrybutu do agenta użytkownika](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Dodaj atrybut dla każdego klienta. Nie istnieje globalna Konfiguracja statyczna. Użytkownik może oznaczyć fabrykę klienta, aby upewnić się, że każdy klient śledzi śledzenie. Aby uzyskać więcej informacji, zobacz ten [przykład fabryki klienta w serwisie GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-the-net-sdk"></a>Przykład: zestaw SDK platformy .NET

W przypadku platformy .NET upewnij się, że ustawiono agenta użytkownika. Biblioteka [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) może służyć do ustawiania agenta użytkownika przy użyciu następującego kodu (przykład w języku C#):

```csharp

var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Oznacz wdrożenie przy użyciu Azure PowerShell

W przypadku wdrażania zasobów za pośrednictwem Azure PowerShell należy dołączyć identyfikator GUID przy użyciu następującej metody:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Dodawanie tagów do wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure

Korzystając z interfejsu wiersza polecenia platformy Azure, aby dołączyć identyfikator GUID, Ustaw zmienną środowiskową **AZURE_HTTP_USER_AGENT** . Możesz ustawić tę zmienną w zakresie skryptu. Możesz również ustawić zmienną globalnie dla zakresu powłoki:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Aby uzyskać więcej informacji, zobacz [Azure SDK dla języka go](/azure/developer/go/).

## <a name="use-terraform"></a>Użyj Terraform

Pomoc techniczna dla programu Terraform jest dostępna za pomocą wersji 1.21.0 dostawcy platformy Azure: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) .  Ta pomoc techniczna dotyczy wszystkich partnerów, którzy wdrażają swoje rozwiązania za pośrednictwem usługi Terraform, oraz wszystkich zasobów wdrożonych i naliczanych przez dostawcę platformy Azure (w wersji 1.21.0 lub nowszej).

Dostawca platformy Azure dla Terraform dodał nowe pole opcjonalne o nazwie [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) , w którym można określić identyfikator GUID śledzenia, który jest używany dla danego rozwiązania. Wartość tego pola może być również źródłem *ARM_PARTNER_ID* zmiennej środowiskowej.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partnerzy, którzy chcą wdrożyć wdrożenie za pośrednictwem Terraform, muszą wykonać następujące czynności:

* Utwórz identyfikator GUID (identyfikator GUID należy dodać dla każdej oferty lub jednostki SKU)
* Zaktualizuj dostawcę platformy Azure, aby ustawić wartość *partner_id* na identyfikator GUID (nie należy wstępnie naprawić identyfikatora GUID przy użyciu identyfikatora "PID", po prostu ustaw go na rzeczywisty identyfikator GUID).

## <a name="verify-the-guid-deployment"></a>Weryfikowanie wdrożenia identyfikatora GUID

Po zmodyfikowaniu szablonu i uruchomieniu wdrożenia testowego należy użyć następującego skryptu programu PowerShell w celu pobrania wdrożonych i otagowanych zasobów.

Możesz użyć skryptu, aby sprawdzić, czy identyfikator GUID został pomyślnie dodany do szablonu Menedżer zasobów. Skrypt nie ma zastosowania do Menedżer zasobów wdrożeń API lub Terraform.

Zaloguj się do platformy Azure. Wybierz subskrypcję ze wdrożeniem, które chcesz zweryfikować przed uruchomieniem skryptu. Uruchom skrypt w kontekście subskrypcji wdrożenia.

**Identyfikator GUID** i nazwa obiektu **zasobów** wdrożenia są wymaganymi parametrami.

Możesz uzyskać [oryginalny skrypt](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) w serwisie GitHub.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Raport

Raport dotyczący przypisywania użycia klientów można znaleźć na pulpicie nawigacyjnym Centrum partnerskiego ( [https://partner.microsoft.com/dashboard/partnerinsights/analytics/overview](https://partner.microsoft.com/dashboard/partnerinsights/analytics/overview) ). Aby wyświetlić raport, należy zalogować się przy użyciu poświadczeń Centrum partnerskiego. Jeśli wystąpią problemy z raportem lub zalogowaniem, Utwórz [żądanie pomocy technicznej](#get-support).

## <a name="notify-your-customers"></a>Powiadamiaj klientów

Partnerzy powinni poinformować klientów o wdrożeniach korzystających z przypisywania użycia przez klienta. Firma Microsoft zgłasza do partnera użycie platformy Azure skojarzone z tymi wdrożeniami. Poniższe przykłady obejmują zawartość, której można użyć do powiadamiania klientów o tych wdrożeniach. W przykładach Zastąp ciąg \<PARTNER> nazwą swojej firmy. Partnerzy powinni upewnić się, że powiadomienia są wyrównane do zasad zachowania poufności danych i ich zbierania, w tym opcji dla klientów, które mają być wykluczone ze śledzenia.

### <a name="notification-for-resource-manager-template-deployments"></a>Powiadomienie dotyczące wdrożeń szablonów Menedżer zasobów

Po wdrożeniu tego szablonu firma Microsoft może zidentyfikować instalację \<PARTNER> oprogramowania przy użyciu wdrożonych zasobów platformy Azure. Firma Microsoft może skorelować zasoby platformy Azure, które są używane do obsługi oprogramowania. Firma Microsoft zbiera te informacje, aby zapewnić najlepsze środowisko dla swoich produktów i prowadzić działalność biznesową. Dane są zbierane i podlegają zasadom zachowania poufności informacji firmy Microsoft, które można znaleźć w witrynie [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

### <a name="notification-for-sdk-or-api-deployments"></a>Powiadomienie o wdrożeniach zestawu SDK lub interfejsu API

Podczas wdrażania \<PARTNER> oprogramowania firma Microsoft może zidentyfikować instalację \<PARTNER> oprogramowania przy użyciu wdrożonych zasobów platformy Azure. Firma Microsoft może skorelować zasoby platformy Azure, które są używane do obsługi oprogramowania. Firma Microsoft zbiera te informacje, aby zapewnić najlepsze środowisko dla swoich produktów i prowadzić działalność biznesową. Dane są zbierane i podlegają zasadom zachowania poufności informacji firmy Microsoft, które można znaleźć w witrynie [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Dowiedz się więcej o opcjach pomocy technicznej w portalu komercyjnym na [potrzeby obsługi komercyjnego programu Marketplace w centrum partnerskim](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Jak przesłać żądanie konsultacji technicznych

1. Odwiedź [usługę partner Services](https://aka.ms/TechnicalJourney).
1. Wybierz pozycję Infrastruktura i zarządzanie chmurą, a nowa strona zostanie otwarta, aby wyświetlić drogę techniczną.
1. W obszarze usługi wdrażania kliknij przycisk Prześlij żądanie.
1. Zaloguj się przy użyciu konta MSA (MPN) lub usługi AAD (konto pulpitu nawigacyjnego partnera). na podstawie poświadczeń logowania zostanie otwarty formularz żądania online:
    * Ukończ/Przejrzyj informacje kontaktowe.
    * Szczegóły konsultacji mogą być wstępnie wypełnione lub wybrane z listy rozwijanej.
    * Wprowadź tytuł i opis problemu (Podaj jak najwięcej szczegółów).

1. Kliknij przycisk Submit (Prześlij).

Zapoznaj się z instrukcjami krok po kroku dotyczącymi zrzutów ekranu [korzystających z usług presprzedaży i wdrażania](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Co dalej

Skontaktujesz się z przedstawicielem firmy Microsoft dotyczącym partnerów technicznych w celu skonfigurowania połączenia w celu określenia zakresu Twoich potrzeb.

## <a name="faq"></a>Często zadawane pytania

**Jakie korzyści daje dodanie identyfikatora GUID do szablonu?**

Firma Microsoft udostępnia partnerom widok wdrożeń klientów rozwiązań i wgląd w ich użycie. Zarówno firma Microsoft, jak i partner mogą korzystać z tych informacji, aby zapewnić bliższe zaangażowanie między zespołami ds. sprzedaży. Zarówno firma Microsoft, jak i partner mogą korzystać z danych, aby uzyskać bardziej spójny widok wpływu na rozwój platformy Azure.

**Czy po dodaniu identyfikatora GUID można go zmienić?**

Tak, partner klienta lub implementacji może dostosować szablon i zmienić lub usunąć identyfikator GUID. Sugerujemy, aby partnerzy mogli aktywnie opisać rolę zasobu i identyfikator GUID klientom i partnerom, aby zapobiec usunięciu lub modyfikacji identyfikatora GUID. Zmiana identyfikatora GUID dotyczy tylko nowych, nie istniejących, wdrożeń i zasobów.

**Czy mogę śledzić szablony wdrożone z repozytorium innego niż Microsoft, takiego jak GitHub?**

Tak, o ile identyfikator GUID jest obecny podczas wdrażania szablonu, jest śledzone użycie. Partnerzy muszą nadal rejestrować identyfikatory GUID.

**Czy klient otrzymuje także raportowanie?**

Klienci mogą śledzić użycie poszczególnych zasobów lub grup zasobów zdefiniowanych przez klienta w ramach Azure Portal. Klienci nie widzą użycia rozdzielonym przez identyfikator GUID.

**Czy ta metodologia jest podobna do cyfrowej partnera rejestrowania (DPOR)?**

Ta nowa metoda łączenia wdrożenia i użycia z rozwiązaniem partnera zapewnia mechanizm łączenia rozwiązania partnerskiego z użyciem platformy Azure. DPOR jest przeznaczony do kojarzenia partnera konsultingowego (integratora systemów) lub zarządzania (dostawca usług zarządzanych) z subskrypcją platformy Azure klienta.

**Czy można użyć prywatnego, niestandardowego wirtualnego dysku twardego dla oferty szablonu rozwiązania w portalu Azure Marketplace?**

Nie. Obraz maszyny wirtualnej musi pochodzić z portalu Azure Marketplace, patrz: [Przewodnik publikowania dla ofert maszyn wirtualnych w portalu Azure Marketplace](marketplace-virtual-machines.md).

Możesz utworzyć ofertę maszyny wirtualnej w portalu Marketplace przy użyciu niestandardowego wirtualnego dysku twardego i oznaczyć ją jako prywatną, tak aby nikt nie mógł go zobaczyć. Następnie odwołuje się do tej maszyny wirtualnej w szablonie rozwiązania.

**Nie można zaktualizować właściwości *contentversion —* szablonu głównego?**

Jest to prawdopodobnie usterka, w przypadkach, gdy szablon jest wdrażany przy użyciu TemplateLink z innego szablonu, który oczekuje starszej Contentversion — z jakiegoś powodu. Obejście polega na użyciu właściwości Metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
