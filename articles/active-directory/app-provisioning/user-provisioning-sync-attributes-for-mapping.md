---
title: Synchronizowanie atrybutów do Azure Active Directory mapowania
description: Podczas konfigurowania aprowizowania użytkowników za pomocą Azure Active Directory i aplikacji SaaS użyj funkcji rozszerzenia katalogu, aby dodać atrybuty źródłowe, które nie są domyślnie synchronizowane.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: f7a2429161cebe867d844b4ca7aa08ec3613edcd
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388214"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Synchronizowanie atrybutów rozszerzenia na celu aprowizowanie aplikacji

Azure Active Directory (Azure AD) musi zawierać wszystkie dane (atrybuty) wymagane do utworzenia profilu użytkownika podczas aprowizowania kont użytkowników z usługi Azure AD w aplikacji [SaaS.](../saas-apps/tutorial-list.md) Podczas dostosowywania mapowań atrybutów dla aprowizacji użytkowników atrybut, który chcesz zmapować, może nie pojawiać się na **liście Atrybut** źródłowy. W tym artykule pokazano, jak dodać brakujący atrybut.

W przypadku użytkowników tylko w usłudze Azure AD można tworzyć rozszerzenia schematu przy użyciu [programu PowerShell](#create-an-extension-attribute-on-a-cloud-only-user)lub Microsoft Graph .

W przypadku użytkowników lokalna usługa Active Directory należy zsynchronizować użytkowników z usługą Azure AD. Użytkowników i atrybuty można synchronizować przy użyciu [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Azure AD Connect automatycznie synchronizuje niektóre atrybuty z usługą Azure AD, ale nie wszystkie atrybuty. Ponadto niektóre atrybuty (takie jak SAMAccountName), które są domyślnie synchronizowane, mogą nie być widoczne przy użyciu usługi Azure AD interfejs Graph API. W takich przypadkach można użyć funkcji rozszerzenia katalogu Azure AD Connect, aby zsynchronizować atrybut [z usługą Azure AD.](#create-an-extension-attribute-using-azure-ad-connect) Dzięki temu atrybut będzie widoczny dla usługi Azure AD interfejs Graph API i usługi aprowowania Azure AD.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Tworzenie atrybutu rozszerzenia dla użytkownika tylko w chmurze
Za pomocą programu Microsoft Graph i programu PowerShell można rozszerzyć schemat użytkownika dla użytkowników w usłudze Azure AD. Te atrybuty rozszerzenia są automatycznie wykrywane w większości przypadków.

Jeśli masz więcej niż 1000 jednostki usługi, możesz znaleźć brakujące rozszerzenia na liście atrybutów źródłowych. Jeśli utworzony atrybut nie jest automatycznie wyświetlany, sprawdź, czy atrybut został utworzony, i dodaj go ręcznie do schematu. Aby sprawdzić, czy został on utworzony, użyj programu Microsoft Graph i [Eksploratora programu Graph.](/graph/graph-explorer/graph-explorer-overview) Aby dodać go ręcznie do schematu, zobacz [Edytowanie listy obsługiwanych atrybutów.](customize-application-attributes.md#editing-the-list-of-supported-attributes)

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Tworzenie atrybutu rozszerzenia dla użytkownika tylko w chmurze przy użyciu Microsoft Graph
Schemat użytkowników usługi Azure AD można rozszerzyć przy użyciu [Microsoft Graph](/graph/overview). 

Najpierw należy wyświetlić listę aplikacji w dzierżawie, aby uzyskać identyfikator aplikacji, nad która pracujesz. Aby dowiedzieć się więcej, zobacz [List extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Następnie utwórz atrybut rozszerzenia. Zastąp właściwość **ID** poniżej identyfikatorem **pobranym** w poprzednim kroku. Musisz użyć atrybutu **"ID",** a nie atrybutu "appId". Aby dowiedzieć się więcej, zobacz [Create extensionProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

Poprzednie żądanie utworzyło atrybut rozszerzenia w formacie `extension_appID_extensionName` . Teraz możesz zaktualizować użytkownika za pomocą tego atrybutu rozszerzenia. Aby dowiedzieć się więcej, zobacz [Aktualizowanie użytkownika](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true).
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Na koniec sprawdź atrybut użytkownika. Aby dowiedzieć się więcej, zobacz [Get a user (Uzyskiwanie użytkownika).](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true)

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Tworzenie atrybutu rozszerzenia dla użytkownika tylko w chmurze przy użyciu programu PowerShell
Utwórz rozszerzenie niestandardowe przy użyciu programu PowerShell i przypisz wartość do użytkownika. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Tworzenie atrybutu rozszerzenia przy użyciu Azure AD Connect

1. Otwórz kreatora Azure AD Connect, wybierz pozycję Zadania, a następnie wybierz pozycję **Dostosuj opcje synchronizacji.**

   ![Azure Active Directory Connect strony Dodatkowe zadania kreatora](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Zaloguj się jako administrator globalny usługi Azure AD. 

3. Na stronie **Funkcje opcjonalne** wybierz pozycję **Synchronizacja atrybutów rozszerzenia katalogu.**
 
   ![Azure Active Directory Connect strony Funkcje opcjonalne](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Wybierz atrybuty, które chcesz rozszerzyć na usługę Azure AD.
   > [!NOTE]
   > W wyszukiwaniu **w obszarze Dostępne atrybuty jest** zróżnicowana wielkość liter.

   ![Zrzut ekranu przedstawiający stronę wyboru "Rozszerzenia katalogów"](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Zakończ proces Azure AD Connect i zezwlij na uruchomienie pełnego cyklu synchronizacji. Po zakończeniu cyklu schemat zostanie rozszerzony, a nowe wartości zostaną zsynchronizowane między lokalną usługą AD i usługą Azure AD.
 
6. W Azure Portal podczas edytowania [mapowań](customize-application-attributes.md)atrybutów użytkownika  lista atrybutu źródłowego będzie teraz zawierać dodany atrybut w formacie `<attributename> (extension_<appID>_<attributename>)` . Wybierz atrybut i zamapuj go na aplikację docelową na celu aprowizowanie.

   ![Azure Active Directory Connect kreatora Wybór rozszerzeń katalogów](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Możliwość aprowizowania atrybutów odwołania z lokalnej usługi AD, takich jak **managedby** lub **DN/DistinguishedName,** nie jest obecnie obsługiwana. Możesz poprosić o tę funkcję w [głosie użytkownika.](https://feedback.azure.com/forums/169401-azure-active-directory) 


## <a name="next-steps"></a>Następne kroki

* [Definiowanie zakresu aprowizowania](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
