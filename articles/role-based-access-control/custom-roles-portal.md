---
title: Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu Azure Portal — RBAC
description: Dowiedz się, jak tworzyć role niestandardowe platformy Azure przy użyciu Azure Portal i kontroli dostępu opartej na rolach (RBAC) na platformie Azure. Obejmuje to wyświetlanie, tworzenie, aktualizowanie i usuwanie ról niestandardowych.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/11/2020
ms.author: rolyon
ms.openlocfilehash: ecda0edcd34999e8cbb6c7ab9039953d17c119e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369230"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>Tworzenie lub aktualizowanie ról niestandardowych platformy Azure za pomocą witryny Azure Portal

Jeśli [wbudowane role platformy Azure](built-in-roles.md) nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne role niestandardowe platformy Azure. Podobnie jak wbudowane role można przypisywać niestandardowe role do użytkowników, grup i jednostek usługi w ramach subskrypcji i zakresów grup zasobów. Role niestandardowe są przechowywane w katalogu Azure Active Directory (Azure AD) i mogą być współużytkowane przez subskrypcje. Każdy katalog może mieć maksymalnie 5000 ról niestandardowych. Role niestandardowe można tworzyć przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. W tym artykule opisano sposób tworzenia ról niestandardowych przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do tworzenia ról niestandardowych wymagane są:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Krok 1. Określanie potrzebnych uprawnień

Platforma Azure ma tysiące uprawnień, które można dołączyć do roli niestandardowej. Poniżej przedstawiono niektóre metody, które mogą pomóc w ustaleniu uprawnień, które mają zostać dodane do roli niestandardowej:

