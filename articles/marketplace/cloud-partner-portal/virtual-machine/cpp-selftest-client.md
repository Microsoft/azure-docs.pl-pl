---
title: Klient samotestowy do wstępnego weryfikowania maszyny wirtualnej | Portal Azure Marketplace
description: Jak utworzyć klienta samotestowego służący do wstępnego sprawdzania poprawności obrazu maszyny wirtualnej w witrynie Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: 9f16d26fa95254282e453cd7bf35d85f8b81ed73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143210"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Tworzenie klienta samotestowego w celu wstępnego zweryfikowania obrazu maszyny wirtualnej platformy Azure

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [certyfikat obrazu maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) , aby zarządzać migrowanymi ofertami.

Ten artykuł zawiera Przewodnik dotyczący tworzenia usługi klienta, która korzysta z interfejsu API samoobsługowego testowania. Aby sprawdzić, czy maszyna wirtualna (VM) spełnia najnowsze wymagania dotyczące publikowania w portalu Marketplace, można użyć interfejsu API samoobsługowego testowania. Ta usługa klienta umożliwia testowanie maszyny wirtualnej przed przesłaniem oferty na potrzeby certyfikacji firmy Microsoft.

## <a name="development-and-testing-overview"></a>Przegląd opracowywania i testowania

W ramach procesu samodzielnego testowania utworzysz klienta lokalnego, który łączy się z portalem Azure Marketplace, aby sprawdzić, czy maszyna wirtualna jest uruchomiona w ramach subskrypcji platformy Azure. Na maszynie wirtualnej może działać system operacyjny Windows lub Linux.

Klient lokalny uruchamia skrypt uwierzytelniający się za pomocą interfejsu API samoobsługowego testowania, wysyła informacje o połączeniu i odbiera wyniki testów.

Ogólne kroki tworzenia klienta samodzielnego są następujące:

1. Wybierz dzierżawę usługi Azure Active Directory (AD) dla swojej aplikacji.
2. Zarejestruj aplikację kliencką.
3. Utwórz token dla aplikacji klienta usługi Azure AD.
4. Przekaż token do interfejsu API samoobsługowego testowania.

Po utworzeniu klienta można przetestować go na maszynie wirtualnej.

### <a name="self-test-client-authorization"></a>Samoobsługowa autoryzacja klienta

Na poniższym diagramie pokazano, jak autoryzacja działa dla wywołań usługi do usługi przy użyciu poświadczeń klienta (wspólny klucz tajny lub certyfikat).

