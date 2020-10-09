---
title: Używanie aplikacji w usłudze Azure Blockchain Workbench
description: Samouczek dotyczący sposobu korzystania z kontraktów aplikacji w usłudze Azure łańcucha bloków Workbench w wersji zapoznawczej.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 5761bf1294691c2d50e0e389fe69ec286df4a06c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74324039"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Samouczek: korzystanie z aplikacji w usłudze Azure łańcucha bloków Workbench

Usługa Blockchain Workbench umożliwia tworzenie kontraktów i podejmowanie związanych z nimi akcji. Można również wyświetlać szczegóły kontraktu, takie jak stan i historia transakcji.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie nowego kontraktu
> * Podejmowanie akcji w stosunku do kontraktu

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożenie usługi Blockchain Workbench. Aby uzyskać więcej informacji, zobacz [wdrażanie usługi Azure łańcucha bloków Workbench](deploy.md) w celu uzyskania szczegółowych informacji o wdrożeniu
* Wdrożona aplikacja łańcucha bloków w usłudze Blockchain Workbench. Zobacz [Tworzenie aplikacji łańcucha bloków w usłudze Azure Blockchain Workbench](create-app.md)

[Otwórz usługę Blockchain Workbench](deploy.md#blockchain-workbench-web-url) w przeglądarce.

![Blockchain Workbench](./media/use/workbench.png)

Musisz zalogować się jako członek usługi Blockchain Workbench. Jeśli na liście nie ma aplikacji, jesteś członkiem usługi Blockchain Workbench, ale nie członkiem aplikacji. Administrator aplikacji Blockchain Workbench może przypisywać członków do aplikacji.

## <a name="create-new-contract"></a>Tworzenie nowego kontraktu

Aby utworzyć nowy kontrakt, musisz być członkiem określonym jako **inicjator**kontraktu. Aby uzyskać informacje na temat definiowania ról aplikacji i inicjatorów dla kontraktu, zobacz sekcję dotyczącą [przepływów pracy w artykule z omówieniem konfiguracji](configuration.md#workflows). Aby uzyskać informacje na temat przypisywania członków do ról aplikacji, zobacz sekcję dotyczącą [dodawania członka do aplikacji](manage-users.md#add-member-to-application).

1. W sekcji aplikacji Blockchain Workbench wybierz kafelek aplikacji zawierający kontrakt, który chcesz utworzyć. Zostanie wyświetlona lista aktywnych kontraktów.

2. Aby utworzyć nowy kontrakt, wybierz pozycję **Nowy kontrakt**.

    ![Przycisk Nowy kontrakt](./media/use/contract-list.png)

3. Zostanie wyświetlone okienko **Nowy kontrakt**. Określ początkowe wartości parametrów. Wybierz przycisk **Utwórz**.

    ![Okienko Nowy kontrakt](./media/use/new-contract.png)

    Nowo utworzony kontrakt zostanie wyświetlony na liście wraz innymi aktywnymi kontraktami.

    ![Lista aktywnych kontraktów](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Podejmowanie akcji w stosunku do kontraktu

W zależności od stanu kontraktu członkowie mogą podejmować akcje mające na celu przejście do następnego stanu kontraktu. Akcje są definiowane jako [przejścia](configuration.md#transitions) w obrębie [stanu](configuration.md#states). Członkowie należący do dozwolonej roli aplikacji lub wystąpienia dla przejścia mogą podejmować akcję. 

1. W sekcji aplikacji Blockchain Workbench wybierz kafelek aplikacji zawierający kontrakt, aby wykonać akcję.
2. Wybierz kontrakt na liście. Szczegółowe informacje o kontrakcie są wyświetlane w różnych sekcjach. 

    ![Szczegóły kontraktu](./media/use/contract-details.png)

    | Sekcja  | Opis  |
    |---------|---------|
    | Stan | Bieżący postęp w na różnych etapach kontraktu |
    | Szczegóły | Bieżące wartości kontraktu |
    | Akcja | Szczegóły ostatniej akcji |
    | Działanie | Historia transakcji kontraktu |
    
3. W sekcji **Akcja** wybierz pozycję **Wykonaj akcję**.

4. Szczegółowe informacje dotyczące bieżącego stanu kontraktu są wyświetlane w okienku. Wybierz akcję, którą chcesz wykonać z listy rozwijanej. 

    ![Wybieranie akcji](./media/use/choose-action.png)

5. Wybierz pozycję **Wykonaj akcję**, aby zainicjować akcję.
6. Jeśli akcja ma wymagane parametry, określ wartości dla akcji.

    ![Wykonywanie akcji](./media/use/take-action.png)

7. Wybierz pozycję **Wykonaj akcję**, aby wykonać akcję.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Obsługa wersji aplikacji Azure Blockchain Workbench](version-app.md)
