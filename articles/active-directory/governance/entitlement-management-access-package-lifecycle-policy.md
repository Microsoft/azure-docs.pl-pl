---
title: Zmienianie ustawień cyklu życia pakietu dostępu w usłudze Azure AD uprawnienia zarządzania — Azure Active Directory
description: Dowiedz się, jak zmienić informacje o żądającym & ustawienia cyklu życia dla pakietu dostępu Azure Active Directory zarządzania prawami.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd119238476b842a4dba1efeec86544ca263920
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980096"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Zmienianie ustawień cyklu życia pakietu dostępu w usłudze Azure AD uprawnienia zarządzania

Jako Menedżer pakietów dostępu można zmienić ustawienia cyklu życia pakietu dostępu w dowolnym momencie, edytując istniejące zasady. Jeśli zmienisz datę wygaśnięcia zasad, Data wygaśnięcia żądań, które już oczekują na zatwierdzenie lub zatwierdzonego stanu, nie ulegnie zmianie.

W tym artykule opisano sposób zmiany ustawień cyklu życia istniejącego pakietu dostępu.

## <a name="open-requestor-information"></a>Otwórz informacje o żądającym
Aby zapewnić użytkownikom prawidłowy dostęp do pakietu dostępu, pytania niestandardowe można skonfigurować w celu zadawania użytkownikom żądania dostępu do określonych pakietów dostępu. Opcje konfiguracji obejmują: lokalizacja, wymagane/opcjonalne oraz format odpowiedzi dla tekstu/wielokrotnego wyboru. Osoby żądające zobaczą pytania, gdy zażądają pakietu i osób zatwierdzających, aby ułatwić im podejmowanie decyzji. Wykonaj następujące kroki, aby skonfigurować pytania w pakiecie dostępu:

## <a name="open-lifecycle-settings"></a>Otwórz ustawienia cyklu życia

Aby zmienić ustawienia cyklu życia pakietu dostępu, należy otworzyć odpowiednie zasady. Wykonaj następujące kroki, aby otworzyć ustawienia cyklu życia pakietu dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **zasady** , a następnie kliknij zasady, które mają ustawienia cyklu życia, które chcesz edytować.

    Zostanie otwarte okienko Szczegóły zasad w dolnej części strony.

    ![Dostęp do programu Access — okienko Szczegóły zasad](./media/entitlement-management-shared/policy-details.png)

1. Kliknij przycisk **Edytuj** , aby edytować zasady.

    ![Dostęp do pakietu — edycja zasad](./media/entitlement-management-shared/policy-edit.png)

1. Kliknij kartę **cykl życia** , aby otworzyć ustawienia cyklu życia.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Następne kroki

- [Ustawienia żądania zmiany i zatwierdzania dla pakietu dostępu](entitlement-management-access-package-request-policy.md)