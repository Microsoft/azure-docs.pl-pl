---
title: Rejestracja umowy Enterprise Agreement platformy Azure za pomocą rozwiązania Cloudyn
description: W tym przewodniku Szybki start opisano szczegółowo proces rejestracji niezbędny do utworzenia subskrypcji wersji próbnej rozwiązania Cloudyn i zalogowania w portalu Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: af8eb5c06d427981c7154750bc069d802152f8c5
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688117"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Rejestracja umowy Enterprise Agreement platformy Azure i wyświetlanie danych kosztów

Do rejestrowania w rozwiązaniu Cloudyn służy umowa Enterprise Agreement platformy Azure. Rejestracja zapewnia dostęp do portalu firmy Cloudyn. W tym przewodniku Szybki start opisano szczegółowo proces rejestracji niezbędny do utworzenia subskrypcji wersji próbnej rozwiązania Cloudyn i zalogowania w portalu Cloudyn. Przedstawiono w nim także, jak od razu rozpocząć wyświetlanie danych kosztów.

Usługa Azure Cost Management oferuje podobne funkcje jak rozwiązanie Cloudyn. Usługa Azure Cost Management to natywne rozwiązanie do zarządzania kosztami na platformie Azure. Ułatwia ona analizowanie kosztów, tworzenie budżetów i zarządzanie nimi, eksportowanie danych, a także zapoznawanie się z rekomendacjami dotyczącymi optymalizacji i ich wdrażanie w celu zaoszczędzenia pieniędzy. Aby uzyskać więcej informacji, zobacz [Azure Cost Management](../cost-management-billing-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="register-with-cloudyn"></a>Rejestrowanie w rozwiązaniu Cloudyn

1. W witrynie Azure Portal kliknij pozycję **Zarządzanie kosztami i rozliczenia** na liście usług.
2. W obszarze **Przegląd** kliknij pozycję **Cloudyn**.  
    ![Strona rozwiązania Cloudyn wyświetlana w witrynie Azure Portal](./media/quick-register-ea/cost-mgt-billing-service.png)
3. Na stronie **Cloudyn** wybierz pozycję **Przejdź do rozwiązania Cloudyn**, aby otworzyć stronę rejestracji rozwiązania Cloudyn w nowym oknie.
4. Na stronie rejestracji wersji próbnej rozwiązania Cloudyn wpisz nazwę swojej firmy i wybierz pozycję **Azure Enterprise Enrollment Administrator** (Administrator rejestracji umowy Enterprise Agreement platformy Azure).  
5. Wprowadź klucz interfejsu API rejestracji Enterprise Portal. Jeśli nie masz klucza pod ręką, kliknij link [Enterprise Portal](https://ea.azure.com) i wykonaj następujące kroki:  
    ![Wklej swój klucz interfejsu API na karcie Billing](./media/quick-register-ea/trial-reg.png) (Rozliczenia)
   1. Zaloguj się do witryny Azure Enterprise i przycisk **Reports** (Raporty), kliknij przycisk **API Access Key** (Klucz dostępu API), a następnie skopiuj klucz podstawowy.  
    ![Przykład klucza interfejsu API EA w portalu EA](./media/quick-register-ea/ea-key.png)
   3. Wróć do strony rejestracji i wklej swój klucz API.
6. Zaakceptuj warunki użytkowania i zweryfikuj swój klucz. Kliknij przycisk **Next** (Dalej), aby autoryzować rozwiązanie Cloudyn do zbierania danych zasobów platformy Azure. Te dane obejmują dane dotyczące użycia, wydajności, rozliczeń i tagów z Twoich subskrypcji.  
    ![Przykład pomyślnej weryfikacji klucza interfejsu API EA](./media/quick-register-ea/ea-key-validated.png)
7. W obszarze **Invite other stakeholders** (Zaproś innych uczestników projektu) możesz dodać użytkowników, wpisując ich adresy e-mail. Po zakończeniu kliknij przycisk **Next** (Dalej). W zależności od rodzaju rejestracji platformy Azure dodanie wszystkich danych dotyczących rozliczeń do rozwiązania Cloudyn może potrwać do 24 godzin.
8. Kliknij pozycję **Go to Cloudyn** (Przejdź do rozwiązania Cloudyn), aby otworzyć portal Cloudyn, a następnie na stronie **Cloud Accounts Management** (Zarządzanie kontami w chmurze) powinny zostać wyświetlone dane Twojego zarejestrowanego konta EA.

Aby obejrzeć film wideo dotyczący rejestrowania umowy Enterprise Agreement, zobacz [How to Find Your EA Enrollment ID and API Key for use in Cloudyn (Jak wyszukać swój identyfikator rejestracji EA do użytku w rozwiązaniu Cloudyn)](https://youtu.be/u_phLs_udig).

[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano rejestrację w rozwiązaniu Cloudyn przy użyciu danych umowy Enterprise Agreement platformy Azure. Przedstawiono także logowanie się do portalu Cloudyn i rozpoczęcie wyświetlania danych kosztów. Aby dowiedzieć się więcej na temat rozwiązania Cloudyn, przejdź do samouczka dotyczącego rozwiązania Cloudyn.

> [!div class="nextstepaction"]
> [Przeglądanie użycia i kosztów](tutorial-review-usage.md)
