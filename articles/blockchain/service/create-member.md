---
title: Tworzenie Azure Blockchain Service — Azure Portal
description: Utwórz członka Azure Blockchain Service dla konsorcjum łańcucha bloków przy użyciu Azure Portal.
ms.reviewer: ravastra
ms.date: 07/16/2020
ms.topic: quickstart
ms.custom:
- references_regions
- mode-portal
ms.openlocfilehash: 9fe8b7856a99930c16b4173e432fa0b30642582c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536191"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Szybki start: tworzenie elementu członkowskiego łańcucha bloków usługi Azure Blockchain Service przy użyciu witryny Azure Portal

W tym przewodniku Szybki start wdrożysz nowego członka łańcucha bloków i konsorcjum w Azure Blockchain Service przy użyciu Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Brak.

## <a name="create-a-blockchain-member"></a>Tworzenie członka łańcucha bloków

Członek Azure Blockchain Service jest węzłem łańcucha bloków w sieci łańcucha bloków prywatnej konsorcjum. Podczas aprowizowania członka możesz utworzyć lub dołączyć do sieci konsorcjum. Potrzebujesz co najmniej jednego członka sieci konsorcjum. Liczba elementów członkowskich łańcucha bloków wymaganych przez uczestników zależy od scenariusza. Uczestnicy konsorcjum mogą mieć co najmniej jednego członka łańcucha bloków lub mogą udostępniać członków innym uczestnikom. Aby uzyskać więcej informacji na temat consortia, zobacz [Azure Blockchain Service consortium](consortium.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Wybierz **pozycję Blockchain** Azure Blockchain Service  >  **(wersja zapoznawcza).**

    ![Tworzenie usługi](./media/create-member/create-member.png)

    Ustawienie | Opis
    --------|------------
    Subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć dla swojej usługi. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | Utwórz nową nazwę grupy zasobów lub wybierz istniejącą nazwę z subskrypcji.
    Region (Region) | Wybierz region, w celu utworzenia członka. Wszyscy członkowie konsorcjum muszą znajdować się w tej samej lokalizacji. Funkcje mogą nie być dostępne w niektórych regionach. Azure Blockchain Data Manager jest dostępna w następujących regionach świadczenia usługi Azure: Wschodnie stany USA i Europa Zachodnia.
    Protokół | Obecnie program Azure Blockchain Service Wersji zapoznawczej obsługuje protokół kworum.
    Konsorcjum | W przypadku nowego konsorcjum wprowadź unikatową nazwę. Jeśli dołączysz do konsorcjum za pośrednictwem zaproszenia, wybierz konsorcjum, do którego dołączasz. Aby uzyskać więcej informacji na temat consortia, zobacz [Azure Blockchain Service consortium](consortium.md).
    Nazwa | Wybierz unikatową nazwę dla Azure Blockchain Service członkowskiego. Nazwa członka łańcucha bloków może zawierać tylko małe litery i cyfry. Pierwszy znak musi być literą. Wartość musi mieć od 2 do 20 znaków.
    Hasło konta członkowskiego | Hasło konta członkowskiego jest używane do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla członka. Do zarządzania konsorcjum używa się konta członkowskiego i hasła konta członkowskiego.
    Ceny | Konfiguracja węzła i koszt nowej usługi. Wybierz link **Zmień,** aby wybrać warstwę **Standardowa** **i** Podstawowa. Użyj warstwy *Podstawowa* do tworzenia, testowania i weryfikacji koncepcji. Użyj warstwy *Standardowa* dla wdrożeń klasy produkcyjnej. Użyj również warstwy *Standardowa,* jeśli używasz Blockchain Data Manager lub wysyłasz dużą ilość transakcji prywatnych. Zmiana warstwy cenowej między podstawową i standardową po utworzeniu członka nie jest obsługiwana.
    Hasło węzła | Hasło domyślnego węzła transakcji członka. Użyj hasła do uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym publicznym punktem końcowym węzła transakcji członka łańcucha bloków.

1. Wybierz **pozycję Przeglądanie + tworzenie,** aby zweryfikować ustawienia. Wybierz **pozycję Utwórz,** aby aprowizować usługę. Aprowizowanie trwa około 10 minut.
1. Wybierz **pozycję Powiadomienia** na pasku narzędzi, aby monitorować proces wdrażania.
1. Po wdrożeniu przejdź do swojego członka łańcucha bloków.

Wybierz **pozycję** Przegląd , aby wyświetlić podstawowe informacje o usłudze, w tym adres RootContract i konto członkowskie.

![Omówienie członka łańcucha bloków](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz użyć członka utworzonego na podstawie następnego przewodnika Szybki start lub samouczka. Gdy zasoby nie będą już potrzebne, możesz je usunąć, usuwając `myResourceGroup` grupę zasobów utworzoną na potrzeby tego przewodnika Szybki start.

Aby usunąć grupę zasobów:

1. W okienku Azure Portal w  okienku nawigacji po lewej stronie przejdź do grupy zasobów i wybierz grupę zasobów, którą chcesz usunąć.
2. Wybierz pozycję **Usuń grupę zasobów**. Zweryfikuj usunięcie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono członka Azure Blockchain Service i nowego konsorcjum. Wypróbuj następny przewodnik Szybki start, aby użyć Azure Blockchain Development Kit for Ethereum w celu dołączenia do Azure Blockchain Service członkowskiego.

> [!div class="nextstepaction"]
> [Nawiązywanie Visual Studio Code z Azure Blockchain Service](connect-vscode.md)
