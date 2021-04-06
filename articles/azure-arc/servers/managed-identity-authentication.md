---
title: Uwierzytelnianie względem zasobów platformy Azure przy użyciu serwerów z włączonym łukiem
description: W tym artykule opisano obsługę Instance Metadata Service platformy Azure dla serwerów z obsługą ARC oraz sposób uwierzytelniania zasobów platformy Azure i lokalnych przy użyciu klucza tajnego.
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96939156"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Uwierzytelnianie względem zasobów platformy Azure przy użyciu serwerów z włączonym łukiem

Aplikacje lub procesy działające bezpośrednio na serwerach z obsługą usługi Azure Arc mogą korzystać z zarządzanych tożsamości w celu uzyskiwania dostępu do innych zasobów platformy Azure, które obsługują uwierzytelnianie oparte na Azure Active Directory. Aplikacja może uzyskać [token dostępu](../../active-directory/develop/developer-glossary.md#access-token) reprezentujący jego tożsamość, która jest przypisana przez system dla serwerów z obsługą łuku, i użyć jej jako tokenu "okaziciela" do samodzielnego uwierzytelnienia w innej usłudze.

Zapoznaj się z dokumentacją [zarządzaną tożsamości](../../active-directory/managed-identities-azure-resources/overview.md) , aby uzyskać szczegółowy opis tożsamości zarządzanych, a także różnice między tożsamościami przypisanymi do systemu i przypisanymi przez użytkownika.

W tym artykule przedstawiono sposób, w jaki serwer może korzystać z tożsamości zarządzanej przypisanej przez system do uzyskiwania dostępu do usługi Azure [Key Vault](../../key-vault/general/overview.md). Obsługa jako Bootstrap, Key Vault umożliwia aplikacji klienckiej używanie klucza tajnego w celu uzyskania dostępu do zasobów niezabezpieczonych przez Azure Active Directory (AD). Na przykład certyfikaty protokołu TLS/SSL używane przez serwery sieci Web usług IIS mogą być przechowywane w Azure Key Vault i bezpiecznie wdrażać certyfikaty na serwerach z systemem Windows lub Linux poza platformą Azure.

## <a name="security-overview"></a>Omówienie zabezpieczeń

Podczas dołączania serwera do serwerów z obsługą usługi Azure Arc można wykonać kilka czynności w celu skonfigurowania przy użyciu tożsamości zarządzanej, podobnie jak w przypadku maszyny wirtualnej platformy Azure:

- Azure Resource Manager odbiera żądanie włączenia zarządzanej tożsamości przypisanej do systemu na serwerze z włączonym łukiem.

- Azure Resource Manager tworzy nazwę główną usługi w usłudze Azure AD na potrzeby tożsamości serwera. Jednostka usługi jest tworzona w dzierżawie usługi Azure AD, która jest zaufana w ramach subskrypcji.

- Azure Resource Manager konfiguruje tożsamość na serwerze, aktualizując punkt końcowy tożsamości usługi Azure Instance Metadata Service (IMDS) dla [systemu Windows](../../virtual-machines/windows/instance-metadata-service.md) lub [Linux](../../virtual-machines/linux/instance-metadata-service.md) przy użyciu identyfikatora klienta jednostki usługi i certyfikatu. Punkt końcowy to punkt końcowy REST dostępny tylko z poziomu serwera przy użyciu dobrze znanego adresu IP bez obsługi routingu. Ta usługa udostępnia podzestaw informacji o metadanych na serwerze z włączonym łukiem, aby ułatwić jego zarządzanie i Konfigurowanie.

Środowisko serwera z włączoną obsługą tożsamości zarządzanej zostanie skonfigurowane przy użyciu następujących zmiennych na serwerze z włączonym systemem Windows ARC:

- **IMDS_ENDPOINT**: adres IP punktu końcowego IMDS `http://localhost:40342` dla serwerów z obsługą łuku.

- **IDENTITY_ENDPOINT**: punkt końcowy localhost odpowiadający tożsamości zarządzanej przez usługę `http://localhost:40342/metadata/identity/oauth2/token` .

Kod uruchomiony na serwerze może zażądać tokenu z punktu końcowego usługi metadanych wystąpienia platformy Azure, dostępne tylko z poziomu serwera.

Zmienna środowiskowa systemowa **IDENTITY_ENDPOINT** jest używana do wykrywania punktu końcowego tożsamości przez aplikacje. Aplikacje powinny próbować pobrać wartości **IDENTITY_ENDPOINT** i **IMDS_ENDPOINT** i używać ich. Aplikacje z dowolnym poziomem dostępu mogą wprowadzać żądania do punktów końcowych. Odpowiedzi na metadane są obsługiwane jako normalne i nadawane dowolnym procesowi na komputerze. Jednak w przypadku zrealizowania żądania, które mógłby ujawnić token, firma Microsoft wymaga, aby zapewnić klucz tajny, aby zaświadczyć, że dostęp do danych jest dostępny tylko dla użytkowników z wyższymi uprawnieniami.

## <a name="prerequisites"></a>Wymagania wstępne

- Zrozumienie zarządzanych tożsamości.
- Serwer podłączony i zarejestrowany z serwerami z obsługą łuku.
- Musisz być członkiem [grupy właścicieli](../../role-based-access-control/built-in-roles.md#owner)* * w subskrypcji lub grupie zasobów, aby wykonać wymagane kroki tworzenia zasobów i zarządzania rolami.
- Azure Key Vault do przechowywania i pobierania poświadczeń. i przypisz tożsamość usługi Azure Arc do magazynu kluczy.

    - Jeśli nie masz utworzonego Key Vault, zobacz [tworzenie Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-).
    - Aby skonfigurować dostęp przy użyciu tożsamości zarządzanej używanej przez serwer, zobacz [udzielanie dostępu dla systemu Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) lub [udzielanie dostępu dla okien](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access). Aby uzyskać numer kroku 5, należy wprowadzić nazwę serwera z włączonym łukiem. Aby ukończyć tę pracę przy użyciu programu PowerShell, zobacz [przypisywanie zasad dostępu przy użyciu programu PowerShell](../../key-vault/general/assign-access-policy-powershell.md).

## <a name="acquiring-an-access-token-using-rest-api"></a>Uzyskiwanie tokenu dostępu przy użyciu interfejsu API REST

Metoda uzyskiwania i używania tożsamości zarządzanej przypisanej przez system do uwierzytelniania za pomocą zasobów platformy Azure jest podobna do przebiegu z maszyną wirtualną platformy Azure.

W przypadku systemu Windows Server z włączonym łukiem przy użyciu programu PowerShell wywoływane jest żądanie sieci Web w celu uzyskania tokenu z hosta lokalnego określonego portu. Określ żądanie przy użyciu adresu IP lub zmiennej środowiskowej **IDENTITY_ENDPOINT**.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

Oto przykład, który jest zwracany:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="Pomyślne pobranie tokenu dostępu przy użyciu programu PowerShell.":::

W przypadku serwera z systemem Linux z włączonym łukiem przy użyciu bash można wywołać żądanie sieci Web, aby uzyskać token z hosta lokalnego określonego portu. Określ następujące żądanie przy użyciu adresu IP lub zmiennej środowiskowej **IDENTITY_ENDPOINT**. Do wykonania tego kroku jest potrzebny klient SSH.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

Oto przykład, który jest zwracany:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Pomyślne pobranie tokenu dostępu przy użyciu bash.":::

Odpowiedź zawiera token dostępu potrzebny do uzyskania dostępu do dowolnego zasobu na platformie Azure. Aby ukończyć konfigurację w celu uwierzytelnienia w Azure Key Vault, zobacz [dostęp Key Vault z systemem Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) lub [dostępem do Key Vault z systemem Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat Azure Key Vault, zobacz [Omówienie usługi Key Vault](../../key-vault/general/overview.md).

- Dowiedz się, jak przypisać zarządzaną tożsamość do zasobu [przy użyciu programu PowerShell](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).