---
title: Udzielanie zgody administratora całej dzierżawy na aplikację — Azure AD
description: Dowiedz się, jak udzielić zgody dla całej dzierżawy na aplikację, aby użytkownicy końcowi nie zobaczyli monitu o zgodę podczas logowania się do aplikacji.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd5017b1437b0f07553e798ab1d96de15fafb3f9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374186"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Udzielanie zgody administratora całej dzierżawy dla aplikacji

  Dowiedz się, jak udzielić aplikacji zgody administratora całej dzierżawy. Ten artykuł zawiera różne sposoby osiągnięcia tego celu.

Aby uzyskać więcej informacji na temat wyrażania zgody na aplikacje, [zobacz Azure Active Directory platformę wyrażania zgody.](../develop/consent-framework.md)

## <a name="prerequisites"></a>Wymagania wstępne

Udzielenie zgody administratora całej dzierżawy wymaga zalogowania się jako użytkownik, który ma autoryzację do wyrażania zgody w imieniu organizacji. Obejmuje to [administratora globalnego](../roles/permissions-reference.md#global-administrator) [i administratora ról](../roles/permissions-reference.md#privileged-role-administrator)uprzywilejowanych oraz, w przypadku niektórych aplikacji, administratora [aplikacji](../roles/permissions-reference.md#application-administrator) i administratora aplikacji [w chmurze.](../roles/permissions-reference.md#cloud-application-administrator) Użytkownik może również być autoryzowany do udzielania zgody dla [](../roles/custom-create.md) całej dzierżawy, jeśli ma przypisaną niestandardową rolę katalogu, która obejmuje uprawnienia do udzielania uprawnień [aplikacjom.](../roles/custom-consent-permissions.md)

> [!WARNING]
> Udzielenie aplikacji zgody administratora dla całej dzierżawy spowoduje przyznanie aplikacji i wydawcy aplikacji dostępu do danych organizacji. Przed wyrażeniem zgody dokładnie sprawdź uprawnienia, których żąda aplikacja.

> [!IMPORTANT]
> Gdy aplikacja ma udzieloną zgodę administratora dla całej dzierżawy, wszyscy użytkownicy będą mogli zalogować się do aplikacji, chyba że została skonfigurowana w celu wymagania przypisania użytkownika. Aby ograniczyć liczbę użytkowników, którzy mogą logować się do aplikacji, należy wymagać przypisania użytkownika, a następnie przypisać użytkowników lub grupy do aplikacji. Aby uzyskać więcej informacji, [zobacz Metody przypisywania użytkowników i grup](./assign-user-or-group-access-portal.md).

## <a name="grant-admin-consent-from-the-azure-portal"></a>Udzielić zgody administratora z Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Udzielanie zgody administratora w aplikacjach dla przedsiębiorstw

Jeśli aplikacja została już aprowizowana w *dzierżawie,* możesz udzielić zgody administratora całej dzierżawy za pośrednictwem aplikacji dla przedsiębiorstw. Na przykład aplikację można aprowizować w dzierżawie, jeśli co najmniej jeden użytkownik wyraził już zgodę na aplikację. Aby uzyskać więcej informacji, zobacz [Jak i dlaczego aplikacje są dodawane](../develop/active-directory-how-applications-are-added.md)do Azure Active Directory .

Aby udzielić zgody administratora całej dzierżawy na aplikację wymienioną w pozycji **Aplikacje dla przedsiębiorstw:**

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako [administrator globalny,](../roles/permissions-reference.md#global-administrator) [administrator aplikacji](../roles/permissions-reference.md#application-administrator)lub administrator aplikacji [w chmurze.](../roles/permissions-reference.md#cloud-application-administrator)
2. Wybierz **Azure Active Directory** następnie **pozycję Aplikacje dla przedsiębiorstw.**
3. Wybierz aplikację, której chcesz udzielić zgody administratora dla całej dzierżawy.
4. Wybierz **pozycję Uprawnienia,** a następnie kliknij pozycję **Ujmij zgodę administratora.**
5. Dokładnie przejrzyj uprawnienia wymagane przez aplikację.
6. Jeśli zgadzasz się z uprawnieniami wymaganymi przez aplikację, przyznaj zgodę. Jeśli nie, kliknij **przycisk Anuluj** lub zamknij okno.

> [!WARNING]
> Udzielenie zgody administratora dla całej dzierżawy za pośrednictwem **aplikacji dla przedsiębiorstw** spowoduje odwołanie wszystkich uprawnień, którym wcześniej udzielono uprawnień dla całej dzierżawy. Nie ma to wpływu na uprawnienia, które zostały wcześniej przyznane przez użytkowników we własnym imieniu. 

### <a name="grant-admin-consent-in-app-registrations"></a>Udzielanie zgody administratora w Rejestracje aplikacji

W przypadku aplikacji opracowanych przez organizację lub zarejestrowanych bezpośrednio w dzierżawie usługi Azure AD możesz również udzielić zgody administratora dla całej dzierżawy z Rejestracje aplikacji **w** Azure Portal.

Aby udzielić zgody administratora całej dzierżawy z **Rejestracje aplikacji:**

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako [administrator globalny,](../roles/permissions-reference.md#global-administrator) [administrator aplikacji](../roles/permissions-reference.md#application-administrator)lub administrator aplikacji [w chmurze.](../roles/permissions-reference.md#cloud-application-administrator)
2. Wybierz **Azure Active Directory** a **następnie Rejestracje aplikacji**.
3. Wybierz aplikację, której chcesz udzielić zgody administratora dla całej dzierżawy.
4. Wybierz pozycję **Uprawnienia interfejsu API,** a następnie kliknij pozycję **Ujmij zgodę administratora.**
5. Dokładnie przejrzyj uprawnienia wymagane przez aplikację.
6. Jeśli zgadzasz się z uprawnieniami wymaganymi przez aplikację, przyznaj zgodę. Jeśli nie, kliknij **przycisk Anuluj** lub zamknij okno.

> [!WARNING]
> Udzielenie zgody administratora całej dzierżawy za **pośrednictwem Rejestracje aplikacji** spowoduje odwołanie wszystkich uprawnień, którym wcześniej udzielono uprawnień dla całej dzierżawy. Nie ma to wpływu na uprawnienia, które zostały wcześniej przyznane przez użytkowników we własnym imieniu. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Konstruowanie adresu URL w celu udzielenia zgody administratora w całej dzierżawie

W przypadku udzielania zgody administratora dla całej dzierżawy przy użyciu jednej z opisanych powyżej metod zostanie otwarte okno z Azure Portal monit o zgodę administratora dla całej dzierżawy. Jeśli znasz identyfikator klienta (znany również jako identyfikator aplikacji) aplikacji, możesz utworzyć ten sam adres URL, aby udzielić zgody administratora całej dzierżawy.

Adres URL zgody administratora całej dzierżawy ma następujący format:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

gdzie:

* `{client-id}` to identyfikator klienta aplikacji (znany także jako identyfikator aplikacji).
* `{tenant-id}` to identyfikator dzierżawy organizacji lub dowolna zweryfikowana nazwa domeny.

Jak zawsze dokładnie sprawdź uprawnienia, których żąda aplikacja, przed udzieleniem zgody.

> [!WARNING]
> Udzielenie zgody administratora całej dzierżawy za pomocą tego adresu URL spowoduje odwołanie wszystkich uprawnień, którym wcześniej udzielono uprawnień dla całej dzierżawy. Nie ma to wpływu na uprawnienia, które zostały wcześniej przyznane przez użytkowników we własnym imieniu. 

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje](configure-user-consent.md)

[Konfigurowanie przepływu pracy wyrażania zgody przez administratora](configure-admin-consent-workflow.md)

[Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)

[Usługa Azure AD w witrynie Microsoft Q&A](/answers/topics/azure-active-directory.html)
