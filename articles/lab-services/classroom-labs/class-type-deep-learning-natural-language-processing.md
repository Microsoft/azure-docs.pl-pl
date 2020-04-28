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
ms.openlocfilehash: 889d0d1e98f5c9947588011774d02e54f05edca1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257763"
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
| Włącz Podłączanie pulpitu zdalnego | Włącz. <p>Włączenie tego ustawienia umożliwi nauczycielom i uczniom łączenie się ze swoimi Virtual Machines (maszyną wirtualną) przy użyciu Pulpit zdalny (RDP).</p><p>**Ważne**: protokół RDP jest już zainstalowany i skonfigurowany na Data Science Virtual Machine obrazu systemu Linux. W związku z tym nauczyciele/uczniowie mogą łączyć się z maszynami wirtualnymi za pośrednictwem protokołu RDP bez dodatkowych kroków. Ponadto, jeśli musisz nawiązać połączenie z pulpitem graficznym, ten obraz ma już zainstalowany [serwer x2go](https://wiki.x2go.org/doku.php/doc:newtox2go) na maszynie wirtualnej. Studenci muszą zainstalować klienta X2Go na swoich komputerach lokalnych i musi użyć klienta do nawiązania połączenia. Aby uzyskać więcej informacji, zobacz następujące przewodniki: <ul><li>[Jak uzyskać dostęp do Data Science Virtual Machine dla systemu Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Nawiązywanie połączenia z szablonową maszyną wirtualną w celu zainstalowania pakietów RDP i interfejsu GUI](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

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

