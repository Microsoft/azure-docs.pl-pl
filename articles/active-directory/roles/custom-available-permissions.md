---
title: Uprawnienia roli niestandardowej do rejestracji aplikacji — Azure AD | Microsoft Docs
description: Delegowanie uprawnień roli administratora niestandardowego do zarządzania rejestracjami aplikacji.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af2c1b912afbcf44cefbfb021c592836dbde5b8
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466290"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Uprawnienia rejestracji aplikacji dla ról niestandardowych w Azure Active Directory

Ten artykuł zawiera aktualnie dostępne uprawnienia rejestracji aplikacji dla niestandardowych definicji ról w Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-tenant-applications"></a>Uprawnienia do zarządzania aplikacjami z jedną dzierżawą

Podczas wybierania uprawnień roli niestandardowej można udzielić dostępu do zarządzania tylko aplikacjami z jedną dzierżawą. aplikacje z jedną dzierżawą są dostępne tylko dla użytkowników w organizacji usługi Azure AD, w których zarejestrowano aplikację. aplikacje o pojedynczej dzierżawie są zdefiniowane jako mające **obsługiwane typy kont** ustawione na "tylko konta w tym katalogu organizacji". W interfejs API programu Graph aplikacje z jedną dzierżawą mają właściwość signInAudience ustawioną na wartość "AzureADMyOrg".

Aby udzielić dostępu do zarządzania tylko aplikacjami z jedną dzierżawą, Użyj poniższych uprawnień z aplikacjami podtype **. weborganization**. Na przykład Microsoft. Directory/Applications. weborganization/Basic/Update.

Zobacz [Omówienie ról niestandardowych](custom-overview.md) , aby uzyskać informacje na temat tego, co oznaczają ogólne warunki podtype, uprawnienie i właściwość. Poniższe informacje dotyczą rejestracji aplikacji.

### <a name="create-and-delete"></a>Tworzenie i usuwanie

Dostępne są dwa uprawnienia do przyznawania możliwości tworzenia rejestracji aplikacji, z których każdy ma inne zachowanie:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>Microsoft. Directory/Applications/createAsOwner

Przypisanie wyników tego uprawnienia do twórcy zostanie dodany jako pierwszy właściciel utworzonej rejestracji aplikacji, a rejestracja utworzonej aplikacji będzie liczona względem przydziału 250 obiektów utworzonych przez twórcę.

#### <a name="microsoftdirectoryapplicationscreate"></a>Microsoft. Directory/Applications/Create

Przypisanie wyników tego uprawnienia do twórcy nie jest dodawany jako pierwszy właściciel utworzonej rejestracji aplikacji, a rejestracja utworzonej aplikacji nie będzie uwzględniać przydziału utworzonych obiektów 250 dla twórcy. Należy dokładnie użyć tego uprawnienia, ponieważ nie ma możliwości uniemożliwienia sobie tworzenia rejestracji aplikacji, dopóki nie zostanie osiągnięty limit przydziału na poziomie katalogu. Jeśli przypisane są oba uprawnienia, ma to pierwszeństwo.

Jeśli przypisane są oba uprawnienia, pierwszeństwo ma uprawnienie/CREATE. Mimo że uprawnienie/createAsOwner nie dodaje tego twórcy jako pierwszego właściciela, właściciele mogą zostać określeni podczas tworzenia rejestracji aplikacji podczas korzystania z interfejsów API programu Graph lub poleceń cmdlet środowiska PowerShell.

Utwórz uprawnienia Udziel dostępu do **nowego polecenia rejestracji** .

[Te uprawnienia zapewniają dostęp do nowego portalu rejestracji](./media/custom-available-permissions/new-custom-role.png)

Dostępne są dwa uprawnienia do przyznawania możliwości usuwania rejestracji aplikacji:

#### <a name="microsoftdirectoryapplicationsdelete"></a>Microsoft. katalog/aplikacje/usuwanie

Przyznaje możliwość usuwania rejestracji aplikacji niezależnie od podtypu; oznacza to zarówno aplikacje z jedną dzierżawą, jak i wieloma dzierżawcami.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. weborganization/Delete

Umożliwia usunięcie rejestracji aplikacji ograniczonych do tych, które są dostępne tylko dla kont w organizacji lub aplikacji z jedną dzierżawą (podtyp organizacji).

