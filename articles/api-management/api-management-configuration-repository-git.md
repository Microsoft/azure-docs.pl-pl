---
title: Konfigurowanie usługi API Management przy użyciu narzędzia Git-Azure | Microsoft Docs
description: Dowiedz się, jak zapisać i skonfigurować konfigurację usługi API Management za pomocą narzędzia Git.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: 18cc42c3447de733447c27db52a9a6d664539464
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400363"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Jak konfigurować i zapisywać konfigurację usługi API Management za pomocą narzędzia Git

Każde wystąpienie usługi API Management obsługuje bazę danych konfiguracji zawierającą informacje o konfiguracji i metadanych wystąpienia usługi. Zmiany można wprowadzać w wystąpieniu usługi przez zmianę ustawienia w Azure Portal przy użyciu polecenia cmdlet programu PowerShell lub wywołania interfejsu API REST. Oprócz tych metod można także zarządzać konfiguracją wystąpienia usługi za pomocą narzędzia Git, włączając scenariusze zarządzania usługami, takie jak:

* Przechowywanie wersji konfiguracji — Pobierz i Zapisz różne wersje konfiguracji usługi
* Zmiany konfiguracji zbiorczej — wprowadzanie zmian do wielu części konfiguracji usługi w lokalnym repozytorium i integrowanie zmian z powrotem na serwerze z jedną operacją
* Znajomość narzędzia Git łańcucha narzędzi i przepływu pracy — korzystanie z narzędzi git i przepływów pracy, które już znasz

Na poniższym diagramie przedstawiono omówienie różnych sposobów konfigurowania wystąpienia usługi API Management.

![Konfiguracja usługi git][api-management-git-configure]

Po wprowadzeniu zmian w usłudze za pomocą Azure Portal, poleceń cmdlet programu PowerShell lub interfejsu API REST zarządzasz swoją bazą danych konfiguracji usługi przy użyciu `https://{name}.management.azure-api.net` punktu końcowego, jak pokazano po prawej stronie diagramu. Po lewej stronie diagramu pokazano, jak można zarządzać konfiguracją usługi przy użyciu repozytorium git i git dla usługi znajdującej się na stronie `https://{name}.scm.azure-api.net` .

Poniższe kroki zawierają omówienie zarządzania wystąpieniem usługi API Management przy użyciu narzędzia Git.

1. Dostęp do konfiguracji narzędzia Git w usłudze
2. Zapisywanie bazy danych konfiguracji usługi w repozytorium git
3. Klonowanie repozytorium Git na komputerze lokalnym
4. Pobierz najnowsze repozytorium do komputera lokalnego, a następnie Zatwierdź i wypchnij zmiany z powrotem do repozytorium
5. Wdróż zmiany z repozytorium w bazie danych konfiguracji usługi

W tym artykule opisano sposób włączania i używania narzędzia Git do zarządzania konfiguracją usługi i zawierania odwołań do plików i folderów w repozytorium git.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Dostęp do konfiguracji narzędzia Git w usłudze

Aby wyświetlić i skonfigurować ustawienia konfiguracji git, można kliknąć menu **wdrażanie i infrastruktura** , a następnie przejść do karty **repozytorium** .

![Włączanie usługi GIT][api-management-enable-git]

> [!IMPORTANT]
> Wszystkie wpisy tajne, które nie są zdefiniowane jako wartości nazwane, będą przechowywane w repozytorium i pozostaną w swojej historii do momentu wyłączenia i ponownego włączenia dostępu git. Nazwane wartości zapewniają bezpieczne miejsce do zarządzania stałymi wartościami ciągów, w tym wpisy tajne, we wszystkich konfiguracjach i zasadach interfejsu API, dlatego nie trzeba przechowywać ich bezpośrednio w instrukcjach zasad. Aby uzyskać więcej informacji, zobacz [jak używać nazwanych wartości w zasadach usługi Azure API Management](api-management-howto-properties.md).
>
>

