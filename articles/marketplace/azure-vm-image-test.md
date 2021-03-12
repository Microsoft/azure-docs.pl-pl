---
title: Testowanie obrazu maszyny wirtualnej platformy Azure dla portalu Azure Marketplace
description: Informacje na temat testowania i przesyłania oferty maszyny wirtualnej platformy Azure w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: github-2407
ms.author: krsh
ms.date: 02/19/2020
ms.openlocfilehash: 48e41e870676bf6939ccaf5268c462f885e67572
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630184"
---
# <a name="test-a-virtual-machine-image"></a>Testowanie obrazu maszyny wirtualnej

W tym temacie opisano kroki testowania obrazu maszyny wirtualnej w celu wdrożenia w portalu Azure Marketplace.

## <a name="deploy-an-azure-vm"></a>Wdróż maszynę wirtualną platformy Azure

Aby wdrożyć maszynę wirtualną z obrazu udostępnionego galerii obrazów:

1. Przejdź do wersji obrazu udostępnionej galerii obrazów
1. Kliknij pozycję Utwórz maszynę wirtualną
1. Podaj nazwę maszyny wirtualnej i wybierz rozmiar maszyny wirtualnej
1. Kliknij przycisk przegląd + Utwórz. Po przekazaniu walidacji kliknij pozycję Utwórz.

