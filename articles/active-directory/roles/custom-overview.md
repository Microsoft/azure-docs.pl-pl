---
title: Przegląd kontroli dostępu opartej na rolach Azure Active Directory (RBAC)
description: Dowiedz się, jak zrozumieć części przypisania roli i ograniczonego zakresu w Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fad2c683890776908afbfbf15ee91d46d564783
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466766"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>Omówienie kontroli dostępu opartej na rolach w Azure Active Directory

W tym artykule opisano sposób zrozumienia kontroli dostępu opartej na rolach w usłudze Azure Active Directory (Azure AD). Role usługi Azure AD umożliwiają przyznawanie szczegółowych uprawnień administratorom, parze zgodnie z zasadą najniższych uprawnień. Wbudowana i niestandardowa rola usługi Azure AD działa w oparciu o koncepcje podobne do tych, które znajdują się w [systemie kontroli dostępu opartej na rolach dla zasobów platformy Azure](../../role-based-access-control/overview.md) (Role platformy Azure). [Różnica między tymi dwoma systemami kontroli dostępu oparta na rolach](../../role-based-access-control/rbac-and-directory-admin-roles.md) jest następująca:

- Role usługi Azure AD kontrolują dostęp do zasobów usługi Azure AD, takich jak użytkownicy, grupy i aplikacje, przy użyciu interfejs API programu Graph
- Role platformy Azure kontrolują dostęp do zasobów platformy Azure, takich jak maszyny wirtualne lub magazyn przy użyciu usługi Azure Resource Management

Oba systemy zawierają podobne definicje ról i przypisania ról. Nie można jednak używać uprawnień roli usługi Azure AD w rolach niestandardowych platformy Azure i na odwrót.

## <a name="understand-azure-ad-role-based-access-control"></a>Omówienie kontroli dostępu opartej na rolach w usłudze Azure AD
Usługa Azure AD obsługuje dwa typy definicji ról — 
* [Role wbudowane](./permissions-reference.md)
* [Role niestandardowe](./custom-create.md)

Wbudowane role to poza Box role, które mają stały zestaw uprawnień. Nie można modyfikować tych definicji ról. Istnieje wiele [wbudowanych ról](./permissions-reference.md) obsługiwanych przez usługę Azure AD, a lista rośnie. Aby zaokrąglić krawędzie i spełnić Twoje zaawansowane wymagania, usługa Azure AD obsługuje również [role niestandardowe](./custom-create.md). Przyznawanie uprawnień przy użyciu niestandardowych ról usługi Azure AD to dwuetapowy proces, który polega na utworzeniu niestandardowej definicji roli, a następnie przypisaniu jej przy użyciu przypisania roli. Niestandardowa definicja roli jest kolekcją uprawnień dodawanych z listy wstępnie zdefiniowanej. Te uprawnienia są te same uprawnienia, które są używane w rolach wbudowanych.  

Po utworzeniu niestandardowej definicji roli (lub przy użyciu wbudowanej roli) można przypisać ją do użytkownika przez utworzenie przypisania roli. Przypisanie roli przyzna użytkownikowi uprawnienia w definicji roli w określonym zakresie. Ten dwuetapowy proces umożliwia utworzenie pojedynczej definicji roli i przypisanie jej wiele razy w różnych zakresach. Zakres definiuje zbiór zasobów usługi Azure AD, do których członek roli ma dostęp. Najbardziej typowym zakresem jest zakres całej organizacji (w całej sieci). Rolę niestandardową można przypisać w zakresie całej organizacji, co oznacza, że członek roli ma uprawnienia roli do wszystkich zasobów w organizacji. Rolę niestandardową można także przypisać do zakresu obiektu. Przykładem zakresu obiektu jest pojedyncza aplikacja. Tę samą rolę można przypisać do jednego użytkownika przez wszystkie aplikacje w organizacji, a następnie do innego użytkownika z zakresem tylko dla aplikacji Contoso wydatków.  

