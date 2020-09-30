---
title: Uwierzytelnianie oparte na certyfikatach z użyciem Azure Cosmos DB i Active Directory
description: Dowiedz się, jak skonfigurować tożsamość usługi Azure AD na potrzeby uwierzytelniania opartego na certyfikatach, aby uzyskać dostęp do kluczy z Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 0b1fdec12b99edc952d24b0b3cc21bad24ec7554
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569737"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Uwierzytelnianie oparte na certyfikatach dla tożsamości usługi Azure AD w celu uzyskiwania dostępu do kluczy z konta Azure Cosmos DB

Uwierzytelnianie oparte na certyfikatach umożliwia uwierzytelnienie aplikacji klienckiej przy użyciu usługi Azure Active Directory (Azure AD) z certyfikatem klienta. Uwierzytelnianie oparte na certyfikatach można wykonywać na maszynie, na której jest potrzebna tożsamość, np. maszynie lokalnej lub maszynie wirtualnej na platformie Azure. Aplikacja może następnie odczytać klucze Azure Cosmos DB bez podawania kluczy bezpośrednio w aplikacji. W tym artykule opisano sposób tworzenia przykładowej aplikacji usługi Azure AD, konfigurowania jej na potrzeby uwierzytelniania opartego na certyfikatach, logowania się do platformy Azure przy użyciu nowej tożsamości aplikacji, a następnie pobierania kluczy z konta usługi Azure Cosmos. W tym artykule Azure PowerShell skonfigurować tożsamości i przedstawiono przykładową aplikację w języku C#, która uwierzytelnia i uzyskuje dostęp do kluczy z konta usługi Azure Cosmos.  

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [najnowszą wersję](/powershell/azure/install-az-ps) programu Azure PowerShell.

