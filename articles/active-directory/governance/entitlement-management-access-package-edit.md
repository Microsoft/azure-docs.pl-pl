---
title: Ukrywanie lub usuwanie pakietu dostępu w usłudze zarządzania prawami — Azure AD
description: Dowiedz się, jak ukryć lub usunąć pakiet dostępu w Azure Active Directory Zarządzanie uprawnieniami.
services: active-directory
documentationCenter: ''
author: msaburnley
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
ms.openlocfilehash: da450a2dfc4351f5bd2c3c759b483cea0c110d9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078793"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Ukrywanie lub usuwanie pakietu dostępu w usłudze Azure AD uprawnienia do zarządzania

Pakiety dostępu są domyślnie odnajdywane. Oznacza to, że jeśli zasady zezwalają użytkownikowi na żądanie pakietu dostępu, automatycznie zobaczą pakiet dostępu wymieniony w portalu mojego dostępu. Ustawienie **ukrywane** można jednak zmienić tak, aby pakiet dostępu nie był wyświetlany na liście Portal użytkownika mojego dostępu.

W tym artykule opisano sposób ukrywania lub usuwania pakietu dostępu.

## <a name="change-the-hidden-setting"></a>Zmień ustawienie ukrywane

Wykonaj następujące kroki, aby zmienić ustawienie **ukryte** dla pakietu dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Na stronie Przegląd kliknij przycisk **Edytuj**.

1. Ustaw ustawienie **ukryte** .

    Jeśli ustawisz wartość **nie**, pakiet dostępu zostanie wyświetlony w portalu dostępu użytkownika.

    W przypadku wybrania **opcji tak**pakiet dostępu nie będzie wyświetlany w portalu dostępu użytkownika. Jedynym sposobem wyświetlania pakietu dostępu przez użytkownika jest to, że użytkownik ma **link bezpośredniego dostępu Portal** do pakietu dostępu. Aby uzyskać więcej informacji, zobacz [udostępnianie linku do żądania pakietu dostępu](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Usuwanie pakietu dostępu

Pakiet dostępu można usunąć tylko wtedy, gdy nie ma aktywnych przypisań użytkownika. Wykonaj następujące kroki, aby usunąć pakiet dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **przypisania** i Usuń dostęp dla wszystkich użytkowników.

1. W menu po lewej stronie kliknij pozycję **Przegląd** , a następnie kliknij pozycję **Usuń**.

1. W wyświetlonym komunikacie Usuń kliknij przycisk **tak**.

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie, Dodawanie i usuwanie przypisań dla pakietu dostępu](entitlement-management-access-package-assignments.md)
- [Wyświetlanie raportów i dzienników](entitlement-management-reports.md)
