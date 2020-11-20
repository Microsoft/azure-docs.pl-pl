---
title: Niestandardowe role administratorów w Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak zrozumieć role niestandardowe usługi Azure AD w Azure Active Directory (Azure AD) z kontrolą dostępu opartą na rolach i zakresami zasobów.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b230361ddc771693849d01de156339d1b595826d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949211"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Niestandardowe role administratorów w Azure Active Directory (wersja zapoznawcza)

W tym artykule opisano, jak zrozumieć role niestandardowe usługi Azure AD w Azure Active Directory (Azure AD) z kontrolą dostępu opartą na rolach i zakresami zasobów. Niestandardowe role usługi Azure AD stanowią podstawowe uprawnienia [ról wbudowanych](permissions-reference.md), dzięki czemu można tworzyć i organizować własne role niestandardowe. Takie podejście umożliwia udzielenie dostępu w bardziej szczegółowy sposób niż wbudowane role, gdy są potrzebne. Ta pierwsza wersja ról niestandardowych usługi Azure AD obejmuje możliwość tworzenia roli w celu przypisywania uprawnień do zarządzania rejestracjami aplikacji. Z upływem czasu zostaną dodane dodatkowe uprawnienia do zasobów organizacji, takich jak aplikacje przedsiębiorstwa, użytkownicy i urządzenia.  

Ponadto role niestandardowe usługi Azure AD obsługują przydziały dla poszczególnych zasobów, a także bardziej tradycyjne przypisania całej organizacji. Takie podejście daje możliwość udzielenia dostępu do zarządzania niektórymi zasobami (na przykład jednej rejestracji aplikacji) bez udzielania dostępu do wszystkich zasobów (wszystkie rejestracje aplikacji).

Kontrola dostępu oparta na rolach usługi Azure AD jest publiczną funkcją w wersji zapoznawczej usługi Azure AD i jest dostępna z dowolnym płatnym planem licencjonowania usługi Azure AD. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="understand-azure-ad-role-based-access-control"></a>Omówienie kontroli dostępu opartej na rolach w usłudze Azure AD

Przyznawanie uprawnień przy użyciu niestandardowych ról usługi Azure AD to dwuetapowy proces, który polega na utworzeniu niestandardowej definicji roli, a następnie przypisaniu jej przy użyciu przypisania roli. Niestandardowa definicja roli jest kolekcją uprawnień dodawanych z listy wstępnie zdefiniowanej. Te uprawnienia są te same uprawnienia, które są używane w rolach wbudowanych.  

Po utworzeniu definicji roli można przypisać ją do użytkownika, tworząc przypisanie roli. Przypisanie roli przyzna użytkownikowi uprawnienia w definicji roli w określonym zakresie. Ten dwuetapowy proces umożliwia utworzenie pojedynczej definicji roli i przypisanie jej wiele razy w różnych zakresach. Zakres definiuje zbiór zasobów usługi Azure AD, do których członek roli ma dostęp. Najbardziej typowym zakresem jest zakres całej organizacji (w całej sieci). Rolę niestandardową można przypisać w zakresie całej organizacji, co oznacza, że członek roli ma uprawnienia roli do wszystkich zasobów w organizacji. Rolę niestandardową można także przypisać do zakresu obiektu. Przykładem zakresu obiektu jest pojedyncza aplikacja. Tę samą rolę można przypisać do jednego użytkownika przez wszystkie aplikacje w organizacji, a następnie do innego użytkownika z zakresem tylko dla aplikacji Contoso wydatków.  

Wbudowana i niestandardowa rola usługi Azure AD działa w oparciu o koncepcje podobne do [kontroli dostępu opartej na rolach (RBAC) na platformie Azure](../../active-directory-b2c/overview.md). [Różnica między tymi dwoma systemami kontroli dostępu oparta na rolach](../../role-based-access-control/rbac-and-directory-admin-roles.md) polega na tym, że usługa Azure RBAC kontroluje dostęp do zasobów platformy Azure, takich jak maszyny wirtualne lub magazyn przy użyciu usługi Azure Resource Management, i role niestandardowe usługi Azure AD kontrolują dostęp do zasobów usługi Azure AD za pomocą interfejs API programu Graph. Oba systemy wykorzystują koncepcję definicji ról i przypisań ról. Uprawnień RBAC usługi Azure AD nie można dołączać do ról platformy Azure i na odwrót.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Jak usługa Azure AD decyduje o tym, czy użytkownik ma dostęp do zasobu

