---
title: Skonfiguruj laboratorium ukierunkowane na głębokie uczenie się za pomocą Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium ukierunkowane na głębokie uczenie w ramach przetwarzania języka naturalnego (NLP) przy użyciu Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 01199e76b7b9cb1a6360b5aba010d0cfd8936c23
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91251460"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Skonfiguruj laboratorium ukierunkowane na głębokie uczenie się w przetwarzaniu języka naturalnego przy użyciu Azure Lab Services
W tym artykule opisano sposób konfigurowania laboratorium ukierunkowanego na głębokie uczenie w ramach przetwarzania języka naturalnego (NLP) przy użyciu Azure Lab Services. Przetwarzanie języka naturalnego (NLP) jest formą sztucznej analizy (AI), która umożliwia komputerom z translacją, rozpoznawanie mowy i innymi funkcjami interpretacji języka.  

Studenci tworzący klasę NLPą mogą uzyskać maszynę wirtualną z systemem Linux, aby dowiedzieć się, jak zastosować algorytmy sieciowe neuronowych w celu opracowania modeli uczenia głębokiego, które są używane do analizowania zapisanych języków ludzkich. 

## <a name="lab-configuration"></a>Konfiguracja laboratorium
Aby skonfigurować to laboratorium, musisz mieć subskrypcję platformy Azure, aby rozpocząć pracę. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Gdy masz subskrypcję platformy Azure, możesz utworzyć nowe konto laboratorium w Azure Lab Services lub użyć istniejącego konta laboratorium. Zapoznaj się z poniższym samouczkiem dotyczącym tworzenia nowego konta laboratorium: [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md).
 
Po utworzeniu konta laboratorium należy włączyć następujące ustawienia na koncie laboratorium: 

| Ustawienie konta laboratorium | Instrukcje |
| ----------- | ------------ |  
| Obrazy z witryny Marketplace | Włącz obraz Data Science Virtual Machine dla systemu Linux (Ubuntu) do użycia w ramach konta laboratorium.  Zapoznaj się z poniższym artykułem, aby uzyskać instrukcje: [Określ obrazy z witryny Marketplace dostępne dla twórców laboratorium](specify-marketplace-images.md). | 

Postępuj zgodnie z [tym samouczkiem](tutorial-setup-classroom-lab.md) , aby utworzyć nowe laboratorium i zastosować następujące ustawienia:

| Ustawienia laboratorium | Wartość/instrukcje | 
| ------------ | ------------------ |
| Rozmiar maszyny wirtualnej (VM) | **Mały procesor GPU (COMPUTE)**. Ten rozmiar najlepiej nadaje się w przypadku aplikacji intensywnie korzystających z obliczeń i sieci, takich jak sztuczna inteligencja i uczenie głębokie. |
| Obraz maszyny wirtualnej | [Data Science Virtual Machine dla systemu Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Ten obraz zawiera platformy uczenia głębokiego i narzędzia do uczenia maszynowego i analizy danych. Aby wyświetlić pełną listę zainstalowanych narzędzi na tym obrazie, zapoznaj się z następującym artykułem: [co obejmuje DSVM?](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Włącz Podłączanie pulpitu zdalnego | <p>Obraz analizy danych jest już skonfigurowany do korzystania z programu X2Go, dzięki czemu nauczyciele i uczniowie mogą łączyć się przy użyciu klasycznego interfejsu użytkownika.  X2Go nie *wymaga włączenia* ustawienia **Włącz Podłączanie pulpitu zdalnego** .  To ustawienie należy włączyć tylko w przypadku wybrania opcji Użyj protokołu RDP.

>**Ważne**: Chociaż zalecamy używanie x2go z obrazem analizy danych, w przypadku wybrania opcji użycia protokołu RDP należy nawiązać połączenie z maszyną wirtualną z systemem Linux za pierwszym razem i zainstalować pakiety protokołów RDP i GUI.  Następnie ty/uczniowie mogą połączyć się z maszyną wirtualną z systemem Linux przy użyciu protokołu RDP później.  Aby uzyskać więcej informacji, zobacz [Włączanie graficznego pulpitu zdalnego dla maszyn wirtualnych z systemem Linux](how-to-enable-remote-desktop-linux.md).

Obraz Data Science Virtual Machine dla systemu Linux zapewnia niezbędne platformy uczenia głębokiego i narzędzia wymagane dla tego typu klasy. W związku z tym po utworzeniu komputera szablonu nie trzeba będzie go jeszcze modyfikować. Można je opublikować dla uczniów. Wybierz przycisk **Publikuj** na stronie szablon, aby opublikować szablon w laboratorium.  

## <a name="cost"></a>Koszt
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

