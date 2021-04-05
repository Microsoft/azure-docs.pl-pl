---
title: Bezpieczny API Management zaplecza przy użyciu uwierzytelniania certyfikatu klienta
titleSuffix: Azure API Management
description: Dowiedz się, jak zarządzać certyfikatami klienta i bezpiecznymi usługami zaplecza przy użyciu uwierzytelniania certyfikatu klienta w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: 2e4a398ab71878134887fb8fba025cd8aa6122ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492854"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Zabezpieczanie usług zaplecza przy użyciu uwierzytelniania certyfikatów klientów na platformie Azure API Management

API Management pozwala zabezpieczyć dostęp do usługi zaplecza interfejsu API przy użyciu certyfikatów klienta. W tym przewodniku przedstawiono sposób zarządzania certyfikatami w wystąpieniu usługi Azure API Management przy użyciu Azure Portal. Wyjaśniono również sposób konfigurowania interfejsu API do korzystania z certyfikatu w celu uzyskania dostępu do usługi zaplecza.

Za pomocą [interfejsu API REST API Management](/rest/api/apimanagement/2020-06-01-preview/certificate)można także zarządzać certyfikatami API Management.

## <a name="certificate-options"></a>Opcje certyfikatu

API Management udostępnia dwie opcje zarządzania certyfikatami używanymi do zabezpieczania dostępu do usług zaplecza:

* Odwoływanie się do certyfikatu zarządzanego w [Azure Key Vault](../key-vault/general/overview.md) 
* Dodaj plik certyfikatu bezpośrednio w API Management

Zaleca się korzystanie z certyfikatów magazynu kluczy, ponieważ ułatwia to poprawę zabezpieczeń API Management:

* Certyfikaty przechowywane w magazynach kluczy mogą być ponownie używane przez usługi
* Szczegółowe [zasady dostępu](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) można stosować do certyfikatów przechowywanych w magazynach kluczy
* Certyfikaty aktualizowane w magazynie kluczy są automatycznie obracane w API Management. Po aktualizacji w magazynie kluczy certyfikat w API Management jest aktualizowany w ciągu 4 godzin. Certyfikat można również odświeżyć ręcznie przy użyciu Azure Portal lub za pośrednictwem interfejsu API REST zarządzania.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Jeśli nie utworzono jeszcze wystąpienia usługi API Management, zobacz [Tworzenie wystąpienia usługi API Management][Create an API Management service instance].
* Usługa zaplecza powinna być skonfigurowana do uwierzytelniania certyfikatu klienta. Aby skonfigurować uwierzytelnianie certyfikatów w Azure App Service, zapoznaj się z [tym artykułem][to configure certificate authentication in Azure WebSites refer to this article]. 
* Wymagany jest dostęp do certyfikatu oraz hasła do zarządzania w magazynie kluczy platformy Azure lub do usługi API Management. Certyfikat musi być w formacie **PFX** . Certyfikaty z podpisem własnym są dozwolone.

### <a name="prerequisites-for-key-vault-integration"></a>Wymagania wstępne dotyczące integracji magazynu kluczy

1. Aby uzyskać instrukcje dotyczące tworzenia magazynu kluczy, zobacz [Szybki Start: Tworzenie magazynu kluczy przy użyciu Azure Portal](../key-vault/general/quick-create-portal.md).
1. Włącz [tożsamość zarządzaną](api-management-howto-use-managed-service-identity.md) przez system lub przypisanej przez użytkownika w wystąpieniu API Management.
1. Przypisz do zarządzanej tożsamości [zasady dostępu do magazynu kluczy](../key-vault/general/assign-access-policy-portal.md) z uprawnieniami do pobierania i wyświetlania certyfikatów z magazynu. Aby dodać zasady:
    1. W portalu przejdź do magazynu kluczy.
    1. Wybierz pozycję **ustawienia > zasady dostępu > + Dodaj zasady dostępu**.
    1. Wybierz pozycję **uprawnienia certyfikatów**, a następnie wybierz pozycję **Pobierz** i **Wyświetl**.
    1. W obszarze **Wybierz podmiot zabezpieczeń** wybierz nazwę zasobu tożsamości zarządzanej. Jeśli używasz tożsamości przypisanej do systemu, podmiot zabezpieczeń jest nazwą wystąpienia API Management.
