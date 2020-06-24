---
title: Konfigurowanie laboratorium skryptów powłoki systemu Linux przy użyciu Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium do uczenia skryptów powłoki w systemie Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 67d3bc1977affc83fd7b55d128e3aee503375ff1
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84899011"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Konfigurowanie laboratorium do uczenia skryptów powłoki w systemie Linux
W tym artykule opisano sposób konfigurowania laboratorium do uczenia skryptów powłoki w systemie Linux. Obsługa skryptów jest przydatną częścią administrowania systemem, która umożliwia administratorom uniknięcie powtarzających się zadań. W tym przykładowym scenariuszu Klasa obejmuje tradycyjne skrypty bash oraz ulepszone skrypty. Ulepszone skrypty to skrypty, które łączą bash polecenia i Ruby. Takie podejście umożliwia używanie języka Ruby do przekazywania danych wokół i bash poleceń w celu współdziałania z powłoką. 

Studenci korzystający z tych klas skryptów otrzymują maszynę wirtualną z systemem Linux, aby poznać podstawy systemu Linux, a także zapoznać się z obsługą skryptów powłoki bash. Na maszynie wirtualnej z systemem Linux jest włączona funkcja dostępu do pulpitu zdalnego z zainstalowanym edytorem tekstu w języku [regedit](https://help.gnome.org/users/gedit/stable/) i [Visual Studio Code](https://code.visualstudio.com/) .

## <a name="lab-configuration"></a>Konfiguracja laboratorium
Aby skonfigurować to laboratorium, musisz mieć subskrypcję platformy Azure, aby rozpocząć pracę. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Gdy masz subskrypcję platformy Azure, możesz utworzyć nowe konto laboratorium w Azure Lab Services lub użyć istniejącego konta laboratorium. Zapoznaj się z poniższym samouczkiem dotyczącym tworzenia nowego konta laboratorium: [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md).

Po utworzeniu konta laboratorium należy włączyć następujące ustawienia na koncie laboratorium: 

| Ustawienie konta laboratorium | Instrukcje |
| ----------- | ------------ |  
| Obrazy z witryny Marketplace | Włącz obraz Ubuntu Server 18,04 LTS do użycia w ramach Twojego konta laboratorium. Aby uzyskać więcej informacji, zobacz temat [Określanie obrazów z witryny Marketplace dostępnych dla twórców laboratorium](specify-marketplace-images.md). | 

Postępuj zgodnie z [tym samouczkiem](tutorial-setup-classroom-lab.md) , aby utworzyć nowe laboratorium i zastosować następujące ustawienia:

| Ustawienia laboratorium | Wartość/instrukcje | 
| ------------ | ------------------ |
| Rozmiar maszyny wirtualnej (VM) | Mały  |
| Obraz maszyny wirtualnej | Ubuntu Server 18,04 LTS|
| Włącz Podłączanie pulpitu zdalnego | Włącz. <p>Włączenie tego ustawienia umożliwi nauczycielom i uczniom łączenie się z maszynami wirtualnymi przy użyciu pulpitu zdalnego (RDP). Aby uzyskać więcej informacji, zobacz [Włączanie maszyn wirtualnych pulpitu zdalnego dla systemu Linux w laboratorium w Azure Lab Services](how-to-enable-remote-desktop-linux.md). </p>|

## <a name="install-desktop-and-rdp"></a>Zainstaluj program Desktop i protokół RDP
Na obrazie LTS serwera Ubuntu Server 18,04 nie jest zainstalowany serwer pulpitu zdalnego RDP. Postępuj zgodnie z instrukcjami podanymi w artykule [Instalowanie i konfigurowanie pulpit zdalny, aby nawiązać połączenie z maszyną wirtualną z systemem Linux w systemie Azure](../virtual-machines/linux/use-remote-desktop.md) , aby zainstalować pakiety, które są konieczne na komputerze szablonu do łączenia się za pośrednictwem protokołu RDP (Remote Desktop Protocol).

## <a name="install-ruby"></a>Instalowanie języka Ruby
Ruby to dynamiczny język, który może być połączony ze skryptami bash. W tej sekcji pokazano, jak `apt-get` zainstalować najnowszą wersję języka [Ruby](https://www.ruby-lang.org/).

1. Zainstaluj aktualizacje, uruchamiając następujące polecenia:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Zainstaluj język [Ruby](https://www.ruby-lang.org/).  Ruby to dynamiczny język, który może być połączony ze skryptami bash. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Instalowanie narzędzi programistycznych
W tej sekcji pokazano, jak zainstalować kilka edytorów tekstu. Program regedit jest domyślnym edytorem tekstu dla środowiska pulpitu GNOME. Jest ona zaprojektowana jako edytor tekstu ogólnego przeznaczenia. Visual Studio Code to edytor tekstu, który obejmuje obsługę debugowania i integracji kontroli źródła.

> [!NOTE]
> Dostępnych jest kilka różnych edytorów tekstu. Visual Studio Code i program regedit są zaledwie dwa przykłady.

1. Zainstaluj program [regedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).  Program Visual Studio Code można zainstalować przy użyciu magazynu Snap.  Aby zapoznać się z alternatywnymi opcjami instalacji, zobacz [Visual Studio Code pliki do pobrania](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Szablon jest teraz aktualizowany i ma zarówno język programowania, jak i narzędzia programistyczne potrzebne do ukończenia laboratorium. Obraz szablonu można teraz opublikować w laboratorium. Wybierz przycisk **Publikuj** na stronie szablon, aby opublikować szablon w laboratorium.  

## <a name="cost"></a>Koszty 
Jeśli chcesz oszacować koszt tego laboratorium, możesz użyć poniższego przykładu:
 
W przypadku klasy 25 studentów z upływem 20 godzin planowanego czasu zajęć i 10 godzin przydziału dla prac domowych lub przydziałów, Cena za laboratorium to: 

25 studentów * (20 + 10) godz. * 20 jednostek laboratoryjnych * 0,01 USD za godzinę = 150 USD

Aby uzyskać więcej informacji na temat cen, można znaleźć w następującym dokumencie: [cennik Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie
W tym artykule omówiono procedurę tworzenia laboratorium dla klas skryptów. Chociaż ten artykuł koncentruje się na konfigurowaniu narzędzi do obsługi skryptów Ruby na komputerze z systemem Linux, można użyć tego samego Instalatora dla innych klas skryptów, takich jak Python w systemie Linux.

## <a name="next-steps"></a>Następne kroki
Następne kroki są wspólne do konfigurowania dowolnego laboratorium:

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users). 





