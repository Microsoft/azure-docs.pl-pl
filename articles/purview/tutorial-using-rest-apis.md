---
title: 'Samouczek: korzystanie z interfejsów API REST'
description: W tym samouczku opisano sposób korzystania z interfejsów API REST usługi Azure kontrolą w celu uzyskania dostępu do zawartości katalogu.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 06f738ea88093ea8de45efebea983fd7fa13216e
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387823"
---
# <a name="tutorial-use-the-rest-apis"></a>Samouczek: korzystanie z interfejsów API REST

W tym samouczku dowiesz się, jak używać interfejsów API REST usługi Azure kontrolą. Każda osoba, która chce przesłać dane do wykazu usługi Azure kontrolą, może uwzględnić wykaz w ramach zautomatyzowanego procesu lub utworzyć własne środowisko użytkownika w wykazie, korzystając z interfejsów API REST.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz nazwę główną usługi (aplikację).
> * Skonfiguruj wykaz, aby ufać jednostce usługi (aplikacji).
> * Zapoznaj się z dokumentacją interfejsów API REST.
> * Zbierz wymagane wartości, aby użyć interfejsów API REST.
> * Aby wywołać interfejsy API REST, użyj klienta programu Poster.
> * Generowanie klienta platformy .NET do wywoływania interfejsów API REST.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby rozpocząć, musisz mieć istniejące konto usługi Azure kontrolą. Jeśli nie masz wykazu, zobacz [Przewodnik Szybki Start dotyczący tworzenia konta usługi Azure kontrolą](create-catalog-portal.md).

