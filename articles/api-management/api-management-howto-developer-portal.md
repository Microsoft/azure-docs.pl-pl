---
title: Omówienie portalu dla deweloperów w usłudze Azure API Management
titleSuffix: Azure API Management
description: Dowiedz się więcej o portalu dla deweloperów w witrynie sieci Web, w której można przeglądać interfejsy API w API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30487218fc95be75d22b5a9ea5a6dbc224ffd025
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074801"
---
# <a name="overview-of-the-developer-portal"></a>Omówienie portalu dla deweloperów

Portal dla deweloperów to automatycznie generowana, w pełni dostosowywalna witryna sieci Web z dokumentacją interfejsów API. W przypadku, gdy konsumenci interfejsu API mogą odnajdywać interfejsy API, dowiedzieć się, jak ich używać, zażądać dostępu i wypróbować.

W tym artykule opisano różnice między samodzielnymi i zarządzanymi wersjami portalu dla deweloperów w API Management. Zawiera również odpowiedzi na często zadawane pytania.

![Portal dla deweloperów API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migracja ze starszego portalu

> [!IMPORTANT]
> Starsza wersja portalu dla deweloperów jest teraz przestarzała i będzie otrzymywać tylko aktualizacje zabezpieczeń. Można nadal z nich korzystać, tak jak na zwykłych okresach, aż do wycofania z października 2023, gdy zostanie on usunięty z wszystkich usług API Management.

Migracja do nowego portalu dla deweloperów została opisana w [artykule dotyczącym dedykowanej dokumentacji](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Dostosowywanie i style

Portal dla deweloperów można dostosować i nadać mu styl za pomocą wbudowanego edytora wizualizacji przeciągnij i upuść. Aby uzyskać więcej informacji, zobacz [ten samouczek](api-management-howto-developer-portal-customize.md) .

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Rozszerzalność

Usługa API Management obejmuje wbudowane, zawsze aktualne, **zarządzane** portale dla deweloperów. Dostęp do niego można uzyskać za pomocą interfejsu Azure Portal.

Jeśli musisz ją rozłożyć z logiką niestandardową, która nie jest obsługiwana, możesz zmodyfikować jej bazę kodu. Baza kodu portalu jest [dostępna w repozytorium GitHub][1]. Można na przykład zaimplementować nowy widżet, który integruje się z systemem pomocy technicznej innej firmy. Podczas implementowania nowej funkcji można wybrać jedną z następujących opcji:

- **Samodzielne hostowanie** tego portalu poza usługą API Management. W przypadku samodzielnego hostowania portalu można go obsłudze i użytkownik jest odpowiedzialny za jego uaktualnienia. Pomoc techniczna platformy Azure jest ograniczona tylko do podstawowej konfiguracji portali z obsługą funkcji samodzielnych, zgodnie z opisem w [sekcji wiki repozytorium][2].
- Otwórz żądanie ściągnięcia dla zespołu API Management, aby scalić nową funkcję z bazą kodu **zarządzanego** portalu.

Aby uzyskać szczegółowe informacje i instrukcje dotyczące rozszerzalności, zapoznaj się z [repozytorium GitHub][1] i [samouczkami dotyczącymi implementacji widżetu][3]. [Samouczek dotyczący dostosowywania portalu zarządzanego](api-management-howto-developer-portal-customize.md) przeprowadzi Cię przez panel administracyjny portalu, który jest typowy dla **zarządzanych** i **samodzielnych** wersji.

## <a name="frequently-asked-questions"></a><a name="faq"></a> Często zadawane pytania

W tej sekcji znajdziesz odpowiedzi na często zadawane pytania dotyczące portalu dla deweloperów, który ma charakter ogólny. Pytania specyficzne dla własnej wersji hostowanej można znaleźć w [sekcji wiki repozytorium GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Jak przeprowadzić migrację z wersji zapoznawczej portalu?

Po pierwszym uruchomieniu wersji zapoznawczej portalu dla deweloperów udostępniono wersję zapoznawczą swojej domyślnej zawartości w usłudze API Management. Zawartość domyślna została znacząco zmodyfikowana w ogólnie dostępnej wersji. Na przykład wersja zapoznawcza domyślnej zawartości nie zawiera przycisków OAuth na stronach logowania, używa różnych widżetów do wyświetlania interfejsów API i opiera się na ograniczonej możliwościach tworzenia struktury stron portalu deweloperów. Mimo że istnieją różnice w zawartości, aparat portalu (w tym bazowe widżety) jest automatycznie aktualizowany przy każdym publikowaniu portalu dla deweloperów.

Jeśli masz mocno dostosowany Portal w oparciu o wersję zapoznawczą zawartości, możesz nadal używać jej jako i ręcznie umieszczać nowe widżety na stronach portalu. W przeciwnym razie zalecamy zastąpienie zawartości portalu nową domyślną zawartością.

Aby zresetować zawartość w portalu zarządzanym, wybierz pozycję **Zresetuj zawartość** w sekcji menu **operacje** . Ta operacja spowoduje usunięcie całej zawartości portalu i udostępnienie nowej zawartości domyślnej. Wszystkie dostosowania i zmiany w portalu deweloperów zostaną utracone. **Tej akcji nie można cofnąć** .

![Resetuj zawartość portalu](media/api-management-howto-developer-portal/reset-content.png)

Jeśli używasz używanej przez siebie wersji, uruchom program `scripts.v2/cleanup.bat` i `scripts.v2/generate.bat` skrypty z repozytorium GitHub, aby usunąć istniejącą zawartość i udostępnić nową zawartość. Upewnij się, że uaktualniono kod portalu do najnowszej wersji z repozytorium GitHub wcześniej.

Jeśli po raz pierwszy uzyskujesz dostęp do portalu po ogólnym ogłoszeniu dostępności w listopadzie 2019, powinien on już obsłużyć nową zawartość domyślną i nie jest wymagana żadna dodatkowa akcja.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Wymagana funkcjonalność nie jest obsługiwana w portalu

Możesz otworzyć żądanie funkcji w [repozytorium GitHub][1] lub [samodzielnie zaimplementować brakujące funkcje][3]. Aby uzyskać więcej informacji, zobacz sekcję **rozszerzalność** powyżej.

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a> Jak można zautomatyzować wdrożenia portalu?

Możesz programowo uzyskać dostęp do zawartości portalu dla deweloperów i zarządzać nią za pośrednictwem interfejsu API REST, niezależnie od tego, czy używasz zarządzanej, czy własnej wersji.

Interfejs API jest udokumentowany w [sekcji wiki repozytorium GitHub][2]. Może służyć do automatyzowania migracji zawartości portalu między środowiskami — na przykład ze środowiska testowego do środowiska produkcyjnego. Więcej informacji o tym procesie można znaleźć [w tym artykule z dokumentacji](https://aka.ms/apimdocs/migrateportal) w witrynie GitHub.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Jak mogę przeniesieniu z zarządzanej do samodzielnej wersji?

Zapoznaj się ze szczegółowym artykułem w [sekcji wiki repozytorium portalu dla deweloperów w witrynie GitHub][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Czy w jednej usłudze API Management można mieć wiele portali dla deweloperów?

Możliwe jest posiadanie jednego zarządzanego portalu i wielu portali samoobsługowych. Zawartość wszystkich portali jest przechowywana w tej samej usłudze API Management, więc będzie taka sama. Jeśli chcesz rozróżnić wygląd i funkcjonalność portali, możesz samodzielnie hostować je przy użyciu własnych niestandardowych widżetów, które dynamicznie dostosowują strony w środowisku uruchomieniowym, na przykład na podstawie adresu URL.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Czy Portal obsługuje Azure Resource Manager szablonów i/lub jest zgodny z zestawem SDK API Management DevOps?

Nie.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Czy zawartość portalu została zapisana przy użyciu funkcji tworzenia kopii zapasowej/przywracania w API Management?

Nie.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Czy muszę włączyć dodatkową łączność sieci wirtualnej dla zależności zarządzanego portalu?

W większości przypadków — nie.

Jeśli Twoja usługa API Management znajduje się w wewnętrznej sieci wirtualnej, Portal dla deweloperów jest dostępny tylko z poziomu usługi. Nazwa hosta punktu końcowego zarządzania musi zostać rozpoznana jako wewnętrzny adres VIP usługi z komputera, który służy do uzyskiwania dostępu do interfejsu administracyjnego portalu. Upewnij się, że punkt końcowy zarządzania jest zarejestrowany w systemie DNS. W przypadku błędnej konfiguracji zostanie wyświetlony komunikat o błędzie: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Jeśli usługa API Management znajduje się w wewnętrznej sieci wirtualnej i uzyskuje do niej dostęp za pośrednictwem Application Gateway z Internetu, upewnij się, że jest włączona łączność z portalem dla deweloperów i punktami końcowymi zarządzania API Management. Może być konieczne wyłączenie reguł zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [ten artykuł dokumentacji](api-management-howto-integrate-internal-vnet-appgateway.md) .

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Mam przypisaną niestandardową domenę API Management i opublikowany Portal nie działa

Po zaktualizowaniu domeny należy [ponownie opublikować Portal](api-management-howto-developer-portal-customize.md#publish) , aby zmiany zaczęły obowiązywać.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Dodaliśmy dostawcę tożsamości i nie mogę go zobaczyć w portalu

Po skonfigurowaniu dostawcy tożsamości (na przykład usługi Azure AD, Azure AD B2C) należy [ponownie opublikować Portal](api-management-howto-developer-portal-customize.md#publish) , aby zmiany zaczęły obowiązywać. Upewnij się, że strony portalu dla deweloperów zawierają widżet przycisków uwierzytelniania OAuth.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Mam konfigurację delegowania, a Portal nie używa jej

Po skonfigurowaniu delegowania należy [ponownie opublikować Portal](api-management-howto-developer-portal-customize.md#publish) , aby zmiany zaczęły obowiązywać.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>MOJE inne API Management zmiany konfiguracji nie zostały rozpropagowane w portalu dla deweloperów

Większość zmian konfiguracji (na przykład Sieć wirtualna, logowanie, warunki produktu) wymaga [ponownego opublikowania portalu](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Otrzymuję błąd CORS podczas korzystania z konsoli interaktywnej

Konsola interaktywna wykonuje żądanie interfejsu API po stronie klienta z przeglądarki. Rozwiąż problem CORS przez dodanie [zasad CORS](api-management-cross-domain-policies.md#CORS) do interfejsów API.

Stan zasad CORS można sprawdzić w sekcji **Omówienie portalu** usługi API Management w Azure Portal. Okno ostrzeżenia wskazuje nieobecne lub nieprawidłowo skonfigurowane zasady.

![Zrzut ekranu pokazujący, gdzie można sprawdzić stan zasad CORS.](media/api-management-howto-developer-portal/cors-azure-portal.png)

Automatyczne stosowanie zasad CORS przez kliknięcie przycisku **Włącz CORS** .

Można również włączyć funkcję CORS ręcznie.

1. Zaznacz pole wyboru **ręcznie Zastosuj na poziomie globalnym** , aby wyświetlić wygenerowany kod zasad.
2. Przejdź do **wszystkich interfejsów API** w sekcji **interfejsy API** usługi API Management w Azure Portal.
3. Wybierz **</>** ikonę w sekcji **Przetwarzanie przychodzące** .
4. Wstaw zasady w **<inbound>** sekcji pliku XML. Upewnij się, że **<origin>** wartość jest zgodna z domeną portalu deweloperów.

> [!NOTE]
> 
> W przypadku zastosowania zasad CORS w zakresie produktu, a nie zakresu interfejsów API, a interfejs API używa uwierzytelniania klucza subskrypcji za pośrednictwem nagłówka, konsola nie będzie działała.
>
> Przeglądarka automatycznie wystawia żądanie HTTP dotyczące opcji, które nie zawiera nagłówka z kluczem subskrypcji. Ze względu na brak klucza subskrypcji API Management nie można skojarzyć opcji z produktem, dlatego nie można zastosować zasad CORS.
>
> Jako obejście można przekazać klucz subskrypcji w parametrze zapytania.

> [!NOTE]
> 
> Tylko jedna zasada CORS jest wykonywana. Jeśli określono wiele zasad CORS (na przykład na poziomie interfejsu API i na poziomie wszystkich interfejsów API), interaktywna konsola może nie zadziałała zgodnie z oczekiwaniami.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Jakie uprawnienia muszę edytować w portalu dla deweloperów?

Jeśli `Oops. Something went wrong. Please try again later.` wystąpi błąd podczas otwierania portalu w trybie administracyjnym, może brakować wymaganych uprawnień (kontrola dostępu Azure).

Starsze portale wymagały uprawnienia do `Microsoft.ApiManagement/service/getssotoken/action` zakresu usługi ( `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ), aby umożliwić administratorowi użytkownika dostęp do portali. Nowy portal wymaga uprawnień `Microsoft.ApiManagement/service/users/token/action` w zakresie `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

Za pomocą poniższego skryptu programu PowerShell można utworzyć rolę z wymaganym uprawnieniem. Pamiętaj, aby zmienić `<subscription-id>` parametr. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Po utworzeniu roli można udzielić jej użytkownikowi z poziomu sekcji **Access Control (IAM)** w Azure Portal. Przypisanie tej roli do użytkownika spowoduje przypisanie uprawnienia do zakresu usługi. Użytkownik będzie mógł generować tokeny SAS w imieniu *dowolnego* użytkownika w usłudze. Na minimalnym poziomie ta rola musi być przypisana do administratora usługi. Następujące polecenie programu PowerShell demonstruje sposób przypisywania roli do użytkownika `user1` w najniższym zakresie, aby uniknąć udzielania niepotrzebnych uprawnień użytkownikowi: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Po udzieleniu uprawnień użytkownikowi użytkownik musi się wylogować i zalogować się ponownie do Azure Portal, aby nowe uprawnienia zaczęły obowiązywać.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Widzę `Unable to start the portal. See if settings are specified correctly (...)` błąd

Ten błąd jest wyświetlany, gdy `GET` wywołanie `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` zakończy się niepowodzeniem. Wywołanie jest wydawane z przeglądarki przez interfejs administracyjny portalu.

Jeśli usługa API Management znajduje się w sieci wirtualnej, zapoznaj się z powyższym pytaniem dotyczącym łączności sieci wirtualnej.

Błąd wywołania może być również spowodowany przez certyfikat TLS/SSL, który jest przypisany do domeny niestandardowej i nie jest zaufany przez przeglądarkę. Jako środek zaradczy, można usunąć niestandardową domenę punktu końcowego zarządzania — API Management powróci do domyślnego punktu końcowego za pomocą zaufanego certyfikatu.

### <a name="whats-the-browser-support-for-the-portal"></a>Co to jest obsługa przeglądarki dla portalu?

| Przeglądarka                     | Obsługiwane       |
|-----------------------------|-----------------|
| Apple Safari                | Tak<sup>1</sup> |
| Google Chrome               | Tak<sup>1</sup> |
| Microsoft Edge              | Tak<sup>1</sup> |
| Microsoft Internet Explorer | Nie              |
| Mozilla Firefox             | Tak<sup>1</sup> |

 <small><sup>1</sup> obsługiwane w dwóch najnowszych wersjach produkcyjnych.</small>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o nowym portalu dla deweloperów:

- [Dostęp do zarządzanego portalu deweloperów i dostosowywanie go](api-management-howto-developer-portal-customize.md)
- [Skonfiguruj samodzielną wersję portalu][2]
- [Implementowanie własnego widżetu][3]

Przeglądaj inne zasoby:

- [Repozytorium GitHub z kodem źródłowym][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
