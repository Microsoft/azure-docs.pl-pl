---
title: Skonfiguruj laboratorium ukierunkowane na głębokie uczenie się za pomocą Azure Lab Services | Microsoft Docs
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
ms.openlocfilehash: 0dab5f3dcdfb6ddabbd94960dcf8a8bf2bce98af
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890768"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Skonfiguruj laboratorium ukierunkowane na głębokie uczenie się w przetwarzaniu języka naturalnego przy użyciu Azure Lab Services
W tym artykule opisano sposób konfigurowania laboratorium ukierunkowanego na głębokie uczenie w ramach przetwarzania języka naturalnego (NLP) przy użyciu Azure Lab Services. Przetwarzanie języka naturalnego (NLP) jest formą sztucznej analizy (AI), która umożliwia komputerom z translacją, rozpoznawanie mowy i innymi funkcjami interpretacji języka.  

Studenci tworzący klasę NLPą mogą uzyskać maszynę wirtualną z systemem Linux, aby dowiedzieć się, jak zastosować algorytmy sieciowe neuronowych w celu opracowania modeli uczenia głębokiego, które są używane do analizowania zapisanych języków ludzkich. 

## <a name="lab-configuration"></a>Konfiguracja laboratorium
Aby skonfigurować to laboratorium, musisz mieć subskrypcję platformy Azure, aby rozpocząć pracę. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) . Gdy masz subskrypcję platformy Azure, możesz utworzyć nowe konto laboratorium w Azure Lab Services lub użyć istniejącego konta laboratorium. Zapoznaj się z poniższym samouczkiem dotyczącym tworzenia nowego konta laboratorium: [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md).
 
Po utworzeniu konta laboratorium należy włączyć następujące ustawienia na koncie laboratorium: 

| Ustawienie konta laboratorium | Instrukcje |
| ----------- | ------------ |  
| Obrazy z witryny Marketplace | Włącz obraz Data Science Virtual Machine dla systemu Linux (Ubuntu) do użycia w ramach konta laboratorium.  Zapoznaj się z poniższym artykułem, aby uzyskać instrukcje: [Określ obrazy z witryny Marketplace dostępne dla twórców laboratorium](specify-marketplace-images.md). | 

Postępuj zgodnie z [tym samouczkiem](tutorial-setup-classroom-lab.md) , aby utworzyć nowe laboratorium i zastosować następujące ustawienia:

| Ustawienia laboratorium | Wartość/instrukcje | 
| ------------ | ------------------ |
| Rozmiar maszyny wirtualnej (VM) | Mały procesor GPU (COMPUTE). Ten rozmiar najlepiej nadaje się w przypadku aplikacji intensywnie korzystających z obliczeń i sieci, takich jak sztuczna inteligencja i uczenie głębokie. |
| Obraz maszyny wirtualnej | [Data Science Virtual Machine dla systemu Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Ten obraz zawiera platformy uczenia głębokiego i narzędzia do uczenia maszynowego i analizy danych. Aby wyświetlić pełną listę zainstalowanych narzędzi na tym obrazie, zapoznaj się z następującym artykułem: [co obejmuje DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Włącz Podłączanie pulpitu zdalnego | <p>Włączenie tego ustawienia umożliwi nauczycielom i uczniom łączenie się ze swoimi Virtual Machines (maszyną wirtualną) przy użyciu Pulpit zdalny (RDP).</p><p>**Ważne**: włączenie tego ustawienia powoduje otwarcie tylko portu **RDP** na maszynach z systemem Linux. Jeśli protokół RDP jest już zainstalowany i skonfigurowany w obrazie maszyny wirtualnej, możesz połączyć się z maszynami wirtualnymi za pośrednictwem protokołu RDP, nie wykonując żadnych dodatkowych kroków. <p>Jeśli na obrazie maszyny wirtualnej nie zainstalowano i skonfigurowano protokół RDP, należy połączyć się z maszyną z systemem Linux przy użyciu protokołu SSH po raz pierwszy, a następnie zainstalować pakiety RDP i GUI, aby umożliwić uczniom łączenie się z maszyną z systemem Linux przy użyciu protokołu RDP. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie pulpit zdalny do nawiązywania połączenia z maszyną wirtualną z systemem Linux na platformie Azure](../../virtual-machines/linux/use-remote-desktop.md). Następnie opublikujesz obraz tak, aby studenci mogli na maszynach wirtualnych z systemem Linux mieć protokół RDP.  |

Obraz Data Science Virtual Machine dla systemu Linux zapewnia niezbędne platformy uczenia głębokiego i narzędzia wymagane dla tego typu klasy. W związku z tym po utworzeniu komputera szablonu nie trzeba będzie go jeszcze modyfikować. Można je opublikować dla uczniów. Wybierz przycisk **Publikuj** na stronie szablon, aby opublikować szablon w laboratorium.  

## <a name="cost"></a>Koszty
Jeśli chcesz oszacować koszt tego laboratorium, możesz użyć poniższego przykładu: 

W przypadku klasy 25 studentów z upływem 20 godzin planowanego czasu zajęć i 10 godzin przydziału dla prac domowych lub przydziałów cena za laboratorium wynosi-25 studentów * (20 + 10) godz. * 139 jednostek laboratoryjnych * 0,01 USD za godzinę = 1042,5 USD

Więcej szczegółowych informacji na temat cen można znaleźć w temacie [Azure Lab Services Cennik](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie
W tym artykule omówiono procedurę tworzenia laboratorium dla klasy przetwarzania języka naturalnego. Możesz użyć podobnej konfiguracji dla innych klas uczenia głębokiego.

## <a name="next-steps"></a>Następne kroki
Następne kroki są wspólne do konfigurowania dowolnego laboratorium:

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users). 

