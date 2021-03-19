---
title: Synchronizowanie atrybutów z usługą Azure AD na potrzeby mapowania
description: Podczas konfigurowania aprowizacji użytkowników do aplikacji SaaS Użyj funkcji rozszerzenia katalogu, aby dodać atrybuty źródłowe, które nie są domyślnie synchronizowane.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 52f34cdafac76a9bca2b4ff0b00e0b3efaa63f5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579437"
---
# <a name="syncing-extension-attributes-attributes"></a>Synchronizowanie atrybutów atrybutów rozszerzenia

Podczas dostosowywania mapowań atrybutów do aprowizacji użytkowników może się okazać, że atrybut, który ma być mapowany, nie jest wyświetlany na liście **atrybutów źródłowych** . W tym artykule pokazano, jak dodać brakujący atrybut przez zsynchronizowanie go z lokalnego Active Directory (AD) do Azure Active Directory (Azure AD) lub przez utworzenie atrybutów rozszerzenia w usłudze Azure AD tylko dla użytkownika w chmurze. 

Usługa Azure AD musi zawierać wszystkie dane wymagane do utworzenia profilu użytkownika podczas aprowizacji kont użytkowników z usługi Azure AD do aplikacji SaaS. W niektórych przypadkach, aby udostępnić dane, może być konieczne zsynchronizowanie atrybutów z lokalnej usługi AD do usługi Azure AD. Azure AD Connect automatycznie synchronizuje niektóre atrybuty do usługi Azure AD, ale nie wszystkie atrybuty. Ponadto niektóre atrybuty (takie jak SAMAccountName), które są domyślnie synchronizowane, mogą nie być ujawnione przy użyciu interfejs API programu Graph usługi Azure AD. W takich przypadkach można użyć funkcji rozszerzenia katalogu Azure AD Connect, aby zsynchronizować atrybut z usługą Azure AD. Dzięki temu atrybut będzie widoczny dla usług Azure AD interfejs API programu Graph i usługi Azure AD Provisioning. Jeśli dane potrzebne do aprowizacji są w Active Directory ale nie są dostępne do aprowizacji z powodów opisanych powyżej, można użyć Azure AD Connect do tworzenia atrybutów rozszerzenia. 

Chociaż większość użytkowników jest prawdopodobnie hybrydowymi użytkownikami synchronizowanymi z Active Directory, można także tworzyć rozszerzenia dla użytkowników tylko w chmurze bez korzystania z Azure AD Connect. Za pomocą programu PowerShell lub Microsoft Graph można zwiększyć schemat tylko dla użytkownika w chmurze. 

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

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Utwórz atrybut rozszerzenia dla użytkownika tylko w chmurze
Aby zwiększyć schemat użytkownika, klienci mogą używać Microsoft Graph i programu PowerShell. Te atrybuty rozszerzenia są automatycznie odnajdywane w większości przypadków, ale klienci z więcej niż 1000 jednostkami usługi mogą znaleźć rozszerzenia na liście atrybutów źródłowych. Jeśli atrybut tworzony przy użyciu poniższych kroków nie zostanie automatycznie wyświetlony na liście atrybutów źródłowych, sprawdź przy użyciu programu Graph, że atrybut rozszerzenia został pomyślnie utworzony, a następnie dodaj go do schematu [ręcznie](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes). Podczas wykonywania poniższych czynności grafu kliknij pozycję Dowiedz się więcej, aby sprawdzić uprawnienia wymagane do żądania. Aby wykonać żądania, można użyć [Eksploratora grafów](https://docs.microsoft.com/graph/graph-explorer/graph-explorer-overview) . 

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Utwórz atrybut rozszerzenia tylko dla użytkownika w chmurze przy użyciu Microsoft Graph
Aby zwiększyć schemat użytkowników, należy użyć aplikacji. Utwórz listę aplikacji w swojej dzierżawie, aby zidentyfikować identyfikator aplikacji, która ma zostać użyta do rozbudowy schematu użytkownika. [Dowiedz się więcej.](https://docs.microsoft.com/graph/api/application-list?view=graph-rest-1.0&tabs=http)

```json
GET https://graph.microsoft.com/v1.0/applications
```

Utwórz atrybut rozszerzenia. Zamień Właściwość **ID** poniżej na **Identyfikator** pobrany w poprzednim kroku. Musisz użyć atrybutu **"ID"** , a nie "AppID". [Dowiedz się więcej.](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)
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

Poprzednie żądanie spowodowało utworzenie atrybutu rozszerzenia o formacie "extension_appID_extensionName". Zaktualizuj użytkownika przy użyciu atrybutu rozszerzenia. [Dowiedz się więcej.](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0&tabs=http)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Sprawdź użytkownika, aby upewnić się, że atrybut został pomyślnie zaktualizowany. [Dowiedz się więcej.](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select)

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

#Set a value for the extension property on the user. Replace the objectid with the id of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="next-steps"></a>Następne kroki

* [Zdefiniuj, kto jest w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