![Uprawnienia te umożliwiają dostęp do polecenia Usuń rejestrację aplikacji](./media/custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Podczas przypisywania roli, która zawiera uprawnienia do tworzenia, przypisanie roli należy wykonać w zakresie katalogu. Uprawnienie CREATE przypisane do zakresu zasobów nie pozwala na tworzenie rejestracji aplikacji.

### <a name="read"></a>Odczyt

Wszyscy użytkownicy będący członkami w organizacji mogą domyślnie odczytywać informacje o rejestracji aplikacji. Jednak użytkownicy-Goście i podmioty usługi aplikacji nie mogą. Jeśli planujesz przypisanie roli do użytkownika lub aplikacji gościa, musisz dołączyć odpowiednie uprawnienia do odczytu.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/Applications/allProperties/Read

Możliwość odczytywania wszystkich właściwości aplikacji z jedną dzierżawą i wielodostępności poza właściwościami, których nie można odczytać w żadnej sytuacji, takiej jak poświadczenia.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. weborganization/allProperties/Read

Przyznaje takie same uprawnienia jak Microsoft. Directory/Applications/allProperties/Read, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/Applications/Owners/Read

Przyznaje możliwość odczytywania właściwości właściciele w aplikacjach z jedną dzierżawą i wieloma dzierżawcami. Przyznaje dostęp do wszystkich pól na stronie właściciele rejestracji aplikacji:

![Te uprawnienia udzielają dostępu do strony właściciele rejestracji aplikacji](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>Microsoft. Directory/Applications/Standard/Read

Przyznaje dostęp do odczytu standardowych właściwości rejestracji aplikacji. Obejmuje to właściwości na stronach rejestracji aplikacji.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. weborganization/Standard/Read

Przyznaje takie same uprawnienia jak Microsoft. Directory/Applications/Standard/Read, ale tylko w przypadku aplikacji z jedną dzierżawą.

### <a name="update"></a>Aktualizacja

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/Applications/allProperties/Update

Możliwość aktualizowania wszystkich właściwości w aplikacjach z jedną dzierżawą i wieloma dzierżawcami.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. weborganization/allProperties/Update

Przyznaje takie same uprawnienia, jak Microsoft. Directory/Applications/allProperties/Update, ale tylko dla aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>Microsoft. katalog/aplikacje/odbiorcy/aktualizacja

Możliwość aktualizacji właściwości obsługiwanego typu konta (signInAudience) dla aplikacji z jedną dzierżawą i wieloma dzierżawcami.

![To uprawnienie udziela dostęp do właściwości typu konta obsługiwana Rejestracja aplikacji na stronie uwierzytelniania](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. katalog/aplikacje. weborganization/odbiorcy/aktualizacja

Przyznaje takie same uprawnienia, jak Microsoft. Directory/Applications/odbiorca/aktualizacja, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja

Możliwość aktualizowania adresu URL odpowiedzi, adresu URL logowania, przepływu niejawnego i właściwości domeny wydawcy w aplikacjach z jedną dzierżawą i wieloma dzierżawcami. Przyznaje dostęp do wszystkich pól na stronie uwierzytelniania rejestracji aplikacji, z wyjątkiem typów obsługiwanych kont:

![Udziela dostępu do uwierzytelniania rejestracji aplikacji, ale nie obsługiwanych typów kont](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. weborganization/Authentication/Update

Przyznaje takie same uprawnienia, jak Microsoft. Directory/Applications/Authentication/Update, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>Microsoft. Directory/Applications/Basic/Update

Możliwość aktualizacji nazwy, logo, adresu URL strony głównej, adresu URL warunków użytkowania oraz właściwości adresu URL zasad zachowania poufności informacji w aplikacjach z jedną dzierżawą i wieloma dzierżawcami. Przyznaje dostęp do wszystkich pól na stronie znakowania rejestracji aplikacji:

![To uprawnienie udziela dostępu do strony znakowania rejestracji aplikacji](./media/custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. weborganization/Basic/Update

Przyznaje takie same uprawnienia jak Microsoft. Directory/Applications/Basic/Update, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>Microsoft. Directory/Applications/Credentials/Update

Możliwość aktualizowania certyfikatów i właściwości tajnych klienta w aplikacjach z jedną dzierżawą i wieloma dzierżawcami. Przyznaje dostęp do wszystkich pól na stronie certyfikatów rejestracji aplikacji & Secret:

![To uprawnienie udziela dostępu do certyfikatów rejestracji aplikacji & strony kluczy tajnych](./media/custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. weborganization/Credentials/Update

Przyznaje takie same uprawnienia, jak Microsoft. Directory/Applications/Credentials/Update, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>Microsoft. Directory/Applications/Owners/Update

Możliwość aktualizowania właściwości Owner w ramach jednej dzierżawy i wielu dzierżawców. Przyznaje dostęp do wszystkich pól na stronie właściciele rejestracji aplikacji:

![Te uprawnienia udzielają dostępu do strony właściciele rejestracji aplikacji](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. weborganizations/Owners/Update

Przyznaje takie same uprawnienia jak Microsoft. Directory/Applications/Owners/Update, ale tylko w przypadku aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>Microsoft. Directory/Applications/Permissions/Update

Możliwość aktualizowania uprawnień delegowanych, uprawnień aplikacji, autoryzowanych aplikacji klienckich, wymaganych uprawnień i przyznawania właściwości zgody w aplikacjach z jedną dzierżawą i wieloma dzierżawcami. Nie przyznaje możliwości zgody. Przyznaje dostęp do wszystkich pól w uprawnieniach interfejsu API rejestracji aplikacji i uwidacznia strony interfejsu API:

![Te uprawnienia udzielają dostępu do strony uprawnień interfejsu API rejestracji aplikacji](./media/custom-available-permissions/app-registration-api-permissions.png)

![Te uprawnienia udzielają dostępu do rejestracji aplikacji Udostępnianie strony interfejsu API](./media/custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. katalog/aplikacje. weborganizations/Permissions/Update

Przyznaje takie same uprawnienia, jak Microsoft. Directory/Applications/Permissions/Update, ale tylko w przypadku aplikacji z jedną dzierżawą.

## <a name="required-license-plan"></a>Wymagany plan licencji

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Następne kroki

- Tworzenie ról niestandardowych przy użyciu [Azure Portal, programu Azure AD PowerShell i interfejs API programu Graph](custom-create.md)
- [Lista przypisań ról](view-assignments.md)