Wbudowana i niestandardowa rola usługi Azure AD działa w oparciu o koncepcje podobne do [kontroli dostępu opartej na rolach (RBAC) na platformie Azure](../develop/access-tokens.md#payload-claims). [Różnica między tymi dwoma systemami kontroli dostępu oparta na rolach](../../role-based-access-control/rbac-and-directory-admin-roles.md) polega na tym, że usługa Azure RBAC kontroluje dostęp do zasobów platformy Azure, takich jak maszyny wirtualne lub magazyn przy użyciu usługi Azure Resource Management, i role niestandardowe usługi Azure AD kontrolują dostęp do zasobów usługi Azure AD za pomocą interfejs API programu Graph. Oba systemy wykorzystują koncepcję definicji ról i przypisań ról. Uprawnień RBAC usługi Azure AD nie można dołączać do ról platformy Azure i na odwrót.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Jak usługa Azure AD decyduje o tym, czy użytkownik ma dostęp do zasobu

Poniżej przedstawiono ogólne kroki, których usługa Azure AD używa do określenia, czy masz dostęp do zasobu zarządzania. Te informacje służą do rozwiązywania problemów z dostępem.

1. Użytkownik (lub jednostka usługi) uzyskuje token w punkcie końcowym grafu Microsoft Graph lub Azure AD.
1. Użytkownik wykonuje wywołanie interfejsu API w celu Azure Active Directory (Azure AD) za pośrednictwem Microsoft Graph lub Azure AD Graph przy użyciu wystawionego tokenu.
1. W zależności od okoliczności usługa Azure AD wykonuje jedną z następujących akcji:
   - Szacuje członkostwo ról użytkownika na podstawie [roszczeń wids](../../active-directory-b2c/access-tokens.md) w tokenie dostępu użytkownika.
   - Pobiera wszystkie przypisania ról, które są stosowane do użytkownika, bezpośrednio lub za pośrednictwem członkostwa w grupie, do zasobu, w którym jest wykonywana akcja.
1. Usługa Azure AD decyduje o tym, czy akcja w wywołaniu interfejsu API jest uwzględniona w rolach, które użytkownik ma dla tego zasobu.
1. Jeśli użytkownik nie ma roli z akcją w żądanym zakresie, dostęp nie zostanie udzielony. Jeśli nie, dostęp jest udzielany.

## <a name="role-assignment"></a>Przypisanie roli

Przypisanie roli to zasób usługi Azure AD, który dołącza *definicję roli* do *użytkownika* w określonym *zakresie* w celu udzielenia dostępu do zasobów usługi Azure AD. Udzielenie dostępu polega na utworzeniu przypisania roli, a odwołanie dostępu — na usunięciu przypisania roli. Na jego rdzeń przypisanie roli składa się z trzech elementów:

- Użytkownik usługi Azure AD
- Definicja roli
- Zakres zasobu

[Przypisania ról można tworzyć](custom-create.md) przy użyciu Azure Portal, programu Azure AD PowerShell lub interfejs API programu Graph. Można również [wyświetlić listę przypisań ról](view-assignments.md).

Na poniższym diagramie przedstawiono przykład przypisania roli. W tym przykładzie Krzysztof zielony ma przypisaną rolę niestandardową administratora rejestracji aplikacji w zakresie rejestracji aplikacji konstruktora widgetu contoso. Przypisanie przyznaje Krzysztofowi uprawnienia roli administratora rejestracji aplikacji tylko dla tej rejestracji konkretnej aplikacji.

![Przypisanie roli jest wymuszane i ma trzy części](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Podmiot zabezpieczeń

Podmiot zabezpieczeń reprezentuje użytkownika, do którego ma zostać przypisany dostęp do zasobów usługi Azure AD. Użytkownik to osoba, która ma profil użytkownika w Azure Active Directory.

### <a name="role"></a>Rola

Definicja roli lub rola jest kolekcją uprawnień. Definicja roli zawiera listę operacji, które mogą być wykonywane w zasobach usługi Azure AD, takich jak tworzenie, odczytywanie, aktualizowanie i usuwanie. Istnieją dwa typy ról w usłudze Azure AD:

- Nie można zmienić wbudowanych ról utworzonych przez firmę Microsoft.
- Role niestandardowe utworzone i zarządzane przez organizację.

### <a name="scope"></a>Zakres

Zakresem jest ograniczenie dozwolonych akcji do określonego zasobu usługi Azure AD w ramach przypisania roli. Podczas przypisywania roli można określić zakres, który ogranicza dostęp administratora do określonego zasobu. Jeśli na przykład chcesz udzielić deweloperowi roli niestandardowej, ale tylko do zarządzania określoną rejestracją aplikacji, możesz dołączyć konkretną rejestrację aplikacji jako zakres do przypisania roli.

## <a name="required-license-plan"></a>Wymagany plan licencji

Korzystanie z wbudowanych ról w usłudze Azure AD jest bezpłatne, natomiast role niestandardowe wymagają licencji na Azure AD — wersja Premium P1. Aby znaleźć licencję odpowiednią do wymagań, zobacz [porównanie ogólnodostępnych funkcji w wersji bezpłatnej, podstawowej i premium](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="next-steps"></a>Następne kroki

- [Omówienie ról usługi Azure AD](concept-understand-roles.md)
- Tworzenie niestandardowych przypisań ról przy użyciu [Azure Portal, programu Azure AD PowerShell i interfejs API programu Graph](custom-create.md)
- [Lista przypisań ról](view-assignments.md)