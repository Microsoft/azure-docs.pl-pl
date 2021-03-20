---
title: Uruchom ponownie maszynę wirtualną w laboratorium w Azure DevTest Labs | Microsoft Docs
description: W tym artykule przedstawiono kroki umożliwiające szybkie i łatwe ponowne uruchomienie maszyn wirtualnych w programie Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d2bcbcbea613aa84da91789fb9f118afd7023fab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481994"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Uruchom ponownie maszynę wirtualną w laboratorium w Azure DevTest Labs
Możesz szybko i łatwo uruchomić ponownie maszynę wirtualną w DevTest Labs, wykonując kroki opisane w tym artykule. Przed ponownym uruchomieniem maszyny wirtualnej Rozważ następujące kwestie:

- Aby można było włączyć funkcję ponownego uruchamiania, maszyna wirtualna musi być uruchomiona.
- Jeśli użytkownik jest połączony z uruchomioną maszyną wirtualną po ponownym uruchomieniu, musi ponownie nawiązać połączenie z maszyną wirtualną po rozpoczęciu tworzenia kopii zapasowej.
- Jeśli artefakt jest stosowany po ponownym uruchomieniu maszyny wirtualnej, pojawi się ostrzeżenie, że artefakt może nie zostać zastosowany.

    ![Ostrzeżenie podczas ponownego uruchamiania podczas stosowania artefaktów](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Jeśli maszyna wirtualna została wstrzymana podczas stosowania artefaktu, można użyć funkcji Uruchom ponownie maszynę wirtualną jako potencjalnego rozwiązania problemu.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Kroki umożliwiające ponowne uruchomienie maszyny wirtualnej w laboratorium w Azure DevTest Labs
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
1. Z listy laboratoriów wybierz laboratorium obejmujące maszynę wirtualną, którą chcesz ponownie uruchomić.
1. W lewym panelu wybierz pozycję **My Virtual Machines**.
1. Z listy maszyn wirtualnych wybierz działającą maszynę wirtualną.
1. W górnej części okienka zarządzanie maszyną wirtualną wybierz pozycję **Uruchom ponownie**.

    ![Przycisk ponownego uruchamiania maszyny wirtualnej](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitoruj stan ponownego uruchomienia, wybierając ikonę **powiadomienia** w prawym górnym rogu okna.

    ![Wyświetlanie stanu ponownego uruchomienia maszyny wirtualnej](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Możesz również ponownie uruchomić uruchomioną maszynę wirtualną, wybierając jej wielokropek (...) na liście **My Virtual Machines**.

![Uruchom ponownie maszynę wirtualną przez wielokropek](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Następne kroki
* Po ponownym uruchomieniu można ponownie nawiązać połączenie z maszyną wirtualną, wybierając pozycję **Połącz** w jej okienku zarządzania.
* Poznaj [galerię szablonów DevTest Labs Azure Resource Manager — szybki start](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
