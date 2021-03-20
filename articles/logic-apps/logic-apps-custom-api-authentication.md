---
title: Dodawanie uwierzytelniania na potrzeby zabezpieczania wywołań niestandardowych interfejsów API
description: Jak skonfigurować uwierzytelnianie, aby zwiększyć bezpieczeństwo wywołań niestandardowych interfejsów API z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 5e0dcd478c6eb6696a0e07d35d4dccddac68ac1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993171"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Zwiększ bezpieczeństwo wywołań niestandardowych interfejsów API z Azure Logic Apps

Aby zwiększyć bezpieczeństwo wywołań interfejsów API, można skonfigurować uwierzytelnianie Azure Active Directory (Azure AD) za pomocą Azure Portal, dzięki czemu nie trzeba aktualizować kodu. Alternatywnie można wymagać i wymuszać uwierzytelnianie za pomocą kodu interfejsu API.

## <a name="authentication-options-for-your-api"></a>Opcje uwierzytelniania dla interfejsu API

Możesz poprawić zabezpieczenia wywołań niestandardowego interfejsu API w następujący sposób:

* [Brak zmian w kodzie](#no-code): Chroń swój interfejs API za pomocą [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) za pomocą Azure Portal, więc nie musisz aktualizować kodu ani ponownie wdrażać interfejsu API.

  > [!NOTE]
  > Domyślnie uwierzytelnianie usługi Azure AD, które można włączyć w Azure Portal nie zapewnia szczegółowej autoryzacji. Na przykład to uwierzytelnianie blokuje interfejs API tylko dla określonej dzierżawy, a nie do określonego użytkownika lub aplikacji. 

* [Aktualizowanie kodu interfejsu API](#update-code): Chroń swój interfejs API, wymuszając [uwierzytelnianie certyfikatu](#certificate), [uwierzytelnianie podstawowe](#basic)lub [uwierzytelnianie usługi Azure AD](#azure-ad-code) za pomocą kodu.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Uwierzytelniaj wywołania interfejsu API bez konieczności zmieniania kodu

Poniżej przedstawiono ogólne kroki tej metody:

1. Utwórz dwie tożsamości aplikacji Azure Active Directory (Azure AD): jedną dla aplikacji logiki i jedną dla aplikacji sieci Web (lub aplikacji interfejsu API).

2. Aby uwierzytelnić wywołania interfejsu API, Użyj poświadczeń (identyfikatora klienta i klucza tajnego) dla jednostki usługi, która jest skojarzona z tożsamością aplikacji usługi Azure AD dla aplikacji logiki.

3. Uwzględnij identyfikatory aplikacji w definicji aplikacji logiki.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Część 1. Tworzenie tożsamości aplikacji usługi Azure AD dla aplikacji logiki

Aplikacja logiki używa tej tożsamości aplikacji usługi Azure AD do uwierzytelniania w usłudze Azure AD. Wystarczy skonfigurować tę tożsamość tylko raz dla katalogu. Można na przykład wybrać używanie tej samej tożsamości dla wszystkich aplikacji logiki, nawet jeśli można utworzyć unikatowe tożsamości dla każdej aplikacji logiki. Można skonfigurować te tożsamości w Azure Portal lub użyć [programu PowerShell](#powershell).

**Utwórz tożsamość aplikacji dla aplikacji logiki w Azure Portal**

1. W [Azure Portal](https://portal.azure.com "https://portal.azure.com")wybierz pozycję **Azure Active Directory**. 

2. Upewnij się, że jesteś w tym samym katalogu, co aplikacja sieci Web lub aplikacja interfejsu API.

   > [!TIP]
   > Aby przełączyć katalogi, wybierz swój profil i wybierz inny katalog. Lub wybierz opcję   >  **katalog przełączników** przegląd.

3. W menu katalog w obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **rejestracja nowej aplikacji**.

   > [!TIP]
   > Domyślnie lista rejestracji aplikacji zawiera wszystkie rejestracje aplikacji w Twoim katalogu. Aby wyświetlić tylko rejestracje aplikacji, obok pola wyszukiwania wybierz pozycję **Moje aplikacje**. 

   ![Tworzenie rejestracji nowej aplikacji](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Nadaj nazwę tożsamości aplikacji, pozostaw **Typ aplikacji** ustawiony na **Web App/API**, podaj unikatowy ciąg sformatowany jako domenę dla **adresu URL logowania**, a następnie wybierz pozycję **Utwórz**.

   ![Podaj nazwę i adres URL logowania dla tożsamości aplikacji](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Tożsamość aplikacji utworzona dla aplikacji logiki zostanie teraz wyświetlona na liście rejestracje aplikacji.

   ![Tożsamość aplikacji dla aplikacji logiki](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Z listy rejestracje aplikacji wybierz swoją nową tożsamość aplikacji. Skopiuj i Zapisz **Identyfikator aplikacji** , który ma być używany jako "identyfikator klienta" aplikacji logiki w części 3.

   ![Kopiowanie i zapisywanie identyfikatora aplikacji dla aplikacji logiki](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Jeśli ustawienia tożsamości aplikacji nie są widoczne, wybierz pozycję **Ustawienia** lub **wszystkie ustawienia**.

7. W obszarze **dostęp do interfejsu API** wybierz pozycję **klucze**. W obszarze **Opis** Podaj nazwę klucza. W obszarze **wygaśnięcie** wybierz czas trwania dla klucza.

   Tworzony klucz działa jako "klucz tajny" tożsamości aplikacji lub hasło aplikacji logiki.

   ![Utwórz klucz dla tożsamości aplikacji logiki](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na pasku narzędzi wybierz pozycję **Zapisz**. W obszarze **wartość** zostanie wyświetlony klucz. 
**Pamiętaj o skopiowaniu i zapisaniu klucza** do późniejszego użycia, ponieważ klucz jest ukryty, gdy opuścisz stronę **klucze** .

   Podczas konfigurowania aplikacji logiki w części 3 należy określić ten klucz jako klucz tajny lub hasło.

   ![Kopiuj i Zapisz klucz do późniejszego](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Tworzenie tożsamości aplikacji dla aplikacji logiki w programie PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

To zadanie można wykonać za pomocą Azure Resource Manager za pomocą programu PowerShell. W programie PowerShell uruchom następujące polecenia:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Wprowadź hasło i naciśnij klawisz ENTER.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Pamiętaj o skopiowaniu **identyfikatora dzierżawy** (identyfikatora GUID dla dzierżawy usługi Azure AD), **identyfikatora aplikacji** i użytego hasła.

Aby uzyskać więcej informacji, Dowiedz się, jak [utworzyć jednostkę usługi przy użyciu programu PowerShell, aby uzyskać dostęp do zasobów](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Część 2. Tworzenie tożsamości aplikacji usługi Azure AD dla aplikacji sieci Web lub aplikacji interfejsu API

Jeśli aplikacja sieci Web lub aplikacja interfejsu API została już wdrożona, można włączyć uwierzytelnianie i utworzyć tożsamość aplikacji w Azure Portal. W przeciwnym razie podczas [wdrażania przy użyciu szablonu Azure Resource Manager można włączyć uwierzytelnianie](#authen-deploy). 

**Utwórz tożsamość aplikacji i Włącz uwierzytelnianie w Azure Portal dla wdrożonych aplikacji**

1. W [Azure Portal](https://portal.azure.com "https://portal.azure.com")Znajdź i wybierz aplikację sieci Web lub aplikację interfejsu API. 

2. W obszarze **Ustawienia** wybierz pozycję **uwierzytelnianie/autoryzacja**. W obszarze **uwierzytelnianie App Service** Włącz uwierzytelnianie **.** W obszarze **dostawcy uwierzytelniania** wybierz pozycję **Azure Active Directory**.

   ![Włącz uwierzytelnianie](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Teraz Utwórz tożsamość aplikacji dla aplikacji sieci Web lub aplikacji interfejsu API, jak pokazano poniżej. Na stronie **ustawienia Azure Active Directory** Ustaw **tryb zarządzania** na **Express**. Wybierz pozycję **Utwórz nową aplikację usługi AD**. Nadaj aplikacji nazwę, a następnie wybierz **przycisk OK**. 

   ![Tworzenie tożsamości aplikacji dla aplikacji sieci Web lub aplikacji interfejsu API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na stronie **Uwierzytelnianie/autoryzacja** wybierz pozycję **Zapisz**.

Teraz musisz znaleźć identyfikator klienta i identyfikator dzierżawy dla tożsamości aplikacji skojarzonej z aplikacją sieci Web lub aplikacją interfejsu API. Te identyfikatory są używane w części 3. Wykonaj następujące kroki, aby uzyskać Azure Portal.

**Znajdź identyfikator klienta i identyfikator dzierżawy tożsamości aplikacji dla aplikacji sieci Web lub aplikacji interfejsu API w Azure Portal**

1. W obszarze **dostawcy uwierzytelniania** wybierz pozycję **Azure Active Directory**. 

   ![Wybieranie pozycji „Azure Active Directory”](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na stronie **ustawienia Azure Active Directory** Ustaw **tryb zarządzania** na **Zaawansowane**.

3. Skopiuj **Identyfikator klienta** i Zapisz ten identyfikator GUID do użycia w części 3.

   > [!TIP] 
   > Jeśli **Identyfikator klienta** i **adres URL wystawcy** nie są wyświetlane, spróbuj odświeżyć Azure Portal i powtórz krok 1.

4. W obszarze **adres URL wystawcy**, skopiuj i Zapisz tylko identyfikator GUID dla części 3. W razie potrzeby można również użyć tego identyfikatora GUID w szablonie wdrożenia aplikacji sieci Web lub aplikacji interfejsu API.

   Ten identyfikator GUID jest identyfikatorem GUID określonej dzierżawy ("Identyfikator dzierżawy") i powinien pojawić się w tym adresie URL: `https://sts.windows.net/{GUID}`

5. Bez zapisywania zmian Zamknij stronę **ustawień Azure Active Directory** .

<a name="authen-deploy"></a>

**Włącz uwierzytelnianie podczas wdrażania przy użyciu szablonu Azure Resource Manager**

Nadal musisz utworzyć tożsamość aplikacji usługi Azure AD dla aplikacji sieci Web lub aplikacji interfejsu API, która różni się od tożsamości aplikacji dla aplikacji logiki. Aby utworzyć tożsamość aplikacji, wykonaj czynności opisane w części 2 dla Azure Portal. 

Możesz również wykonać czynności opisane w części 1, ale upewnij się, że korzystasz z aplikacji sieci Web lub rzeczywistej aplikacji interfejsu API `https://{URL}` dla **adresu URL logowania** i **identyfikatora aplikacji**. Z tych kroków należy zapisać identyfikator klienta i identyfikator dzierżawy do użycia w szablonie wdrożenia aplikacji, a także dla części 3.

> [!NOTE]
> Podczas tworzenia tożsamości aplikacji usługi Azure AD dla aplikacji sieci Web lub aplikacji interfejsu API należy użyć Azure Portal, a nie programu PowerShell. Program PowerShell polecenia cmdlet nie konfiguruje wymaganych uprawnień do podpisywania użytkowników w witrynie sieci Web.

Po otrzymaniu identyfikatora klienta i identyfikatora dzierżawy należy uwzględnić te identyfikatory jako zasób aplikacji sieci Web lub aplikacji interfejsu API w szablonie wdrożenia:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Aby automatycznie wdrożyć pustą aplikację sieci Web i aplikację logiki wraz z Azure Active Directory uwierzytelnianiem, [Zobacz kompletny szablon w tym miejscu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)lub kliknij pozycję **Wdróż na platformie Azure** tutaj:

[![Wdrażanie na platformie Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Część 3: wypełnienie sekcji autoryzacja w aplikacji logiki

Dla poprzedniego szablonu została już skonfigurowana Ta sekcja autoryzacji, ale jeśli tworzysz bezpośrednio aplikację logiki, musisz dołączyć sekcję pełna autoryzacja.

Otwórz definicję aplikacji logiki w widoku Kod, przejdź do definicji akcji **http** , Znajdź sekcję **autoryzacja** i Uwzględnij następujące właściwości:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Właściwość | Wymagane | Opis | 
| -------- | -------- | ----------- | 
| dzierżaw | Tak | Identyfikator GUID dzierżawy usługi Azure AD | 
| publiczn | Tak | Identyfikator GUID zasobu docelowego, do którego chcesz uzyskać dostęp, czyli identyfikator klienta z tożsamości aplikacji dla aplikacji sieci Web lub aplikacji interfejsu API | 
| clientId | Tak | Identyfikator GUID klienta żądającego dostępu, który jest IDENTYFIKATORem klienta z tożsamości aplikacji dla aplikacji logiki | 
| wpis tajny | Tak | Klucz lub hasło tożsamości aplikacji dla klienta żądającego tokenu dostępu | 
| typ | Tak | Typ uwierzytelniania. W przypadku uwierzytelniania ActiveDirectoryOAuth wartość jest równa `ActiveDirectoryOAuth` . | 
|||| 

Na przykład:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Bezpieczne wywołania interfejsu API za poorednictwem kodu

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Uwierzytelnianie certyfikatu

Aby zweryfikować przychodzące żądania z aplikacji logiki do aplikacji sieci Web lub aplikacji interfejsu API, można użyć certyfikatów klienta. Aby skonfigurować swój kod, Dowiedz się, [jak skonfigurować wzajemne uwierzytelnianie protokołu TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

W sekcji **autoryzacja** uwzględnij następujące właściwości:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Właściwość | Wymagane | Opis |
| -------- | -------- | ----------- |
| `type` | Tak | Typ uwierzytelniania. W przypadku certyfikatów klienta protokołu TLS/SSL wartość musi być równa `ClientCertificate` . |
| `password` | Nie | Hasło do uzyskiwania dostępu do certyfikatu klienta (plik PFX) |
| `pfx` | Tak | Zakodowana w formacie base64 zawartość certyfikatu klienta (plik PFX) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Aby zweryfikować przychodzące żądania z aplikacji logiki do aplikacji sieci Web lub aplikacji interfejsu API, możesz użyć uwierzytelniania podstawowego, takiego jak nazwa użytkownika i hasło. Uwierzytelnianie podstawowe jest typowym wzorcem i można użyć tego uwierzytelniania w dowolnym języku używanym do kompilowania aplikacji sieci Web lub aplikacji interfejsu API.

W sekcji **autoryzacja** uwzględnij następujące właściwości:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Właściwość | Wymagane | Opis | 
| -------- | -------- | ----------- | 
| typ | Tak | Typ uwierzytelniania, którego chcesz użyć. W przypadku uwierzytelniania podstawowego należy wykonać wartość `Basic` . | 
| nazwa użytkownika | Tak | Nazwa użytkownika, która ma być używana do uwierzytelniania | 
| hasło | Tak | Hasło, które ma być używane do uwierzytelniania | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Azure Active Directory uwierzytelniania za poorednictwem kodu

Domyślnie uwierzytelnianie usługi Azure AD, które można włączyć w Azure Portal nie zapewnia szczegółowej autoryzacji. Na przykład to uwierzytelnianie blokuje interfejs API tylko dla określonej dzierżawy, a nie do określonego użytkownika lub aplikacji. 

Aby ograniczyć dostęp API do aplikacji logiki za pomocą kodu, Wyodrębnij nagłówek, który ma token sieci Web JSON (JWT). Sprawdź tożsamość obiektu wywołującego i Odrzuć żądania, które nie są zgodne.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie i wywoływanie niestandardowych interfejsów API z przepływów pracy aplikacji logiki](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
