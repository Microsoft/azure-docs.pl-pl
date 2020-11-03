---
title: Nie można uzyskać dostępu do plików magazynu Data Lake w usłudze Azure HDInsight
description: Nie można uzyskać dostępu do plików magazynu Data Lake w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/13/2019
ms.openlocfilehash: 8bac53cd08629e8b0a9cb91e596856c0ae6b5a2f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289118"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Nie można uzyskać dostępu do plików magazynu Data Lake w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue-acl-verification-failed"></a>Problem: Weryfikacja listy ACL nie powiodła się

Zostanie wyświetlony komunikat o błędzie podobny do:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Przyczyna

Użytkownik mógł mieć odwołane uprawnienia jednostki usługi (SP) w plikach/folderach.

### <a name="resolution"></a>Rozwiązanie

1. Sprawdź, czy SP ma uprawnienia "x" do przechodzenia wzdłuż ścieżki. Aby uzyskać więcej informacji, zobacz [uprawnienia](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html). Przykładowe `dfs` polecenie, aby sprawdzić dostęp do plików/folderów na koncie magazynu Data Lake:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Skonfiguruj wymagane uprawnienia, aby uzyskać dostęp do ścieżki na podstawie wykonywanej operacji odczytu/zapisu. Zobacz tutaj, aby uzyskać uprawnienia wymagane do wykonywania różnych operacji systemu plików.

---

## <a name="issue-service-principal-certificate-expiry"></a>Problem: ważność certyfikatu głównego usługi

Zostanie wyświetlony komunikat o błędzie podobny do:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Przyczyna

Certyfikat podany dla dostępu do jednostki usługi mógł wygasnąć.

1. SSH do węzła głównego. Sprawdź dostęp do konta magazynu przy użyciu następującego `dfs` polecenia:

    ```
    hdfs dfs -ls /
    ```

1. Upewnij się, że komunikat o błędzie jest podobny do następującego:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Pobierz jeden z adresów URL z programu `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls` .

1. Uruchom następujące polecenie zwinięcie, aby pobrać token uwierzytelniania OAuth.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. Dane wyjściowe dla prawidłowej jednostki usługi powinny mieć postać podobną do:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Jeśli certyfikat główny usługi wygasł, dane wyjściowe będą wyglądać następująco:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Wszystkie inne Azure Active Directory pokrewne błędy/błędy powiązane z certyfikatem można rozpoznać, wysyłając polecenie ping do adresu URL bramy w celu pobrania tokenu OAuth.

1. Jeśli podczas próby dostępu do ADLS z klastra HDI pojawia się następujący błąd. Sprawdź, czy certyfikat wygasł, wykonując kroki wymienione powyżej.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Rozwiązanie

Utwórz nowy certyfikat lub Przypisz istniejący certyfikat przy użyciu następującego skryptu programu PowerShell:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

W przypadku przypisywania istniejącego certyfikatu należy utworzyć certyfikat z gotowym plikiem. pfx i hasłem. Skojarz certyfikat z jednostką usługi, z którą klaster został utworzony, za pomocą gotowego identyfikatora AppId.

Wykonaj polecenie programu PowerShell po zastąpieniu parametrów wartościami rzeczywistymi.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]