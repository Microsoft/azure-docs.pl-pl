---
title: Zmień typ uwierzytelniania poddomeny za pomocą programu PowerShell i grafu Azure Active Directory | Microsoft Docs
description: Zmień domyślne ustawienia uwierzytelniania poddomen dziedziczone z ustawień domeny głównej w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 734e6824f13e62ad080500eff18c4892e1f76807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95503664"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Zmień typ uwierzytelniania poddomen w Azure Active Directory

Po dodaniu domeny głównej do Azure Active Directory (Azure AD) wszystkie kolejne domeny podrzędne dodane do tego katalogu głównego w organizacji usługi Azure AD automatycznie dziedziczą ustawienie uwierzytelniania z domeny głównej. Jeśli jednak chcesz zarządzać ustawieniami uwierzytelniania domeny niezależnie od ustawień domeny głównej, możesz teraz za pomocą interfejsu API Microsoft Graph. Na przykład jeśli masz domenę główną Federacji, taką jak contoso.com, ten artykuł może pomóc w sprawdzeniu poddomeny, takiej jak child.contoso.com jako zarządzana, a nie Federacji.

W portalu usługi Azure AD, gdy domena nadrzędna jest federacyjna, a administrator próbuje zweryfikować zarządzaną poddomenę na stronie **niestandardowe nazwy domeny** , zostanie wyświetlony komunikat o błędzie "nie można dodać domeny" z powodu "co najmniej jedna właściwość zawiera nieprawidłowe wartości". Jeśli spróbujesz dodać tę poddomenę z centrum administracyjnego Microsoft 365, zostanie wyświetlony podobny błąd. Aby uzyskać więcej informacji o błędzie, zobacz [domena podrzędna nie dziedziczy zmian domeny nadrzędnej w pakiecie Office 365, Azure lub Intune](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes).

## <a name="how-to-verify-a-custom-subdomain"></a>Jak zweryfikować niestandardową poddomenę

Ponieważ domeny podrzędne domyślnie dziedziczą typ uwierzytelniania domeny katalogu głównego, należy podnieść poddomenę do domeny głównej w usłudze Azure AD przy użyciu Microsoft Graph, aby można było ustawić typ uwierzytelniania na żądany typ.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Dodaj poddomenę i Wyświetl jej typ uwierzytelniania

1. Użyj programu PowerShell, aby dodać nową poddomenę, która ma domyślny typ uwierzytelniania domeny głównej. Centrum administracyjne usługi Azure AD i Microsoft 365 nie obsługują jeszcze tej operacji.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Użyj [Eksploratora Azure AD Graph](https://graphexplorer.azurewebsites.net) do pobrania domeny. Ponieważ domena nie jest domeną główną, dziedziczy typ uwierzytelniania domeny głównej. Twoje polecenie i wyniki mogą wyglądać w następujący sposób przy użyciu własnego identyfikatora dzierżawy:

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

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Użyj interfejsu API Eksploratora usługi Azure AD Graph, aby utworzyć tę domenę główną

Użyj następującego polecenia, aby podwyższyć poziom poddomeny:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Zmień typ uwierzytelniania poddomeny

1. Aby zmienić typ uwierzytelniania poddomeny, użyj następującego polecenia:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Sprawdź przy użyciu funkcji GET w Eksploratorze grafów usługi Azure AD, że obecnie jest zarządzany typ uwierzytelniania domeny podrzędnej:

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
- [ForceDelete niestandardową nazwę domeny za pomocą interfejsu API Microsoft Graph](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)