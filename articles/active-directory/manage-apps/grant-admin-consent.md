---
title: Udzielanie zgody administratora w całej dzierżawie na aplikację — Azure AD
description: Dowiedz się, jak przyznać całej dzierżawie zgodę na aplikację, tak aby użytkownicy końcowi nie monitowani o zgodę podczas logowania się do aplikacji.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: d31f03d8e6138829bbf57003b504f68f5c2315b2
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763367"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Udzielanie zgody administratora całej dzierżawy dla aplikacji

Dowiedz się, jak uprościć środowisko użytkownika, udzielając zgody administratora na dostęp do aplikacji. Ten artykuł zawiera różne sposoby osiągnięcia tego celu. Metody dotyczą wszystkich użytkowników końcowych w dzierżawie usługi Azure Active Directory (Azure AD).

Aby uzyskać więcej informacji na temat wyrażania zgody na aplikacje, zobacz [Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przyznanie zgody administratora całej dzierżawy wymaga zalogowania się jako [administrator globalny](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [administrator aplikacji](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)lub [administrator aplikacji w chmurze](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).

> [!IMPORTANT]
> Gdy aplikacja otrzymuje zgodę na dostęp do całej dzierżawy, wszyscy użytkownicy będą mogli zalogować się do aplikacji, o ile nie została skonfigurowana tak, aby wymagała przypisania użytkownika. Aby określić, którzy użytkownicy mogą logować się do aplikacji, należy zażądać przypisania użytkownika, a następnie przypisać użytkowników lub grupy do aplikacji. Aby uzyskać więcej informacji, zobacz [metody przypisywania użytkowników i grup](methods-for-assigning-users-and-groups.md).

> [!WARNING]
> Przyznanie administratorowi zgody na dostęp do całej dzierżawy dla aplikacji spowoduje przyznanie aplikacji i wydawcy aplikacji dostępu do danych organizacji. Uważnie Przejrzyj uprawnienia, których aplikacja żąda przed udzieleniem zgody.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Udziel zgody administratora z Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Udzielanie zgody administratora w aplikacjach dla przedsiębiorstw

Jeśli aplikacja została już zainicjowana w dzierżawie, można udzielić zgody administratora na całej dzierżawie za pomocą *aplikacji dla przedsiębiorstw* . Na przykład aplikacja może zostać zainicjowana w dzierżawie, jeśli co najmniej jeden użytkownik już wyraził zgodę na aplikację. Aby uzyskać więcej informacji, zobacz [jak i dlaczego aplikacje są dodawane do Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Aby udzielić zgody administratora na poziomie dzierżawy aplikacji wymienionej w **aplikacjach dla przedsiębiorstw**:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako [administrator globalny](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [administrator aplikacji](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)lub [administrator aplikacji w chmurze](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Wybierz **Azure Active Directory** następnie **aplikacje dla przedsiębiorstw**.
3. Wybierz aplikację, do której chcesz udzielić zgody administratora w całej dzierżawie.
4. Wybierz pozycję **uprawnienia** , a następnie kliknij pozycję **Udziel zgody administratora**.
5. Uważnie Przejrzyj uprawnienia wymagane przez aplikację.
6. Jeśli wyrażasz zgodę na uprawnienia wymagane przez aplikację, udziel zgody. Jeśli nie, kliknij przycisk **Anuluj** lub Zamknij okno.

### <a name="grant-admin-consent-in-app-registrations"></a>Udzielanie zgody administratora w Rejestracje aplikacji

W przypadku aplikacji opracowanych przez organizację lub zarejestrowanych bezpośrednio w dzierżawie usługi Azure AD można także udzielić zgody administratora na poziomie dzierżawy **rejestracje aplikacji** w Azure Portal.

Aby udzielić zgody administratora na poziomie dzierżawy od **rejestracje aplikacji**:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako [administrator globalny](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [administrator aplikacji](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)lub [administrator aplikacji w chmurze](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Wybierz **Azure Active Directory** następnie **rejestracje aplikacji**.
3. Wybierz aplikację, do której chcesz udzielić zgody administratora w całej dzierżawie.
4. Wybierz pozycję **uprawnienia interfejsu API** , a następnie kliknij pozycję **Udziel zgody administratora**.
5. Uważnie Przejrzyj uprawnienia wymagane przez aplikację.
6. Jeśli wyrażasz zgodę na uprawnienia wymagane przez aplikację, udziel zgody. Jeśli nie, kliknij przycisk **Anuluj** lub Zamknij okno.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Utwórz adres URL na potrzeby udzielania zgody administratora całej dzierżawy

Podczas udzielania zgody administratora na całej dzierżawie przy użyciu dowolnej metody opisanej powyżej zostanie otwarte okno z Azure Portal, aby wyświetlić monit o zgodę na korzystanie z administracji całej dzierżawy. Jeśli znasz identyfikator klienta (znany również jako identyfikator aplikacji) aplikacji, możesz utworzyć ten sam adres URL, aby udzielić zgody administratora całej dzierżawy.

Adres URL zgody administratora dla całej dzierżawy jest następujący:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

gdzie:

* `{client-id}`jest IDENTYFIKATORem klienta aplikacji (znanym także jako identyfikator aplikacji).
* `{tenant-id}`jest IDENTYFIKATORem dzierżawy organizacji lub dowolną zweryfikowaną nazwą domeny.

Zawsze należy uważnie przejrzeć uprawnienia żądania aplikacji przed udzieleniem zgody.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje](configure-user-consent.md)

[Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)

[Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/active-directory-v2-scopes.md)

[Usługa Azure AD w systemie StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
