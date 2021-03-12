---
title: 'Instrukcje: usuwanie zarejestrowanej aplikacji z platformy tożsamości firmy Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: W tym instruktażu przedstawiono sposób usuwania aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft.
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
ms.openlocfilehash: e04884c078bd9a5693ddcbc4e71470bb23e13d60
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199793"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Usuwanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft

Deweloperzy w przedsiębiorstwach i dostawcy usług typu "oprogramowanie jako usługa" (SaaS), którzy zarejestrowali aplikacje na platformie tożsamości firmy Microsoft, mogą wymagać usunięcia rejestracji aplikacji.

W poniższych sekcjach dowiesz się, jak:

* Usuwanie aplikacji utworzonej przez Ciebie lub Twoją organizację
* Usuwanie aplikacji utworzonej przez inną organizację

## <a name="prerequisites"></a>Wymagania wstępne

* [Aplikacja zarejestrowana w dzierżawie usługi Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Usuwanie aplikacji utworzonej przez Ciebie lub Twoją organizację

Aplikacje zarejestrowane przez Ciebie lub Twoją aplikację są reprezentowane przez obiekt aplikacji i obiekt jednostki usługi w dzierżawie. Aby uzyskać więcej informacji, zobacz [Obiekty aplikacji i obiekty jednostki usługi](./app-objects-and-service-principals.md).

> [!NOTE]
> Usunięcie aplikacji spowoduje również usunięcie jej obiektu głównego usługi w katalogu macierzystym aplikacji. W przypadku aplikacji wielodostępnych obiekty główne usługi w innych katalogach nie zostaną usunięte.

Aby usunąć aplikację, należy ją wyświetlić jako właściciela aplikacji lub mieć uprawnienia administratora.

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym zarejestrowano aplikację.
1. Wyszukaj i wybierz **Azure Active Directory**. 
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  i wybierz aplikację, którą chcesz skonfigurować. Po wybraniu aplikacji zobaczysz stronę **Przegląd** aplikacji.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.
1. Odczytaj konsekwencje usunięcia.  Zaznacz pole wyboru, jeśli występuje w dolnej części okienka.
1. Wybierz pozycję **Usuń** , aby potwierdzić, że chcesz usunąć aplikację.

## <a name="remove-an-application-authored-by-another-organization"></a>Usuwanie aplikacji utworzonej przez inną organizację

Jeśli wyświetlasz stronę **Rejestracje aplikacji** w kontekście dzierżawy, podzbiór aplikacji wyświetlanych na karcie **Wszystkie aplikacje** pochodzi z innej dzierżawy, a aplikacje z tego podzbioru zostały zarejestrowane w Twojej dzierżawie w ramach procesu udzielania zgody. Dokładniej mówiąc, są reprezentowane tylko przez obiekt główny usługi w dzierżawie, bez odpowiadającego mu obiektu aplikacji. Aby uzyskać więcej informacji na temat różnic między obiektami aplikacji a jednostkami usługi, zobacz [Application and service principal objects in Azure AD (Obiekty aplikacji i obiekty jednostki usługi w usłudze Azure AD)](./app-objects-and-service-principals.md).

Aby usunąć dostęp aplikacji do Twojego katalogu (po wcześniejszym udzieleniu zgody), administrator firmy musi usunąć jej jednostkę usługi. Administrator musi mieć globalny dostęp do admininstrator i może usunąć aplikację za pomocą Azure Portal lub użyć [poleceń cmdlet programu PowerShell usługi Azure AD](/previous-versions/azure/jj151815(v=azure.100)) w celu usunięcia dostępu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [obiektach głównych aplikacji i usług](app-objects-and-service-principals.md) na platformie tożsamości firmy Microsoft.