Poniżej przedstawiono ogólne kroki, których usługa Azure AD używa do określenia, czy masz dostęp do zasobu zarządzania. Te informacje służą do rozwiązywania problemów z dostępem.

1. Użytkownik (lub jednostka usługi) uzyskuje token w punkcie końcowym grafu Microsoft Graph lub Azure AD.

1. Użytkownik wykonuje wywołanie interfejsu API w celu Azure Active Directory (Azure AD) za pośrednictwem Microsoft Graph lub Azure AD Graph przy użyciu wystawionego tokenu.

1. W zależności od okoliczności usługa Azure AD wykonuje jedną z następujących akcji:

    - Szacuje członkostwo ról użytkownika na podstawie [roszczeń wids](../../active-directory-b2c/access-tokens.md) w tokenie dostępu użytkownika.
    - Pobiera wszystkie przypisania ról, które są stosowane do użytkownika, bezpośrednio lub za pośrednictwem członkostwa w grupie, do zasobu, w którym jest wykonywana akcja.

1. Usługa Azure AD decyduje o tym, czy akcja w wywołaniu interfejsu API jest uwzględniona w rolach, które użytkownik ma dla tego zasobu.
1. Jeśli użytkownik nie ma roli z akcją w żądanym zakresie, dostęp nie zostanie udzielony. Jeśli nie, dostęp jest udzielany.

### <a name="role-assignments"></a>Przypisania ról

Przypisanie roli to obiekt, który dołącza definicję roli do użytkownika w określonym zakresie, aby przyznać dostęp do zasobów usługi Azure AD. Udzielenie dostępu polega na utworzeniu przypisania roli, a odwołanie dostępu — na usunięciu przypisania roli. Na jego rdzeń przypisanie roli składa się z trzech elementów:

- Użytkownik (osoba, która ma profil użytkownika w Azure Active Directory)
- Definicja roli
- Zakres zasobu

[Przypisania ról można tworzyć](custom-create.md) przy użyciu Azure Portal, programu Azure AD PowerShell lub interfejs API programu Graph. Możesz również [wyświetlić przypisania dla roli niestandardowej](custom-view-assignments.md#view-the-assignments-of-a-role).

Na poniższym diagramie przedstawiono przykład przypisania roli. W tym przykładzie Krzysztof zielony ma przypisaną rolę niestandardową administratora rejestracji aplikacji w zakresie rejestracji aplikacji konstruktora widgetu contoso. Przypisanie przyznaje Krzysztofowi uprawnienia roli administratora rejestracji aplikacji tylko dla tej rejestracji konkretnej aplikacji.

![Przypisanie roli jest wymuszane i ma trzy części](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Podmiot zabezpieczeń

Podmiot zabezpieczeń reprezentuje użytkownika, do którego ma zostać przypisany dostęp do zasobów usługi Azure AD. *Użytkownik* to osoba, która ma profil użytkownika w Azure Active Directory.

### <a name="role"></a>Rola

Definicja roli lub rola jest kolekcją uprawnień. Definicja roli zawiera listę operacji, które mogą być wykonywane w zasobach usługi Azure AD, takich jak tworzenie, odczytywanie, aktualizowanie i usuwanie. Istnieją dwa typy ról w usłudze Azure AD:

- Nie można zmienić wbudowanych ról utworzonych przez firmę Microsoft.
- Role niestandardowe utworzone i zarządzane przez organizację.

### <a name="scope"></a>Zakres

Zakresem jest ograniczenie dozwolonych akcji do określonego zasobu usługi Azure AD w ramach przypisania roli. Podczas przypisywania roli można określić zakres, który ogranicza dostęp administratora do określonego zasobu. Jeśli na przykład chcesz udzielić deweloperowi roli niestandardowej, ale tylko do zarządzania określoną rejestracją aplikacji, możesz dołączyć konkretną rejestrację aplikacji jako zakres do przypisania roli.

  > [!Note]
  > Role niestandardowe można przypisywać w zakresie katalogu i w zakresie zasobów. Nie mogą jeszcze być przypisane w zakresie jednostki administracyjnej.
  > Wbudowane role można przypisywać w zakresie katalogu, a w niektórych przypadkach — zakres jednostki administracyjnej. Nie można ich jednak przypisać do zakresu zasobów usługi Azure AD.

## <a name="required-license-plan"></a>Wymagany plan licencji

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Następne kroki

- Tworzenie niestandardowych przypisań ról przy użyciu [Azure Portal, programu Azure AD PowerShell i interfejs API programu Graph](custom-create.md)
- [Wyświetlanie przypisań roli niestandardowej](custom-view-assignments.md)
