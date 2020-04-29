---
title: Diagnozowanie błędów artefaktów na Azure DevTest Labs maszynie wirtualnej
description: DevTest Labs dostarczają informacji, których można użyć do diagnozowania błędu artefaktu. W tym artykule opisano sposób rozwiązywania problemów z błędami artefaktów.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 7229f1ee4061eb38b7c6da09df21102ab302ab42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76760321"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnozowanie błędów artefaktów w laboratorium 
Po utworzeniu artefaktu możesz sprawdzić, czy zakończyło się powodzeniem, czy niepowodzeniem. Dzienniki artefaktów w Azure DevTest Labs zawierają informacje, których można użyć do diagnozowania błędu artefaktu. Istnieje kilka opcji wyświetlania informacji o dzienniku artefaktów dla maszyny wirtualnej z systemem Windows:

* W witrynie Azure Portal
* Na maszynie wirtualnej

> [!NOTE]
> Aby upewnić się, że błędy są poprawnie zidentyfikowane i wyjaśnione, ważne jest, aby artefakt miał prawidłową strukturę. Aby uzyskać informacje na temat poprawnego konstruowania artefaktu, zobacz [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md). Aby zobaczyć przykład prawidłowo uporządkowanej artefaktu, sprawdź artefakty [typów parametrów testu](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) .

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Rozwiązywanie problemów z błędami artefaktów przy użyciu Azure Portal

1. W Azure Portal na liście zasobów wybierz swoje laboratorium.
2. Wybierz maszynę wirtualną z systemem Windows, która zawiera artefakt, który chcesz zbadać.
3. W lewym panelu w obszarze **Ogólne**wybierz pozycję **artefakty**. Zostanie wyświetlona lista artefaktów skojarzonych z tą maszyną wirtualną. Nazwa artefaktu i stan artefaktu są wskazane.

   ![Stan artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Wybierz artefakt, który pokazuje stan **niepowodzenia** . Artefakt zostanie otwarty. Zostanie wyświetlony komunikat o rozszerzeniu, który zawiera szczegółowe informacje o błędzie artefaktu.

   ![Komunikat o błędzie artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Rozwiązywanie problemów z błędami artefaktów z poziomu maszyny wirtualnej

1. Zaloguj się do maszyny wirtualnej zawierającej artefakt, który chcesz zdiagnozować.
2. Przejdź do C:\Packages\Plugins\Microsoft.COMPUTE.CustomScriptExtension\\*1,9*\Status, gdzie *1,9* to numer wersji rozszerzenia niestandardowego skryptu platformy Azure.

   ![Plik stanu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Otwórz plik **stanu** .

Aby uzyskać instrukcje dotyczące znajdowania plików dziennika na maszynie wirtualnej z **systemem Linux** , zobacz następujący artykuł: [Korzystanie z rozszerzenia niestandardowego skryptu platformy Azure w wersji 2 z maszynami wirtualnymi z systemem Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Powiązane wpisy w blogu
* [Przyłączanie maszyny wirtualnej do istniejącej domeny Active Directory przy użyciu szablonu Menedżer zasobów w DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać repozytorium git do laboratorium](devtest-lab-add-artifact-repo.md).