* Jeśli nie masz [subskrypcji platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="register-an-app-in-azure-ad"></a>Rejestrowanie aplikacji w usłudze Azure AD

W tym kroku zostanie zarejestrowana Przykładowa aplikacja sieci Web na koncie usługi Azure AD. Ta aplikacja jest później używana do odczytywania kluczy z konta Azure Cosmos DB. Aby zarejestrować aplikację, wykonaj następujące kroki: 

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. Otwórz okienko **Active Directory** platformy Azure, przejdź do okienka **rejestracje aplikacji** i wybierz pozycję **Nowa rejestracja**. 

   :::image type="content" source="./media/certificate-based-authentication/new-app-registration.png" alt-text="Rejestracja nowej aplikacji w Active Directory":::

1. Wypełnij formularz **zarejestruj aplikację** z następującymi szczegółami:  

   * **Nazwa** — Podaj nazwę aplikacji, która może być dowolną nazwą, taką jak "sampleApp".
   * **Obsługiwane typy kont** — wybierz **konta w tym katalogu organizacji (katalog domyślny)** , aby zezwolić na dostęp do tej aplikacji zasobom w bieżącym katalogu. 
   * **Adres URL przekierowania** — wybierz pozycję Aplikacja typu **Web** i podaj adres URL, pod którym aplikacja jest hostowana, może być dowolnym adresem URL. Na potrzeby tego przykładu możesz podać adres URL testu, który `https://sampleApp.com` jest dobry nawet wtedy, gdy aplikacja nie istnieje.

   :::image type="content" source="./media/certificate-based-authentication/register-sample-web-app.png" alt-text="Rejestracja nowej aplikacji w Active Directory":::

1. Po wypełnieniu formularza wybierz pozycję **zarejestruj** .

1. Po zarejestrowaniu aplikacji Zanotuj **Identyfikator aplikacji (klienta)** i **Identyfikator obiektu**, które będą używane w następnych krokach. 

   :::image type="content" source="./media/certificate-based-authentication/get-app-object-ids.png" alt-text="Rejestracja nowej aplikacji w Active Directory":::

## <a name="install-the-azuread-module"></a>Instalowanie modułu AzureAD

W tym kroku zostanie zainstalowany moduł Azure AD PowerShell. Ten moduł jest wymagany do pobrania identyfikatora aplikacji zarejestrowanej w poprzednim kroku i skojarzenia certyfikatu z podpisem własnym z tą aplikacją. 

1. Otwórz Windows PowerShell ISE z uprawnieniami administratora. Jeśli jeszcze tego nie zrobiono, zainstaluj moduł AZ PowerShell module i Połącz się z subskrypcją. Jeśli masz wiele subskrypcji, możesz ustawić kontekst bieżącej subskrypcji, jak pokazano w następujących poleceniach:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Instalowanie i importowanie modułu [AzureAD](/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Zaloguj się do usługi Azure AD

Zaloguj się do usługi Azure AD, w której zarejestrowano aplikację. Za pomocą polecenia Connect-AzureAD Zaloguj się do swojego konta, a następnie wprowadź swoje poświadczenia konta platformy Azure w oknie podręcznym. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Otwórz kolejne wystąpienie Windows PowerShell ISE i uruchom następujące polecenia, aby utworzyć certyfikat z podpisem własnym i odczytać klucz skojarzony z certyfikatem:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Tworzenie poświadczeń opartych na certyfikacie 

Następnie uruchom następujące polecenia, aby uzyskać identyfikator obiektu aplikacji i utworzyć poświadczenia oparte na certyfikacie. W tym przykładzie ustawimy certyfikat na wygasający po roku, można ustawić go na dowolną wymaganą datę końcową.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Powyższe polecenie spowoduje wyniki podobne do poniższego zrzutu ekranu:

:::image type="content" source="./media/certificate-based-authentication/certificate-based-credential-output.png" alt-text="Rejestracja nowej aplikacji w Active Directory":::

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Skonfiguruj konto usługi Azure Cosmos, aby używało nowej tożsamości

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. Przejdź do swojego konta usługi Azure Cosmos, Otwórz blok **Kontrola dostępu (IAM)** .

1. Wybierz pozycję **Dodaj** i **Dodaj przypisanie roli**. Dodaj sampleApp utworzone w poprzednim kroku z rolą **współautor** , jak pokazano na poniższym zrzucie ekranu:

   :::image type="content" source="./media/certificate-based-authentication/configure-cosmos-account-with-identify.png" alt-text="Rejestracja nowej aplikacji w Active Directory":::

1. Wybierz pozycję **Zapisz** po wypełnieniu formularza

## <a name="register-your-certificate-with-azure-ad"></a>Rejestrowanie certyfikatu w usłudze Azure AD

Poświadczenie oparte na certyfikacie można skojarzyć z aplikacją kliencką w usłudze Azure AD z poziomu Azure Portal. Aby skojarzyć poświadczenie, należy przekazać plik certyfikatu, wykonując następujące czynności:

W usłudze Azure App Registration dla aplikacji klienckiej:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. Otwórz okienko **Active Directory** platformy Azure, przejdź do okienka **rejestracje aplikacji** i Otwórz przykładową aplikację utworzoną w poprzednim kroku. 

1. Wybierz pozycję **certyfikaty & wpisy tajne** , a następnie **Przekaż certyfikat**. Przejrzyj plik certyfikatu, który został utworzony w poprzednim kroku do przekazania.

1. Wybierz pozycję **Dodaj**. Po przekazaniu certyfikatu zostaną wyświetlone wartości odcisku palca, Data rozpoczęcia i wygaśnięcie.

## <a name="access-the-keys-from-powershell"></a>Uzyskiwanie dostępu do kluczy z programu PowerShell

W tym kroku nastąpi logowanie do platformy Azure przy użyciu aplikacji i utworzonego certyfikatu oraz dostęp do kluczy konta usługi Azure Cosmos. 

1. Na początku Wyczyść poświadczenia konta platformy Azure, które zostały użyte do zalogowania się do konta. Poświadczenia można wyczyścić za pomocą następującego polecenia:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Następnie sprawdź, czy możesz zalogować się do Azure Portal przy użyciu poświadczeń aplikacji i uzyskaj dostęp do kluczy Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Poprzednie polecenie wyświetli podstawowe i pomocnicze klucze podstawowe konta usługi Azure Cosmos. Możesz wyświetlić dziennik aktywności konta usługi Azure Cosmos, aby sprawdzić, czy żądanie Get Keys zakończyło się pomyślnie, a zdarzenie jest inicjowane przez aplikację "sampleApp".

:::image type="content" source="./media/certificate-based-authentication/activity-log-validate-results.png" alt-text="Rejestracja nowej aplikacji w Active Directory":::

## <a name="access-the-keys-from-a-c-application"></a>Uzyskiwanie dostępu do kluczy z aplikacji C# 

Możesz również sprawdzić poprawność tego scenariusza, uzyskując dostęp do kluczy z aplikacji języka C#. Następująca aplikacja konsolowa w języku C#, która może uzyskiwać dostęp do Azure Cosmos DB kluczy za pomocą aplikacji zarejestrowanej w Active Directory. Przed uruchomieniem kodu Pamiętaj, aby zaktualizować tenantId, clientID, CertName, nazwę grupy zasobów, Identyfikator subskrypcji, nazwę konta Azure Cosmos. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Ten skrypt wyprowadza podstawowe i pomocnicze klucze podstawowe, jak pokazano na poniższym zrzucie ekranu:

:::image type="content" source="./media/certificate-based-authentication/csharp-application-output.png" alt-text="Rejestracja nowej aplikacji w Active Directory":::

Podobnie jak w poprzedniej sekcji, można wyświetlić dziennik aktywności konta usługi Azure Cosmos, aby sprawdzić, czy zdarzenie pobierania kluczy jest inicjowane przez aplikację "sampleApp". 


## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie kluczy usługi Azure Cosmos przy użyciu usługi Azure Key Vault](access-secrets-from-keyvault.md)

* [Linia bazowa zabezpieczeń dla Azure Cosmos DB](security-baseline.md)
