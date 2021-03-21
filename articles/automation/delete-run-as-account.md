---
title: Usuwanie Azure Automation konta Uruchom jako
description: W tym artykule opisano sposób usuwania konta Uruchom jako z programem PowerShell lub z poziomu Azure Portal.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055896"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Usuwanie Azure Automation konta Uruchom jako

Konta Uruchom jako w Azure Automation umożliwiają uwierzytelnianie w celu zarządzania zasobami w ramach Azure Resource Manager lub klasycznego modelu wdrażania platformy Azure przy użyciu elementów Runbook usługi Automation i innych funkcji automatyzacji. W tym artykule opisano, jak usunąć konto Uruchom jako lub klasyczne. Konto usługi Automation jest zachowywane podczas wykonywania tej akcji. Po usunięciu konta Uruchom jako możesz je ponownie utworzyć w Azure Portal lub przy użyciu dostarczonego skryptu programu PowerShell.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako

1. W witrynie Azure Portal otwórz konto usługi Automation.

2. W okienku po lewej stronie wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.

3. Na stronie właściwości Konta Uruchom jako wybierz konto Uruchom jako albo klasyczne konto Uruchom jako, które chcesz usunąć.

4. W okienku właściwości wybranego konta kliknij pozycję **Usuń**.

   ![Usuwanie konta Uruchom jako](media/delete-run-as-account/automation-account-delete-run-as.png)

5. W trakcie usuwania konta postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

## <a name="next-steps"></a>Następne kroki

Aby ponownie utworzyć konto Uruchom jako lub klasycznego konta Uruchom jako, zobacz [Tworzenie kont Uruchom jako](create-run-as-account.md).