---
title: Rejestrowanie i skanowanie dzierżawy Power BI (wersja zapoznawcza)
description: Dowiedz się, jak za pomocą portalu Azure Purview zarejestrować i zeskanować Power BI dzierżawy.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 6646f131488a5ae4aa9b20fe614d7ebb46133444
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538862"
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

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Dodawanie wykazu przez jego wyszukanie":::

    Powinno zostać wyświetlony komunikat o sukcesie z informacjami o jego dodaniu.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Dodawanie powodzenia pliku MSI wykazu":::

## <a name="associate-the-security-group-with-the-tenant"></a>Kojarzenie grupy zabezpieczeń z dzierżawą

1. Zaloguj się do [Power BI administracyjnego.](https://app.powerbi.com/admin-portal/tenantSettings)
1. Wybierz stronę **Ustawienia dzierżawy.**

    > [!Important]
    > Musisz być administratorem Power BI, aby wyświetlić stronę ustawień dzierżawy.

1. Wybierz **pozycję Ustawienia interfejsu API** administratora Zezwalaj jednostkom usługi na używanie interfejsów API Power BI administratora  >  **(wersja zapoznawcza).**
1. Wybierz **pozycję Określone grupy zabezpieczeń.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Obraz przedstawiający sposób zezwalania jednostkom usługi na uzyskiwanie uprawnień interfejsu API Power BI tylko do odczytu":::

    > [!Caution]
    > Jeśli zezwolisz utworzonej grupie zabezpieczeń (która ma tożsamość zarządzaną usługi Purview jako członek) na używanie interfejsów API administratora usługi Power BI tylko do odczytu, możesz również zezwolić jej na dostęp do metadanych (np. nazw pulpitów nawigacyjnych i raportów, właścicieli, opisów itp.) dla wszystkich artefaktów usługi Power BI w tej dzierżawie. Po ściągniętiu metadanych do widoku usługi Azure Purview, uprawnienia aplikacji Purview, a nie uprawnienia Power BI, określają, kto może zobaczyć te metadane.

    > [!Note]
    > Grupę zabezpieczeń można usunąć z ustawień dewelopera, ale wcześniej wyodrębnione metadane nie zostaną usunięte z konta programu Purview. Jeśli chcesz, możesz usunąć go oddzielnie.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Rejestrowanie Power BI i konfigurowanie skanowania

Teraz, gdy masz uprawnienia tożsamości zarządzanej programu Purview do nawiązywania połączenia z interfejsem API administratora dzierżawy usługi Power BI, możesz skonfigurować skanowanie z poziomu programu Azure Purview Studio.

1. Wybierz **ikonę Centrum zarządzania.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Ikona centrum zarządzania.":::

1. Następnie wybierz **pozycję + Nowy w** **źródłach danych.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Obraz przycisku nowego źródła danych":::

    Wybierz **Power BI** jako źródło danych.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Obraz przedstawiający listę źródeł danych dostępnych do wyboru":::

3. Nadaj wystąpieniu Power BI przyjazną nazwę.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Obraz przedstawiający Power BI nazwę przyjazną dla źródła danych":::

    Nazwa musi mieć od 3 do 63 znaków i może zawierać tylko litery, cyfry, podkreślenia i łączniki.  Spacje są niedozwolone.

    Domyślnie system znajdzie dzierżawę usługi Power BI, która istnieje w tej samej subskrypcji platformy Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI zarejestrowane źródło danych":::

    > [!Note]
    > W Power BI rejestracji i skanowania źródła danych jest dozwolone tylko dla jednego wystąpienia.


4. Nadaj nazwę skanowaniu. Następnie wybierz opcję dołączania lub wykluczania osobistych obszarów roboczych. Zwróć uwagę, że jedyną obsługiwaną metodą uwierzytelniania jest **tożsamość zarządzana**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Obraz przedstawiający Power BI skanowania":::

    > [!Note]
    > * Przełączenie konfiguracji skanowania w celu dołączyć lub wykluczyć osobisty obszar roboczy spowoduje wyzwolenie pełnego skanowania źródła usługi PowerBI
    > * Nazwa skanowania musi mieć od 3 do 63 znaków i może zawierać tylko litery, cyfry, podkreślenia i łączniki. Spacje są niedozwolone.

5. Konfigurowanie wyzwalacza skanowania. Dostępne opcje to **Raz,** **Co 7 dni** i **Co 30 dni.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Obraz wyzwalacza skanowania":::

6. Na **stronie Przeglądanie nowego skanowania** wybierz pozycję Zapisz i **uruchom,** aby uruchomić skanowanie.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Zapisywanie i uruchamianie Power BI ekranu":::

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie katalogu danych usługi Azure Purview](how-to-browse-catalog.md)
- [Wyszukiwanie w usłudze Azure Purview Data Catalog](how-to-search-catalog.md)
