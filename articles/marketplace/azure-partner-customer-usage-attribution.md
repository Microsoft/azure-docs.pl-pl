---
title: Atrybucja partnera i użycia klienta w portalu Marketplace
description: Zapoznaj się z omówieniem śledzenia użycia klientów dla rozwiązań w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e061baa8e7eb73bae0a78f4215f0d48610e85fcb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686729"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Atrybucja partnera i użycia klienta w portalu Marketplace

Atrybucja użycia klienta to metoda kojarzenia zasobów platformy Azure działających w ramach subskrypcji klientów, wdrożonych w celu uruchomienia rozwiązania, z Tobą jako partnerem. Tworzenie tych skojarzeń w wewnętrznych systemach firmy Microsoft zapewnia lepszą widoczność śladu platformy Azure uruchomionego oprogramowania. Po ustosowujesz tę funkcję śledzenia, można wyrównać z zespołami sprzedaży firmy Microsoft i uzyskać kredyt dla programów partnerskich firmy Microsoft.

Możesz utworzyć skojarzenie za pośrednictwem portalu Azure Marketplace, repozytorium Szybki start, prywatnych repozytoriów Usługi GitHub i zaangażowania klientów 1:1, które tworzą trwały adres IP (na przykład tworzenie aplikacji).

Atrybucja użycia klienta obsługuje trzy opcje wdrażania:

- Szablony usługi Azure Resource Manager: partnerzy mogą używać szablonów Usługi Resource Manager do wdrażania usług platformy Azure w celu uruchomienia oprogramowania partnera. Partnerzy mogą utworzyć szablon Menedżera zasobów, aby zdefiniować infrastrukturę i konfigurację ich rozwiązania platformy Azure. Szablon Menedżera zasobów umożliwia tobie i klientom wdrażanie rozwiązania w całym jego cyklu życia. Można mieć pewność, że zasoby są wdrażane w spójnym stanie.
- Interfejsy API usługi Azure Resource Manager: partnerzy mogą bezpośrednio wywoływać interfejsy API Menedżera zasobów w celu wdrożenia szablonu Usługi Resource Manager lub wygenerowania wywołań interfejsu API w celu bezpośredniego aprowizowania usług platformy Azure.
- Terraform: Partnerzy mogą używać terraform do wdrażania szablonu Usługi Resource Manager lub bezpośredniego wdrażania usług platformy Azure.

>[!IMPORTANT]
>- Atrybucja użycia klienta nie jest przeznaczona do śledzenia pracy integratorów systemów, dostawców usług zarządzanych ani narzędzi przeznaczonych do wdrażania oprogramowania działającego na platformie Azure i zarządzania nim.
>
>- Atrybucja użycia klienta jest dla nowych wdrożeń i NIE obsługuje tagowanie istniejących zasobów, które zostały już wdrożone.
>
>- Atrybucja użycia klienta jest wymagana dla ofert [aplikacji platformy Azure](./partner-center-portal/create-new-azure-apps-offer.md) opublikowanych w witrynie Azure Marketplace.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Tworzenie identyfikatorów GUID