Aby uzyskać informacje na temat włączania lub wyłączania dostępu git przy użyciu interfejsu API REST, zobacz [Włączanie lub wyłączanie dostępu git przy użyciu interfejsu API REST](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Aby zapisać konfigurację usługi w repozytorium git

Pierwszym krokiem przed klonem repozytorium jest zapisanie bieżącego stanu konfiguracji usługi do repozytorium. Kliknij pozycję **Zapisz w repozytorium**.

Wprowadź odpowiednie zmiany na ekranie potwierdzenia i kliknij przycisk **Zapisz** , aby zapisać.

Po kilku chwilach konfiguracja zostanie zapisana i zostanie wyświetlony stan konfiguracji repozytorium, łącznie z datą i godziną ostatniej zmiany konfiguracji oraz ostatnią synchronizacją między konfiguracją usługi i repozytorium.

Po zapisaniu konfiguracji w repozytorium można ją sklonować.

Aby uzyskać informacje na temat wykonywania tej operacji przy użyciu interfejsu API REST, zobacz temat [zatwierdzenie migawki konfiguracji przy użyciu interfejsu API REST](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Aby sklonować repozytorium na komputerze lokalnym

Aby sklonować repozytorium, potrzebny jest adres URL repozytorium, nazwę użytkownika i hasło. Aby uzyskać nazwę użytkownika i inne poświadczenia, kliknij przycisk **dostępu poświadczenia** w górnej części strony.

Aby wygenerować hasło, należy najpierw upewnić się, że **wygaśnięcie** jest ustawione na żądaną datę i godzinę wygaśnięcia, a następnie kliknij przycisk **Generuj**.

> [!IMPORTANT]
> Zanotuj to hasło. Gdy opuścisz Tę stronę, hasło nie zostanie wyświetlone ponownie.
>

W poniższych przykładach użyto narzędzia Git bash z usługi [git dla systemu Windows](https://www.git-scm.com/downloads) , ale można użyć dowolnego narzędzia Git, które znasz.

Otwórz narzędzie Git w żądanym folderze i uruchom następujące polecenie, aby sklonować repozytorium git do komputera lokalnego przy użyciu polecenia dostarczonego przez Azure Portal.

```
git clone https://{name}.scm.azure-api.net/
```

Po wyświetleniu monitu podaj nazwę użytkownika i hasło.

Jeśli wystąpią jakieś błędy, spróbuj zmodyfikować `git clone` polecenie, aby zawierało nazwę użytkownika i hasło, jak pokazano w poniższym przykładzie.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Jeśli wystąpi błąd, spróbuj zakodować adres URL części polecenia. Aby to zrobić, należy otworzyć program Visual Studio i wydać następujące polecenie w **oknie bezpośrednim**. Aby otworzyć **okno bezpośrednie**, Otwórz dowolne rozwiązanie lub projekt w programie Visual Studio (lub Utwórz nową pustą aplikację konsolową) i wybierz **pozycję Windows**, **natychmiastowe** z menu **Debuguj** .

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

Użyj zakodowanego hasła wraz z nazwą użytkownika i lokalizacją repozytorium, aby skonstruować polecenie git.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Po sklonowaniu repozytorium można wyświetlać i korzystać z niego w lokalnym systemie plików. Aby uzyskać więcej informacji, zobacz informacje [o strukturze plików i folderów w lokalnym repozytorium git](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Aby zaktualizować repozytorium lokalne przy użyciu najnowszej konfiguracji wystąpienia usługi

Jeśli wprowadzisz zmiany w wystąpieniu usługi API Management w Azure Portal lub przy użyciu interfejsu API REST, musisz zapisać te zmiany w repozytorium, aby można było zaktualizować lokalne repozytorium przy użyciu najnowszych zmian. W tym celu kliknij pozycję **Zapisz do repozytorium** na karcie **repozytorium** w Azure Portal, a następnie wydaj następujące polecenie w lokalnym repozytorium.

```
git pull
```

Przed uruchomieniem `git pull` upewnij się, że jesteś w folderze lokalnego repozytorium. Jeśli wykonano polecenie po prostu `git clone` , należy zmienić katalog na repozytorium, uruchamiając polecenie podobne do poniższego.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Aby wypchnąć zmiany z repozytorium lokalnego do repozytorium serwera
Aby wypchnąć zmiany z repozytorium lokalnego do repozytorium serwera, należy zatwierdzić zmiany, a następnie wypchnąć je do repozytorium serwera. Aby zatwierdzić zmiany, Otwórz narzędzie poleceń git, przejdź do katalogu lokalnego repozytorium i wydaj następujące polecenia.

```
git add --all
git commit -m "Description of your changes"
```

Aby wypchnąć wszystkie zatwierdzenia do serwera, uruchom następujące polecenie.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Aby wdrożyć wszystkie zmiany konfiguracji usługi w wystąpieniu usługi API Management

Gdy lokalne zmiany zostaną zatwierdzone i wypychane do repozytorium serwera, można je wdrożyć w wystąpieniu usługi API Management.

Aby uzyskać informacje na temat wykonywania tej operacji przy użyciu interfejsu API REST, zobacz [wdrażanie usługi git zmian w bazie danych konfiguracji przy użyciu interfejsu API REST](/rest/api/apimanagement/2019-12-01/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Dokumentacja struktury plików i folderów w lokalnym repozytorium git

Pliki i foldery w lokalnym repozytorium git zawierają informacje o konfiguracji wystąpienia usługi.

| Element | Opis |
| --- | --- |
| główny interfejs API — folder zarządzania |Zawiera konfigurację najwyższego poziomu dla wystąpienia usługi |
| folder interfejsów API |Zawiera konfigurację interfejsów API w wystąpieniu usługi |
| folder grup |Zawiera konfigurację grup w wystąpieniu usługi |
| folder zasad |Zawiera zasady w wystąpieniu usługi |
| folder portalStyles |Zawiera konfigurację dostosowań portalu deweloperów w wystąpieniu usługi |
| folder produkty |Zawiera konfigurację produktów w wystąpieniu usługi |
| folder szablonów |Zawiera konfigurację szablonów wiadomości e-mail w wystąpieniu usługi |

Każdy folder może zawierać jeden lub więcej plików, a w niektórych przypadkach jeden lub więcej folderów, na przykład folder dla każdego interfejsu API, produktu lub grupy. Pliki w poszczególnych folderach są specyficzne dla typu jednostki opisanego przez nazwę folderu.

| Typ pliku | Przeznaczenie |
| --- | --- |
| json |Informacje o konfiguracji odpowiedniej jednostki |
| html |Opisy dotyczące jednostki, często wyświetlane w portalu dla deweloperów |
| xml |Instrukcje zasad |
| css |Arkusze stylów dla dostosowywania portalu deweloperów |

Te pliki można tworzyć, usuwać, edytować i zarządzać nimi w lokalnym systemie plików, a zmiany wdrożone z powrotem do wystąpienia usługi API Management.

> [!NOTE]
> Następujące jednostki nie są zawarte w repozytorium git i nie można ich skonfigurować przy użyciu usługi git.
>
> * [Użytkownicy](/rest/api/apimanagement/2019-12-01/user)
> * [Subskrypcje](/rest/api/apimanagement/2019-12-01/subscription)
> * Nazwane wartości
> * Jednostki portalu dla deweloperów inne niż style
>

### <a name="root-api-management-folder"></a>Główny interfejs API — folder zarządzania
Folder główny `api-management` zawiera `configuration.json` plik zawierający informacje najwyższego poziomu dotyczące wystąpienia usługi w następującym formacie.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

Pierwsze cztery ustawienia ( `RegistrationEnabled` , `UserRegistrationTerms` , `UserRegistrationTermsEnabled` i) są `UserRegistrationTermsConsentRequired` mapowane na następujące ustawienia na karcie **tożsamości** w sekcji **Portal dla deweloperów** .

| Ustawienie tożsamości | Mapuje do |
| --- | --- |
| RegistrationEnabled |Obecność dostawcy tożsamości **nazwy użytkownika i hasła** |
| UserRegistrationTerms |**Warunki użytkowania w** polu tekstowym tworzenia konta użytkownika |
| UserRegistrationTermsEnabled |Pole wyboru **Pokaż warunki użytkowania na stronie rejestracji** |
| UserRegistrationTermsConsentRequired |Pole wyboru **wymaganie zgody** |
| RequireUserSigninEnabled |Pole wyboru **Przekieruj użytkowników anonimowych do strony logowania** |

Kolejne cztery ustawienia ( `DelegationEnabled` , `DelegationUrl` , `DelegatedSubscriptionEnabled` i) są `DelegationValidationKey` mapowane na następujące ustawienia na karcie **delegowanie** w sekcji Portal dla **deweloperów** .

| Ustawienie delegowania | Mapuje do |
| --- | --- |
| DelegationEnabled |Pole wyboru **delegowania logowania & logowania** |
| DelegationUrl |Pole tekstowe **adresu URL punktu końcowego delegowania** |
| DelegatedSubscriptionEnabled |Pole wyboru **delegowania subskrypcji produktu** |
| DelegationValidationKey |Pole tekstowe **delegowania klucza weryfikacji** |

Ustawienie końcowe, `$ref-policy` mapuje na plik globalnych zestawień zasad dla wystąpienia usługi.

### <a name="apis-folder"></a>folder interfejsów API
`apis`Folder zawiera folder dla każdego interfejsu API w wystąpieniu usługi, który zawiera następujące elementy.

* `apis\<api name>\configuration.json` — Konfiguracja interfejsu API i zawiera informacje o adresie URL usługi wewnętrznej bazy danych i operacjach. Są to te same informacje, które zostałyby zwrócone w przypadku wywołania [funkcji Pobierz konkretny interfejs API](/rest/api/apimanagement/2019-12-01/apis/get) `export=true` w formacie in `application/json` .
* `apis\<api name>\api.description.html` — jest to opis interfejsu API i odpowiada `description` właściwości [jednostki interfejsu API](/java/api/com.microsoft.azure.storage.table.entityproperty).
* `apis\<api name>\operations\` -Ten folder zawiera `<operation name>.description.html` pliki, które są mapowane na operacje w interfejsie API. Każdy plik zawiera opis pojedynczej operacji w interfejsie API, która jest mapowana na `description` Właściwość [jednostki operacji](/rest/api/visualstudio/operations/list#operationproperties) w interfejsie API REST.

### <a name="groups-folder"></a>folder grup
`groups`Folder zawiera folder dla każdej grupy zdefiniowanej w wystąpieniu usługi.

* `groups\<group name>\configuration.json` -Konfiguracja grupy. Są to te same informacje, które zostałyby zwrócone w przypadku wywołania operacji [Pobierz określoną grupę](/rest/api/apimanagement/2019-12-01/group/get) .
* `groups\<group name>\description.html` -jest to opis grupy i odpowiada `description` właściwości [jednostki grupy](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>folder zasad
`policies`Folder zawiera instrukcje zasad dla Twojego wystąpienia usługi.

* `policies\global.xml` -zawiera zasady zdefiniowane w globalnym zakresie dla Twojego wystąpienia usługi.
* `policies\apis\<api name>\` — Jeśli masz jakieś zasady zdefiniowane w zakresie interfejsu API, są one zawarte w tym folderze.
* `policies\apis\<api name>\<operation name>\` folder — Jeśli masz jakieś zasady zdefiniowane w zakresie operacji, są one zawarte w tym folderze w `<operation name>.xml` plikach, które mapują do instrukcji zasad dla każdej operacji.
* `policies\products\` — Jeśli masz jakieś zasady zdefiniowane w zakresie produktu, są one zawarte w tym folderze, który zawiera `<product name>.xml` pliki, które są mapowane na oświadczenia zasad dla każdego produktu.

### <a name="portalstyles-folder"></a>folder portalStyles
`portalStyles`Folder zawiera konfiguracje i arkusze stylów dla dostosowań portalu deweloperów dla wystąpienia usługi.

* `portalStyles\configuration.json` -zawiera nazwy arkuszy stylów używanych przez portal dla deweloperów
* `portalStyles\<style name>.css` -Każdy `<style name>.css` plik zawiera style dla portalu dla deweloperów ( `Preview.css` i `Production.css` domyślnie).

### <a name="products-folder"></a>folder produkty
`products`Folder zawiera folder dla każdego produktu zdefiniowanego w wystąpieniu usługi.

* `products\<product name>\configuration.json` — jest to konfiguracja produktu. Są to te same informacje, które zostałyby zwrócone w przypadku wywołania operacji [pobierania określonego produktu](/rest/api/apimanagement/2019-12-01/product/get) .
* `products\<product name>\product.description.html` -Opis produktu i odpowiada `description` właściwości [jednostki produktu](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) w interfejsie API REST.

### <a name="templates"></a>szablonów
`templates`Folder zawiera konfigurację [szablonów wiadomości e-mail](api-management-howto-configure-notifications.md) wystąpienia usługi.

* `<template name>\configuration.json` — konfiguracja szablonu wiadomości e-mail.
* `<template name>\body.html` -treść szablonu wiadomości e-mail.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat innych sposobów zarządzania wystąpieniem usługi, zobacz:

* Zarządzanie wystąpieniem usługi przy użyciu następujących poleceń cmdlet programu PowerShell
  * [Wdrażanie usług — dokumentacja poleceń cmdlet programu PowerShell](/powershell/module/wds)
  * [Zarządzanie usługami — dokumentacja poleceń cmdlet programu PowerShell](/powershell/azure/servicemanagement/overview)
* Zarządzanie wystąpieniem usługi za pomocą interfejsu API REST
  * [Dokumentacja interfejsu API REST API Management](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png
