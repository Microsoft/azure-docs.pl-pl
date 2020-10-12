---
title: Wyłącz weryfikację wiadomości e-mail podczas rejestracji klienta
titleSuffix: Azure AD B2C
description: Dowiedz się, jak wyłączyć weryfikację poczty e-mail podczas tworzenia konta klienta w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10613bd2d6219272248f882e45ae1c33d2cc9d61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85384213"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Wyłącz weryfikację poczty e-mail podczas rejestracji klienta w Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Wykonaj następujące kroki, aby wyłączyć weryfikację wiadomości e-mail:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Użyj filtru **katalogów i subskrypcji** w górnym menu, aby wybrać katalog, który zawiera dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika, dla którego chcesz wyłączyć weryfikację wiadomości e-mail. Na przykład *B2C_1_signinsignup*.
1. Wybierz pozycję **układy strony**.
1. Wybierz **stronę rejestracji konta lokalnego**.
1. W obszarze **atrybuty użytkownika**wybierz opcję **adres e-mail**.
1. Z listy rozwijanej **wymaganie weryfikacji** wybierz pozycję **nie**.
1. Wybierz pozycję **Zapisz**. Weryfikacja poczty e-mail jest teraz wyłączona dla tego przepływu użytkownika.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [dostosować interfejs użytkownika w Azure Active Directory B2C](customize-ui-overview.md)

