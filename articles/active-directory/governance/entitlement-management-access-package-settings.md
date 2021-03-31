---
title: Link udostępniania do żądania pakietu dostępu w usłudze Azure AD uprawnień zarządzanie — Azure Active Directory
description: Dowiedz się, jak udostępnić link, aby zażądać pakietu dostępu w Azure Active Directory Zarządzanie uprawnieniami.
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
ms.openlocfilehash: 32973d063c36a45eee79db0b4da4e7419cb70013
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798550"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Link udostępniania do żądania pakietu dostępu w usłudze Azure AD uprawnień zarządzanie

Większość użytkowników w katalogu może zalogować się do portalu My Access i automatycznie wyświetlić listę pakietów dostępu, których mogą żądać. Jednak w przypadku użytkowników zewnętrznego partnera biznesowego, którzy nie znajdują się jeszcze w Twoim katalogu, należy wysłać im link umożliwiający zażądanie pakietu dostępu. 

Tak długo, jak katalog pakietu dostępu jest [włączony dla użytkowników zewnętrznych](entitlement-management-catalog-create.md) i masz [zasady dla katalogu użytkownika zewnętrznego](entitlement-management-access-package-request-policy.md), użytkownik zewnętrzny może skorzystać z linku Portal dostępu do aplikacji, aby zażądać pakietu dostępu.

## <a name="share-link-to-request-an-access-package"></a>Link udostępniania do żądania pakietu dostępu

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Na stronie Przegląd Skopiuj **łącze Mój dostęp do portalu**.

    ![Przegląd pakietu dostępu — mój link portalu dostępu](./media/entitlement-management-shared/my-access-portal-link.png)

    Ważne jest, aby skopiować całe łącze portalu dostępu, gdy wyśle je do wewnętrznego partnera biznesowego. Gwarantuje to, że partner uzyska dostęp do portalu Twojego katalogu w celu zgłoszenia żądania. Łącze rozpoczyna się od `myaccess` , zawiera wskazówkę katalogu i kończą się identyfikatorem pakietu dostępu.  (W przypadku instytucji rządowych Stanów Zjednoczonych jest to domena w linku my Access Portal `myaccess.microsoft.us` ).

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Wiadomość e-mail lub Wyślij link do zewnętrznego partnera biznesowego. Mogą oni udostępnić link swoim użytkownikom, aby zażądać pakietu dostępu.

## <a name="next-steps"></a>Następne kroki

- [Zażądaj dostępu do pakietu dostępu](entitlement-management-request-access.md)
- [Zatwierdzanie lub odrzucanie żądań dostępu](entitlement-management-request-approve.md)