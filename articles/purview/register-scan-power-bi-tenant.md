---
title: Rejestrowanie i skanowanie dzierżawy Power BI (wersja zapoznawcza)
description: Dowiedz się, jak zarejestrować i przeskanować dzierżawę Power BI przy użyciu portalu Azure kontrolą.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 2ecc5df9db51bb6c923b9e0f47163e492bd76cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695752"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Rejestrowanie i skanowanie dzierżawy Power BI (wersja zapoznawcza)

W tym artykule pokazano, jak zarejestrować i przeskanować dzierżawę Power BI przy użyciu portalu Azure kontrolą.

> [!Note]
> Jeśli wystąpienie kontrolą i dzierżawa Power BI znajdują się w tej samej dzierżawie platformy Azure, można użyć uwierzytelniania tożsamości zarządzanej (MSI) tylko w celu skonfigurowania skanowania dzierżawy Power BI. 

## <a name="create-a-security-group-for-permissions"></a>Utwórz grupę zabezpieczeń dla uprawnień

Aby skonfigurować uwierzytelnianie, należy utworzyć grupę zabezpieczeń i dodać do niej tożsamość zarządzaną kontrolą.

1. W [Azure Portal](https://portal.azure.com)wyszukaj ciąg **Azure Active Directory**.
1. Utwórz nową grupę zabezpieczeń w Azure Active Directory, wykonując następujące czynności: [Utwórz grupę podstawową i Dodaj członków przy użyciu Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Możesz pominąć ten krok, jeśli masz już grupę zabezpieczeń, której chcesz użyć.

1. Wybierz pozycję **zabezpieczenia** jako **Typ grupy**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Typ grupy zabezpieczeń":::

1. Dodaj tożsamość zarządzaną kontrolą do tej grupy zabezpieczeń. Wybierz pozycję **elementy członkowskie**, a następnie wybierz pozycję **+ Dodaj członków**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Dodaj wystąpienie zarządzane wykazu do grupy.":::

1. Wyszukaj tożsamość zarządzaną kontrolą i wybierz ją.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Dodaj katalog, wyszukując go":::

    Powinno zostać wyświetlone powiadomienie o powodzeniu informujące o tym, że zostało dodane.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Pomyślnie dodano plik MSI katalogu":::

## <a name="associate-the-security-group-with-the-tenant"></a>Skojarz grupę zabezpieczeń z dzierżawcą

1. Zaloguj się do [portalu administracyjnego Power BI](https://app.powerbi.com/admin-portal/tenantSettings).
1. Wybierz stronę **Ustawienia dzierżawy** .

    > [!Important]
    > Musisz być administratorem Power BI, aby wyświetlić stronę Ustawienia dzierżawy.

1. Wybierz pozycję **Ustawienia interfejsu API administratora**  >  **Zezwalaj podmiotom usługi na korzystanie z interfejsów API administratora Power BI tylko do odczytu (wersja zapoznawcza)**.
1. Wybierz **określone grupy zabezpieczeń**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Obraz przedstawiający sposób zezwalania podmiotom usługi na pobieranie tylko do odczytu uprawnień Power BI administratora":::

    > [!Caution]
    > Gdy zezwolisz utworzoną przez Ciebie grupę zabezpieczeń (która ma tożsamość zarządzaną przez program kontrolą jako członka), aby używać interfejsów API administratora Power BI tylko do odczytu, możesz również zezwolić na dostęp do metadanych (np. nazw pulpitów nawigacyjnych i raportów, właścicieli, opisów itp.) dla wszystkich artefaktów Power BI w tej dzierżawie. Po pobraniu metadanych do usługi Azure kontrolą, uprawnienia kontrolą, a nie Power BI, określają, kto może zobaczyć te metadane.

    > [!Note]
    > Grupę zabezpieczeń można usunąć z ustawień dewelopera, ale wcześniej wyodrębnione metadane nie zostaną usunięte z konta kontrolą. Możesz go usunąć oddzielnie, jeśli chcesz.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Zarejestruj Power BI i Skonfiguruj skanowanie

Teraz, gdy masz uprawnienia do kontrolą zarządzanej tożsamości w celu nawiązania połączenia z interfejsem API administratora dzierżawy Power BI, możesz skonfigurować skanowanie z poziomu platformy Azure kontrolą Studio.

Najpierw Dodaj specjalną flagę funkcji do adresu URL kontrolą 

1. Wybierz ikonę **centrum zarządzania** .

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Ikona centrum zarządzania.":::

1. Następnie wybierz pozycję **+ Nowy** w obszarze **źródła danych**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Obraz przycisku nowego źródła danych":::

    Wybierz **Power BI** jako źródło danych.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Obraz przedstawiający listę źródeł danych dostępnych do wyboru":::

3. Nadaj wystąpieniu Power BI przyjazną nazwę.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Obraz przedstawiający Power BI przyjazną nazwę źródła danych":::

    Nazwa musi mieć długość od 3-63 znaków i może zawierać tylko litery, cyfry, podkreślenia i łączniki.  Spacje są niedozwolone.

    Domyślnie system znajdzie dzierżawę Power BI, która istnieje w tej samej subskrypcji platformy Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Zarejestrowano źródło danych Power BI":::

    > [!Note]
    > W przypadku Power BI Rejestracja i skanowanie źródła danych jest dozwolone tylko dla jednego wystąpienia.


4. Nadaj nazwę skanowaniu. Następnie wybierz opcję dołączania lub wykluczania osobistych obszarów roboczych. Należy zauważyć, że jedyną obsługiwaną metodą uwierzytelniania jest **tożsamość zarządzana**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Obraz przedstawiający konfigurację skanowania Power BI":::

    > [!Note]
    > * Przełączenie konfiguracji skanowania w celu dołączenia lub wykluczenia osobistego obszaru roboczego spowoduje pełne skanowanie źródła usługi PowerBI
    > * Nazwa skanowania musi mieć długość od 3-63 znaków i może zawierać tylko litery, cyfry, podkreślenia i łączniki. Spacje są niedozwolone.

5. Konfigurowanie wyzwalacza skanowania. Opcje są **jednokrotne**, **co 7 dni** i **co 30 dni**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Obraz wyzwalacza skanowania":::

6. Na **przejrzeć nowe skanowanie** wybierz pozycję **Zapisz i uruchom** , aby uruchomić skanowanie.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Zapisz i uruchom Power BI obraz ekranu":::

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)
