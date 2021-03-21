---
title: Samoobsługowe resetowanie haseł licencji — Azure Active Directory
description: Dowiedz się więcej o różnicach Azure Active Directory wymagania licencyjne samoobsługowego resetowania hasła
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8f270f740951b95b0d4c00cbcf9b08e20a8e0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743728"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Wymagania dotyczące licencjonowania Azure Active Directory samoobsługowego resetowania hasła

Aby zmniejszyć liczbę wezwań pomocy technicznej i zwiększyć produktywność, gdy użytkownik nie może zalogować się na swoje urządzenie lub aplikację, konta użytkowników w Azure Active Directory (Azure AD) można włączyć do samoobsługowego resetowania hasła (SSPR). Funkcje wchodzące w skład SSPR obejmują zmianę hasła, zresetowanie, odblokowanie i zapisanie zwrotne do katalogu lokalnego. Podstawowe funkcje SSPR są dostępne w Microsoft 365 Business standardowym lub wyższym i we wszystkich Azure AD — wersja Premium jednostkach SKU bez ponoszenia kosztów.

W tym artykule szczegółowo opisano różne sposoby licencjonowania i używania funkcji samoobsługowego resetowania hasła. Aby uzyskać szczegółowe informacje na temat cen i rozliczeń, zobacz [stronę z cennikiem usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porównanie wersji i funkcji

SSPR jest licencjonowany na użytkownika. Aby zapewnić zgodność, organizacje muszą przypisywać odpowiednie licencje użytkownikom.

W poniższej tabeli opisano różne scenariusze SSPR dotyczące zmiany haseł, resetowania lub lokalnego zapisywania zwrotnego, a także jednostki SKU, które udostępniają funkcję.

| Cecha | Usługa Azure AD — warstwa Bezpłatna | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Usługa Azure AD — wersja Premium P1 lub P2 |
| --- |:---:|:---:|:---:|:---:|
| **Zmiana hasła użytkownika tylko w chmurze**<br />Gdy użytkownik w usłudze Azure AD wie swoje hasło i chce zmienić go na nowy. | ● | ● | ● | ● |
| **Resetowanie hasła użytkownika tylko w chmurze**<br />Gdy użytkownik w usłudze Azure AD zapomniał hasło i musi je zresetować. | | ● | ● | ● |
| **Zmienianie lub Resetowanie hasła użytkownika hybrydowego przy użyciu funkcji zapisywania zwrotnego Premium**<br />Gdy użytkownik w usłudze Azure AD, który jest synchronizowany z katalogu lokalnego przy użyciu Azure AD Connect chce zmienić lub zresetować swoje hasło, a także napisać nowe hasło z powrotem do Premium. | | | ● | ● |

> [!WARNING]
> Autonomiczne plany licencjonowania Microsoft 365 podstawowe i standardowe nie obsługują SSPR z lokalnym zapisem zwrotnym. Funkcja lokalnego zapisywania zwrotnego wymaga Azure AD — wersja Premium P1, Premium P2 lub Microsoft 365 Business Premium.

Dodatkowe informacje o licencjonowaniu, w tym koszty, można znaleźć na następujących stronach:

* [Cennik Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory funkcje i możliwości](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Włączanie licencjonowania grupowego lub użytkownika

Usługa Azure AD obsługuje Licencjonowanie oparte na grupach. Administratorzy mogą przypisywać licencje zbiorczo do grupy użytkowników, a nie przypisywać ich pojedynczo. Aby uzyskać więcej informacji, zobacz [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Niektóre usługi firmy Microsoft nie są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, administrator musi określić właściwość **Lokalizacja użycia** dla użytkownika. Przypisanie licencji można wykonać w   >    >  sekcji **ustawień** profilu użytkownika w Azure Portal. *W przypadku korzystania z przypisania licencji grupy Wszyscy użytkownicy bez określonej lokalizacji użycia dziedziczą lokalizację katalogu.*

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą SSPR, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Samouczek: Włączanie funkcji samoobsługowego resetowania hasła (SSPR)](tutorial-enable-sspr.md)