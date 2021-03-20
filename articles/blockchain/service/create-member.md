---
title: Tworzenie elementu członkowskiego usługi Azure łańcucha bloków — Azure Portal
description: Utwórz członka usługi Azure łańcucha bloków dla konsorcjum łańcucha bloków przy użyciu Azure Portal.
ms.date: 07/16/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: 8a7f5aaea56f34e8107664ab786a14b59cd1cb7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91292732"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Szybki Start: Tworzenie członka usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu Azure Portal

W tym przewodniku szybki start wdrożono nowego członka łańcucha bloków i konsorcjum w usłudze Azure łańcucha bloków przy użyciu Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Brak.

## <a name="create-a-blockchain-member"></a>Tworzenie elementu członkowskiego łańcucha bloków

Członek usługi Azure łańcucha bloków to węzeł łańcucha bloków w sieci prywatnej łańcucha bloków. Podczas aprowizacji elementu członkowskiego można utworzyć sieć konsorcjum lub dołączyć do niej. Potrzebujesz co najmniej jednego elementu członkowskiego dla sieci konsorcjum. Liczba członków łańcucha bloków wymaganych przez uczestników zależy od danego scenariusza. Uczestnicy konsorcjum mogą mieć co najmniej jednego członka łańcucha bloków lub mogą współdzielić członków z innymi uczestnikami. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Wybierz pozycję **łańcucha bloków**  >  **Azure łańcucha bloków Service (wersja zapoznawcza)**.

    ![Utwórz usługę](./media/create-member/create-member.png)

    Ustawienie | Opis
    --------|------------
    Subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla usługi. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | Utwórz nową nazwę grupy zasobów lub wybierz istniejącą z subskrypcji.
    Region (Region) | Wybierz region, w którym ma zostać utworzony element członkowski. Wszyscy członkowie konsorcjum muszą znajdować się w tej samej lokalizacji. Funkcje mogą być niedostępne w niektórych regionach. Usługa Azure łańcucha bloków Data Manager jest dostępna w następujących regionach świadczenia usługi Azure: Wschodnie stany USA i Europa Zachodnia.
    Protokół | Obecnie usługa Azure łańcucha bloków w wersji zapoznawczej obsługuje protokół kworum.
    Konsorcjum | W przypadku nowego konsorcjum wprowadź unikatową nazwę. W przypadku dołączania konsorcjum przy użyciu zaproszenia wybierz konsorcjum, do którego chcesz dołączyć. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md).
    Nazwa | Wybierz unikatową nazwę elementu członkowskiego usługi Azure łańcucha bloków. Nazwa elementu członkowskiego łańcucha bloków może zawierać tylko małe litery i cyfry. Pierwszy znak musi być literą. Wartość musi mieć długość od 2 do 20 znaków.
    Hasło konta elementu członkowskiego | Hasło konta elementu członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla elementu członkowskiego. Do zarządzania konsorcjum używasz konta elementu członkowskiego i hasła konta elementu członkowskiego.
    Cennik | Konfiguracja węzła i koszt nowej usługi. Wybierz łącze **Zmień** , aby wybrać między warstwami **standardowa** i **podstawowa** . Skorzystaj z warstwy *podstawowa* na potrzeby tworzenia, testowania i sprawdzania poprawności koncepcji. Użyj warstwy *standardowa* dla wdrożeń klasy produkcyjnej. W przypadku korzystania z łańcucha bloków Data Manager lub wysyłania dużej liczby transakcji prywatnych należy również użyć warstwy *standardowa* . Zmiana warstwy cenowej między podstawowa i Standardowa po utworzeniu elementu członkowskiego nie jest obsługiwana.
    Hasło węzła | Hasło dla domyślnego węzła transakcji elementu członkowskiego. Użyj hasła uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym punktem końcowym węzła transakcji elementu członkowskiego łańcucha bloków.

1. Wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić poprawność ustawień. Wybierz pozycję **Utwórz** , aby zainicjować obsługę administracyjną usługi. Inicjowanie obsługi trwa około 10 minut.
1. Wybierz pozycję **powiadomienia** na pasku narzędzi, aby monitorować proces wdrażania.
1. Po wdrożeniu przejdź do elementu członkowskiego łańcucha bloków.

Wybierz pozycję **Przegląd**, aby wyświetlić podstawowe informacje o usłudze, w tym adres RootContract i konto elementu członkowskiego.

![Przegląd składowej łańcucha bloków](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz użyć elementu członkowskiego utworzonego dla następnego przewodnika Szybki start lub samouczka. Gdy zasoby nie będą już potrzebne, można je usunąć przez usunięcie `myResourceGroup` grupy zasobów utworzonej dla przewodnika Szybki Start.

Aby usunąć grupę zasobów:

1. W Azure Portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
2. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usuwanie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono członka usługi Azure łańcucha bloków i nowego konsorcjum. Skorzystaj z następnego przewodnika Szybki Start, aby użyć usługi Azure łańcucha bloków Development Kit dla Ethereum w celu dołączenia do członka usług Azure łańcucha bloków.

> [!div class="nextstepaction"]
> [Użyj Visual Studio Code, aby nawiązać połączenie z usługą Azure łańcucha bloków](connect-vscode.md)