* Jeśli musisz dodać dane do wykazu, zapoznaj się z [samouczkiem, aby uruchomić zestaw startowy i przeskanować dane](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-service-principal-application"></a>Tworzenie nazwy głównej usługi (aplikacji)

Aby klient interfejsu API REST mógł uzyskać dostęp do wykazu, klient musi mieć nazwę główną usługi (aplikację) i tożsamość, która jest rozpoznawana przez wykaz i jest skonfigurowana do zaufania. Podczas wykonywania wywołań interfejsu API REST do wykazu korzystają one z tożsamości jednostki usługi.

Klienci, którzy korzystali z istniejących nazw głównych usług (identyfikatorów aplikacji), mają wysoką liczbę błędów. Dlatego zalecamy utworzenie nowej jednostki usługi na potrzeby wywoływania interfejsów API.

Aby utworzyć nową nazwę główną usługi:

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. Na stronie **Azure Active Directory** wybierz pozycję **rejestracje aplikacji** z okienka po lewej stronie.
1. Wybierz pozycję **Nowa rejestracja**.
1. Na stronie **zarejestruj aplikację** :
    1. Wprowadź **nazwę** aplikacji (nazwę główną usługi).
    1. Wybierz **konta tylko w tym katalogu organizacji (tylko _&lt; nazwa &gt; dzierżawy_ — Single dzierżawca)**.
    1. Dla **identyfikatora URI przekierowania (opcjonalnie)** wybierz pozycję **Sieć Web** i wprowadź wartość. Ta wartość nie musi być prawidłowym identyfikatorem URI.
    1. Wybierz pozycję **Zarejestruj**.
1. Na nowej stronie głównej usługi Skopiuj wartości **Nazwa wyświetlana** i **Identyfikator aplikacji (klienta)** , aby zapisać je później.

   Identyfikator aplikacji jest `client_id` wartością w przykładowym kodzie.

Aby użyć nazwy głównej usługi (aplikacji), należy uzyskać hasło. Oto kroki tej procedury:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **rejestracje aplikacji** w okienku po lewej stronie.
1. Wybierz nazwę główną usługi (aplikację) z listy.
1. Wybierz pozycję **certyfikaty & wpisy tajne** w okienku po lewej stronie.
1. Wybierz pozycję **Nowy wpis tajny klienta**.
1. Na stronie **Dodawanie wpisu tajnego klienta** wprowadź **Opis**, wybierz czas wygaśnięcia w obszarze **wygaśnięcie**, a następnie wybierz pozycję **Dodaj**.

   Na stronie wpisy **tajne klienta** ciąg w kolumnie **wartość** nowego wpisu tajnego to hasło. Zapisz tę wartość.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Zrzut ekranu przedstawiający klucz tajny klienta.":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>Skonfiguruj wykaz, aby ufać jednostce usługi (aplikacji)

Aby skonfigurować usługę Azure kontrolą do zaufania nowej nazwy głównej usługi:

1. Przejdź do konta kontrolą

1. Na stronie **konto kontrolą** wybierz pozycję **Kontrola dostępu do karty (IAM).**

1. Kliknij pozycję **+ Dodaj**

1. Wybierz pozycję **Dodaj przypisanie roli**

1. Dla typu roli w **kontrolą Data Curator**

    > [!Note]
    > Aby uzyskać więcej informacji na temat uprawnień do usługi Azure kontrolą Data Catalog, zobacz [uprawnienia katalogu](catalog-permissions.md). Jeśli na przykład chcesz mieć uprawnienia do wyzwalania skanowania, typem roli musi być **kontrolą Administrator źródła danych**.

1. W przypadku **uprawnienia do przypisywania** pozostaw wartość domyślną, **użytkownika, grupy lub nazwy głównej usługi**

1. W polu **Wybierz** wprowadź nazwę użytkownika, grupę Azure Active Directory lub jednostkę usługi, którą chcesz przypisać, a następnie kliknij jej nazwę w okienku wyników.

1. Kliknij pozycję **Zapisz**

Nazwa główna usługi została skonfigurowana jako administrator aplikacji, co umożliwia jej wysyłanie zawartości do wykazu.

## <a name="view-the-rest-apis-documentation"></a>Wyświetlanie dokumentacji interfejsów API REST

Aby wyświetlić dokumentację interfejsu API struktury Swagger, Pobierz [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip), Wyodrębnij pliki i Otwórz index.html.

Jeśli chcesz dowiedzieć się więcej na temat zaawansowanego interfejsu API wyszukiwania/sugerowania udostępnianego przez usługę Azure kontrolą, zobacz dokumentację filtru wyszukiwania interfejsu API REST. Klient wygenerowany przez AutoRest nie obsługuje obecnie niestandardowych parametrów wyszukiwania. Aby obejść ten krok, postępuj zgodnie z dokumentem filtru wyszukiwania, aby zdefiniować klasy filtru w kodzie jako parametry wywołania interfejsu API. Dokument index.html zawiera przykłady tych interfejsów API.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>Zbierz wymagane wartości, aby użyć interfejsów API REST

Znajdź i Zapisz następujące wartości:

* Identyfikator dzierżawy:
  * W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **Azure Active Directory**.
  * W sekcji **Zarządzanie** w okienku po lewej stronie wybierz pozycję **Właściwości**, Znajdź **Identyfikator dzierżawy**, a następnie wybierz ikonę **Kopiuj do schowka** , aby zapisać jej wartość.
* Punkt końcowy w Atlasie:
  * Na [stronie konta usługi Azure kontrolą](https://aka.ms/purviewportal) w Azure Portal Znajdź i wybierz swoje konto platformy Azure kontrolą na liście.
  * Wybierz **Właściwości**, Znajdź **punkt końcowy w Atlasie**, a następnie wybierz ikonę **Kopiuj do schowka** , aby zapisać jej wartość. Usuń część *https://* ciągu, gdy zostanie ona użyta później.
* Nazwa konta:
  * Wyodrębnij nazwę wykazu z ciągu punktu końcowego w Atlasie. Na przykład, jeśli punkt końcowy w Atlasie to `https://ThisIsMyCatalog.catalog.purview.azure.com` , nazwa konta to `ThisIsMyCatalog` .

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>Używanie klienta programu do wywoływania interfejsów API REST

1. Zainstaluj [klienta programu Poster](https://www.getpostman.com/).
1. Na stronie klienta wybierz pozycję **Importuj**, a następnie użyj opcji [Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json).
1. Wybierz pozycję **kolekcje**, a następnie wybierz pozycję **Testuj**.
1. Wybierz pozycję **Pobierz token**:
    1. W adresie URL obok pozycji Opublikuj Zastąp *&lt; Identyfikator &gt;* dzierżawy identyfikatorem dzierżawy, który został skopiowany w poprzedniej sekcji.
    1. Wybierz kartę **treść** , a następnie Zastąp symbole zastępcze w ścieżce i treści z poprzedniego kroku.

       Po wybraniu opcji **Wyślij** treść odpowiedzi zawiera strukturę JSON, w tym nazwę *access_token* i wartość ciągu ujętego w cudzysłów. 
    1. Skopiuj wartość tokenu okaziciela (bez cudzysłowów), która ma zostać użyta w następnym kroku.

1. Wybierz **/v2/Types/Typedefs**:
    1. Zastąp symbol zastępczy w ścieżce wartością punktu końcowego w Atlasie. Tę wartość można uzyskać, przechodząc do wystąpienia katalogu w portalu Ibiza, a następnie klikając pozycję przegląd. 

       Token okaziciela jest ustawiany z pierwszego kroku (lub można go skopiować na kartę "Autoryzacja" ręcznie).

    1. Wybierz pozycję **Wyślij** , aby uzyskać odpowiedź.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>Generowanie klienta platformy .NET do wywoływania interfejsów API REST

### <a name="install-autorest"></a>Zainstaluj program AutoRest



1. [Zainstaluj Node.js](https://github.com/Azure/autorest/blob/master/docs/installing-autorest.md).
1. Otwórz program PowerShell i uruchom następujące polecenie:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>Pobieranie rest-api-specs.zip i tworzenie klienta

1. Pobierz [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) i Wyodrębnij jego pliki.
1. Uruchom następujące polecenie w programie PowerShell z folderu wyodrębnione-API-specyfikacje ze specyfikacją:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   W danych wyjściowych tego procesu tworzony jest folder PurviewCatalogClient i podfolder CSharp w folderze REST-API-specyfikacji.

### <a name="create-a-sample-net-console-application"></a>Tworzenie przykładowej aplikacji konsolowej platformy .NET

1. Otwórz program Visual Studio 2019. Te instrukcje zostały przetestowane z bezpłatną wersją społecznościową.
1. Na stronie **Tworzenie nowego projektu** Utwórz projekt **Aplikacja konsolowa (.NET Core)** w języku C#.
1. Kopiowanie i wklejanie dostarczonego [przykładowego kodu](#sample-code-for-the-console-application).
1. Zastąp wartości *AccountName*, *servicePrincipalId*, *servicePrincipalKey* i *tenantId* wartościami, które zostały wcześniej zebrane.
1. Użyj **Eksplorator rozwiązań** , aby dodać folder o nazwie **Generate** w projekcie programu Visual Studio.
1. Skopiuj wcześniej utworzony folder rest-api-specs\PurviewCatalogClient\CSharp do folderu \Generated. Użyj Eksploratora plików lub monitu wiersza polecenia dla operacji kopiowania, co spowoduje wyzwolenie programu Visual Studio na automatyczne dodanie plików do projektu.
1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.
1. Wybierz kartę **przeglądanie** . Znajdź i wybierz pozycję **Microsoft. Rest. ClientRuntime**.
1. Upewnij się, że wersja jest równa co najmniej 2.3.21, a następnie wybierz pozycję **Zainstaluj**.
1. Skompiluj i uruchom aplikację.

Przykładowy kod zwraca liczbę elementów typedef znajdujących się w katalogu i pokazuje, jak obsługiwać przypisania ról. Aby uzyskać szczegółowe informacje, zobacz `DoRoleAssignmentOperations()` w przykładowym kodzie. Aby uzyskać więcej informacji o projekcie, zobacz [Konfiguracja projektu](https://github.com/Azure/autorest/blob/master/docs/client/proj-setup.md).

### <a name="sample-code-for-the-console-application"></a>Przykładowy kod dla aplikacji konsolowej

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie źródłami danych](manage-data-sources.md)
