---
title: Zmienianie typu uwierzytelniania poddomeny przy użyciu programu PowerShell i programu Graph — Azure Active Directory | Microsoft Docs
description: Zmień domyślne ustawienia uwierzytelniania domeny podrzędnej dziedziczone z ustawień domeny głównej w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4acf01a6672d17e62b6ebf5c6f43c8d6145f95a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739318"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Zmienianie typu uwierzytelniania poddomeny w Azure Active Directory

Po dodaniu domeny głównej do usługi Azure Active Directory (Azure AD) wszystkie kolejne domeny podrzędne dodane do tego katalogu głównego w organizacji usługi Azure AD automatycznie dziedziczą ustawienie uwierzytelniania z domeny głównej. Jeśli jednak chcesz zarządzać ustawieniami uwierzytelniania domeny niezależnie od ustawień domeny głównej, możesz teraz używać interfejsu API Microsoft Graph API. Jeśli na przykład masz federacyjną domenę główną, taką jak contoso.com, ten artykuł może pomóc w zweryfikowaniu poddomeny, takiej jak child.contoso.com jako zarządzana, a nie federowana.

W portalu usługi Azure AD, gdy domena nadrzędna jest federowana, a  administrator próbuje zweryfikować zarządzaną poddomenę na stronie Niestandardowe nazwy domen, zostanie wyświetlany błąd "Nie można dodać domeny" z powodu "Co najmniej jedna właściwości zawiera nieprawidłowe wartości". Jeśli spróbujesz dodać tę poddomenę z centrum administracyjne platformy Microsoft 365, zostanie wyświetlony podobny błąd. Aby uzyskać więcej informacji o błędzie, zobacz [A child domain doesn't inherit parent domain changes in Office 365, Azure, or Intune](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)(Domena podrzędna nie dziedziczy zmian domeny nadrzędnej w usłudze Office 365, Azure lub Intune).

## <a name="how-to-verify-a-custom-subdomain"></a>Jak zweryfikować niestandardową poddomenę

Ponieważ domeny podrzędne dziedziczą domyślnie typ uwierzytelniania domeny głównej, należy poddomeny poddomeny w usłudze Azure AD promować przy użyciu usługi Microsoft Graph, aby można było ustawić żądany typ uwierzytelniania.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Dodawanie poddomeny i wyświetlanie jej typu uwierzytelniania

1. Użyj programu PowerShell, aby dodać nową poddomenę, która ma domyślny typ uwierzytelniania domeny głównej. Usługa Azure AD i Microsoft 365 administracyjne jeszcze nie obsługują tej operacji.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Użyj [Eksploratora programu Graph usługi Azure AD,](https://graphexplorer.azurewebsites.net) aby pobrać domenę. Ponieważ domena nie jest domeną główną, dziedziczy typ uwierzytelniania domeny głównej. Polecenie i wyniki mogą wyglądać następująco przy użyciu własnego identyfikatora dzierżawy:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Użyj interfejsu API eksploratora programu Graph usługi Azure AD, aby uczynić tę domenę główną

Użyj następującego polecenia, aby promować poddomenę:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Zmienianie typu uwierzytelniania poddomeny

1. Użyj następującego polecenia, aby zmienić typ uwierzytelniania poddomeny:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Sprawdź za pomocą metody GET w Eksploratorze programu Graph usługi Azure AD, czy typ uwierzytelniania poddomeny jest teraz zarządzany:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Następne kroki

- [Dodawanie niestandardowych nazw domen](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Zarządzanie nazwami domen](domains-manage.md)
- [ForceDelete niestandardową nazwę domeny przy użyciu interfejsu API Microsoft Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)