- Zapoznaj się z istniejącymi [wbudowanymi rolami](built-in-roles.md).
- Utwórz listę usług platformy Azure, do których chcesz udzielić dostępu.
- Określ [dostawców zasobów mapowanych na usługi platformy Azure](../azure-resource-manager/management/azure-services-resource-providers.md). Metoda wyszukiwania została opisana w dalszej części [kroku 4: uprawnienia](#step-4-permissions).
- Wyszukaj uprawnienia [dostępne](resource-provider-operations.md) , aby znaleźć uprawnienia, które chcesz dołączyć. Metoda wyszukiwania została opisana w dalszej części [kroku 4: uprawnienia](#step-4-permissions).

## <a name="step-2-choose-how-to-start"></a>Krok 2. Wybieranie sposobu uruchamiania

Istnieją trzy sposoby tworzenia roli niestandardowej. Można sklonować istniejącą rolę, zacząć od podstaw lub zacząć od pliku JSON. Najprostszym sposobem jest znalezienie istniejącej roli, która ma większość potrzebnych uprawnień, a następnie klonowanie i zmodyfikowanie go dla danego scenariusza. 

### <a name="clone-a-role"></a>Klonowanie roli

Jeśli istniejąca rola nie ma żadnych potrzebnych uprawnień, można sklonować ją, a następnie zmodyfikować uprawnienia. Wykonaj następujące kroki, aby rozpocząć klonowanie roli.

1. W Azure Portal Otwórz subskrypcję lub grupę zasobów, w której chcesz przypisać rolę niestandardową, a następnie otwórz pozycję **Kontrola dostępu (IAM)**.

    Poniższy zrzut ekranu przedstawia stronę kontroli dostępu (IAM) otwartą dla subskrypcji.

    ![Strona kontroli dostępu (IAM) dla subskrypcji](./media/custom-roles-portal/access-control-subscription.png)

1. Kliknij kartę **role** , aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

1. Wyszukaj rolę, którą chcesz sklonować, taką jak rola czytnika rozliczeń.

1. Na końcu wiersza kliknij przycisk wielokropka (**...**), a następnie kliknij przycisk **Klonuj**.

    ![Menu kontekstowe klonowania](./media/custom-roles-portal/clone-menu.png)

    Spowoduje to otwarcie edytora ról niestandardowych z wybraną opcją **Klonuj rolę** .

1. Przejdź do [kroku 3: podstawy](#step-3-basics).

### <a name="start-from-scratch"></a>Rozpoczynanie od zera

Jeśli wolisz, możesz wykonać następujące kroki, aby uruchomić rolę niestandardową od podstaw.

1. W Azure Portal Otwórz subskrypcję lub grupę zasobów, w której chcesz przypisać rolę niestandardową, a następnie otwórz pozycję **Kontrola dostępu (IAM)**.

1. Kliknij przycisk **Dodaj** , a następnie kliknij pozycję **Dodaj rolę niestandardową**.

    ![Dodawanie niestandardowego menu roli](./media/custom-roles-portal/add-custom-role-menu.png)

    Spowoduje to otwarcie edytora ról niestandardowych z wybraną opcją **Rozpocznij od podstaw** .

1. Przejdź do [kroku 3: podstawy](#step-3-basics).

### <a name="start-from-json"></a>Rozpocznij od formatu JSON

Jeśli wolisz, możesz określić większość niestandardowych wartości ról w pliku JSON. Plik można otworzyć w edytorze ról niestandardowych, wprowadzić dodatkowe zmiany, a następnie utworzyć rolę niestandardową. Wykonaj następujące kroki, aby rozpocząć pracę z plikiem JSON.

1. Utwórz plik JSON o następującym formacie:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. W pliku JSON Określ wartości dla różnych właściwości. Oto przykład z dodanymi wartościami. Aby uzyskać informacje o różnych właściwościach, zobacz [Omówienie ról platformy Azure](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. W Azure Portal Otwórz stronę **Kontrola dostępu (IAM)** .

1. Kliknij przycisk **Dodaj** , a następnie kliknij pozycję **Dodaj rolę niestandardową**.

    ![Dodawanie niestandardowego menu roli](./media/custom-roles-portal/add-custom-role-menu.png)

    Spowoduje to otwarcie edytora ról niestandardowych.

1. Na karcie podstawowe w obszarze **uprawnienia linii bazowej** wybierz pozycję **Rozpocznij od formatu JSON**.

1. Obok pola wybierz plik kliknij przycisk folder, aby otworzyć okno dialogowe Otwórz.

1. Wybierz plik JSON, a następnie kliknij przycisk **Otwórz**.

1. Przejdź do [kroku 3: podstawy](#step-3-basics).

## <a name="step-3-basics"></a>Krok 3. podstawy

Na karcie **podstawowe** należy określić nazwę, opis i uprawnienia linii bazowej dla roli niestandardowej.

1. W polu **Nazwa roli niestandardowej** Podaj nazwę roli niestandardowej. Nazwa musi być unikatowa dla katalogu usługi Azure AD. Nazwa może zawierać litery, cyfry, spacje i znaki specjalne.

1. W polu **Opis** wprowadź opcjonalny opis roli niestandardowej. Stanie się to etykietka narzędzia dla roli niestandardowej.

    Opcja **uprawnienia linii bazowej** powinna być już ustawiona na podstawie poprzedniego kroku, ale można ją zmienić.

    ![Karta podstawy z określonymi wartościami](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Krok 4. uprawnienia

Na karcie **uprawnienia** należy określić uprawnienia dla roli niestandardowej. W zależności od tego, czy Sklonowano rolę, czy po rozpoczęciu pracy z notacją JSON, na karcie uprawnienia może być już wymieniona część uprawnień.

![Karta uprawnienia w obszarze Utwórz rolę niestandardową](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Dodawanie lub usuwanie uprawnień

Wykonaj następujące kroki, aby dodać lub usunąć uprawnienia dla roli niestandardowej.

1. Aby dodać uprawnienia, kliknij przycisk **Dodaj uprawnienia** , aby otworzyć okienko Dodawanie uprawnień.

    W tym okienku są wyświetlane wszystkie dostępne uprawnienia pogrupowane w różne kategorie w formacie karty. Każda kategoria reprezentuje *dostawcę zasobów*, który jest usługą dostarczającą zasoby platformy Azure.

1. W polu **Wyszukaj uprawnienia** wpisz ciąg, aby wyszukać uprawnienia. Na przykład wyszukaj *fakturę* , aby znaleźć uprawnienia powiązane z fakturą.

    Lista kart dostawcy zasobów zostanie wyświetlona na podstawie ciągu wyszukiwania. Aby uzyskać listę, jak dostawcy zasobów mapują usługi platformy Azure, zobacz [dostawcy zasobów dla usług platformy Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

    ![Dodawanie okienka uprawnień przy użyciu dostawcy zasobów](./media/custom-roles-portal/add-permissions-provider.png)

1. Kliknij kartę dostawcy zasobów, która może mieć uprawnienia, które chcesz dodać do roli niestandardowej, np. **rozliczenia firmy Microsoft**.

    Lista uprawnień zarządzania dla tego dostawcy zasobów jest wyświetlana na podstawie ciągu wyszukiwania.

    ![Dodaj listę uprawnień](./media/custom-roles-portal/add-permissions-list.png)

1. Jeśli szukasz uprawnień odnoszących się do płaszczyzny danych, kliknij pozycję **Akcje danych**. W przeciwnym razie pozostaw akcje przełącz ustawienie na **Akcje** , aby wyświetlić listę uprawnień dotyczących płaszczyzny zarządzania. Aby uzyskać więcej informacji o różnicach między płaszczyzną zarządzania i płaszczyzną danych, zobacz [Zarządzanie i operacje na danych](role-definitions.md#management-and-data-operations).

1. W razie potrzeby zaktualizuj ciąg wyszukiwania, aby uściślić wyszukiwanie.

1. Po znalezieniu co najmniej jednego uprawnienia, które chcesz dodać do roli niestandardowej, należy dodać znacznik wyboru obok uprawnienia. Na przykład Dodaj znacznik wyboru obok pozycji **inne: Pobierz fakturę** , aby dodać uprawnienie do pobierania faktur.

1. Kliknij przycisk **Dodaj** , aby dodać uprawnienie do listy uprawnień.

    Uprawnienie zostanie dodane jako `Actions` lub `DataActions` .

    ![Dodano uprawnienie](./media/custom-roles-portal/permissions-list-add.png)

1. Aby usunąć uprawnienia, kliknij ikonę Usuń na końcu wiersza. W tym przykładzie, ponieważ użytkownik nie będzie potrzebować możliwości tworzenia biletów pomocy technicznej, `Microsoft.Support/*` uprawnienie można usunąć.

### <a name="add-wildcard-permissions"></a>Dodawanie uprawnień symboli wieloznacznych

W zależności od wybranego sposobu uruchamiania możesz mieć uprawnienia z symbolami wieloznacznymi ( `*` ) na liście uprawnień. Symbol wieloznaczny ( `*` ) rozszerza uprawnienia do wszystkich elementów, które pasują do podanego ciągu akcji. Na przykład następujący symbol wieloznaczny dodaje wszystkie uprawnienia dotyczące Azure Cost Management i eksportu. Obejmuje to również wszelkie przyszłe uprawnienia eksportu, które mogą zostać dodane.

```
Microsoft.CostManagement/exports/*
```

Jeśli chcesz dodać nowe uprawnienie do symbolu wieloznacznego, nie możesz dodać go za pomocą okienka **Dodawanie uprawnień** . Aby dodać uprawnienie symboli wieloznacznych, należy dodać je ręcznie przy użyciu karty **JSON** . Aby uzyskać więcej informacji, zobacz [krok 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Wyklucz uprawnienia

Jeśli rola ma uprawnienie do symbolu wieloznacznego ( `*` ) i chcesz wykluczyć lub odjęciu określonych uprawnień z tego uprawnienia, można je wykluczyć. Załóżmy na przykład, że masz następujące uprawnienia do symboli wieloznacznych:

```
Microsoft.CostManagement/exports/*
```

Jeśli nie chcesz zezwolić na usunięcie eksportu, możesz wykluczyć następujące uprawnienie do usuwania:

```
Microsoft.CostManagement/exports/delete
```

Po wykluczeniu uprawnienia zostanie ono dodane jako `NotActions` lub `NotDataActions` . Efektywne uprawnienia zarządzania są obliczane przez dodanie wszystkich, `Actions` a następnie odjęcie wszystkich elementów `NotActions` . Obowiązujące uprawnienia do danych są obliczane przez dodanie wszystkich elementów, `DataActions` a następnie odjęcie wszystkich elementów `NotDataActions` .

> [!NOTE]
> Wykluczenie uprawnienia nie jest takie samo jak odmowa. Wyłączenie uprawnień jest po prostu wygodnym sposobem odejmowania uprawnień z poziomu uprawnień z symboli wieloznacznych.

1. Aby wykluczyć lub odjąć uprawnienia z dozwolonego uprawnienia do symbolu wieloznacznego, kliknij pozycję **Wyklucz uprawnienia** , aby otworzyć okienko Wyklucz uprawnienia.

    W tym okienku należy określić uprawnienia do zarządzania lub danych, które są wykluczone lub odejmowane.

1. Po znalezieniu co najmniej jednego uprawnienia, które chcesz wykluczyć, Dodaj znacznik wyboru obok pozycji uprawnienia, a następnie kliknij przycisk **Dodaj** .

    ![Okno Wyklucz uprawnienia — wybrane uprawnienie](./media/custom-roles-portal/exclude-permissions-select.png)

    Uprawnienie zostanie dodane jako `NotActions` lub `NotDataActions` .

    ![Wykluczone uprawnienia](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Krok 5. zakresy, które można przypisać

Na karcie **zakresy** możliwe do przypisania Określ lokalizację, w której rola niestandardowa ma być dostępna do przypisania, na przykład subskrypcja lub Grupa zasobów. W zależności od wybranej opcji Rozpocznij na tej karcie można wyświetlić listę zakresów, w których otwarto stronę kontroli dostępu (IAM). Ustawienie zakresu możliwego do przypisania do zakresu głównego ("/") nie jest obsługiwane. Obecnie nie można dodać grupy zarządzania jako zakresu możliwego do przypisania.

1. Kliknij pozycję **Dodaj zakresy** , które można przypisać, aby otworzyć okienko Dodaj zakresy z możliwością przypisania.

    ![Karta zakresy przypisane](./media/custom-roles-portal/assignable-scopes.png)

1. Kliknij co najmniej jeden zakres, który ma być używany, zazwyczaj subskrypcją.

    ![Dodaj zakresy, które można przypisać](./media/custom-roles-portal/add-assignable-scopes.png)

1. Kliknij przycisk **Dodaj** , aby dodać swój zakres możliwy do przypisania.

## <a name="step-6-json"></a>Krok 6. kod JSON

Na karcie **JSON** zobaczysz niestandardową rolę sformatowaną w formacie JSON. Jeśli chcesz, możesz bezpośrednio edytować kod JSON. Jeśli chcesz dodać uprawnienie symbol wieloznaczny ( `*` ), musisz użyć tej karty.

1. Aby edytować kod JSON, kliknij przycisk **Edytuj**.

    ![Karta JSON przedstawiająca rolę niestandardową](./media/custom-roles-portal/json.png)

1. Wprowadź zmiany w formacie JSON.

    Jeśli kod JSON nie jest prawidłowo sformatowany, zobaczysz czerwoną linię i wskaźnik w pionie.

1. Po zakończeniu edycji kliknij przycisk **Zapisz**.

## <a name="step-7-review--create"></a>Krok 7. przegląd + tworzenie

Na karcie **Przegląd i tworzenie** możesz przejrzeć ustawienia roli niestandardowej.

1. Przejrzyj ustawienia roli niestandardowej.

    ![Przejrzyj i Utwórz kartę](./media/custom-roles-portal/review-create.png)

1. Kliknij przycisk **Utwórz** , aby utworzyć rolę niestandardową.

    Po kilku chwilach zostanie wyświetlone okno komunikatu z informacją o tym, że rola niestandardowa została pomyślnie utworzona.

    ![Utwórz komunikat roli niestandardowej](./media/custom-roles-portal/custom-role-success.png)

    W przypadku wykrycia błędów zostanie wyświetlony komunikat.

    ![Przegląd + Tworzenie błędu](./media/custom-roles-portal/review-create-error.png)

1. Wyświetl nową rolę niestandardową na liście **role** . Jeśli nie widzisz roli niestandardowej, kliknij przycisk **Odśwież**.

     Niestandardowa rola użytkownika może potrwać kilka minut.

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Wykonaj następujące kroki, aby wyświetlić role niestandardowe.

1. Otwórz subskrypcję lub grupę zasobów, a następnie otwórz pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **role** , aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

1. Na liście **Typ** wybierz pozycję **CustomRole** , aby po prostu zobaczyć role niestandardowe.

    Jeśli masz właśnie utworzoną rolę niestandardową i nie widzisz jej na liście, kliknij przycisk **Odśwież**.

    ![Lista ról niestandardowych](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

1. Zgodnie z opisem we wcześniejszej części tego artykułu, należy otworzyć listę ról niestandardowych.

1. Kliknij przycisk wielokropka (**...**) dla roli niestandardowej, którą chcesz zaktualizować, a następnie kliknij przycisk **Edytuj**. Należy pamiętać, że nie można aktualizować wbudowanych ról.

    Rola niestandardowa zostanie otwarta w edytorze.

    ![Niestandardowe menu roli](./media/custom-roles-portal/edit-menu.png)

1. Użyj różnych kart, aby zaktualizować rolę niestandardową.

1. Po zakończeniu wprowadzania zmian kliknij kartę **Recenzja + tworzenie** , aby przejrzeć zmiany.

1. Kliknij przycisk **Aktualizuj** , aby zaktualizować rolę niestandardową.

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

1. Zgodnie z opisem we wcześniejszej części tego artykułu, należy otworzyć listę ról niestandardowych.

1. Usuń wszystkie przypisania ról, które korzystają z roli niestandardowej.

1. Kliknij przycisk wielokropka (**...**) dla roli niestandardowej, którą chcesz usunąć, a następnie kliknij pozycję **Usuń**.

    ![Zrzut ekranu przedstawiający listę ról niestandardowych, które można wybrać do usunięcia.](./media/custom-roles-portal/delete-menu.png)

    Całkowite usunięcie roli niestandardowej może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu Azure PowerShell](tutorial-custom-role-powershell.md)
- [Role niestandardowe platformy Azure](custom-roles.md)
- [Operacje dostawcy zasobów platformy Azure](resource-provider-operations.md)
