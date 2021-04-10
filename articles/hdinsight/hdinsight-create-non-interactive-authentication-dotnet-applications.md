---
title: Aplikacja .NET dla nieinterakcyjnego uwierzytelniania — Azure HDInsight
description: Dowiedz się, jak tworzyć nieinteraktywne uwierzytelnianie Microsoft .NET aplikacji w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/23/2019
ms.openlocfilehash: 0fffe763f77fffcec6010d43deb92ffea9ece15d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094926"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Tworzenie aplikacji usługi HDInsight platformy .NET z uwierzytelnianiem nieinterakcyjnym

Uruchom aplikację usługi HDInsight Microsoft .NET Azure w ramach własnej tożsamości aplikacji (nieinteraktywnej) lub w obszarze tożsamość zalogowanego użytkownika aplikacji (Interactive). W tym artykule pokazano, jak utworzyć aplikację .NET z nieinteraktywnym uwierzytelnianiem w celu nawiązania połączenia z platformą Azure i zarządzania usługą HDInsight. Przykład aplikacji interaktywnej zawiera temat [nawiązywanie połączenia z usługą Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

W aplikacji nieinteraktywnej platformy .NET potrzebne są:

* Identyfikator dzierżawy subskrypcji platformy Azure (nazywany również *identyfikatorem katalogu*). Zobacz [Pobieranie identyfikatora dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* Identyfikator klienta aplikacji Azure Active Directory (Azure AD). Zobacz [Tworzenie aplikacji Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) i [Pobieranie identyfikatora aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* Klucz tajny aplikacji usługi Azure AD. Zobacz [Pobieranie klucza uwierzytelniania aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="prerequisites"></a>Wymagania wstępne

HDInsight An klaster. Zobacz [samouczek wprowadzający](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Przypisywanie roli do aplikacji usługi Azure AD

Przypisywanie [roli](../role-based-access-control/built-in-roles.md)do aplikacji usługi Azure AD w celu udzielenia uprawnień do wykonywania akcji. Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone do niższych poziomów zakresu. Na przykład dodanie aplikacji do roli czytelnik dla grupy zasobów oznacza, że aplikacja może odczytać grupę zasobów i wszystkie znajdujące się w niej zasoby. W tym artykule ustawisz zakres na poziomie grupy zasobów. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról platformy Azure do zarządzania dostępem do zasobów subskrypcji platformy Azure](../role-based-access-control/role-assignments-portal.md).

**Aby dodać rolę właściciela do aplikacji usługi Azure AD**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do grupy zasobów, w której znajduje się klaster usługi HDInsight w dalszej części tego artykułu. Jeśli masz dużą liczbę grup zasobów, możesz użyć filtru, aby znaleźć żądany obiekt.
1. W menu Grupa zasobów wybierz pozycję **Kontrola dostępu (IAM)**.
1. Wybierz kartę **przypisania ról** , aby wyświetlić bieżące przypisania ról.
1. W górnej części strony wybierz pozycję **+ Dodaj**.
1. Postępuj zgodnie z instrukcjami, aby dodać rolę właściciela do aplikacji usługi Azure AD. Po pomyślnym dodaniu roli aplikacja zostanie wyświetlona w obszarze roli właściciela.

## <a name="develop-an-hdinsight-client-application"></a>Tworzenie aplikacji klienckiej usługi HDInsight

1. Utwórz aplikację konsolową języka C#.
2. Dodaj następujące pakiety [NuGet](https://www.nuget.org/) :

    * `Install-Package Microsoft.Azure.Common.Authentication -Pre`
    * `Install-Package Microsoft.Azure.Management.HDInsight -Pre`
    * `Install-Package Microsoft.Azure.Management.Resources -Pre`

3. Uruchom następujący kod:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```

## <a name="next-steps"></a>Następne kroki

* [Utwórz Azure Active Directory aplikację i nazwę główną usługi w Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).
* Dowiedz się, jak [uwierzytelniać jednostkę usługi za pomocą Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Dowiedz się więcej [na temat kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/role-assignments-portal.md).
