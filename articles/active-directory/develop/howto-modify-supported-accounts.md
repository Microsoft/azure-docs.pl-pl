---
title: 'Instrukcje: zmienianie typów kont obsługiwanych przez aplikację | Azure'
titleSuffix: Microsoft identity platform
description: W tej metodzie opisano konfigurowanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft w celu zmiany użytkownika lub konta, które mogą uzyskiwać dostęp do aplikacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 3ae6616263de605d5910f244423b9e7ffc036c5d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080353"
---
# <a name="how-to-modify-the-accounts-supported-by-an-application"></a>Jak zmodyfikować konta obsługiwane przez aplikację

Po zarejestrowaniu aplikacji za pomocą platformy tożsamości firmy Microsoft określono typy kont, które są dostępne dla użytkownika. Można na przykład określić konta tylko w organizacji, która jest aplikacją z *jedną dzierżawą* . Możesz też określić konta w dowolnej organizacji (w tym z Twojej firmy), która jest aplikacją z *wieloma dzierżawcami* .

W poniższych sekcjach opisano sposób modyfikowania rejestracji aplikacji w Azure Portal, aby zmienić, kto lub jakie typy kont mogą uzyskać dostęp do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* [Aplikacja zarejestrowana w dzierżawie usługi Azure AD](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Zmiana rejestracji aplikacji pod kątem obsługi różnych kont

Aby określić inne ustawienia dla typów kont obsługiwanych przez rejestrację istniejącej aplikacji:

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację.
1. Teraz Określ, kto może korzystać z aplikacji, czasami nazywaną *odbiorcami logowania*.

    | Obsługiwane typy kont | Opis |
    |-------------------------|-------------|
    | **Konta tylko w tym katalogu organizacyjnym** | Wybierz tę opcję, jeśli tworzysz aplikację do użycia tylko przez użytkowników (lub Gości *) w* dzierżawie.<br><br>Często nazywana jest aplikacją biznesową (LOB, *line-of-Business* ), jest to aplikacja o **pojedynczej dzierżawie** na platformie tożsamości firmy Microsoft. |
    | **Konta w dowolnym katalogu organizacyjnym** | Wybierz tę opcję, jeśli chcesz, aby użytkownicy w *dowolnej* dzierżawie usługi Azure AD mogli korzystać z aplikacji. Ta opcja jest odpowiednia, jeśli na przykład tworzysz aplikację "oprogramowanie jako usługa" (SaaS), którą zamierzasz udostępnić w wielu organizacjach.<br><br>Jest to tzw. aplikacja **wielodostępna** na platformie tożsamości firmy Microsoft. |
1. Wybierz pozycję **Zapisz**.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Dlaczego zmiana na wiele dzierżawców może zakończyć się niepowodzeniem

Przełączenie rejestracji aplikacji z jednej do wielu dzierżawców może czasami zakończyć się niepowodzeniem z powodu kolizji nazw identyfikatora aplikacji (identyfikator URI aplikacji). Przykładowy identyfikator URI aplikacji to `https://contoso.onmicrosoft.com/myapp` .

Identyfikator URI identyfikatora aplikacji jest jednym ze sposobów, w jaki aplikacja jest identyfikowana w komunikatach protokołu. W przypadku aplikacji z jedną dzierżawą identyfikator URI identyfikatora aplikacji musi być unikatowy tylko w ramach tej dzierżawy. W przypadku aplikacji z wieloma dzierżawcami musi ona być globalnie unikatowa, aby usługa Azure AD mogła znaleźć aplikację we wszystkich dzierżawcach. Globalna unikatowość jest wymuszana przez wymaganie, aby nazwa hosta identyfikatora URI identyfikatora aplikacji była zgodna z jedną z [zweryfikowanych domen wydawcy](howto-configure-publisher-domain.md)dzierżawy usługi Azure AD.

Na przykład jeśli nazwa dzierżawy to *contoso.onmicrosoft.com*, `https://contoso.onmicrosoft.com/myapp` jest to prawidłowy identyfikator URI aplikacji. Jeśli dzierżawca ma zweryfikowaną domenę *contoso.com*, prawidłowy identyfikator URI aplikacji również będzie miał wartość `https://contoso.com/myapp` . Jeśli identyfikator URI aplikacji nie jest zgodny z drugim wzorcem, `https://contoso.com/myapp` przekonwertowanie rejestracji aplikacji na wiele dzierżawców zakończy się niepowodzeniem.

Aby uzyskać więcej informacji na temat konfigurowania zweryfikowanej domeny wydawcy, zobacz [Konfigurowanie zweryfikowanej domeny](howto-configure-publisher-domain.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wymaganiach dotyczących [konwertowania aplikacji z jednej do wielu dzierżawców](howto-convert-app-to-be-multi-tenant.md).
