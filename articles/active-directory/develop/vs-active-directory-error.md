---
title: Diagnozowanie błędów za pomocą usługi połączonej z usługą Azure AD (Visual Studio)
description: Usługa połączona z usługą Active Directory wykryła niezgodny typ uwierzytelniania
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 7b437e3117540719c8c0adc5701ac1a5e934340b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88114476"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnozowanie błędów za pomocą połączonej usługi Azure Active Directory

Podczas wykrywania poprzedniego kodu uwierzytelniania usługa połączona Azure Active Directory wykryła niezgodny typ uwierzytelniania.

Aby poprawnie wykryć poprzedni kod uwierzytelniania w projekcie, należy ponownie skompilować projekt. Jeśli widzisz ten błąd i nie masz poprzedniego kodu uwierzytelniania w projekcie, Odbuduj i spróbuj ponownie.

## <a name="project-types"></a>Typy projektów

Połączona usługa sprawdza typ projektu, który jest opracowywany, dzięki czemu może wstrzyknąć właściwą logikę uwierzytelniania do projektu. Jeśli istnieje jakikolwiek kontroler pochodzący z `ApiController` projektu, projekt jest traktowany jako projekt WebApi. Jeśli istnieją tylko kontrolery, które pochodzą z `MVC.Controller` projektu, projekt jest traktowany jako projekt MVC. Połączona usługa nie obsługuje żadnego innego typu projektu.

## <a name="compatible-authentication-code"></a>Zgodny kod uwierzytelniania

Usługa połączona sprawdza również ustawienia uwierzytelniania, które zostały wcześniej skonfigurowane lub są zgodne z usługą. Jeśli wszystkie ustawienia są obecne, jest ono traktowane jako ponowny przypadek i zostanie otwarta usługa połączona.  Jeśli są obecne tylko niektóre ustawienia, jest ono traktowane jako przypadek błędu.

W projekcie MVC usługa połączona sprawdza następujące ustawienia, które wynikają z wcześniejszego użycia usługi:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:AADInstance" value="" />
<add key="ida:PostLogoutRedirectUri" value="" />
```

Ponadto połączona usługa sprawdza następujące ustawienia w projekcie interfejsu API sieci Web, które wynikają z wcześniejszego użycia usługi:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:Audience" value="" />
```

## <a name="incompatible-authentication-code"></a>Niezgodny kod uwierzytelniania

Na koniec usługa połączona próbuje wykryć wersje kodu uwierzytelniania, które zostały skonfigurowane z poprzednimi wersjami programu Visual Studio. Jeśli wystąpił ten błąd, oznacza to, że projekt zawiera niezgodny typ uwierzytelniania. Połączona usługa wykrywa następujące typy uwierzytelniania z poprzednich wersji programu Visual Studio:

* Uwierzytelnianie systemu Windows
* Indywidualne konta użytkowników
* Konta organizacyjne

Aby wykryć uwierzytelnianie systemu Windows w projekcie MVC, podłączany szuka `authentication` elementu w `web.config` pliku.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Aby wykryć uwierzytelnianie systemu Windows w projekcie interfejsu API sieci Web, połączona usługa szuka `IISExpressWindowsAuthentication` elementu w `.csproj` pliku projektu:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Aby wykryć uwierzytelnianie poszczególnych kont użytkowników, połączona usługa szuka elementu pakietu w `packages.config` pliku.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Aby wykryć starą formę uwierzytelniania konta organizacji, połączona usługa szuka następującego elementu w `web.config` :

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Aby zmienić typ uwierzytelniania, Usuń niezgodny typ uwierzytelniania i spróbuj ponownie dodać podłączoną usługę.

Aby uzyskać więcej informacji, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](./authentication-vs-authorization.md).