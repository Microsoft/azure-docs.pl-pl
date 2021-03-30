---
title: Diagnozowanie błędów artefaktów na Azure DevTest Labs maszynie wirtualnej
description: DevTest Labs dostarczają informacji, których można użyć do diagnozowania błędu artefaktu. W tym artykule opisano sposób rozwiązywania problemów z błędami artefaktów.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 440ce6a537ac8d6a21ae8010bfbb3c38a82bf01e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480817"
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
3. W lewym panelu w obszarze **Ogólne** wybierz pozycję **artefakty**. Zostanie wyświetlona lista artefaktów skojarzonych z tą maszyną wirtualną. Nazwa artefaktu i stan artefaktu są wskazane.

   ![Stan artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. Wybierz artefakt, który pokazuje stan **niepowodzenia** . Artefakt zostanie otwarty. Zostanie wyświetlony komunikat o rozszerzeniu, który zawiera szczegółowe informacje o błędzie artefaktu.

   ![Komunikat o błędzie artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Rozwiązywanie problemów z błędami artefaktów z poziomu maszyny wirtualnej

1. Zaloguj się do maszyny wirtualnej zawierającej artefakt, który chcesz zdiagnozować.
2. Przejdź do C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension \\ *1,9*\Status, gdzie *1,9* to numer wersji rozszerzenia niestandardowego skryptu platformy Azure.

   ![Plik stanu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. Otwórz plik **stanu** .

Aby uzyskać instrukcje dotyczące znajdowania plików dziennika na maszynie wirtualnej z **systemem Linux** , zobacz następujący artykuł: [Korzystanie z rozszerzenia niestandardowego skryptu platformy Azure w wersji 2 z maszynami wirtualnymi z systemem Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Powiązane wpisy w blogu
* [Przyłączanie maszyny wirtualnej do istniejącej domeny Active Directory przy użyciu szablonu Menedżer zasobów w DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać repozytorium git do laboratorium](devtest-lab-add-artifact-repo.md).

