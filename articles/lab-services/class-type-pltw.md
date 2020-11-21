---
title: Konfigurowanie projektu do realizacji w laboratoriach z Azure Lab Services
description: Dowiedz się, jak skonfigurować laboratoria do uczenia się projektów w sposób klasy.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024623"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Konfigurowanie laboratoriów dla liderów projektu — klasy metod

[Project ołów w ten sposób (PLTW)](https://www.pltw.org/) to organizacja non profit, która udostępnia PreK &ndash; 12 dla Stany Zjednoczone w nauce technologicznej, inżynieryjnej i biomedycznej.  W każdej klasie PLTW studenci korzystają z różnych aplikacji w ramach praktycznego doświadczenia.  Wiele aplikacji oprogramowania wymaga szybkiego procesora CPU lub, w niektórych przypadkach, procesora GPU.  W tym artykule opisano sposób konfigurowania laboratoriów dla następujących klas PLTW, które zazwyczaj są oferowane studentom w kategorii 9 &ndash; 12:

- **Wprowadzenie do projektowania inżynieryjnego**

    Studenci są wprowadzani do procesu projektowania inżynieryjnego, który obejmuje użycie oprogramowania Autodesk do projektowania aplikacji trójwymiarowych [(CAD)](https://www.autodesk.com/products/inventor/new-features) .

- **Zasady inżynierii**
    
    Studenci poznają mechanizmy inżynieryjne, siłę strukturalną i materiałową oraz automatyzację.  Ta klasa używa oprogramowania, takiego jak [stałe MD](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), [Projektant mostka dla punktów zachodnich](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)i [symulacja armii](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Inżynieria inżynieryjna i architektura**

    Uczniowie uczyć się kompilowania i projektowania witryny i opracowywaniu jej przy użyciu oprogramowania [Autodesk Revit](https://www.autodesk.com/products/revit/overview) Architecture Designing for 3D Building Modeling (BIM).

- **Produkcja Zintegrowana z komputerem**

    Studenci przedstawiają nowoczesne procesy produkcyjne, które obejmują naznanie i automatyzację.   W tej klasie studenci wykorzystują program Autodesk do tworzenia [spisów](https://www.autodesk.com/products/inventor/new-features) oprogramowania, a na przykład oprogramowanie [do produkcji](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Cyfrowe elektronika**

    Studenci badają elektroniczne obwody logiczne i urządzenia, korzystając z [Narodowego instrumentu Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) symulacji i oprogramowania do projektowania obwodów.

- **Projektowanie inżynieryjne i programowanie**

    Uczniowie przyczyniają się do kompleksowego rozwiązania, łącząc badania, projekt i testy, które znajdują się w panelu inżynierów.  W tej klasie studenci korzystają z oprogramowania [programu CAD](https://www.autodesk.com/products/inventor/new-features) w programie Autodesk.

- **Podstawowe informacje o nauce komputerowej**

    Studenci są wprowadzani do koncepcji i narzędzi obliczeniowych.  Zaczynają się one od programowania opartego na blokach, a następnie przenosimy do kodowania opartego na tekście przy użyciu środowisk kodowania, takich jak [bloki VEXcode V5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Zasady dotyczące nauki komputerowej**
    
    Studenci rozwijają wiedzę programistyczną w języku [Python](https://www.python.org/) za pomocą [środowiska programistycznego kodu Microsoft Visual Studio](https://code.visualstudio.com/). 

- **Komputer A**

    Studenci rozszerzają swoje kompetencje programistyczne w tej klasie przez uczenie się tworzenia aplikacji mobilnych.  W tej klasie poznają [język Java](https://www.java.com/) przy użyciu [środowiska deweloperskiego kodu Microsoft Visual Studio](https://code.visualstudio.com/).  Studenci używają również emulatora, który umożliwia im uruchamianie i testowanie kodu aplikacji mobilnej.  Aby uzyskać informacje na temat sposobu konfigurowania emulatora w Azure Lab Services, [skontaktuj się z Azure Lab Services](mailto:AzLabsCOVIDSupport@microsoft.com).

Aby zapoznać się z pełną listą oprogramowania klasy, przejdź do [witryny PLTW](https://www.pltw.org/pltw-software) dla każdej klasy.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby rozpocząć konfigurowanie laboratoriów dla programu PLTW, musisz mieć subskrypcję platformy Azure i konto laboratorium. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). 

Po uzyskaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Konfigurowanie konta laboratorium](./tutorial-setup-lab-account.md). Możesz również użyć istniejącego konta laboratorium.

Po skonfigurowaniu konta laboratorium należy utworzyć oddzielne laboratorium dla każdej sesji klasy PLTW, którą oferuje Twoja szkoła.  Zalecamy również utworzenie oddzielnych obrazów dla każdego typu klasy PLTW.  Aby uzyskać więcej informacji na temat struktury laboratoriów i obrazów, zapoznaj się z wpisem w blogu [przenoszonym z poziomu laboratorium fizycznego na Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium

Włącz ustawienia konta laboratorium zgodnie z opisem w poniższej tabeli. Aby uzyskać więcej informacji o sposobie włączania obrazów w portalu Azure Marketplace, zobacz temat [Określanie obrazów portalu Azure Marketplace dostępnych dla twórców laboratorium](./specify-marketplace-images.md).

| Ustawienie konta laboratorium | Instrukcje |
| -------------------- | ----- |
| Obraz z witryny Marketplace | Włącz obraz systemu Windows 10 Pro do użycia w ramach konta laboratorium. |

<br>

### <a name="lab-settings"></a>Ustawienia laboratorium
Rozmiar maszyny wirtualnej (VM), która jest zalecana dla klas PLTW, zależy od typów obciążeń wykonywanych przez uczniów w klasie.  W przypadku wymienionych wcześniej klas zalecamy użycie małych procesorów GPU (wizualizacji) i dużych rozmiarów maszyn wirtualnych. Podczas konfigurowania laboratoriów dla klas PLTW zapoznaj się ze wskazówkami w poniższej tabeli:

| Ustawienie laboratorium | Wartość i opis | Zalecenie dotyczące klasy |
| ------------ | ------------------ | --- |
| Rozmiar maszyny wirtualnej | **Mały procesor GPU (wizualizacja)**<br>Najlepiej dopasowane do zdalnej wizualizacji, przesyłania strumieniowego, gier i kodowania za pomocą platform, takich jak OpenGL i DirectX. | Zalecamy korzystanie z tego rozmiaru dla następujących klas PLTW: inżynierii inżynieryjnej i architektury, cyfrowej elektroniki, zintegrowanej produkcji komputerów oraz projektowania i programowania.
| Rozmiar maszyny wirtualnej | **Duży**<br>Najlepiej dopasowane do aplikacji wymagających szybszych procesorów CPU, lepszej wydajności dysków lokalnych, dużych baz danych i pamięci podręcznych dużych ilości pamięci. | Zalecamy korzystanie z tego rozmiaru dla następujących klas PLTW: wprowadzenie do projektowania inżynieryjnego, zasady inżynierii, podstawowe informacje o nauce komputerowej, zasady analizy komputera i informacje o nauce komputerowej. |

<br>

### <a name="license-server"></a>Serwer licencji
Większość oprogramowania używanego we wcześniejszych klasach PLTW *nie* wymaga dostępu do serwera licencji.  Należy jednak uzyskać dostęp do serwera licencji, jeśli planujesz używać modelu licencjonowania sieci Autodesk dla następującego oprogramowania:
-   Revit
-   Inwentarz
-   Kamera zapasowa

Aby korzystać z licencjonowania sieci za pomocą oprogramowania Autodesk, [PLTW zawiera szczegółowe instrukcje](https://www.pltw.org/pltw-software) dotyczące instalowania Menedżera licencji sieci Autodesk na serwerze licencji.  Ten serwer licencji znajduje się zwykle w sieci lokalnej lub hostowanej na maszynie wirtualnej platformy Azure w ramach usługi Azure Virtual Network.

Po skonfigurowaniu serwera licencji musisz nawiązać [połączenie równorzędne z siecią wirtualną](./how-to-connect-peer-virtual-network.md) przy użyciu [konta laboratorium](./tutorial-setup-lab-account.md). *Przed* utworzeniem laboratorium należy wykonać komunikację równorzędną sieci, aby maszyny wirtualne laboratorium mogły uzyskiwać dostęp do serwera licencji i na odwrót.

Pliki licencji generowane przez Autodesk osadzają adres MAC serwera licencji.  Jeśli zdecydujesz się na hostowanie serwera licencji przy użyciu maszyny wirtualnej platformy Azure, pamiętaj, aby upewnić się, że adres MAC serwera licencji nie jest zmieniany. Jeśli adres MAC ulegnie zmianie, należy ponownie wygenerować pliki licencjonowania. Aby uniemożliwić zmianę adresu MAC, wykonaj następujące czynności:

- [Ustaw statyczny prywatny adres IP i Mac](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) dla maszyny wirtualnej platformy Azure, która hostuje serwer licencji.
- Należy pamiętać o skonfigurowaniu zarówno konta laboratorium, jak i sieci wirtualnej serwera licencji w regionie lub lokalizacji, która ma wystarczającą pojemność maszyn wirtualnych, dzięki czemu nie trzeba będzie przenosić tych zasobów do nowego regionu lub lokalizacji później.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie serwera licencji jako zasobu udostępnionego](./how-to-create-a-lab-with-shared-resource.md).

### <a name="template-machine"></a>Komputer szablonu
Niektóre pliki instalacyjne potrzebne do PLTW są duże. Pobranie plików na maszynę wirtualną szablonu laboratorium może zająć dużo czasu.

Zamiast pobierać pliki instalacyjne na maszynę szablonu i instalować wszystko, zalecamy tworzenie obrazów PLTW w środowisku fizycznym.  Następnie można zaimportować obrazy niestandardowe do galerii obrazów udostępnionych, aby można było ich używać do tworzenia laboratoriów.  Aby uzyskać więcej informacji, zobacz [przekazywanie obrazu niestandardowego do galerii obrazów udostępnionych](./upload-custom-image-shared-image-gallery.md).

Postępując zgodnie z tym zaleceniem, zanotuj najważniejsze zadania dotyczące konfigurowania laboratorium:

1. W środowisku fizycznym Utwórz obraz dla klasy.

    a.  Użyj szczegółowych czynności PLTW do pobrania plików instalacyjnych i zainstalowania wymaganego oprogramowania.

    > [!NOTE]    
    > Gdy instalujesz aplikacje Autodesk, komputer, na którym są instalowane, musi być w stanie komunikować się z serwerem licencji. W Kreatorze instalacji Autodesk zostanie wyświetlony monit o określenie nazwy komputera, na którym jest hostowany serwer licencji.  Jeśli przechowujesz serwer licencji na maszynie wirtualnej platformy Azure, może być konieczne poczekanie na zainstalowanie programu Autodesk na maszynie wirtualnej z szablonem laboratorium, aby Kreator instalacji mógł uzyskać dostęp do serwera licencji.

    b.  [Zainstaluj i skonfiguruj usługę OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) lub inne opcje tworzenia kopii zapasowej, które mogą być używane w szkole.
    
    c.  [Zainstaluj i skonfiguruj aktualizacje systemu Windows](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Przekaż obraz niestandardowy do [galerii obrazów udostępnionych, która jest dołączona do Twojego konta laboratorium](./how-to-attach-detach-shared-image-gallery.md).

1.  Utwórz laboratorium, a następnie wybierz obraz niestandardowy przekazany w poprzednim kroku.

1.  Po utworzeniu laboratorium Uruchom i Połącz się z szablonową maszyną wirtualną, aby sprawdzić, czy obraz działa zgodnie z oczekiwaniami.

1.  Na koniec Opublikuj maszynę wirtualną z szablonem, aby utworzyć maszyny wirtualne uczniów.

## <a name="student-devices"></a>Urządzenia ucznia
Studenci mogą łączyć się z maszynami wirtualnymi laboratorium z komputerów z systemem Windows, komputerów Mac i Chromebook. Aby uzyskać instrukcje, zobacz:

- [Łączenie z systemu Windows](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Połącz z komputera Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Łączenie z Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Cost (Koszt)
Poszukaj przykładowego oszacowania kosztów dla klas PLTW.  To oszacowanie nie obejmuje kosztu korzystania z serwera licencji lub udostępnionej galerii obrazów. Załóżmy, że masz klasę 25 studentów, z których każdy ma 20 godzin zaplanowanych godzin.  Każdy student ma także dodatkowe 10 godzin przydziału dla prac domowych lub przydziałów poza zaplanowanym czasem klasy.  Poniżej przedstawiono szacowane koszty:

- **Duża maszyna wirtualna**

    25 studentów &times; (20 zaplanowanych godzin + 10 godzin przydziału) &times; 70 jednostek laboratoryjnych &times; USD 0,01 za godzinę = 525.00 USD

- **Mały procesor GPU (wizualizacja)**

    25 studentów &times; (20 zaplanowanych godzin + 10 godzin przydziału) &times; 160 jednostek laboratoryjnych &times; USD 0,01 za godzinę = 1200.00 USD

> [!IMPORTANT] 
> Oszacowanie kosztów odbywa się tylko na przykład.  Aby uzyskać aktualne informacje o cenach, zobacz [Cennik usługi Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Wiele klas PLTW korzysta z aplikacji, do których dostęp uzyskuje się za pośrednictwem przeglądarki, na przykład spisu aplikacji MIT.  Te aplikacje oparte na przeglądarce nie wymagają szybkiego procesora CPU ani procesora GPU i można uzyskać do nich dostęp z dowolnego urządzenia z połączeniem internetowym.  Gdy uczniowie korzystają z tych typów aplikacji, zalecamy korzystanie z przeglądarki na urządzeniu fizycznym zamiast przeglądarki na maszynie wirtualnej laboratorium. Studenci mogą pomóc w zmniejszeniu kosztów przy użyciu maszyny wirtualnej laboratorium tylko w przypadku aplikacji wymagających szybkiego procesora CPU lub procesora GPU.

## <a name="next-steps"></a>Następne kroki

Podczas konfigurowania laboratorium zapoznaj się z następującymi artykułami:

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustawianie przydziałów](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users) 
