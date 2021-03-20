---
title: Samoobsługowe resetowanie haseł w Azure Active Directory B2C | Microsoft Docs
description: Pokazuje, jak skonfigurować Samoobsługowe resetowanie haseł dla klientów w Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0c9edaf3356ea4c1a521a89f2ec60a4b6ba1a5ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87481499"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Konfigurowanie samoobsługowego resetowania haseł dla klientów

Dzięki funkcji samoobsługowego resetowania hasła Klienci, którzy zarejestrowali się do kont lokalnych, mogą samodzielnie resetować swoje hasła. Znacznie zmniejsza to obciążenie personelu pomocy technicznej, zwłaszcza jeśli aplikacja ma miliony klientów regularnie korzystających z nich. Obecnie przy użyciu zweryfikowanego adresu e-mail jest jedyną obsługiwaną metodą odzyskiwania.

> [!NOTE]
> Ten artykuł ma zastosowanie do samoobsługowego resetowania hasła używanego w kontekście standardowego **logowania w** przepływie użytkownika, który używa **konta lokalnego logowania** jako dostawcy tożsamości. Jeśli chcesz w pełni dostosowywalne przepływy użytkownika resetowania haseł wywoływane z poziomu aplikacji, zobacz [ten artykuł](user-flow-overview.md).
>
>

Domyślnie katalog nie ma włączonej funkcji samoobsługowego resetowania hasła. Wykonaj następujące kroki, aby je włączyć:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator subskrypcji. To samo konto służbowe lub szkolne konto Microsoft, które zostało użyte do utworzenia katalogu.
2. Otwórz **Azure Active Directory** (na pasku nawigacyjnym po lewej stronie).
3. Przewiń w dół w bloku opcje i wybierz pozycję **Resetowanie hasła**.
4. Ustaw funkcję samoobsługowego **resetowania hasła**  na **wszystkie**.
5. Kliknij przycisk **Zapisz** w górnej części strony. Wszystko gotowe!

Aby przetestować, użyj funkcji "Uruchom teraz" w dowolnym przepływie użytkownika logowania, który ma konta lokalne jako dostawcę tożsamości. Na stronie logowania do konta lokalnego (w przypadku wprowadzenia adresu e-mail i hasła lub nazwy użytkownika i hasła) kliknij pozycję **nie można uzyskać dostępu do konta?** , aby zweryfikować środowisko klienta.

> [!NOTE]
> Strony samoobsługowego resetowania hasła można dostosować przy użyciu [funkcji znakowania firmowego](../active-directory/fundamentals/customize-branding.md).
>
>

