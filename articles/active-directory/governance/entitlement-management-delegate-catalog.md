---
title: Delegowanie dostępu ładu do twórców wykazu w usłudze Azure AD uprawnień zarządzanie — Azure Active Directory
description: Dowiedz się, jak delegować nadzór dostępu od administratorów IT do twórców wykazu i menedżerów projektów, aby mogli zarządzać samymi prawami dostępu.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e41f372cd0105896765d5a267456b6bda767dd4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798430"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegowanie dostępu ładu do twórców wykazu w usłudze Azure AD uprawnienia zarządzania

Katalog jest kontenerem zasobów i pakietów dostępu. Katalog można utworzyć, gdy chcesz grupować powiązane zasoby i pakiety dostępu. Domyślnie Administrator globalny lub administrator użytkownika może [utworzyć wykaz](entitlement-management-catalog-create.md)i może dodać dodatkowych użytkowników jako właścicieli katalogu.

Aby delegować użytkowników, którzy nie są administratorami, aby mogli tworzyć własne wykazy, można dodać tych użytkowników do roli twórcy zdefiniowana przez Zarządzanie prawami usługi Azure AD. Można dodać poszczególnych użytkowników lub dodać grupę, której członkowie mogą tworzyć wykazy.  Po utworzeniu wykazu można następnie dodać do nich zasoby, które są do nich przypisane.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Jako administrator IT delegowanie do twórcy katalogu

Wykonaj następujące kroki, aby przypisać użytkownika do roli twórcy katalogu.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie w sekcji **Zarządzanie prawami** kliknij pozycję **Ustawienia**.

1. Kliknij pozycję **Edytuj**.

    ![Ustawienia do dodawania twórców katalogu](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. W sekcji **Zarządzanie uprawnieniami delegowania** kliknij pozycję **Dodaj twórców wykazu** , aby wybrać użytkowników lub grupy, do których chcesz delegować tę rolę zarządzania uprawnieniami.

1. Kliknij pozycję **Wybierz**.

1. Kliknij pozycję **Zapisz**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Zezwalaj na role delegowane w celu uzyskania dostępu do Azure Portal

Aby zezwolić na role delegowane, takie jak twórcy katalogu i menedżerów pakietów dostępu, aby uzyskać dostęp do Azure Portal do zarządzania pakietami dostępu, należy sprawdzić ustawienie portalu administracyjnego.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Użytkownicy**.

1. W menu po lewej stronie kliknij pozycję **Ustawienia użytkownika**.

1. Upewnij się, że ustawienie **Ogranicz dostęp do portalu administracyjnego usługi Azure AD** jest ustawione na wartość **nie**.

    ![Ustawienia użytkownika usługi Azure AD — Portal administracyjny](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie wykazu zasobów i zarządzanie nim](entitlement-management-catalog-create.md)
- [Delegowanie dostępu ładu do menedżerów pakietów](entitlement-management-delegate-managers.md)

