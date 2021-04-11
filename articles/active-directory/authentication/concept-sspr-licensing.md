---
title: Samoobsługowe resetowanie haseł licencji — Azure Active Directory
description: Dowiedz się więcej o różnicach Azure Active Directory wymagania licencyjne samoobsługowego resetowania hasła
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d332c831cc764c61a4672ea5ad1db231b68e106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952375"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Wymagania dotyczące licencjonowania Azure Active Directory samoobsługowego resetowania hasła

Aby zmniejszyć liczbę wezwań pomocy technicznej i zwiększyć produktywność, gdy użytkownik nie może zalogować się na swoje urządzenie lub aplikację, konta użytkowników w Azure Active Directory (Azure AD) można włączyć do samoobsługowego resetowania hasła (SSPR). Funkcje wchodzące w skład SSPR obejmują zmianę hasła, zresetowanie, odblokowanie i zapisanie zwrotne do katalogu lokalnego. Podstawowe funkcje SSPR są dostępne w Microsoft 365 Business standardowym lub wyższym i we wszystkich Azure AD — wersja Premium jednostkach SKU bez ponoszenia kosztów.

W tym artykule szczegółowo opisano różne sposoby licencjonowania i używania funkcji samoobsługowego resetowania hasła. Aby uzyskać szczegółowe informacje na temat cen i rozliczeń, zobacz [stronę z cennikiem usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porównanie wersji i funkcji

SSPR wymaga licencji tylko dla dzierżawy. 

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

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą SSPR, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Samouczek: Włączanie funkcji samoobsługowego resetowania hasła (SSPR)](tutorial-enable-sspr.md)