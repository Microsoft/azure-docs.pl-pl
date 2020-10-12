---
title: Zabezpieczanie usług zaplecza przy użyciu uwierzytelniania certyfikatów klientów
titleSuffix: Azure API Management
description: Dowiedz się, jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatu klienta w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 8e02a47cd6ae6e4883b5113b07d4049cd723232d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86250196"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Jak zabezpieczać usługi zaplecza przy użyciu uwierzytelniania za pomocą certyfikatów klienta w usłudze Azure API Management

API Management pozwala zabezpieczyć dostęp do usługi zaplecza interfejsu API przy użyciu certyfikatów klienta. W tym przewodniku pokazano, jak zarządzać certyfikatami w wystąpieniu usługi Azure API Management w Azure Portal. Wyjaśniono również sposób konfigurowania interfejsu API do korzystania z certyfikatu w celu uzyskania dostępu do usługi zaplecza.

Informacje o zarządzaniu certyfikatami za pomocą interfejsu API REST API Management można znaleźć w temacie <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Jednostka certyfikatu interfejsu API REST platformy Azure API Management</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym przewodniku pokazano, jak skonfigurować wystąpienie usługi API Management do korzystania z uwierzytelniania przy użyciu certyfikatu klienta w celu uzyskania dostępu do usługi zaplecza dla interfejsu API. Przed wykonaniem kroków opisanych w tym artykule należy mieć skonfigurowaną usługę zaplecza do uwierzytelniania certyfikatu klienta ([Aby skonfigurować uwierzytelnianie certyfikatu w Azure App Service zapoznaj się z tym artykułem][to configure certificate authentication in Azure WebSites refer to this article]). Wymagany jest dostęp do certyfikatu oraz hasła do przekazywania go do usługi API Management.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Przekaż certyfikat

> [!NOTE]
> Zamiast przekazanego certyfikatu można użyć certyfikatu przechowywanego w usłudze [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , jak pokazano w tym [przykładzie](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml).

![Dodawanie certyfikatów klienta](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Postępuj zgodnie z poniższymi instrukcjami, aby przekazać nowy certyfikat klienta. Jeśli jeszcze nie utworzono wystąpienia usługi API Management, zobacz samouczek [Tworzenie wystąpienia usługi API Management][Create an API Management service instance].

1. Przejdź do wystąpienia usługi Azure API Management w Azure Portal.
2. Z menu wybierz pozycję **Certyfikaty** .
3. Kliknij przycisk **+ Dodaj**.
    ![Dodawanie certyfikatów klienta](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Przeglądaj w poszukiwaniu certyfikatu, podaj jego identyfikator i hasło.
5. Kliknij przycisk **Utwórz**.

> [!NOTE]
> Certyfikat musi być w formacie **PFX** . Certyfikaty z podpisem własnym są dozwolone.

Po przekazaniu certyfikatu zostanie on wyświetlony w obszarze **Certyfikaty**.  Jeśli masz wiele certyfikatów, zanotuj odcisk palca żądanego certyfikatu, aby [skonfigurować interfejs API do korzystania z certyfikatu klienta na potrzeby uwierzytelniania bramy][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Aby wyłączyć weryfikację łańcucha certyfikatów przy użyciu programu, na przykład certyfikat z podpisem własnym, wykonaj kroki opisane w tym [elemencie](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)często zadawane pytania.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Usuwanie certyfikatu klienta

Aby usunąć certyfikat, **kliknij menu kontekstowe, a** następnie wybierz pozycję **Usuń** obok certyfikatu.

![Usuwanie certyfikatów klienta](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Jeśli certyfikat jest używany przez interfejs API, zostanie wyświetlony ekran ostrzegawczy. Aby usunąć certyfikat, należy najpierw usunąć ten certyfikat z dowolnych interfejsów API, które są skonfigurowane do korzystania z niej.

![Niepowodzenie usuwania certyfikatów klienta](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Konfigurowanie interfejsu API do korzystania z certyfikatu klienta na potrzeby uwierzytelniania bramy

1. Kliknij pozycję **interfejsy API** w menu **API Management** po lewej stronie i przejdź do interfejsu API.
    ![Włączanie certyfikatów klienta](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Na karcie **projektowanie** kliknij ikonę ołówka sekcji **zaplecza** .
3. Zmień **poświadczenia bramy** na **certyfikat klienta** i wybierz certyfikat z listy rozwijanej.
    ![Włączanie certyfikatów klienta](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Kliknij przycisk **Zapisz**.

> [!WARNING]
> Ta zmiana obowiązuje natychmiast, a wywołania operacji tego interfejsu API będą używać certyfikatu do uwierzytelniania na serwerze zaplecza.


> [!TIP]
> Jeśli certyfikat jest określony dla uwierzytelniania bramy usługi zaplecza interfejsu API, stanie się częścią zasad dla tego interfejsu API i może być wyświetlany w edytorze zasad.

## <a name="self-signed-certificates"></a>Certyfikaty z podpisem własnym

W przypadku korzystania z certyfikatów z podpisem własnym należy wyłączyć weryfikację łańcucha certyfikatów, aby program API Management mógł komunikować się z systemem zaplecza. W przeciwnym razie zwróci kod błędu 500. Aby skonfigurować tę opcję, można użyć [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (dla nowego zaplecza) lub [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (dla istniejących zaplecza) poleceń cmdlet programu PowerShell i ustawić `-SkipCertificateChainValidation` parametr na `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

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

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