1. Utwórz lub zaimportuj certyfikat do magazynu kluczy. Zobacz [Szybki Start: Ustawianie i pobieranie certyfikatu z Azure Key Vault przy użyciu Azure Portal](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Dodawanie certyfikatu magazynu kluczy

Zapoznaj się z [wymaganiami wstępnymi dotyczącymi integracji magazynu kluczy](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> W przypadku korzystania z certyfikatu magazynu kluczy w API Management należy zachować ostrożność, aby nie usuwać certyfikatu, magazynu kluczy ani tożsamości zarządzanej używanej do uzyskiwania dostępu do magazynu kluczy.

Aby dodać certyfikat magazynu kluczy do API Management:

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management.
1. W obszarze **zabezpieczenia** wybierz pozycję **Certyfikaty**.
1. Wybierz pozycję **Certyfikaty**  >  **i Dodaj**.
1. W polu **Identyfikator** wprowadź wybraną nazwę.
1. W obszarze **certyfikat** wybierz pozycję **Magazyn kluczy**.
1. Wprowadź identyfikator certyfikatu magazynu kluczy lub wybierz opcję **Wybierz** , aby wybrać certyfikat z magazynu kluczy.
    > [!IMPORTANT]
    > Jeśli ręcznie wprowadzisz identyfikator certyfikatu magazynu kluczy, upewnij się, że nie ma on informacji o wersji. W przeciwnym razie certyfikat nie zostanie automatycznie obrócony w API Management po aktualizacji w magazynie kluczy.
1. W obszarze **tożsamość klienta** wybierz przypisaną przez system lub istniejącą tożsamość zarządzaną przez użytkownika. Dowiedz się [, jak dodać lub zmodyfikować zarządzane tożsamości w usłudze API Management](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Tożsamość wymaga uprawnień do pobierania i wyświetlania certyfikatów z magazynu kluczy. Jeśli dostęp do magazynu kluczy nie został jeszcze skonfigurowany, API Management zostanie wyświetlony komunikat z prośbą o to, aby mógł on automatycznie skonfigurować tożsamość z niezbędnymi uprawnieniami.
1. Wybierz pozycję **Dodaj**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Dodaj certyfikat magazynu kluczy":::

## <a name="upload-a-certificate"></a>Przekaż certyfikat

Aby przekazać certyfikat klienta do API Management: 

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management.
1. W obszarze **zabezpieczenia** wybierz pozycję **Certyfikaty**.
1. Wybierz pozycję **Certyfikaty**  >  **i Dodaj**.
1. W polu **Identyfikator** wprowadź wybraną nazwę.
1. W obszarze **certyfikat** wybierz pozycję **niestandardowy**.
1. Przejdź do opcji wybierz plik Certificate. pfx, a następnie wprowadź hasło.
1. Wybierz pozycję **Dodaj**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Przekaż certyfikat klienta":::

Po przekazaniu certyfikatu zostanie on wyświetlony w oknie **Certyfikaty** . Jeśli masz wiele certyfikatów, zanotuj odcisk palca żądanego certyfikatu, aby skonfigurować interfejs API do korzystania z certyfikatu klienta na potrzeby [uwierzytelniania bramy](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Aby wyłączyć weryfikację łańcucha certyfikatów przy użyciu programu, na przykład certyfikat z podpisem własnym, wykonaj kroki opisane w [certyfikatach](#self-signed-certificates)z podpisem własnym, w dalszej części tego artykułu.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Konfigurowanie interfejsu API do używania certyfikatu klienta na potrzeby uwierzytelniania bramy

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management.
1. W obszarze **interfejsy API** wybierz pozycję **interfejsy API**.
1. Wybierz z listy interfejs API. 
2. Na karcie **projektowanie** wybierz ikonę Edytor w sekcji **zaplecze** .
3. W obszarze **poświadczenia bramy** wybierz pozycję certyfikat **klienta** i wybierz certyfikat z listy rozwijanej.
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Używanie certyfikatu klienta do uwierzytelniania bramy":::

> [!CAUTION]
> Ta zmiana obowiązuje natychmiast, a wywołania operacji tego interfejsu API będą używać certyfikatu do uwierzytelniania na serwerze wewnętrznej bazy danych.

> [!TIP]
> Jeśli certyfikat jest określony na potrzeby uwierzytelniania bramy dla usługi zaplecza interfejsu API, stanie się częścią zasad dla tego interfejsu API i może być wyświetlany w edytorze zasad.

## <a name="self-signed-certificates"></a>Certyfikaty z podpisem własnym

W przypadku korzystania z certyfikatów z podpisem własnym należy wyłączyć weryfikację łańcucha certyfikatów dla API Management, aby komunikować się z systemem zaplecza. W przeciwnym razie zwróci kod błędu 500. Aby skonfigurować tę opcję, można użyć [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) poleceń cmdlet programu PowerShell (dla nowego zaplecza) lub [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (dla istniejących zaplecza) i ustawić `-SkipCertificateChainValidation` parametr na `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Usuwanie certyfikatu klienta

Aby usunąć certyfikat, zaznacz go, a następnie wybierz pozycję **Usuń** z menu kontekstowego (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Usuwanie certyfikatu":::

> [!IMPORTANT]
> Jeśli do certyfikatu odwołują się żadne zasady, zostanie wyświetlony ekran ostrzegawczy. Aby usunąć certyfikat, należy najpierw usunąć ten certyfikat z dowolnych zasad skonfigurowanych do korzystania z niej.

## <a name="next-steps"></a>Następne kroki

* [Jak zabezpieczać interfejsy API przy użyciu uwierzytelniania za pomocą certyfikatów klienta w usłudze API Management](api-management-howto-mutual-certificates-for-clients.md)
* Informacje o [zasadach w API Management](api-management-howto-policies.md)


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

