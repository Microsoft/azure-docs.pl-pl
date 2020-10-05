---
title: 'Szybki Start: Tworzenie Azure Data Catalog'
description: W tym przewodniku szybki start opisano sposób tworzenia Azure Data Catalog przy użyciu Azure Portal.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: e08527ae530b7b5ed5e5293a3af9e2d8dd3f795a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88118794"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Szybki Start: Tworzenie Azure Data Catalog

Azure Data Catalog to w pełni zarządzana usługa w chmurze służąca jako system rejestracji i odnajdywania zasobów danych przedsiębiorstwa. Szczegółowe omówienie tej usługi można znaleźć w artykule [Co to jest usługa Azure Data Catalog?](overview.md).

Ten przewodnik Szybki Start pomaga rozpocząć tworzenie Azure Data Catalog.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

> [!Note]
> Ze względu na wymagania dotyczące zabezpieczeń platformy Azure Azure Data Catalog ma enfored Transport Layer Security (TLS) 1,2. Protokoły TLS 1,0 i TLS 1,1 zostały wyłączone. Jeśli maszyna nie została zaktualizowana w przypadku protokołu TLS 1,2, mogą wystąpić błędy uruchamiania narzędzia rejestracji. Aby zaktualizować maszynę pod kątem protokołu TLS 1,2, zobacz [Enable Transport Layer Security (1,2)](https://docs.microsoft.com/mem/configmgr/core/plan-design/security/enable-tls-1-2) .

Aby rozpocząć, musisz dysponować:

* Subskrypcję platformy [Microsoft Azure](https://azure.microsoft.com/).
* Musisz mieć własną [dzierżawę Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Aby skonfigurować Data Catalog, musisz być właścicielem lub współwłaścicielem subskrypcji platformy Azure.

## <a name="create-a-data-catalog"></a>Tworzenie wykazu danych

W ramach organizacji — domeny usługi Azure Active Directory — można aprowizować tylko jeden wykaz danych. W związku z tym, Jeśli właścicielem lub współwłaścicielem subskrypcji platformy Azure należącej do tej domeny Azure Active Directory już utworzono wykaz, nie można ponownie utworzyć katalogu, nawet jeśli masz wiele subskrypcji platformy Azure. Aby zobaczyć, czy w domenie usługi Azure Active Directory został utworzony wykaz danych, przejdź do [strony głównej usługi Azure Data Catalog](http://azuredatacatalog.com) i sprawdź, czy wykaz jest widoczny. Jeśli wykaz został już utworzony, pomiń poniższą procedurę i przejdź do następnej sekcji.

1. Przejdź do [Azure Portal](https://portal.azure.com)  >  **Utwórz zasób** i wybierz pozycję **Data Catalog**.

    ![Przycisk tworzenia Azure Data Catalog](media/data-catalog-get-started/data-catalog-create.png)

2. Określ **nazwę** wykazu danych, **subskrypcję** , której chcesz użyć, **lokalizację** katalogu i **warstwę cenową**. Następnie wybierz przycisk **Utwórz**.

3. Przejdź do [strony głównej usługi Azure Data Catalog](http://azuredatacatalog.com) i kliknij pozycję **Publikuj dane**.

   ![Usługa Azure Data Catalog — przycisk Publikuj dane](media/data-catalog-get-started/data-catalog-publish-data.png)

   Możesz również przejść do strony głównej Data Catalog na [stronie usługi Data Catalog](https://azure.microsoft.com/services/data-catalog) , wybierając pozycję **Rozpocznij pracę**.

   ![Usługa Azure Data Catalog — marketingowa strona docelowa](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Przejdź do strony **Ustawienia** .

    ![Usługa Azure Data Catalog — aprowizowanie wykazu danych](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Rozwiń **Cennik** i sprawdź **wersję** Azure Data Catalog (bezpłatna lub standardowa).

    ![Usługa Azure Data Catalog — wybieranie wersji](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Jeśli wybierzesz wersję *Standard* Edition jako warstwę cenową, możesz rozwinąć **grupy zabezpieczeń** i włączyć autoryzację Active Directory grup zabezpieczeń, aby uzyskać dostęp do Data Catalog i włączyć automatyczne dostosowywanie rozliczeń.

    ![Azure Data Catalog grupy zabezpieczeń](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Rozwiń sekcję **Użytkownicy wykazu** i kliknij pozycję **Dodaj**, aby dodać użytkowników wykazu danych. Użytkownik jest automatycznie dodawany do tej grupy.

    ![Usługa Azure Data Catalog — użytkownicy](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Jeśli wybierzesz wersję *Standard* Edition jako warstwę cenową, możesz rozwinąć pozycję **administratorzy słowników** i kliknąć przycisk **Dodaj** , aby dodać administratorów słownika. Użytkownik jest automatycznie dodawany do tej grupy.

    ![Administratorzy słownika Azure Data Catalog](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Rozwiń sekcję **Administratorzy wykazu** i kliknij pozycję **Dodaj**, aby dodać administratorów wykazu danych. Użytkownik jest automatycznie dodawany do tej grupy.

    ![Usługa Azure Data Catalog — administratorzy](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Rozwiń węzeł **tytuł portalu** i Dodaj dodatkowy tekst, który będzie wyświetlany w tytule portalu.

    ![Azure Data Catalog — tytuł portalu](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Po zakończeniu pracy ze stroną **Ustawienia** przejdź do strony **Publikowanie** .

    ![Usługa Azure Data Catalog — utworzony wykaz](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Znajdowanie wykazu danych w portalu Azure

1. Na osobnej karcie lub w osobnym oknie przeglądarki sieci Web przejdź do [portalu Azure](https://portal.azure.com) i zaloguj się przy użyciu tego samego konta, którego użyto do utworzenia wykazu danych w poprzednim kroku.

2. Wybierz pozycję **wszystkie usługi** , a następnie kliknij pozycję **Data Catalog**.

    ![Azure Data Catalog — Przeglądaj platformę Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Zobaczysz utworzony wykaz danych.

    ![Usługa Azure Data Catalog — wykaz widoczny na liście](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Kliknij utworzony wykaz. W portalu pojawi się blok **Wykaz danych**.

   ![Usługa Azure Data Catalog — blok w portalu](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Właściwości wykazu danych można wyświetlać i aktualizować. Możesz na przykład kliknąć pozycję **Warstwa cenowa** i zmienić wersję.

    ![Usługa Azure Data Catalog — warstwa cenowa](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia Azure Data Catalog dla organizacji. Teraz można rejestrować źródła danych w wykazie danych.

> [!div class="nextstepaction"]
> [Rejestrowanie źródeł danych w Azure Data Catalog](data-catalog-how-to-register.md)
