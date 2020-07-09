---
title: Włączanie logowania jednokrotnego dla aplikacji wielodostępnej
description: Wskazówki dla niezależnych dostawców oprogramowania na potrzeby integracji z usługą Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fa2b82b89c7b9ce1e05062718f2c4d0c93197c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763282"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Włącz logowanie jednokrotne dla aplikacji wielodostępnych  

W przypadku oferowania aplikacji do użytku przez inne firmy za pomocą zakupu lub subskrypcji możesz udostępnić swoją aplikację klientom w ramach swoich dzierżaw platformy Azure. Jest to tzw. Tworzenie aplikacji z wieloma dzierżawcami. Aby zapoznać się z omówieniem tego pojęcia, zobacz wielodostępne [aplikacje na platformie Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) i [dzierżawę w Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Co to jest logowanie jednokrotne

Logowanie jednokrotne (SSO) zwiększa bezpieczeństwo i wygodę, gdy użytkownicy logują się do aplikacji przy użyciu Azure Active Directory i innych tożsamości. Gdy aplikacja jest włączona z logowaniem jednokrotnym, użytkownicy nie muszą wprowadzać oddzielnych poświadczeń w celu uzyskania dostępu do tej aplikacji. Pełne wyjaśnienie logowania jednokrotnego. [Zobacz Logowanie jednokrotne do aplikacji w Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Dlaczego należy włączyć logowanie jednokrotne w aplikacji?

Istnieje wiele korzyści, aby włączyć logowanie jednokrotne w aplikacji wielodostępnej. Po włączeniu logowania jednokrotnego dla aplikacji:

* Twoja aplikacja może zostać wyświetlona w portalu Azure Marketplace, w którym aplikacja jest wykrywana przez miliony organizacji przy użyciu Azure Active Directory.
  * Umożliwia klientom szybkie konfigurowanie aplikacji za pomocą usługi Azure AD.

* Aplikację można odnaleźć w galerii aplikacji pakietu Office 365, uruchamiania aplikacji pakietu Office 365 i w ramach wyszukiwania firmy Microsoft w witrynie Office.com

* Aplikacja może korzystać z interfejsu API REST Microsoft Graph, aby uzyskać dostęp do danych, które są dyskami o wydajności użytkowników, które są dostępne w Microsoft Graph.

* Możesz zmniejszyć koszty pomocy technicznej, ułatwiając klientom korzystanie z nich.
  * Dokumentacja specyficzna dla aplikacji z zespołem usługi Azure AD dla naszych wzajemnych klientów upraszcza wdrażanie.
  * Jeśli włączono Logowanie jednokrotne, Administratorzy IT klientów nie muszą dowiedzieć się, jak skonfigurować aplikację do użycia w organizacji.

* Klienci mogą w całości zarządzać uwierzytelnianiem i autoryzacją tożsamości pracowników i Gości.

  * Zapewnienie odpowiedzialności za zarządzanie kontami i zgodność z właścicielem tych tożsamości przez klienta.

  * Zapewnienie możliwości włączenia lub wyłączenia logowania jednokrotnego dla określonych dostawców tożsamości, grup lub użytkowników w celu spełnienia potrzeb firmy.

* Zwiększ swój poziom rynkowy i jego przyjęcie. Wiele dużych organizacji wymaga, aby (lub zależy) pracownicy mogli bezproblemowo korzystać z logowania jednokrotnego we wszystkich aplikacjach. Łatwe tworzenie logowania jednokrotnego.

* Zmniejszenie liczby użytkowników końcowych, co może zwiększyć użycie użytkowników końcowych i zwiększyć przychody.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Jak włączyć logowanie jednokrotne w opublikowanej aplikacji

1. [Wybierz właściwy protokół federacyjny dla aplikacji](isv-choose-multi-tenant-federation.md)wielodostępnej.
1. Implementowanie logowania jednokrotnego w aplikacji
   - Zobacz [wskazówki dotyczące wzorców uwierzytelniania](../develop/v2-app-types.md)
   - Zobacz [przykłady kodu usługi Azure Active Directory](../develop/sample-v2-code.md) dla protokołów OIDC i OAuth
1. [Tworzenie dzierżawy platformy Azure](isv-tenant-multi-tenant-app.md) i testowanie aplikacji
1. [Utwórz i Opublikuj dokumentację logowania jednokrotnego w witrynie](isv-create-sso-documentation.md).
1. [Prześlij swoją listę aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) i partnera do firmy Microsoft, aby utworzyć dokumentację witryny firmy Microsoft.
1. [Dołącz do Microsoft Partner Network (bezpłatnie) i Utwórz plan na rynku](https://partner.microsoft.com/en-us/explore/commercial#gtm).
