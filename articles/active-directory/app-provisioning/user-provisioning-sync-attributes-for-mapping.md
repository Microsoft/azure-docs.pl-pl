---
title: Synchronizuj atrybuty do Azure Active Directory na potrzeby mapowania
description: Podczas konfigurowania aprowizacji użytkowników przy użyciu aplikacji Azure Active Directory i SaaS należy użyć funkcji rozszerzenia katalogu w celu dodania atrybutów źródłowych, które nie są domyślnie synchronizowane.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: 102c0f7363b8d4f635762a33b82825e9ae71dfc6
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120796"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Synchronizowanie atrybutów rozszerzenia dla aprowizacji aplikacji

Azure Active Directory (Azure AD) musi zawierać wszystkie dane (atrybuty) wymagane do utworzenia profilu użytkownika podczas aprowizacji kont użytkowników z usługi Azure AD do [aplikacji SaaS](../saas-apps/tutorial-list.md). Podczas dostosowywania mapowań atrybutów na potrzeby aprowizacji użytkowników można znaleźć atrybut, który ma być mapowany, nie jest wyświetlany na liście **atrybutów źródłowych** . W tym artykule pokazano, jak dodać brakujący atrybut.

W przypadku użytkowników tylko w usłudze Azure AD można [tworzyć rozszerzenia schematów przy użyciu programu PowerShell lub Microsoft Graph](#create-an-extension-attribute-on-a-cloud-only-user).

W przypadku użytkowników w Active Directory lokalnym należy zsynchronizować użytkowników z usługą Azure AD. Użytkowników i atrybuty można synchronizować przy użyciu [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Azure AD Connect automatycznie synchronizuje niektóre atrybuty do usługi Azure AD, ale nie wszystkie atrybuty. Ponadto niektóre atrybuty (takie jak SAMAccountName), które są domyślnie synchronizowane, mogą nie być ujawnione przy użyciu interfejs API programu Graph usługi Azure AD. W takich przypadkach można [użyć funkcji rozszerzenia katalogu Azure AD Connect, aby zsynchronizować atrybut z usługą Azure AD](#create-an-extension-attribute-using-azure-ad-connect). Dzięki temu atrybut będzie widoczny dla usług Azure AD interfejs API programu Graph i usługi Azure AD Provisioning.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Utwórz atrybut rozszerzenia dla użytkownika tylko w chmurze
Możesz użyć Microsoft Graph i programu PowerShell, aby zwiększyć schemat użytkownika dla użytkowników w usłudze Azure AD. Te atrybuty rozszerzenia są automatycznie odnajdywane w większości przypadków.

Jeśli masz więcej niż 1000 jednostek usługi, na liście atrybutów źródłowych mogą znajdować się rozszerzenia. Jeśli utworzony atrybut nie jest automatycznie wyświetlany, sprawdź, czy atrybut został utworzony i dodaj go ręcznie do schematu. Aby sprawdzić, czy został on utworzony, użyj Microsoft Graph i [Eksploratora grafów](/graph/graph-explorer/graph-explorer-overview.md). Aby ręcznie dodać do schematu, zobacz [Edytowanie listy obsługiwanych atrybutów](customize-application-attributes.md#editing-the-list-of-supported-attributes).

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Utwórz atrybut rozszerzenia tylko dla użytkownika w chmurze przy użyciu Microsoft Graph
Możesz zwiększyć schemat użytkowników usługi Azure AD przy użyciu [Microsoft Graph](/graph/overview.md). 

Najpierw utwórz listę aplikacji w swojej dzierżawie, aby uzyskać identyfikator aplikacji, nad którą pracujesz. Aby dowiedzieć się więcej, zobacz [Lista extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Następnie Utwórz atrybut rozszerzenia. Zamień Właściwość **ID** poniżej na **Identyfikator** pobrany w poprzednim kroku. Musisz użyć atrybutu **"ID"** , a nie "AppID". Aby dowiedzieć się więcej, zobacz [Create extensionProperty]/Graph/API/Application-post-extensionproperty.MD? View = Graph-REST-1.0 karty&= http&Preserve-View = true).

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

Poprzednie żądanie spowodowało utworzenie atrybutu rozszerzenia w formacie `extension_appID_extensionName` . Teraz możesz zaktualizować użytkownika przy użyciu tego atrybutu rozszerzenia. Aby dowiedzieć się więcej, zobacz temat [Aktualizowanie użytkownika](/graph/api/user-update.md?view=graph-rest-1.0&tabs=http&preserve-view=true).
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Na koniec sprawdź atrybut dla użytkownika. Aby dowiedzieć się więcej, zobacz temat [pobieranie użytkownika](/graph/api/user-get.md?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Tworzenie atrybutu rozszerzenia tylko dla użytkownika w chmurze przy użyciu programu PowerShell
Utwórz niestandardowe rozszerzenie przy użyciu programu PowerShell i przypisz wartość do użytkownika. 

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

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Utwórz atrybut rozszerzenia przy użyciu Azure AD Connect

1. Otwórz Kreatora Azure AD Connect, wybierz pozycję zadania, a następnie wybierz pozycję **Dostosuj opcje synchronizacji**.

   ![Strona dodatkowych zadań kreatora Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Zaloguj się jako Administrator globalny usługi Azure AD. 

3. Na stronie **funkcje opcjonalne** wybierz pozycję **Synchronizacja atrybutów rozszerzenia katalogu**.
 
   ![Strona funkcji opcjonalnych kreatora Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Wybierz atrybuty, które chcesz zwiększyć do usługi Azure AD.
   > [!NOTE]
   > Wyszukiwanie w obszarze **dostępne atrybuty** uwzględnia wielkość liter.

   ![Zrzut ekranu przedstawiający stronę wyboru "rozszerzenia katalogu"](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Zakończ pracę kreatora Azure AD Connect i Zezwalaj na uruchomienie pełnego cyklu synchronizacji. Po zakończeniu cyklu schemat zostanie rozszerzony i nowe wartości są synchronizowane między lokalną usługą AD i usługą Azure AD.
 
6. W Azure Portal, podczas [edytowania mapowań atrybutów użytkowników](customize-application-attributes.md), lista **atrybutów źródłowych** będzie teraz zawierać dodany atrybut w formacie `<attributename> (extension_<appID>_<attributename>)` . Wybierz atrybut i zamapuj go do aplikacji docelowej w celu aprowizacji.

   ![Strona wyboru rozszerzeń katalogu kreatora Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Możliwość udostępniania atrybutów odwołań z lokalnej usługi AD, takich jak **zarządzane** lub **DN/odróżnionyname**, nie jest obecnie obsługiwana. Tę funkcję można zażądać na [głos użytkownika](https://feedback.azure.com/forums/169401-azure-active-directory). 


## <a name="next-steps"></a>Następne kroki

* [Zdefiniuj, kto jest w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