> [!NOTE]
> Jeśli musisz utworzyć maszynę wirtualną na podstawie pliku VHD, postępuj zgodnie z instrukcjami podanymi w poniższych artykułach, [Przygotuj szablon Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/azure-vm-image-test#prepare-an-azure-resource-manager-template) lub [Wdróż maszynę wirtualną platformy Azure przy użyciu programu PowerShell](https://docs.microsoft.com/azure/marketplace/azure-vm-image-test#deploy-an-azure-vm-using-powershell).

W tym artykule opisano sposób testowania i przesyłania obrazu maszyny wirtualnej (VM) w komercyjnej witrynie Marketplace, aby upewnić się, że spełnia on najnowsze wymagania dotyczące publikowania w witrynie Azure Marketplace.

Przed przesłaniem oferty maszyny wirtualnej wykonaj następujące kroki:

- Wdróż maszynę wirtualną platformy Azure przy użyciu uogólnionego obrazu; Zobacz [Tworzenie maszyny wirtualnej przy użyciu zatwierdzonej podstawy](azure-vm-create-using-approved-base.md) lub [Tworzenie maszyny wirtualnej przy użyciu własnego obrazu](azure-vm-create-using-own-image.md).
- Uruchamianie walidacji.

## <a name="run-validations"></a>Wykonaj walidacje

Istnieją dwa sposoby na wykonanie walidacji wdrożonego obrazu.

### <a name="use-certification-test-tool-for-azure-certified"></a>Używanie narzędzia testowego certyfikacji dla certyfikatu platformy Azure

#### <a name="download-and-run-the-certification-test-tool"></a>Pobieranie i uruchamianie narzędzia testowego certyfikacji

Narzędzie Test certyfikacji dla certyfikatu platformy Azure jest uruchamiane na lokalnym komputerze z systemem Windows, ale testuje maszynę wirtualną z systemem Windows lub Linux opartą na platformie Azure. Zaświadcza on, że obraz maszyny wirtualnej użytkownika może być używany z Microsoft Azure i że zostały spełnione wskazówki i wymagania dotyczące przygotowywania wirtualnego dysku twardego.

1. Pobierz i zainstaluj najnowsze [Narzędzie testowania certyfikacji dla certyfikatu platformy Azure](https://www.microsoft.com/download/details.aspx?id=44299).
2. Otwórz narzędzie certyfikacji, a następnie wybierz pozycję **Rozpocznij nowy test**.
3. Na ekranie informacje o testach wprowadź **nazwę testu** dla przebiegu testu.
4. Wybierz platformę dla maszyny wirtualnej, **system Windows Server** lub **Linux**. Wybór platformy ma wpływ na pozostałe opcje.
5. Jeśli maszyna wirtualna korzysta z tej usługi bazy danych, zaznacz pole wyboru **Testuj dla Azure SQL Database** .

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Łączenie Narzędzia certyfikacji z obrazem maszyny wirtualnej

1. Wybierz tryb uwierzytelniania SSH: uwierzytelnianie hasła lub uwierzytelnianie przy użyciu pliku klucza.
2. W przypadku korzystania z uwierzytelniania opartego na hasłach wprowadź wartości w polu Nazwa DNS, **Nazwa użytkownika** i **hasło** **maszyny wirtualnej**. Możesz również zmienić domyślny numer portu SSH.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Pokazuje wybór informacji o testowaniu maszyn wirtualnych.":::

3. W przypadku użycia uwierzytelniania opartego na plikach klucza wpisz wartości w polu Nazwa DNS maszyny wirtualnej, nazwa użytkownika i lokalizacja klucza prywatnego. Możesz również uwzględnić hasło lub zmienić domyślny numer portu SSH.
4. Wprowadź w pełni kwalifikowaną nazwę DNS maszyny wirtualnej (na przykład MyVMName.Cloudapp.net).
5. Wprowadź **nazwę użytkownika** i **hasło**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Pokazuje wybór nazwy użytkownika i hasła maszyny wirtualnej.":::

6. Wybierz opcję **Dalej**.

#### <a name="run-a-certification-test"></a>Uruchamianie testu certyfikacji

Po podaniu wartości parametrów dla obrazu maszyny wirtualnej w narzędziu certyfikacji wybierz pozycję Testuj połączenie, aby utworzyć prawidłowe połączenie z maszyną wirtualną. Po zweryfikowaniu połączenia wybierz pozycję **dalej** , aby rozpocząć test. Po zakończeniu testu wyniki są wyświetlane w tabeli. W kolumnie Stan są wyświetlane (pass/Fail/ostrzeżenie) dla każdego testu. Jeśli którykolwiek z testów zakończy się niepowodzeniem, obraz nie zostanie certyfikowany. W takim przypadku Przejrzyj wymagania i komunikaty o błędach, wprowadź sugerowane zmiany i ponownie uruchom test.

Po zakończeniu testu automatycznego podaj dodatkowe informacje o obrazie maszyny wirtualnej na karcie dwie karty ekranu kwestionariusza, ogólne oceny i dostosowanie jądra, a następnie wybierz przycisk Dalej.

Ostatni ekran umożliwia podanie dodatkowych informacji, takich jak informacje o dostępie SSH dla obrazu maszyny wirtualnej z systemem Linux, oraz wyjaśnienie ewentualnych ocen zakończonych niepowodzeniem, jeśli szukasz wyjątków.

Na koniec wybierz pozycję Generuj raport, aby pobrać wyniki testów i pliki dziennika dla wykonanych przypadków testowych wraz z odpowiedziami na kwestionariusz.
> [!Note]
> Kilku wydawców ma scenariusze, w których maszyny wirtualne muszą być zablokowane, ponieważ mają one oprogramowanie takie jak zapory zainstalowane na maszynie wirtualnej. W takim przypadku Pobierz narzędzie do [testowania certyfikowane](https://aka.ms/AzureCertificationTestTool) tutaj i prześlij raport [w centrum partnerskim.](https://aka.ms/marketplacepublishersupport)

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Jak korzystać z interfejsu API Self-Test przy użyciu programu PowerShell

### <a name="on-linux-os"></a>W systemie operacyjnym Linux

Wywoływanie interfejsu API w programie PowerShell:

1. Użyj polecenia Invoke-WebRequest, aby wywołać interfejs API.
2. Metoda ma wartość post i typ zawartości to JSON, jak pokazano w poniższym przykładzie kodu i przechwytywaniu ekranu.
3. Określ parametry treści w formacie JSON.

W poniższym przykładzie pokazano wywołanie interfejsu API programu PowerShell:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Oto przykład wywołania interfejsu API w programie PowerShell:

[![Przykładowy ekran służący do wywoływania interfejsu API w programie PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Korzystając z poprzedniego przykładu, można pobrać kod JSON i przeanalizować go w celu uzyskania następujących szczegółów:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Ten przykładowy ekran, który pokazuje `$res.Content` , pokazuje szczegóły wyników testów w formacie JSON:

[![Przykład ekranu służący do wywoływania interfejsu API w programie PowerShell ze szczegółowymi wynikami testów.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Oto przykład wyników testów JSON wyświetlanych w podglądzie JSON w trybie online (na przykład [Code Beautify](https://codebeautify.org/jsonviewer) lub [Podgląd JSON](https://jsonformatter.org/json-viewer)).

![Więcej wyników testów w podglądzie JSON w trybie online.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>W systemie operacyjnym Windows

Wywoływanie interfejsu API w programie PowerShell:

1. Użyj polecenia Invoke-WebRequest, aby wywołać interfejs API.
2. Metoda ma wartość post i typ zawartości to JSON, jak pokazano w poniższym przykładzie kodu i ekranie przykładowym.
3. Utwórz parametry treści w formacie JSON.

Ten przykładowy kod pokazuje wywołanie programu PowerShell do interfejsu API:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Te Przykładowe ekrany pokazują przykład wywołania interfejsu API w programie PowerShell:

**Z kluczem SSH**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Wywoływanie interfejsu API w programie PowerShell przy użyciu klucza SSH.":::

**Z hasłem**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Wywoływanie interfejsu API w programie PowerShell przy użyciu hasła.":::

<br>Korzystając z poprzedniego przykładu, można pobrać kod JSON i przeanalizować go w celu uzyskania następujących szczegółów:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Ten ekran, który pokazuje `$res.Content` , pokazuje szczegóły wyników testów w formacie JSON:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Szczegóły wyników testu w formacie JSON.":::

<br>Oto przykładowe wyniki testów wyświetlane w podglądzie JSON w trybie online (na przykład [kod Beautify](https://codebeautify.org/jsonviewer) lub [Podgląd JSON](https://jsonformatter.org/json-viewer)).

![Wyniki testów w podglądzie JSON w trybie online.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Jak używać funkcji zazwinięcie do korzystania z interfejsu API Self-Test w systemie operacyjnym Linux

W tym przykładzie zwinięcie zostanie użyte do nawywołania interfejsu API do Azure Active Directory i Self-Host maszyny wirtualnej.

1. Zażądaj tokenu usługi Azure AD do uwierzytelniania na samoobsługowej maszynie wirtualnej

   Upewnij się, że poprawne wartości są zastępowane w żądaniu zwinięcie.

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   Oto przykład odpowiedzi z żądania:

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Prześlij żądanie samotestowej maszyny wirtualnej

   Upewnij się, że token i parametry okaziciela są zastępowane prawidłowymi wartościami.

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Przykładowa odpowiedź z wywołania interfejsu API samoobsługowego testowania maszyn wirtualnych

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Następne kroki

- Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/).
