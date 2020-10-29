---
title: Rejestrowanie przy użyciu informacji o partnerze programu CSP w rozwiązaniu Cloudyn na platformie Azure
description: Dowiedz się więcej o procesie rejestracji używanym przez partnerów do dołączania klientów do portalu Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543325"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Rejestrowanie w programie partnerskim programu CSP i wyświetlanie danych kosztów

Jako partner CSP i zarejestrowany użytkownik rozwiązania Cloudyn możesz przeglądać i analizować swoje wydatki na chmurę w portalu Cloudyn. [Usługa Azure Cost Management jest natywnie dostępna dla bezpośrednich partnerów](../costs/get-started-partners.md), którzy dołączyli swoich klientów do umowy klienta firmy Microsoft i zakupili plan platformy Azure.

Rejestracja zapewnia dostęp do portalu firmy Cloudyn. W tym przewodniku Szybki start opisano szczegółowo proces rejestracji niezbędny do utworzenia subskrypcji wersji próbnej rozwiązania Cloudyn i zalogowania w portalu Cloudyn.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Konfigurowanie dostępu do pośredniego programu CSP w rozwiązaniu Cloudyn

Domyślnie interfejs API Centrum partnerskiego jest dostępny tylko dla bezpośrednich programów CSP. Jednak dostawca bezpośredniego programu CSP może skonfigurować dostęp dla swoich klientów lub partnerów pośredniego programu CSP, używając grup jednostek w rozwiązaniu Cloudyn.

W celu umożliwienia dostępu klientom lub partnerom pośredniego programu CSP wykonaj następujące czynności, aby posegmentować dane pośredniego programu CSP przy użyciu grup jednostek rozwiązania Cloudyn. Przypisz odpowiednie uprawnienia użytkowników do grup jednostek.

1. Utwórz grupę jednostek, korzystając z informacji zawartych w sekcji [Tworzenie jednostek](tutorial-user-access.md#create-and-manage-entities).
2. Wykonaj czynności opisane w artykule [Przypisywanie subskrypcji do jednostek kosztów](https://www.youtube.com/watch?v=d9uTWSdoQYo). Skojarz konto klienta pośredniego programu CSP i jego subskrypcje z uprzednio utworzoną jednostką.
3. Wykonaj czynności opisane w sekcji [Tworzenie użytkownika z dostępem administratora](tutorial-user-access.md#create-a-user-with-admin-access), aby utworzyć konto użytkownika z dostępem administratora. Następnie upewnij się, że konto użytkownika ma dostęp administratora do określonych jednostek, które utworzono wcześniej dla konta pośredniego.

Partnerzy pośredniego programu CSP mogą logować się do portalu Cloudyn przy użyciu utworzonych dla nich kont.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano rejestrację w rozwiązaniu Cloudyn przy użyciu informacji programu CSP. Przedstawiono także logowanie się do portalu Cloudyn i rozpoczęcie wyświetlania danych kosztów. Aby dowiedzieć się więcej na temat rozwiązania Cloudyn, przejdź do samouczka dotyczącego rozwiązania Cloudyn.

> [!div class="nextstepaction"]
> [Przeglądanie użycia i kosztów](tutorial-review-usage.md)