---
title: Zabezpieczanie API Management zaplecza przy użyciu uwierzytelniania certyfikatu klienta
titleSuffix: Azure API Management
description: Dowiedz się, jak zarządzać certyfikatami klienta i zabezpieczać usługi zaplecza przy użyciu uwierzytelniania certyfikatu klienta w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d198f34abe826af2dce0c1ed9950600d4c7bbd3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812304"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Zabezpieczanie usług zaplecza przy użyciu uwierzytelniania certyfikatu klienta w usłudze Azure API Management

API Management umożliwia zabezpieczenie dostępu do usługi zaplecza interfejsu API przy użyciu certyfikatów klienta. W tym przewodniku pokazano, jak zarządzać certyfikatami w wystąpieniu usługi Azure API Management przy użyciu Azure Portal. Wyjaśniono również, jak skonfigurować interfejs API do używania certyfikatu w celu uzyskania dostępu do usługi zaplecza.

Możesz również zarządzać certyfikatami API Management przy użyciu interfejsu [API REST](/rest/api/apimanagement/2020-06-01-preview/certificate)usługi API Management .

## <a name="certificate-options"></a>Opcje certyfikatu

API Management udostępnia dwie opcje zarządzania certyfikatami używanymi do zabezpieczania dostępu do usług zaplecza:

* Odwołanie do certyfikatu zarządzanego w [programie Azure Key Vault](../key-vault/general/overview.md) 
* Dodawanie pliku certyfikatu bezpośrednio w API Management

Używanie certyfikatów magazynu kluczy jest zalecane, ponieważ pomaga zwiększyć API Management zabezpieczeń:

* Certyfikaty przechowywane w magazynach kluczy mogą być ponownie używane w usługach
* Szczegółowe [zasady dostępu można](../key-vault/general/security-features.md#privileged-access) stosować do certyfikatów przechowywanych w magazynach kluczy
* Certyfikaty aktualizowane w magazynie kluczy są automatycznie obracane w API Management. Po aktualizacji w magazynie kluczy certyfikat w API Management zostanie zaktualizowany w ciągu 4 godzin. Certyfikat można również odświeżyć ręcznie przy użyciu interfejsu Azure Portal lub za pośrednictwem interfejsu API REST zarządzania.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Jeśli jeszcze nie utworzono API Management usługi, zobacz [Tworzenie API Management usługi][Create an API Management service instance].
* Usługa zaplecza powinna być skonfigurowana do uwierzytelniania certyfikatu klienta. Aby skonfigurować uwierzytelnianie certyfikatu w Azure App Service, zapoznaj się z [tym artykułem.][to configure certificate authentication in Azure WebSites refer to this article] 
* Potrzebujesz dostępu do certyfikatu i hasła do zarządzania w usłudze Azure Key Vault lub przekazania do API Management usługi. Certyfikat musi być w **formacie PFX.** Certyfikaty z podpisem własnym są dozwolone.

### <a name="prerequisites-for-key-vault-integration"></a>Wymagania wstępne dotyczące integracji z magazynem kluczy

1. Aby uzyskać instrukcje dotyczące tworzenia magazynu kluczy, zobacz [Szybki start: tworzenie magazynu kluczy przy użyciu Azure Portal](../key-vault/general/quick-create-portal.md).
1. Włącz tożsamość zarządzaną przypisaną przez system lub przypisaną przez użytkownika [w](api-management-howto-use-managed-service-identity.md) wystąpieniu API Management zarządzanego.
1. Przypisz [zasady dostępu magazynu kluczy do](../key-vault/general/assign-access-policy-portal.md) tożsamości zarządzanej z uprawnieniami do uzyskiwania certyfikatów z magazynu i ich listy. Aby dodać zasady:
    1. W portalu przejdź do magazynu kluczy.
    1. Wybierz **pozycję Ustawienia > zasad dostępu > + Dodaj zasady dostępu.**
    1. Wybierz **pozycję Uprawnienia certyfikatu,** a następnie **wybierz pozycję Pobierz** i **wyekstoruj.**
    1. Na **stronie Wybierz podmiot** zabezpieczeń wybierz nazwę zasobu tożsamości zarządzanej. Jeśli używasz tożsamości przypisanej przez system, podmiot zabezpieczeń jest nazwą wystąpienia API Management.
1. Utwórz lub zaimportuj certyfikat do magazynu kluczy. Zobacz [Szybki start: ustawianie i pobieranie certyfikatu z usługi Azure Key Vault użyciu Azure Portal](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Dodawanie certyfikatu magazynu kluczy

Zobacz [Wymagania wstępne dotyczące integracji z magazynem kluczy](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> W przypadku używania certyfikatu magazynu kluczy w API Management należy uważać, aby nie usuwać certyfikatu, magazynu kluczy lub tożsamości zarządzanej używanej do uzyskiwania dostępu do magazynu kluczy.

Aby dodać certyfikat magazynu kluczy do API Management:

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego API Management wystąpienia.
1. W **obszarze Zabezpieczenia** wybierz pozycję **Certyfikaty.**
1. Wybierz **pozycję Certyfikaty** i  >  **dodaj**.
1. W **identyfikatorze** wprowadź nazwę.
1. W **chmurze Certyfikat** wybierz **pozycję Magazyn kluczy.**
1. Wprowadź identyfikator certyfikatu magazynu kluczy lub wybierz pozycję **Wybierz,** aby wybrać certyfikat z magazynu kluczy.
    > [!IMPORTANT]
    > Jeśli samodzielnie wprowadzasz identyfikator certyfikatu magazynu kluczy, upewnij się, że nie zawiera on informacji o wersji. W przeciwnym razie certyfikat nie zostanie automatycznie obrócony API Management aktualizacji w magazynie kluczy.
1. W **tożsamości klienta** wybierz tożsamość zarządzaną przypisaną przez system lub istniejącą przypisaną przez użytkownika. Dowiedz się, [jak dodawać lub modyfikować tożsamości zarządzane w usłudze API Management usługi](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Tożsamość musi mieć uprawnienia do uzyskania i uzyskania certyfikatu z magazynu kluczy. Jeśli nie skonfigurowano jeszcze dostępu do magazynu kluczy, API Management monit, aby automatycznie skonfigurować tożsamość z niezbędnymi uprawnieniami.
1. Wybierz pozycję **Dodaj**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Dodawanie certyfikatu magazynu kluczy":::

## <a name="upload-a-certificate"></a>Przekazywanie certyfikatu

Aby przekazać certyfikat klienta do usługi API Management: 

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego API Management wystąpienia.
1. W **obszarze Zabezpieczenia** wybierz pozycję **Certyfikaty.**
1. Wybierz **pozycję Certyfikaty** i  >  **dodaj**.
1. W **identyfikatorze** wprowadź nazwę.
1. W **elektorze** Certyfikat wybierz pozycję **Niestandardowy.**
1. Przejdź do pliku pfx certyfikatu i wprowadź jego hasło.
1. Wybierz pozycję **Dodaj**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Przekazywanie certyfikatu klienta":::

Po przesłaniu certyfikatu jest on przedstawiany w **oknie** Certyfikaty. Jeśli masz wiele certyfikatów, zanotuj odcisk palca żądanego certyfikatu, aby skonfigurować interfejs API do używania certyfikatu klienta na potrzeby [uwierzytelniania bramy.](#configure-an-api-to-use-client-certificate-for-gateway-authentication)

> [!NOTE]
> Aby wyłączyć weryfikację łańcucha certyfikatów w przypadku używania na przykład certyfikatu z podpisem własnym, wykonaj kroki opisane w artykule Certyfikaty z podpisem własnym [w](#self-signed-certificates)dalszej części tego artykułu.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Konfigurowanie interfejsu API do używania certyfikatu klienta do uwierzytelniania bramy

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego API Management wystąpienia.
1. W **obszarze Interfejsy API** wybierz pozycję **Interfejsy API.**
1. Wybierz interfejs API z listy. 
2. Na karcie **Projekt** wybierz ikonę edytora w **sekcji Zaplecza.**
3. Na **stronie Poświadczenia bramy** wybierz pozycję Certyfikat **klienta** i wybierz certyfikat z listy rozwijanej.
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Używanie certyfikatu klienta do uwierzytelniania bramy":::

> [!CAUTION]
> Ta zmiana obowiązuje natychmiast, a wywołania operacji tego interfejsu API będą używać certyfikatu do uwierzytelniania na serwerze zaplecza.

> [!TIP]
> Gdy certyfikat jest określony na celu uwierzytelnianie bramy dla usługi zaplecza interfejsu API, staje się on częścią zasad dla tego interfejsu API i można go wyświetlić w edytorze zasad.

## <a name="self-signed-certificates"></a>Certyfikaty z podpisem własnym

Jeśli używasz certyfikatów z podpisem własnym, musisz wyłączyć weryfikację łańcucha certyfikatów, aby API Management komunikować się z systemem zaplecza. W przeciwnym razie zwróci kod błędu 500. Aby to skonfigurować, można użyć poleceń [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) cmdlet programu PowerShell (dla nowego zaplecza) lub (dla istniejącego zaplecza) i ustawić [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) `-SkipCertificateChainValidation` parametr na wartość `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Usuwanie certyfikatu klienta

Aby usunąć certyfikat, wybierz go, a następnie wybierz pozycję **Usuń** z menu kontekstowego (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Usuwanie certyfikatu":::

> [!IMPORTANT]
> Jeśli do certyfikatu odwołują się jakiekolwiek zasady, zostanie wyświetlony ekran ostrzegawczy. Aby usunąć certyfikat, należy najpierw usunąć certyfikat ze wszystkich zasad skonfigurowanych do korzystania z niego.

## <a name="next-steps"></a>Następne kroki

* [Jak zabezpieczać interfejsy API przy użyciu uwierzytelniania za pomocą certyfikatów klienta w usłudze API Management](api-management-howto-mutual-certificates-for-clients.md)
* Dowiedz się więcej [o zasadach w API Management](api-management-howto-policies.md)


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

