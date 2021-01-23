---
title: Włącz rozszerzenie maszyny wirtualnej na podstawie Azure Portal
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na serwerach z obsługą usługi Azure ARC z systemem w środowiskach chmury hybrydowej z poziomu Azure Portal.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 43bbcef28b77e7c7112880fdac1bbd4809791cef
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728957"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Włącz rozszerzenia maszyny wirtualnej platformy Azure z poziomu Azure Portal

W tym artykule opisano sposób wdrażania i odinstalowywania rozszerzeń maszyn wirtualnych platformy Azure, które są obsługiwane przez serwery z obsługą usługi Azure ARC, na maszynę hybrydową z systemem Linux lub Windows za pomocą Azure Portal.

> [!NOTE]
> Rozszerzenie maszyny wirtualnej Key Vault (wersja zapoznawcza) nie obsługuje wdrożenia z Azure Portal, tylko przy użyciu interfejsu wiersza polecenia platformy Azure, Azure PowerShell lub przy użyciu szablonu Azure Resource Manager.

## <a name="enable-extensions-from-the-portal"></a>Włącz rozszerzenia portalu

Rozszerzenia maszyny wirtualnej mogą być stosowane do Twojego łuku dla maszyny zarządzanej przez serwer za pomocą Azure Portal.

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com).

2. W portalu przejdź do opcji **serwery — Azure Arc** i wybierz maszynę hybrydową z listy.

3. Wybierz pozycję **rozszerzenia**, a następnie wybierz pozycję **Dodaj**. Wybierz odpowiednie rozszerzenie z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze. W tym przykładzie zostanie wdrożone rozszerzenie maszyny wirtualnej Log Analytics.

    ![Wybierz rozszerzenie maszyny wirtualnej dla wybranej maszyny](./media/manage-vm-extensions/add-vm-extensions.png)

    W poniższym przykładzie przedstawiono instalację rozszerzenia maszyny wirtualnej Log Analytics z Azure Portal:

    ![Zainstaluj rozszerzenie maszyny wirtualnej Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    Aby ukończyć instalację, należy podać identyfikator obszaru roboczego i klucz podstawowy. Jeśli nie wiesz, jak znaleźć te informacje, zobacz temat [Uzyskiwanie identyfikatora i klucza obszaru roboczego](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. Po potwierdzeniu wymaganych informacji wybierz pozycję **Utwórz**. Zostanie wyświetlone podsumowanie wdrożenia i można sprawdzić stan wdrożenia.

>[!NOTE]
>Chociaż wiele rozszerzeń można wsadowo i przetwarzać, są one instalowane seryjnie. Po zakończeniu instalacji pierwszego rozszerzenia zostanie podjęta próba instalacji następnego rozszerzenia.

## <a name="list-extensions-installed"></a>Zainstalowano rozszerzenia list

Możesz uzyskać listę rozszerzeń maszyn wirtualnych na serwerze z włączonym Łukem z Azure Portal. Wykonaj następujące kroki, aby je zobaczyć.

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com).

2. W portalu przejdź do opcji **serwery — Azure Arc** i wybierz maszynę hybrydową z listy.

3. Wybierz **rozszerzenia** i zostanie zwrócona lista zainstalowanych rozszerzeń.

    ![Wyświetl listę rozszerzeń maszyny wirtualnej wdrożonych na wybranej maszynie](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>Odinstaluj rozszerzenie

Można usunąć jedno lub więcej rozszerzeń z serwera z obsługą łuku z Azure Portal. Wykonaj następujące kroki, aby usunąć rozszerzenie.

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com).

2. W portalu przejdź do opcji **serwery — Azure Arc** i wybierz maszynę hybrydową z listy.

3. Wybierz **rozszerzenia**, a następnie wybierz rozszerzenie z listy zainstalowanych rozszerzeń.

4. Wybierz pozycję **Odinstaluj** , a po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak** , aby wykonać operację.

## <a name="next-steps"></a>Następne kroki

- Można wdrażać i usuwać rozszerzenia maszyn wirtualnych oraz zarządzać nimi przy użyciu szablonów [interfejsu wiersza polecenia platformy Azure](manage-vm-extensions-cli.md), [programu PowerShell](manage-vm-extensions-powershell.md)lub [Azure Resource Manager](manage-vm-extensions-template.md).

- Informacje dotyczące rozwiązywania problemów można znaleźć w [podręczniku Rozwiązywanie problemów z maszynami](troubleshoot-vm-extensions.md)wirtualnymi.