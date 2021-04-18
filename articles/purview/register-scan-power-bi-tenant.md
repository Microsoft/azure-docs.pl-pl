---
title: Rejestrowanie i skanowanie dzierżawy Power BI (wersja zapoznawcza)
description: Dowiedz się, jak za pomocą portalu Azure Purview zarejestrować i zeskanować Power BI dzierżawy.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 8fb4c797df7961726ca785a56a6ab25807999842
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600866"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Rejestrowanie i skanowanie dzierżawy Power BI (wersja zapoznawcza)

W tym artykule pokazano, jak za pomocą portalu Azure Purview zarejestrować i zeskanować Power BI dzierżawy.

> [!Note]
> Jeśli wystąpienie programu Purview i dzierżawa usługi Power BI znajdują się w tej samej dzierżawie platformy Azure, możesz użyć tylko uwierzytelniania tożsamości zarządzanej (MSI) w celu skonfigurowania skanowania dzierżawy Power BI dzierżawy. 

## <a name="create-a-security-group-for-permissions"></a>Tworzenie grupy zabezpieczeń dla uprawnień

Aby skonfigurować uwierzytelnianie, utwórz grupę zabezpieczeń i dodaj do niego tożsamość zarządzaną programu Purview.

1. W [Azure Portal](https://portal.azure.com)wyszukaj **Azure Active Directory**.
1. Utwórz nową grupę zabezpieczeń w Azure Active Directory, korzystając z tematu Tworzenie grupy podstawowej i dodawanie członków przy [użyciu Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Możesz pominąć ten krok, jeśli masz już grupę zabezpieczeń, której chcesz użyć.

1. Wybierz **pozycję Zabezpieczenia** jako typ **grupy.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Typ grupy zabezpieczeń":::

1. Dodaj tożsamość zarządzaną usługi Purview do tej grupy zabezpieczeń. Wybierz **pozycję Członkowie**, a następnie wybierz pozycję + Dodaj **członków.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Dodaj wystąpienie zarządzane katalogu do grupy.":::

1. Wyszukaj tożsamość zarządzaną aplikacji Purview i wybierz ją.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Dodawanie wykazu przez wyszukanie go":::

    Powinno zostać wyświetlony komunikat o sukcesie z informacjami o jego dodaniu.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Dodawanie powodzenia pliku MSI wykazu":::

## <a name="associate-the-security-group-with-the-tenant"></a>Kojarzenie grupy zabezpieczeń z dzierżawą

1. Zaloguj się do [Power BI administracyjnego.](https://app.powerbi.com/admin-portal/tenantSettings)
1. Wybierz stronę **Ustawienia dzierżawy.**

    > [!Important]
    > Aby wyświetlić stronę ustawień dzierżawy, Power BI administratorem.

1. Wybierz **pozycję Ustawienia interfejsu API** administratora Zezwalaj jednostkom usługi na używanie interfejsów API Power BI administratora  >  **(wersja zapoznawcza).**
1. Wybierz **pozycję Określone grupy zabezpieczeń.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Obraz przedstawiający sposób zezwalania jednostkom usługi na uzyskiwanie uprawnień interfejsu API Power BI tylko do odczytu":::

    > [!Caution]
    > Jeśli zezwolisz utworzonej grupie zabezpieczeń (która ma tożsamość zarządzaną usługi Purview jako członek) na używanie interfejsów API administratora usługi Power BI tylko do odczytu, możesz również zezwolić jej na dostęp do metadanych (np. nazw pulpitów nawigacyjnych i raportów, właścicieli, opisów itp.) dla wszystkich artefaktów usługi Power BI w tej dzierżawie. Po ściągnięcia metadanych do usługi Azure Purview, uprawnienia aplikacji Purview, a nie uprawnienia Power BI, określają, kto może zobaczyć te metadane.

    > [!Note]
    > Grupę zabezpieczeń można usunąć z ustawień dewelopera, ale wcześniej wyodrębnione metadane nie zostaną usunięte z konta programu Purview. Jeśli chcesz, możesz usunąć go oddzielnie.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Rejestrowanie Power BI i konfigurowanie skanowania

Teraz, po nadawać tożsamości zarządzanej programu Purview uprawnienia do nawiązywania połączenia z interfejsem API administratora dzierżawy usługi Power BI, możesz skonfigurować skanowanie z poziomu programu Azure Purview Studio.

1. Wybierz pozycję **Źródła na** lewym pasku nawigacyjnym.

1. Następnie wybierz pozycję **Zarejestruj**.

    Wybierz **Power BI** jako źródło danych.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Obraz przedstawiający listę dostępnych źródeł danych do wyboru":::

3. Nadaj wystąpieniu Power BI przyjazną nazwę.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Obraz przedstawiający Power BI nazwę przyjazną dla źródła danych":::

    Nazwa musi mieć od 3 do 63 znaków i może zawierać tylko litery, cyfry, podkreślenia i łączniki.  Spacje są niedozwolone.

    Domyślnie system znajdzie dzierżawę usługi Power BI, która istnieje w tej samej subskrypcji platformy Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI zarejestrowane źródło danych":::

    > [!Note]
    > W Power BI rejestracji i skanowania źródła danych jest dozwolone tylko dla jednego wystąpienia.


4. Nadaj nazwę skanowaniu. Następnie wybierz opcję dołączania lub wykluczania osobistych obszarów roboczych. Zwróć uwagę, że jedyną obsługiwaną metodą uwierzytelniania jest **tożsamość zarządzana.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Obraz przedstawiający Power BI skanowania":::

    > [!Note]
    > * Przełączenie konfiguracji skanowania w celu dołączyć lub wykluczyć osobisty obszar roboczy spowoduje wyzwolenie pełnego skanowania źródła usługi PowerBI
    > * Nazwa skanowania musi mieć od 3 do 63 znaków i może zawierać tylko litery, cyfry, podkreślenia i łączniki. Spacje są niedozwolone.

5. Konfigurowanie wyzwalacza skanowania. Dostępne opcje to **Raz,** **Co 7 dni** i **Co 30 dni.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Obraz wyzwalacza skanowania":::

6. Na **stronie Przejrzyj nowe skanowanie** wybierz pozycję Zapisz i **uruchom,** aby uruchomić skanowanie.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Obraz zapisywania i uruchamiania Power BI ekranu":::

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie katalogu danych usługi Azure Purview](how-to-browse-catalog.md)
- [Wyszukaj w witrynie Azure Purview Data Catalog](how-to-search-catalog.md)
