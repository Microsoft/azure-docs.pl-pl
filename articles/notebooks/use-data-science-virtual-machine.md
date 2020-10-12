---
title: Korzystanie z usługi Azure Data Science Virtual Machines
description: Dowiedz się, jak nawiązać połączenie z usługą Azure Data Science Virtual Machine (DSVM), aby zwiększyć moc obliczeniową dostępną dla Azure Notebooks wersji zapoznawczej.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 291f1ac093568f50ad6146b70fa1ef69263b7b5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85830314"
---
# <a name="use-azure-data-science-virtual-machines"></a>Korzystanie z usługi Azure Data Science Virtual Machines

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Domyślnie projekty są uruchamiane w **bezpłatnej warstwie obliczeniowej** , co jest ograniczone do 4 GB pamięci i 1 GB danych, aby zapobiec nadużyciu. Te ograniczenia można ominąć przy użyciu innej maszyny wirtualnej, która została zainicjowana w ramach subskrypcji platformy Azure. W tym celu najlepszym wyborem jest Data Science Virtual Machine platformy Azure (DSVM) przy użyciu obrazu **Data Science Virtual Machine for Linux (Ubuntu)** . Taki DSVM jest wstępnie skonfigurowany z wszystko, czego potrzebujesz do Azure Notebooks i pojawia się automatycznie na liście rozwijanej **uruchamiania** w Azure Notebooks.

> [!Note]
> Azure Notebooks jest obsługiwana tylko na DSVMs utworzonych przy użyciu obrazu Ubuntu w systemie Linux. Notesy nie są obsługiwane w obrazach CentOS systemu Windows 2012, Windows 2016 lub Linux.

## <a name="create-a-dsvm-instance"></a>Tworzenie wystąpienia DSVM

Aby utworzyć nowe wystąpienie DSVM, postępuj zgodnie z instrukcjami dotyczącymi [tworzenia Ubuntu Data Science VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Aby uzyskać więcej informacji, w tym informacje o cenach, zobacz [Virtual Machines analizy danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Połącz z DSVM

Po utworzeniu DSVM wybierz listę rozwijaną **uruchamiania** na pulpicie nawigacyjnym projektu Azure Notebooks i wybierz odpowiednie wystąpienie DSVM. Lista rozwijana zawiera wystąpienia DSVM, jeśli są spełnione następujące warunki:

- Zalogowano się do Azure Notebooks przy użyciu konta korzystającego z usługi Azure Active Directory (AAD), np. konta firmowego.
- Twoje konto jest połączone z subskrypcją platformy Azure.
- W tej subskrypcji masz co najmniej jedną maszynę wirtualną z dostępem do czytnika, która używa obrazu Data Science Virtual Machine dla systemu Linux (Ubuntu).

![Wystąpienia Data Science Virtual Machine na liście rozwijanej na pulpicie nawigacyjnym projektu](media/project-compute-tier-dsvm.png)

Po wybraniu wystąpienia DSVM, Azure Notebooks może monitować o podanie określonych poświadczeń komputera używanych podczas tworzenia maszyny wirtualnej.

> [!Important]
> Nazwa użytkownika musi być mała, aby można jej było używać z JupyterHub.

Jeśli którykolwiek z warunków nie zostanie spełniony, nadal możesz połączyć się z DSVM. Z listy rozwijanej wybierz opcję przetwarzanie **bezpośrednie** , która wyświetla komunikat o nazwie (do wyświetlenia na liście), adres IP i port maszyny wirtualnej (zazwyczaj 8000, domyślny port, do którego nasłuchuje JupyterHub) i poświadczenia maszyny wirtualnej:

![Monituj o zbieranie informacji o serwerze dla opcji przetwarzania bezpośredniego](media/project-compute-tier-direct.png)

Te wartości są uzyskiwane ze strony DSVM w Azure Portal.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Uzyskiwanie dostępu do plików Azure Notebooks z DSVM

Dostęp do systemu plików jest obsługiwany w DSVM wersjach 19.06.15 lub nowszych. Aby sprawdzić wersję, najpierw Połącz się z usługą DSVM za pośrednictwem protokołu SSH, a następnie uruchom następujące polecenie: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (należy użyć dokładnego adresu IP podanego w tym miejscu). Numer wersji jest pokazywany w danych wyjściowych dla "wersja".

Aby zachować zgodność ścieżek plików z bezpłatną warstwą **obliczeniową** , można otworzyć tylko jeden projekt na raz w DSVM. Aby otworzyć nowy projekt, należy najpierw zamknąć otwarty projekt.

Gdy projekt jest uruchamiany na maszynie wirtualnej, pliki są instalowane w katalogu głównym serwera Jupyter (katalog przedstawiony w JupyterHub), zastępując domyślne pliki Azure Notebooks. Po wyłączeniu maszyny wirtualnej za pomocą przycisku **zamykania** w interfejsie użytkownika notesu Azure Notebooks przywraca domyślne pliki.

![Przycisk zamykania w Azure Notebooks](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Tworzenie nowych użytkowników DSVM

Jeśli wielu użytkowników współużytkuje DSVM, można uniknąć zablokowania siebie przez utworzenie i użycie użytkownika DSVM dla każdego użytkownika notesu:

1. Na [Azure Portal](https://portal.azure.com)przejdź do maszyny wirtualnej.
1. W obszarze **Pomoc techniczna i rozwiązywanie problemów** na lewym marginesie wybierz pozycję **Resetuj hasło**.
1. Wprowadź nową **nazwę użytkownika**. Nazwa użytkownika musi być mała, aby można jej było używać z JupyterHub. Wprowadź hasło. Następnie wybierz pozycję **Aktualizuj**. (Nie dotyczy to istniejących nazw użytkowników).
1. Powtórz poprzedni krok dla każdego dodatkowego użytkownika.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o DSVMs na temat [wprowadzenia do analizy danych na platformie Azure Virtual Machines](/azure/machine-learning/data-science-virtual-machine/overview).