![Proces autoryzacji klienta](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>Interfejs API klienta samoobsługowego testowania

Interfejs API samoobsługowego testowania zawiera pojedynczy punkt końcowy obsługujący tylko metodę POST.  Ma ona następującą strukturę.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

W poniższej tabeli opisano pola interfejsu API.


|      Pole         |    Opis    |
|  ---------------   |  ---------------  |
|  Autoryzacja     |  Ciąg "Bearer xxxx-xxxx-xxxx-XXXXX" zawiera token klienta Azure Active Directory (AD), który można utworzyć za pomocą programu PowerShell.          |
|  DNSName           |  Nazwa DNS maszyny wirtualnej do przetestowania    |
|  Użytkownik              |  Nazwa użytkownika służąca do logowania się do maszyny wirtualnej         |
|  Hasło          |  Hasło do logowania się do maszyny wirtualnej          |
|  System operacyjny                |  System operacyjny maszyny wirtualnej: `Linux` lub`Windows`          |
|  PortNo            |  Otwórz numer portu, aby nawiązać połączenie z maszyną wirtualną. Numer portu jest zazwyczaj `22` przeznaczony dla systemów Linux `5986` i Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Korzystanie z interfejsu API

Interfejs API samoobsługowego testowania można wykorzystać przy użyciu programu PowerShell lub zwinięcie.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Korzystanie z interfejsu API w systemie operacyjnym Linux przy użyciu programu PowerShell

Aby wywołać interfejs API w programie PowerShell, wykonaj następujące kroki:

1. Użyj `Invoke-WebRequest` polecenia, aby wywołać interfejs API.
2. Metoda ma wartość post i typ zawartości to JSON, jak pokazano w poniższym przykładzie kodu i przechwytywaniu ekranu.
3. Określ parametry treści w formacie JSON.

Poniższy przykład kodu pokazuje wywołanie programu PowerShell do interfejsu API.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```
Poniższy zrzut ekranu przedstawia przykład wywołania interfejsu API w programie PowerShell.

![Wywoływanie interfejsu API za pomocą programu PowerShell dla systemu operacyjnego Linux](./media/stclient-call-api-ps-linuxvm.png)

Korzystając z poprzedniego przykładu, można pobrać kod JSON i przeanalizować go w celu uzyskania następujących szczegółów:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

Poniższy zrzut ekranu, który pokazuje `$res.Content`, zawiera szczegółowe informacje o wynikach testu w formacie JSON.

![Wyniki JSON z wywołania programu PowerShell do systemu Linux](./media/stclient-pslinux-rescontent-json.png)

Poniższy zrzut ekranu przedstawia przykład wyników testów JSON wyświetlanych w podglądzie JSON online (na przykład [Code Beautify](https://codebeautify.org/jsonviewer) lub [Podgląd JSON](https://jsonformatter.org/json-viewer)).

![Wyniki JSON z wywołania programu PowerShell do maszyny wirtualnej z systemem Linux](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Korzystanie z interfejsu API w systemie operacyjnym Windows przy użyciu programu PowerShell

Aby wywołać interfejs API w programie PowerShell, wykonaj następujące kroki:

1. Użyj `Invoke-WebRequest` polecenia, aby wywołać interfejs API.
2. Metoda ma wartość post i typ zawartości to JSON, jak pokazano w poniższym przykładzie kodu i przechwytywaniu ekranu.
3. Utwórz parametry treści w formacie JSON.

Poniższy przykład kodu pokazuje wywołanie programu PowerShell do interfejsu API.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```

Poniższy zrzut ekranu przedstawia przykład wywołania interfejsu API w programie PowerShell.

![Wywoływanie interfejsu API za pomocą programu PowerShell dla maszyny wirtualnej z systemem Windows](./media/stclient-call-api-ps-windowsvm.png)

Korzystając z poprzedniego przykładu, można pobrać kod JSON i przeanalizować go w celu uzyskania następujących szczegółów:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

Poniższy zrzut ekranu, który pokazuje `$res.Content`, zawiera szczegółowe informacje o wynikach testu w formacie JSON.

![Wyniki JSON z wywołania programu PowerShell do systemu Windows](./media/stclient-pswindows-rescontent-json.png)

Poniższy zrzut ekranu przedstawia wyniki testów wyświetlane w podglądzie JSON w trybie online.
(na przykład [Code Beautify](https://codebeautify.org/jsonviewer), [Podgląd JSON](https://jsonformatter.org/json-viewer))

![Wyniki JSON z wywołania programu PowerShell do maszyny wirtualnej z systemem Windows](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Korzystanie z programu zwinięcie do korzystania z interfejsu API w systemie operacyjnym Linux

Aby wywołać interfejs API z zwinięciem, wykonaj następujące kroki:

1. Użyj polecenia zwinięcie, aby wywołać interfejs API.
2. Metoda ma wartość post i typ zawartości to JSON, jak pokazano w poniższym fragmencie kodu.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

Na poniższym ekranie przedstawiono przykład użycia zwinięcie do wywołania interfejsu API.

![Wywołanie interfejsu API za pomocą polecenia zwinięcie](./media/stclient-consume-api-curl.png)

Poniższy przechwytywanie ekranu pokazuje wyniki JSON z wywołania zazwinięcie.

![Wyniki JSON z wywołania pliku](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Wybierz dzierżawę usługi Azure AD dla aplikacji

Wykonaj poniższe kroki, aby wybrać dzierżawę usługi Azure AD, w której chcesz utworzyć aplikację.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Na górnym pasku menu wybierz swoje konto i na liście katalog wybierz dzierżawę Active Directory, w której chcesz zarejestrować aplikację. Lub wybierz ikonę **katalogu i subskrypcji** , aby zobaczyć globalny filtr subskrypcji. Na poniższym zrzucie ekranu przedstawiono przykład tego filtru.

   ![Wybierz filtr subskrypcji](./media/stclient-subscription-filter.png)

3. Na pasku nawigacyjnym po lewej stronie wybierz pozycję **wszystkie usługi** , a następnie wybierz pozycję **Azure Active Directory**.

   W poniższych krokach może być wymagana nazwa dzierżawy (lub nazwa katalogu) lub identyfikator dzierżawy (lub identyfikator katalogu).

   **Aby uzyskać informacje o dzierżawie:**

   W obszarze **przegląd Azure Active Directory**Wyszukaj ciąg "Properties" (właściwości), a następnie wybierz pozycję **Properties**(właściwości). Poniższy przykład przechwytywania ekranu:

   - **Nazwa** — nazwa dzierżawy lub nazwa katalogu
   - **Identyfikator katalogu** — identyfikator dzierżawy lub identyfikator katalogu albo użyj paska przewijania, aby znaleźć właściwości.

   ![Strona właściwości Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Rejestrowanie aplikacji klienckiej

Wykonaj poniższe kroki, aby zarejestrować aplikację kliencką.

1. Na pasku nawigacyjnym po lewej stronie wybierz pozycję **wszystkie usługi** , a następnie wybierz pozycję **rejestracje aplikacji**.
2. W obszarze **rejestracje aplikacji**wybierz pozycję **+ Nowa rejestracja aplikacji**.
3. W obszarze **Utwórz**podaj informacje wymagane dla następujących pól:

   - **Nazwa** — wprowadź przyjazną nazwę aplikacji. Na przykład "SelfTestClient".
   - **Typ aplikacji** — wybierz pozycję **aplikacja sieci Web/interfejs API**
   - **Adres URL logowania** — typ "https:\//isvapp.azurewebsites.NET/SelfTest-VM"

4. Wybierz przycisk **Utwórz**.
5. W obszarze **rejestracje aplikacji** lub **zarejestrowanej aplikacji**Skopiuj **Identyfikator aplikacji**.

   ![Pobieranie identyfikatora aplikacji](./media/stclient-app-id.png)

6. Na pasku narzędzi rejestracja aplikacji wybierz pozycję **Ustawienia**.
7. Wybierz pozycję **wymagane uprawnienia** , aby skonfigurować uprawnienia dla aplikacji.
8. W obszarze **wymagane uprawnienia**wybierz pozycję **+ Dodaj**.
9. W obszarze **Dodaj dostęp do interfejsu API** **Wybierz opcję wybieraj interfejs API**.
10. W obszarze **Wybierz interfejs API**wpisz "klasyczny model wdrażania systemu Windows Azure", aby wyszukać interfejs API.
11. W wynikach wyszukiwania wybierz **klasyczny model wdrażania systemu Windows Azure** , a następnie kliknij przycisk **Wybierz**.

    ![Konfigurowanie wielu dzierżawców dla aplikacji](./media/stclient-select-api.png)

12. W obszarze **Dodaj dostęp do interfejsu API** **Wybierz pozycję uprawnienia SELECT**.
13. Wybierz pozycję **dostęp "Windows Azure interfejs API zarządzania usługami"**.

    ![Włącz dostęp do interfejsu API dla aplikacji](./media/stclient-enable-api-access.png)

14. Kliknij pozycję **Wybierz**.
15. Wybierz pozycję **Gotowe**.
16. W obszarze **Ustawienia** wybierz pozycję **Właściwości**.
17. W obszarze **Właściwości**przewiń w dół do pozycji **wiele dzierżawców**. Wybierz pozycję **tak**.

    ![Konfigurowanie wielu dzierżawców dla aplikacji](./media/stclient-yes-multitenant.png)

18. Wybierz pozycję **Zapisz**.
19. W obszarze **Ustawienia**wybierz pozycję **klucze**.
20. Utwórz klucz tajny, zaznaczając pole tekstowe **Opis** klucza. Skonfiguruj następujące pola:

    - Wpisz nazwę klucza. Na przykład "selftestclient"
    - Na liście rozwijanej **wygaśnięcie** wybierz pozycję "w 1 roku".
    - Wybierz pozycję **Zapisz** , aby wygenerować klucz.
    - W obszarze **wartość**skopiuj klucz.

      >[!Important]
      >Po zamknięciu formularza **klucze** nie będzie można zobaczyć wartości klucza.

    ![Formularz wartości klucza](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Tworzenie tokenu dla aplikacji klienckiej

Do utworzenia i pobrania tokenu za pomocą interfejsu API REST protokołu OAuth można użyć dowolnego z następujących programów:

- Postman
- Zwinięcie w systemie Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Aby utworzyć i uzyskać token przy użyciu programu Poster

 Aby poprosił rozwiązanie Auth0 o tokeny dla dowolnej z autoryzowanych aplikacji, wykonaj operację POST w [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) punkcie końcowym z ładunkiem w następującym formacie:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Przekaż następujące parametry w treści żądania:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Przekaż następujące parametry w nagłówku żądania:

```
Content-Type: application/x-www-form-urlencoded
```

Poniższy zrzut ekranu przedstawia przykład użycia programu Poster do pobrania tokenu.

![Pobieranie tokenu za pomocą programu Poster](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Aby utworzyć i uzyskać token przy użyciu zwinięcia w systemie Linux

Aby poprosił rozwiązanie Auth0 o tokeny dla dowolnej z autoryzowanych aplikacji, wykonaj operację POST w [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) punkcie końcowym z ładunkiem w następującym formacie:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Poniższy zrzut ekranu przedstawia przykład użycia zwinięcie polecenia, aby uzyskać token.

![Pobieranie tokenu za pomocą polecenia zwinięcie](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Aby utworzyć i uzyskać token przy użyciu&#35; C

Aby poprosił rozwiązanie Auth0 o tokeny dla dowolnych z autoryzowanych aplikacji, wykonaj operację POST w\/punkcie końcowym https:/soamtenant.Auth0.com/OAuth/token z ładunkiem w następującym formacie:

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Aby utworzyć i uzyskać token przy użyciu programu PowerShell

Aby poprosił rozwiązanie Auth0 o tokeny dla dowolnych z autoryzowanych aplikacji, wykonaj operację POST w\/punkcie końcowym https:/soamtenant.Auth0.com/OAuth/token z ładunkiem w następującym formacie:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP "
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>Przekaż token aplikacji klienta do interfejsu API

Przekaż token do interfejsu API samoobsługowego testowania przy użyciu następującego kodu w nagłówku autoryzacji:

```powershell
$redirectUri = 'https://isvapp.azurewebsites.net/selftest-vm'
$accesstoken = 'place your token here'

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Testowanie klienta samotestowego

Aby przetestować klienta, wykonaj następujące kroki:

1. Wdróż maszynę wirtualną, którą chcesz przetestować.
2. Wywołaj interfejs API służący do samoobsługowego testowania przy użyciu tokenu aplikacji klienta do autoryzacji.
3. Pobierz wyniki testu w formacie JSON.

### <a name="test-result-examples"></a>Przykłady wyników testu

Poniższe fragmenty kodu pokazują wyniki testów w formacie JSON.

**Wyniki testu dla maszyny wirtualnej z systemem Windows:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Wyniki testu dla maszyny wirtualnej z systemem Linux:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Następne kroki

Po pomyślnym przetestowaniu maszyny wirtualnej platformy Azure możesz [opublikować tę ofertę](./cpp-publish-offer.md).
