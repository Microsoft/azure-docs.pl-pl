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
ms.openlocfilehash: 033d5cfa5dbe5e0010bc1182f54643ec04db3ce3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017616"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Usuwanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft

Deweloperzy w przedsiębiorstwach oraz dostawcy oprogramowania jako usługi (SaaS), którzy zarejestrowali aplikacje za pomocą platformy tożsamości firmy Microsoft, mogą potrzebować usunąć rejestrację aplikacji.

W poniższych sekcjach dowiesz się, jak:

* Usuwanie aplikacji utworzonej przez Ciebie lub Twoją organizację
* Usuwanie aplikacji utworzonej przez inną organizację

## <a name="prerequisites"></a>Wymagania wstępne

* [Aplikacja zarejestrowana w dzierżawie usługi Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Usuwanie aplikacji utworzonej przez Ciebie lub Twoją organizację

Aplikacje zarejestrowane przez Ciebie lub Twoją aplikację są reprezentowane przez obiekt aplikacji i obiekt jednostki usługi w dzierżawie. Aby uzyskać więcej informacji, zobacz [Obiekty aplikacji i obiekty jednostki usługi](./app-objects-and-service-principals.md).

Aby usunąć aplikację, musisz być właścicielem aplikacji lub mieć uprawnienia administratora.

1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** a następnie wybierz pozycję **rejestracje aplikacji**. Znajdź i wybierz aplikację, którą chcesz skonfigurować. Po wybraniu aplikacji zobaczysz stronę **Przegląd** aplikacji.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.
1. Wybierz pozycję **Tak**, aby potwierdzić, że chcesz usunąć aplikację.

## <a name="remove-an-application-authored-by-another-organization"></a>Usuwanie aplikacji utworzonej przez inną organizację

Jeśli wyświetlasz stronę **Rejestracje aplikacji** w kontekście dzierżawy, podzbiór aplikacji wyświetlanych na karcie **Wszystkie aplikacje** pochodzi z innej dzierżawy, a aplikacje z tego podzbioru zostały zarejestrowane w Twojej dzierżawie w ramach procesu udzielania zgody. Dokładniej mówiąc, są reprezentowane tylko przez obiekt główny usługi w dzierżawie, bez odpowiadającego mu obiektu aplikacji. Aby uzyskać więcej informacji na temat różnic między obiektami aplikacji a jednostkami usługi, zobacz [Application and service principal objects in Azure AD (Obiekty aplikacji i obiekty jednostki usługi w usłudze Azure AD)](./app-objects-and-service-principals.md).

Aby usunąć dostęp aplikacji do Twojego katalogu (po wcześniejszym udzieleniu zgody), administrator firmy musi usunąć jej jednostkę usługi. Administrator z uprawnieniami administratora globalnego może usunąć aplikację w witrynie Azure Portal lub za pomocą [poleceń cmdlet programu PowerShell usługi Azure AD](/previous-versions/azure/jj151815(v=azure.100)).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [obiektach głównych aplikacji i usług](app-objects-and-service-principals.md) na platformie tożsamości firmy Microsoft.
