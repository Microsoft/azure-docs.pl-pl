---
title: 'Szybki Start: usuwanie zarejestrowanej aplikacji z platformy tożsamości firmy Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak usunąć aplikację zarejestrowaną na platformie tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 0a0150112602cd34168f64132785faf1f8c33f62
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612391"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Szybki Start: Usuwanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft

Deweloperzy w przedsiębiorstwach oraz dostawcy oprogramowania jako usługi (SaaS), którzy zarejestrowali aplikacje za pomocą platformy tożsamości firmy Microsoft, mogą potrzebować usunąć rejestrację aplikacji.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Usuwanie aplikacji utworzonej przez Ciebie lub Twoją organizację
* Usuwanie aplikacji utworzonej przez inną organizację

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [szybkiego startu: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Usuwanie aplikacji utworzonej przez Ciebie lub Twoją organizację

Aplikacje zarejestrowane przez Ciebie lub Twoją aplikację są reprezentowane przez obiekt aplikacji i obiekt jednostki usługi w dzierżawie. Aby uzyskać więcej informacji, zobacz [Obiekty aplikacji i obiekty jednostki usługi](./app-objects-and-service-principals.md).

Aby usunąć aplikację, musisz być właścicielem aplikacji lub mieć uprawnienia administratora.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** a następnie wybierz pozycję **rejestracje aplikacji**. Znajdź i wybierz aplikację, którą chcesz skonfigurować. Po wybraniu aplikacji zobaczysz stronę **Przegląd** aplikacji.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.
1. Wybierz pozycję **Tak**, aby potwierdzić, że chcesz usunąć aplikację.

## <a name="remove-an-application-authored-by-another-organization"></a>Usuwanie aplikacji utworzonej przez inną organizację

Jeśli wyświetlasz stronę **Rejestracje aplikacji** w kontekście dzierżawy, podzbiór aplikacji wyświetlanych na karcie **Wszystkie aplikacje** pochodzi z innej dzierżawy, a aplikacje z tego podzbioru zostały zarejestrowane w Twojej dzierżawie w ramach procesu udzielania zgody. Dokładniej mówiąc, są reprezentowane tylko przez obiekt główny usługi w dzierżawie, bez odpowiadającego mu obiektu aplikacji. Aby uzyskać więcej informacji na temat różnic między obiektami aplikacji a jednostkami usługi, zobacz [Application and service principal objects in Azure AD (Obiekty aplikacji i obiekty jednostki usługi w usłudze Azure AD)](./app-objects-and-service-principals.md).

Aby usunąć dostęp aplikacji do Twojego katalogu (po wcześniejszym udzieleniu zgody), administrator firmy musi usunąć jej jednostkę usługi. Administrator z uprawnieniami administratora globalnego może usunąć aplikację w witrynie Azure Portal lub za pomocą [poleceń cmdlet programu PowerShell usługi Azure AD](https://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o obiektach głównych aplikacji i usług na platformie tożsamości firmy Microsoft:

> [!div class="nextstepaction"]
> [Obiekty aplikacji i jednostki usługi w usłudze Azure Active Directory](app-objects-and-service-principals.md)