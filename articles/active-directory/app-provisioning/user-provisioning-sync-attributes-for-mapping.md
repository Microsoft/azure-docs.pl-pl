---
title: Synchronizowanie atrybutów z usługą Azure AD na potrzeby mapowania
description: Dowiedz się, jak synchronizować atrybuty z Active Directory lokalnego z usługą Azure AD. Podczas konfigurowania aprowizacji użytkowników do aplikacji SaaS Użyj funkcji rozszerzenia katalogu, aby dodać atrybuty źródłowe, które nie są domyślnie synchronizowane.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.author: kenwith
ms.openlocfilehash: 0f8369c80a7a219b159f31aacb7d10a0dd009d00
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418678"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synchronizowanie atrybutu z Active Directory lokalnego z usługą Azure AD w celu aprowizacji aplikacji

Podczas dostosowywania mapowań atrybutów do aprowizacji użytkowników może się okazać, że atrybut, który ma być mapowany, nie jest wyświetlany na liście **atrybutów źródłowych** . W tym artykule pokazano, jak dodać brakujący atrybut przez zsynchronizowanie go z lokalnej Active Directory (AD) do Azure Active Directory (Azure AD).

Usługa Azure AD musi zawierać wszystkie dane wymagane do utworzenia profilu użytkownika podczas aprowizacji kont użytkowników z usługi Azure AD do aplikacji SaaS. W niektórych przypadkach, aby udostępnić dane, może być konieczne zsynchronizowanie atrybutów z lokalnej usługi AD do usługi Azure AD. Azure AD Connect automatycznie synchronizuje niektóre atrybuty do usługi Azure AD, ale nie wszystkie atrybuty. Ponadto niektóre atrybuty (takie jak SAMAccountName), które są domyślnie synchronizowane, mogą nie być ujawnione przy użyciu interfejsu API Microsoft Graph. W takich przypadkach można użyć funkcji rozszerzenia katalogu Azure AD Connect, aby zsynchronizować atrybut z usługą Azure AD. Dzięki temu atrybut będzie widoczny dla interfejsu API Microsoft Graph i usługi Azure AD Provisioning.

Jeśli dane potrzebne do aprowizacji są w Active Directory ale nie są dostępne do aprowizacji z powodów opisanych powyżej, można użyć Azure AD Connect lub programu PowerShell do utworzenia atrybutów rozszerzenia. 
 
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

## <a name="create-an-extension-attribute-using-powershell"></a>Tworzenie atrybutu rozszerzenia przy użyciu programu PowerShell
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