Identyfikator GUID jest unikatowym identyfikatorem referencyjnym, który ma 32 cyfry szesnastkowe. Aby utworzyć identyfikatory GUID do śledzenia, należy użyć generatora identyfikatorów GUID. Zespół usługi Azure Storage utworzył [formularz generatora identyfikatorów GUID,](https://aka.ms/StoragePartners) który będzie wysyłał ci identyfikator GUID o poprawnym formacie i można go ponownie wyhodować w różnych systemach śledzenia.

> [!NOTE]
> Zdecydowanie zaleca się, aby utworzyć identyfikator GUID w [formularzu azure storage.](https://aka.ms/StoragePartners) Aby uzyskać więcej informacji, zobacz nasze [FAQ](#faq).

Zalecamy utworzenie unikatowego identyfikatora GUID dla każdej oferty i kanału dystrybucji dla każdego produktu. Możesz wybrać jeden identyfikator GUID dla wielu kanałów dystrybucji produktu, jeśli nie chcesz, aby raportowanie było dzielone.

Jeśli wdrożysz produkt przy użyciu szablonu i jest on dostępny zarówno w portalu Azure Marketplace, jak i w usłudze GitHub, można utworzyć i zarejestrować dwa różne identyfikatory GUIDS:

- Produkt A w portalu Azure Marketplace
- Produkt A w serwisie GitHub

Raportowanie odbywa się za pomocą identyfikatora sieci partnerów firmy Microsoft i identyfikatora GUID.

Można również śledzić użycie na poziomie bardziej szczegółowym, rejestrując dodatkowe identyfikatory GUID i zmieniając identyfikatory GUID między planami, gdzie plany są wariantami oferty.

## <a name="register-guids"></a>Rejestrowanie identyfikatorów GUID

Identyfikatory GUID muszą być zarejestrowane w Centrum partnerów, aby umożliwić atrybucję użycia klienta.

Po dodaniu identyfikatora GUID do szablonu lub agenta użytkownika i zarejestrowaniu identyfikatora GUID w Centrum partnerów przyszłe wdrożenia są śledzone.

1. Zaloguj się do [Centrum partnerów](https://partner.microsoft.com/dashboard).

1. Zarejestruj się jako [wydawca rynku komercyjnego](https://aka.ms/JoinMarketplace).

   * Partnerzy muszą [mieć profil w Centrum partnerów.](https://docs.microsoft.com/azure/marketplace/become-publisher) Zachęcamy do wyświetlenia oferty w portalu Azure Marketplace lub AppSource.
   * Partnerzy mogą rejestrować wiele identyfikatorów GUID.
   * Partnerzy mogą rejestrować identyfikatory GUID dla szablonów i ofert rozwiązań innych niż marketplace.

1. W prawym górnym rogu wybierz ikonę biegu ustawień, a następnie wybierz pozycję **Ustawienia dewelopera**.

1. Na **stronie Ustawienia konta**wybierz pozycję **Dodaj identyfikator GUID śledzenia.**

1. W polu **GUID** wprowadź identyfikator GUID śledzenia. Wprowadź tylko identyfikator GUID bez prefiksu **pid.** W polu **Opis** wprowadź nazwę lub opis oferty.

1. Aby zarejestrować więcej niż jeden identyfikator GUID, wybierz ponownie **pozycję Dodaj identyfikator GUID śledzenia.** Na stronie pojawią się dodatkowe pola.

1. Wybierz pozycję **Zapisz**.

## <a name="use-resource-manager-templates"></a>Używanie szablonów usługi Resource Manager
Wiele rozwiązań partnerskich są wdrażane przy użyciu szablonów usługi Azure Resource Manager. Jeśli masz szablon Menedżera zasobów, który jest dostępny w portalu Azure Marketplace, w usłudze GitHub lub jako przewodnik Szybki start, proces modyfikowania szablonu w celu umożliwienia atrybucji użycia klienta jest prosty.

> [!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia i publikowania szablonów rozwiązań, zobacz
> * [Utwórz i wdrudnie pierwszy szablon Menedżera zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
>* [Oferta aplikacji platformy Azure](./partner-center-portal/create-new-azure-apps-offer.md).
>* Klip wideo: [tworzenie szablonów rozwiązań i aplikacji zarządzanych dla portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Aby dodać unikatowy identyfikator (GUID), należy wprowadzić pojedynczą modyfikację do głównego pliku szablonu:

1. [Utwórz identyfikator GUID](#create-guids) przy użyciu sugerowanej metody i [zarejestruj identyfikator GUID](#register-guids).

1. Otwórz szablon Menedżera zasobów.

1. Dodaj nowy zasób w głównym pliku szablonu. Zasób musi znajdować się tylko w pliku **mainTemplate.json** lub **azuredeploy.json,** a nie w żadnych szablonach zagnieżdżonych lub połączonych.

1. Wprowadź wartość GUID po prefiksie **pid** (np. pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Sprawdź szablon pod kątem błędów.

1. Ponownie opublikuj szablon w odpowiednich repozytoriach.

1. [Sprawdź sukces identyfikatora GUID we wdrożeniu szablonu](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Przykładowy kod szablonu Menedżera zasobów

Aby włączyć zasoby śledzenia szablonu, należy dodać następujący dodatkowy zasób w sekcji zasoby. Upewnij się, że zmodyfikujesz poniższy przykładowy kod za pomocą własnych danych wejściowych po dodaniu go do głównego pliku szablonu.
Zasób musi zostać dodany tylko w pliku **mainTemplate.json** lub **azuredeploy.json,** a nie w żadnych szablonach zagnieżdżonych lub połączonych.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
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

## <a name="use-the-resource-manager-apis"></a>Korzystanie z interfejsów API Menedżera zasobów

W niektórych przypadkach może wolisz nawiązywać połączenia bezpośrednio z interfejsami API REST Menedżera zasobów w celu wdrożenia usług platformy Azure. [Platforma Azure obsługuje wiele zestawów SDK,](https://docs.microsoft.com/azure/?pivot=sdkstools) aby włączyć te wywołania. Można użyć jednego z pakietów SDK lub wywołać interfejsy API REST bezpośrednio do wdrażania zasobów.

Jeśli używasz szablonu Menedżera zasobów, należy oznaczyć rozwiązanie, postępując zgodnie z instrukcjami opisanymi wcześniej. Jeśli nie używasz szablonu Menedżera zasobów i wywołujesz bezpośredni interfejs API, nadal możesz oznaczyć wdrożenie w celu skojarzenia użycia zasobów platformy Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Oznaczanie wdrożenia za pomocą interfejsów API Menedżera zasobów

Aby włączyć atrybucję użycia klienta, podczas projektowania wywołań interfejsu API, należy dołączyć identyfikator GUID w nagłówku agenta użytkownika w żądaniu. Dodaj identyfikator GUID dla każdej oferty lub jednostki SKU. Sformatuj ciąg za pomocą prefiksu **pid-** i dołącz identyfikator GUID wygenerowany przez partnera. Oto przykład formatu GUID do wstawiania do agenta użytkownika:

![Przykładowy format guid](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> Format ciągu jest ważny. Jeśli prefiks **pid-nie** jest uwzględniony, nie można zbadać danych. Różne SDK śledzą inaczej. Aby zaimplementować tę metodę, przejrzyj podejście pomocy technicznej i śledzenia preferowanego sdk platformy Azure.

#### <a name="example-the-python-sdk"></a>Przykład: moduł SDK języka Python

W przypadku języka Python użyj atrybutu **config.** Atrybut można dodać tylko do agenta użytkownika. Oto przykład:

![Dodawanie atrybutu do agenta użytkownika](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Dodaj atrybut dla każdego klienta. Nie ma globalnej konfiguracji statycznej. Możesz oznaczyć fabrykę klienta, aby upewnić się, że każdy klient jest śledzący. Aby uzyskać więcej informacji, zobacz ten [przykład fabryki klienta w usłudze GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Oznaczanie wdrożenia przy użyciu programu Azure PowerShell

Jeśli zasoby są wdrażane za pośrednictwem programu Azure PowerShell, dołącz identyfikator GUID przy użyciu następującej metody:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Oznaczanie wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli używasz interfejsu wiersza polecenia platformy Azure do dołączania identyfikatora GUID, ustaw zmienną środowiskową **AZURE_HTTP_USER_AGENT.** Tę zmienną można ustawić w zakresie skryptu. Można również ustawić zmienną globalnie dla zakresu powłoki:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Aby uzyskać więcej informacji, zobacz [Zestaw SDK platformy Azure dla Go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Użyj Terraform

Obsługa terraform jest dostępna w wersji 1.21.0 [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)dostawcy platformy Azure: .  Ta pomoc techniczna dotyczy wszystkich partnerów, którzy wdrażają swoje rozwiązanie za pośrednictwem programu Terraform, oraz wszystkich zasobów wdrożonych i mierzonych przez dostawcę platformy Azure (wersja 1.21.0 lub nowsza).

Dostawca platformy Azure dla terraform dodał nowe opcjonalne pole o nazwie [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) czyli w którym określono identyfikator GUID śledzenia, którego używasz dla rozwiązania. Wartość tego pola może być również pochodzą z *ARM_PARTNER_ID* zmiennej środowiskowej.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partnerzy, którzy chcą rozpocząć wdrażanie za pośrednictwem terraform śledzone przez atrybucję użycia klienta, muszą wykonać następujące czynności:

* Tworzenie identyfikatora GUID (identyfikator GUID powinien zostać dodany dla każdej oferty lub jednostki SKU)
* Zaktualizuj swojego dostawcę platformy Azure, aby ustawić wartość *partner_id* identyfikatora GUID (NIE naprawij identyfikatora GUID za pomocą "pid-", po prostu ustaw go na rzeczywisty identyfikator GUID)


## <a name="verify-the-guid-deployment"></a>Weryfikowanie wdrożenia identyfikatora GUID

Po zmodyfikowaniu szablonu i uruchomieniu wdrożenia testowego użyj następującego skryptu programu PowerShell, aby pobrać zasoby, które zostały wdrożone i oznaczone.

Za pomocą skryptu można sprawdzić, czy identyfikator GUID został pomyślnie dodany do szablonu Menedżera zasobów. Skrypt nie ma zastosowania do wdrożeń interfejsu API Menedżera zasobów lub Terraform.

Zaloguj się do platformy Azure. Wybierz subskrypcję z wdrożeniem, które chcesz zweryfikować przed uruchomieniem skryptu. Uruchom skrypt w kontekście subskrypcji wdrożenia.

**Identyfikator GUID** i **resourceGroup** nazwa wdrożenia są wymagane parametry.

[Oryginalny skrypt](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) można uzyskać na GitHub.

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

Raport dotyczący atrybucji użycia klienta można znaleźć[https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)na pulpicie nawigacyjnym Centrum partnerów ( ). Aby wyświetlić raport, musisz użyć poświadczeń Centrum partnerów, aby się zalogować. Jeśli wystąpią jakiekolwiek problemy z raportem lub zalogowaniem się, utwórz żądanie pomocy technicznej zgodnie z instrukcją w sekcji Uzyskaj pomoc techniczną.

Wybierz pozycję Śledzony szablon na liście rozwijanej Typ skojarzenia partnerów, aby wyświetlić raport.

![Raport dotyczący atrybucji użycia klienta](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Powiadamiaj swoich klientów

Partnerzy powinni informować swoich klientów o wdrożeniach korzystających z atrybucji użycia klienta. Firma Microsoft zgłasza partnerowi użycie platformy Azure skojarzone z tymi wdrożeniami. Poniższe przykłady obejmują zawartość, której można użyć do powiadamiania klientów o tych wdrożeniach. W przykładach zastąp \<PARTNER> nazwą firmy. Partnerzy powinni upewnić się, że powiadomienie jest zgodne z ich zasadami ochrony prywatności i zbierania danych, w tym opcjami wykluczenia klientów ze śledzenia.

### <a name="notification-for-resource-manager-template-deployments"></a>Powiadomienie o wdrożeniach szablonów Menedżera zasobów

Po wdrożeniu tego szablonu firma \<Microsoft jest w stanie zidentyfikować instalację oprogramowania partner> z wdrożonymi zasobami platformy Azure. Firma Microsoft może skorelować zasoby platformy Azure, które są używane do obsługi oprogramowania. Firma Microsoft zbiera te informacje w celu zapewnienia najlepszego środowiska z ich produktami i prowadzenia działalności. Dane są zbierane i regulowane przez zasady ochrony prywatności https://www.microsoft.com/trustcenterfirmy Microsoft, które można znaleźć na stronie .

### <a name="notification-for-sdk-or-api-deployments"></a>Powiadomienie o wdrożeniach SDK lub API

Podczas wdrażania \<oprogramowania partner> firma Microsoft jest w \<stanie zidentyfikować instalację oprogramowania partnerów> z wdrożonymi zasobami platformy Azure. Firma Microsoft może skorelować zasoby platformy Azure, które są używane do obsługi oprogramowania. Firma Microsoft zbiera te informacje w celu zapewnienia najlepszego środowiska z ich produktami i prowadzenia działalności. Dane są zbierane i regulowane przez zasady ochrony prywatności https://www.microsoft.com/trustcenterfirmy Microsoft, które można znaleźć na stronie .

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Istnieją dwa kanały pomocy technicznej w zależności od problemów, z którymi się borykasz.

Jeśli wystąpią jakiekolwiek problemy w Centrum partnerów, takie jak wyświetlanie raportu atrybucji użycia klienta lub logowanie się, utwórz żądanie pomocy technicznej z zespołem pomocy technicznej Centrum partnerów tutaj:[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Jeśli potrzebujesz pomocy w zakresie dołączania do platformy Marketplace i/lub atrybucji użycia klienta, na przykład konfigurowania atrybucji użycia klienta, wykonaj poniższe czynności:

1. Przejdź do [strony pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=844975).

1. W obszarze **Typ problemu**wybierz pozycję **Dołączanie do marketplace**.

1. Wybierz **kategorię** problemu:

   - W przypadku problemów ze skojarzeniem użycia wybierz pozycję **Inne**.
   - Aby uzyskać problemy z dostępem do portalu Azure Marketplace, wybierz pozycję **Problem z programem Access**.

     ![Wybierz kategorię problemu](media/marketplace-publishers-guide/lu-article-incident.png)

1. Wybierz **pozycję Rozpocznij żądanie**.

1. Na następnej stronie wprowadź wymagane wartości. Wybierz przycisk **Kontynuuj**.

1. Na następnej stronie wprowadź wymagane wartości.

   > [!IMPORTANT]
   > W polu **Tytuł incydentu** wprowadź **wpisanie śledzenia użycia isv**. Opisz szczegółowo swój problem.

   ![Wprowadź śledzenie użycia isv dla tytułu zdarzenia](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Wypełnij formularz, a następnie wybierz pozycję **Prześlij**.

Możesz również otrzymać wskazówki techniczne od konsultanta technicznego partnera firmy Microsoft dotyczące scenariuszy przedsprzedaży technicznej, wdrażania i tworzenia aplikacji, aby zrozumieć i uwzględnić atrybucję użycia klienta.

### <a name="how-to-submit-a-technical-consultation-request"></a>Jak złożyć wniosek o konsultację techniczną

1. Odwiedź [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Wybierz infrastrukturę chmury i zarządzanie, a otworzy się nowa strona, aby wyświetlić podróż techniczną.
1. W obszarze Usługi wdrażania kliknij przycisk Prześlij żądanie
1. Zaloguj się przy użyciu konta MSA (MPN) lub konta AAD (konto pulpitu nawigacyjnego partnera); na podstawie poświadczeń logowania zostanie otwarty formularz wniosku online:
    * Uzupełnij/przejrzyj informacje kontaktowe.
    * Szczegóły konsultacji mogą być wstępnie wypełnione lub wybrać z rozwijanych.
    * Wprowadź tytuł i opis problemu (podaj jak najwięcej szczegółów).
1. Kliknij przycisk Submit (Prześlij).

Wyświetlanie instrukcji krok po kroku ze [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)zrzutami ekranu na .

### <a name="whats-next"></a>Co dalej

Skontaktujemy się z Tobą konsultant techniczny partnera firmy Microsoft, aby skonfigurować połączenie do zakresu twoich potrzeb.

## <a name="faq"></a>Często zadawane pytania

**Jakie są korzyści z dodania identyfikatora GUID do szablonu?**

Firma Microsoft udostępnia partnerom widok wdrożeń ich rozwiązań przez klientów oraz szczegółowe informacje na temat ich wpływu na użycie. Zarówno firma Microsoft, jak i partner mogą używać tych informacji do bliższego zaangażowania między zespołami sprzedaży. Zarówno firma Microsoft, jak i partner mogą korzystać z danych, aby uzyskać bardziej spójny widok wpływu poszczególnych partnerów na rozwój platformy Azure.

**Czy po dodaniu identyfikatora GUID można go zmienić?**

Tak, klient lub partner implementacjuje może dostosować szablon i może zmienić lub usunąć identyfikator GUID. Sugerujemy, aby partnerzy proaktywnie opisywali rolę zasobu i identyfikatora GUID swoim klientom i partnerom, aby zapobiec usunięciu lub zmianom identyfikatora GUID. Zmiana identyfikatora GUID dotyczy tylko nowych, a nie istniejących wdrożeń i zasobów.

**Czy mogę śledzić szablony wdrożone z repozytorium firmy innych niż Microsoft, takiego jak GitHub?**

Tak, tak długo, jak identyfikator GUID jest obecny podczas wdrażania szablonu, użycie jest śledzone. Partnerzy muszą nadal rejestrować swoje identyfikatory GUID.

**Czy klient otrzymuje również raporty?**

Klienci mogą śledzić ich użycie poszczególnych zasobów lub grup zasobów zdefiniowanych przez klienta w witrynie Azure portal. Klienci nie widzą użycia w podziale według identyfikatora GUID.

**Czy ta metodologia jest podobna do cyfrowego partnera rekordu (DPOR)?**

Ta nowa metoda łączenia wdrożenia i użycia z rozwiązaniem partnera zapewnia mechanizm łączenia rozwiązania partnerskiego z użyciem platformy Azure. DPOR jest przeznaczony do skojarzenia partnera konsultingowego (Integrator systemów) lub zarządzania (Managed Service Provider) z subskrypcją platformy Azure klienta.

**Jakie są korzyści z korzystania z formularza generatora identyfikatorów GUID usługi Azure Storage?**

Formularz generatora identyfikatorów GUID usługi Azure Storage jest gwarantowany do generowania identyfikatora GUID wymaganego formatu. Ponadto jeśli używasz dowolnej z metod śledzenia płaszczyzny danych usługi Azure Storage, możesz wykorzystać ten sam identyfikator GUID do śledzenia płaszczyzny sterowania w portalu Marketplace. Dzięki temu można wykorzystać pojedynczy ujednolicony identyfikator GUID dla atrybucji partnera bez konieczności utrzymywania oddzielnych identyfikatorów GUIDS.

**Czy mogę używać prywatnego, niestandardowego dysku twardego VHD dla oferty szablonu rozwiązania w portalu Azure Marketplace?**

Nie, nie można. Obraz maszyny wirtualnej musi pochodzić z [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)portalu Azure Marketplace, zobacz: .

Możesz utworzyć ofertę maszyn wirtualnych w portalu Marketplace przy użyciu niestandardowego dysku wirtualnego i oznaczyć ją jako prywatną, aby nikt jej nie widział. Następnie odwołanie do tej maszyny Wirtualnej w szablonie rozwiązania.

**Nie można zaktualizować *contentVersion* właściwości dla głównego szablonu?**

Prawdopodobnie błąd w niektórych przypadkach, gdy szablon jest wdrażany przy użyciu TemplateLink z innego szablonu, które oczekują starszych contentVersion z jakiegoś powodu. Obejście polega na użyciu właściwości metadanych:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
