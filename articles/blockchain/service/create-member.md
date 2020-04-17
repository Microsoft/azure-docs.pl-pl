---
title: Tworzenie członka usługi Azure Blockchain — witryna Azure portal
description: Utwórz członka usługi Azure Blockchain dla konsorcjum łańcucha bloków przy użyciu witryny Azure portal.
ms.date: 03/30/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 7d29382cdedf7e35d186c73c9d32547e10fb10d6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460277"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Szybki start: tworzenie członka łańcucha bloków usługi Azure Blockchain przy użyciu portalu Azure

W tym przewodniku Szybki start można wdrożyć nowego członka łańcucha bloków i konsorcjum w usłudze Azure Blockchain za pomocą witryny Azure portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Tworzenie członka łańcucha bloków

Członek usługi Azure Blockchain Service jest węzłem łańcucha bloków w sieci łańcucha bloków prywatnego konsorcjum. Podczas inicjowania obsługi administracyjnej członka, można utworzyć lub dołączyć do sieci konsorcjum. Potrzebujesz co najmniej jednego członka sieci konsorcjum. Liczba członków łańcucha bloków potrzebnych uczestnikom zależy od scenariusza. Uczestnicy konsorcjum mogą mieć jednego lub więcej członków łańcucha bloków lub mogą dzielić się członkami z innymi uczestnikami. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [konsorcjum usługi Azure Blockchain Service](consortium.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Wybierz **usługę Blockchain** > **Azure Blockchain (wersja zapoznawcza)**.

    ![Utwórz usługę](./media/create-member/create-member.png)

    Ustawienie | Opis
    --------|------------
    Subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć dla swojej usługi. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | Utwórz nową nazwę grupy zasobów lub wybierz istniejącą z subskrypcji.
    Region | Wybierz region, który ma utworzyć element członkowski. Wszyscy członkowie konsorcjum muszą znajdować się w tej samej lokalizacji.
    Protocol (Protokół) | Obecnie usługa Azure Blockchain Service Preview obsługuje protokół Kworum.
    Konsorcjum | W przypadku nowego konsorcjum wprowadź unikatową nazwę. Jeśli dołączysz do konsorcjum za pośrednictwem zaproszenia, wybierz konsorcjum, do którego dołączasz. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [konsorcjum usługi Azure Blockchain Service](consortium.md).
    Nazwa | Wybierz unikatową nazwę dla członka usługi Azure Blockchain Service. Nazwa członka łańcucha bloków może zawierać tylko małe litery i cyfry. Pierwszy znak musi być literą. Wartość musi zawierać się o długości od 2 do 20 znaków.
    Hasło do konta użytkownika | Hasło konta członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum, który jest tworzony dla twojego członka. Do zarządzania konsorcjum używasz konta członkowskiego i hasła do konta członkowskiego.
    Cennik | Konfiguracja węzła i koszt nowej usługi. Wybierz łącze **Zmień,** aby wybrać warstwę **Standardowa** i **Podstawowa.** Użyj warstwy *Podstawowa* do tworzenia, testowania i weryfikacji pojęć. Użyj warstwy *Standardowa* dla wdrożeń na poziomie produkcyjnym. Należy również użyć *warstwy Standardowa,* jeśli używasz Menedżera danych Blockchain lub wysyłasz dużą liczbę transakcji prywatnych. Zmiana warstwy cenowej między podstawową a standardową po utworzeniu elementu członkowskiego nie jest obsługiwana.
    Hasło węzła | Hasło do domyślnego węzła transakcji członka. Użyj hasła do uwierzytelniania podstawowego podczas łączenia się z domyślnym węzłem transakcji publicznej punktu końcowego członka łańcucha bloków.

1. Wybierz **opcję Przejrzyj + utwórz,** aby sprawdzić poprawność ustawień. Wybierz **pozycję Utwórz,** aby aprowizować usługę. Inicjowanie obsługi administracyjnej trwa około 10 minut.
1. Wybierz **powiadomienia** na pasku narzędzi, aby monitorować proces wdrażania.
1. Po wdrożeniu przejdź do swojego członka łańcucha bloków.

Wybierz **opcję Przegląd**, można wyświetlić podstawowe informacje o usłudze, w tym adres RootContract i konto członkowskie.

![Przegląd członków łańcucha bloków](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Można użyć członka utworzonego dla następnego przewodnika Szybki start lub samouczka. Gdy nie są już potrzebne, można usunąć `myResourceGroup` zasoby, usuwając grupę zasobów utworzoną dla szybkiego startu.

Aby usunąć grupę zasobów:

1. W witrynie Azure Portal przejdź do **grupy zasobów** w lewym okienku nawigacji i wybierz grupę zasobów, którą chcesz usunąć.
2. Wybierz pozycję **Usuń grupę zasobów**. Zweryfikuj usunięcie, wprowadzając nazwę grupy zasobów i wybierając pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono członka usługi Azure Blockchain service i nowe konsorcjum. Wypróbuj następny przewodnik Szybki start, aby dołączyć do członka usługi Azure Blockchain Service za pomocą zestawu Azure Blockchain Development Kit for Ethereum.

> [!div class="nextstepaction"]
> [Łączenie się z usługą Azure Blockchain Service za pomocą kodu programu Visual Studio](connect-vscode.md)
