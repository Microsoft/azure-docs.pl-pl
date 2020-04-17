---
title: Przewodnik Szybki start platformy Azure — Tworzenie konta usługi Azure Automation | Microsoft Docs
description: Dowiedz się, jak utworzyć konto usługi Azure Automation i uruchomić element runbook
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7704f080b7c1878f2fa2b079a1f242c8c2cc87a9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536985"
---
# <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

Konto usługi Azure Automation można utworzyć za pośrednictwem platformy Azure przy użyciu witryny Azure portal, interfejsu użytkownika opartego na przeglądarce, umożliwiającego dostęp do wielu zasobów. Jedno konto automatyzacji może zarządzać zasobami we wszystkich regionach i subskrypcjach dla danej dzierżawy. 

Ten przewodnik szybki start prowadzi cię podczas tworzenia konta automatyzacji i uruchamiania ego księgi runbook na koncie. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[Zaloguj się na platformie Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Tworzenie konta usługi Automation

1. Wybierz nazwę swojego konta platformy Azure. Nazwy kont automatyzacji są unikatowe dla regionu i grupy zasobów. Nazwy kont automatyzacji, które zostały usunięte, mogą nie być natychmiast dostępne.

    > [!NOTE]
    > Nie można zmienić nazwy konta po wprowadzeniu jej w interfejsie użytkownika. 

2. Kliknij przycisk **Utwórz zasób** znaleziony w lewym górnym rogu witryny Azure Portal.

3. Wybierz **pozycję Narzędzia do zarządzania &** IT, a następnie wybierz pozycję **Automatyzacja**.

4. Wprowadź informacje o koncie, w tym nazwę wybranego konta. W obszarze **Utwórz konto Uruchom jako platformy Azure** wybierz pozycję **Tak**, aby artefakty upraszczające uwierzytelnianie zostały automatycznie włączone. Po zakończeniu tych informacji kliknij przycisk **Utwórz,** aby rozpocząć wdrażanie konta automatyzacji.

    ![Wprowadzanie na stronie informacji dotyczących konta usługi Automation](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Aby uzyskać zaktualizowaną listę lokalizacji, do których można wdrożyć konto automatyzacji, zobacz [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

5. Po zakończeniu wdrażania kliknij pozycję **Wszystkie usługi**.

6. Wybierz **pozycję Konta automatyzacji,** a następnie wybierz utworzone konto automatyzacji.

    ![Przegląd konta usługi Automation](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Uruchamianie elementu runbook

Uruchom jeden z elementów runbook z samouczka.

1. Kliknij **pozycję Elementy runbook w** obszarze **Automatyzacja procesów**. Zostanie wyświetlona lista elementów runbook. Domyślnie na koncie jest włączonych kilka uruchomieniu cząs.

    ![Lista elementów runbook konta usługi Automation](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Wybierz element runbook **AzureAutomationTutorialScript**. Ta akcja spowoduje otwarcie strony przeglądu elementu runbook.

    ![Przegląd elementu runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Kliknij pozycję **Uruchom**, a na stronie Uruchamianie elementu runbook kliknij przycisk **OK**, aby uruchomić element runbook.

    ![Strona zadania elementu runbook](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Po stanie zadania `Running`kliknij pozycję **Dane wyjściowe** lub **Wszystkie dzienniki,** aby wyświetlić dane wyjściowe zadania runbook. W przypadku tego elementu runbook z samouczka danymi wyjściowymi jest lista Twoich zasobów platformy Azure.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono konto usługi Automation, uruchomiono element runbook i wyświetlono wyniki zadania. Aby dowiedzieć się więcej na temat usługi Azure Automation, przejdź do przewodnika Szybki start dotyczącego tworzenia pierwszego elementu runbook.

> [!div class="nextstepaction"]
> [Przewodnik Szybki start usługi Automation — tworzenie elementu runbook](./automation-quickstart-create-runbook.md)

