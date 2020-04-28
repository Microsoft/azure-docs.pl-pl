---
title: Ustawienia żądania zmiany i zatwierdzania dla pakietu dostępu w usłudze Azure AD uprawnień zarządzanie — Azure Active Directory
description: Dowiedz się, jak zmienić ustawienia żądań i zatwierdzania dla pakietu dostępu w Azure Active Directory Zarządzanie uprawnieniami.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/30/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0eae4f53283c33f3d7372a606f8c0a3ac27d079
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655966"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Ustawienia żądania zmiany i zatwierdzania dla pakietu dostępu w usłudze Azure AD uprawnienia zarządzania

Jako Menedżer pakietów dostępu można zmienić użytkowników, którzy mogą zażądać pakietu dostępu w dowolnym momencie, edytując zasady lub dodając nowe zasady. Możesz również zmienić ustawienia zatwierdzania.

W tym artykule opisano sposób zmiany ustawień żądania i zatwierdzania istniejącego pakietu dostępu.

## <a name="choose-between-one-or-multiple-polices"></a>Wybór między jedną lub wieloma zasadami

Sposób określania, kto może zażądać pakietu dostępu, jest zasadą. Podczas tworzenia pakietu dostępu należy określić ustawienie żądanie i zatwierdzenie, które tworzy zasady. Większość pakietów dostępu będzie mieć pojedyncze zasady, ale jeden pakiet dostępu może mieć wiele zasad. Należy utworzyć wiele zasad dla pakietu dostępu, jeśli chcesz zezwolić różnym zestawom użytkowników na przyznawanie przypisań z różnymi ustawieniami żądania i zatwierdzania. Na przykład pojedyncze zasady nie mogą być używane do przypisywania użytkowników wewnętrznych i zewnętrznych do tego samego pakietu dostępu. Można jednak utworzyć dwie zasady w tym samym pakiecie dostępu — jeden dla użytkowników wewnętrznych i jeden dla użytkowników zewnętrznych. Jeśli istnieje wiele zasad, które mają zastosowanie do użytkownika, zostanie wyświetlony monit o określenie zasad, do których mają być przypisane. Na poniższym diagramie przedstawiono pakiet dostępu z dwoma zasadami.

![Wiele zasad w pakiecie dostępu](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scenariusz | Liczba zasad |
| --- | --- |
| Chcę, aby wszyscy użytkownicy w katalogu mieli te same ustawienia żądania i zatwierdzania dla pakietu dostępu | Jeden |
| Chcę, aby wszyscy użytkownicy w niektórych połączonych organizacjach mogli żądać pakietu dostępu | Jeden |
| Chcę zezwolić użytkownikom w moim katalogu, a także użytkownikom spoza mojego katalogu na żądanie pakietu dostępu | Wiele |
| Chcę określić różne ustawienia zatwierdzania dla niektórych użytkowników | Wiele |
| Chcę, aby niektórzy użytkownicy mieli dostęp do przypisań pakietów, a inni użytkownicy mogą ją rozciągnąć | Wiele |

Aby uzyskać informacje o logice priorytetu, która jest używana w przypadku stosowania wielu zasad, zobacz [wiele zasad](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Otwieranie istniejących zasad ustawień żądania i zatwierdzania

Aby zmienić ustawienia żądań i zatwierdzeń dla pakietu dostępu, należy otworzyć odpowiednie zasady. Wykonaj następujące kroki, aby otworzyć ustawienia żądania i zatwierdzania dla pakietu dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **zasady** , a następnie kliknij zasady, które chcesz edytować.

    Zostanie otwarte okienko Szczegóły zasad w dolnej części strony.

    ![Dostęp do programu Access — okienko Szczegóły zasad](./media/entitlement-management-shared/policy-details.png)

1. Kliknij przycisk **Edytuj** , aby edytować zasady.

    ![Dostęp do pakietu — edycja zasad](./media/entitlement-management-shared/policy-edit.png)

1. Kliknij kartę **żądania** , aby otworzyć ustawienia żądania i zatwierdzania.

1. Wykonaj kroki opisane w jednej z następujących sekcji żądania.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Dodawanie nowych zasad ustawień żądania i zatwierdzania

Jeśli masz zestaw użytkowników, którzy powinni mieć różne ustawienia żądań i zatwierdzeń, prawdopodobnie trzeba będzie utworzyć nowe zasady. Wykonaj następujące kroki, aby rozpocząć dodawanie nowych zasad do istniejącego pakietu dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **zasady** , a następnie **Dodaj zasady**.

1. Wpisz nazwę i opis zasad.

    ![Tworzenie zasad o nazwie i opisie](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Kliknij przycisk **dalej** , aby otworzyć kartę **żądania** .

1. Wykonaj kroki opisane w jednej z następujących sekcji żądania.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Jeśli edytujesz zasady, kliknij przycisk **Aktualizuj**. Jeśli dodajesz nowe zasady, kliknij przycisk **Utwórz**.

## <a name="next-steps"></a>Następne kroki

- [Zmień ustawienia cyklu życia pakietu dostępu](entitlement-management-access-package-lifecycle-policy.md)
- [Wyświetl żądania dla pakietu dostępu](entitlement-management-access-package-requests.md)