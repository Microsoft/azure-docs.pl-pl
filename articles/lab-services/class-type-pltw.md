---
title: Konfigurowanie projektu do realizacji w laboratoriach z Azure Lab Services
description: Dowiedz się, jak skonfigurować laboratoria do uczenia się projektów w sposób klasy.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: 8585d09759319eef04da5ed68fec603cfa390093
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497269"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Konfigurowanie laboratoriów dla liderów projektu — klasy metod

[Potencjalni klienci projektu w ten sposób (PLTW)](https://www.pltw.org/) to organizacja non profit, która zapewnia PreK-12 dla Stany Zjednoczone w nauce technologicznej, inżynieryjnej i biomedycznej.  W każdej klasie PLTW studenci korzystają z różnych aplikacji w ramach praktycznego doświadczenia.  Wiele aplikacji oprogramowania wymaga szybkiego procesora CPU lub w niektórych przypadkach procesor GPU.  W tym artykule opisano sposób konfigurowania laboratoriów dla następujących klas PLTW, które są zwykle oferowane studentom w kategorii 9-12:

- **Wprowadzenie do projektowania inżynieryjnego**

    Studenci są wprowadzani do procesu projektowania inżynieryjnego, który obejmuje użycie oprogramowania [Autodesk do projektowania](https://www.autodesk.com/products/inventor/new-features) w modelu 3D.

- **Zasady inżynierii**
    
    Studenci poznają mechanizmy inżynieryjne, siłę structural\material i automatyzację.  Ta klasa używa oprogramowania, takiego jak [stałe MD](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), [Projektant mostka dla punktów zachodnich](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)i [symulacja armii](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Inżynieria inżynieryjna i architektura**

    Studenci opracowują pomysły i projektowanie i programowanie witryn przy użyciu oprogramowania [Autodesk Revit](https://www.autodesk.com/products/revit/overview) platform Designing for 3D Building Modeling (BIM).

- **Produkcja Zintegrowana z komputerem**

    Studenci przedstawiają nowoczesne procesy produkcyjne, które obejmują naznanie i automatyzację.   W tej klasie studenci używają programów [CAD w firmie Autodesk](https://www.autodesk.com/products/inventor/new-features) oraz oprogramowania do [produkcji w firmie Autodesk](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Cyfrowe elektronika**

    Studenci badają elektroniczne obwody logiczne i urządzenia przy użyciu oprogramowania do symulacji Multisim i układu do projektowania [instrumentów narodowych](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) .

- **Projektowanie inżynieryjne i programowanie**

    Uczniowie przyczyniają się do kompleksowego rozwiązania łączącego badania, projektowanie i testowanie, które znajdują się w panelu inżynierów.  W tej klasie studenci korzystają z oprogramowania [CAD w firmie Autodesk](https://www.autodesk.com/products/inventor/new-features) .

- **Podstawowe informacje o nauce komputerowej**

    Studenci są wprowadzani do koncepcji i narzędzi obliczeniowych.  Zaczynają się one od programowania opartego na blokach, a następnie do użycia kodowania opartego na tekście przy użyciu środowisk kodowania, takich jak [bloki VEXcode V5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Zasady dotyczące nauki komputerowej**
    
    Studenci rozwijają wiedzę programistyczną w języku [Python](https://www.python.org/) za pomocą [Visual Studio Code środowiska deweloperskiego firmy Microsoft](https://code.visualstudio.com/). 

- **Komputer A**

    Uczniowie, aby rozwijać swoją wiedzę programistyczną w tej klasie, ucząc rozwój aplikacji mobilnych.  W tej klasie poznajemy [język Java](https://www.java.com/) przy użyciu [Visual Studio Code środowiska deweloperskiego firmy Microsoft](https://code.visualstudio.com/).  Studenci używają również emulatora, który umożliwia im uruchamianie i testowanie kodu aplikacji mobilnej.  Aby uzyskać informacje na temat sposobu konfigurowania emulatora w laboratoriach platformy Azure, skontaktuj się z nami pod adresem azlabspilot@microsoft.com .

Zapoznaj się z witryną usługi PLTW, aby uzyskać [pełną listę oprogramowania](https://www.pltw.org/pltw-software) dla każdej klasy.

## <a name="lab-configuration"></a>Konfiguracja laboratorium
Aby skonfigurować laboratoria dla usługi PLTW, musisz mieć subskrypcję platformy Azure i konto laboratorium, aby rozpocząć pracę. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Po otrzymaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz Samouczek dotyczący [konfigurowania konta laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). Możesz również użyć istniejącego konta laboratorium.

Po utworzeniu konta laboratorium należy utworzyć osobne laboratoria dla każdej sesji klasy PLTW, którą oferuje Twoja szkoła.  Zalecamy również utworzenie oddzielnych obrazów dla każdego typu klasy PLTW.  Aby uzyskać więcej informacji na temat struktury laboratoriów i obrazów, Przeczytaj wpis w blogu: [przechodzenie z laboratorium fizycznego do Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium
Włącz ustawienia opisane w poniższej tabeli dla konta laboratorium. Aby uzyskać więcej informacji o sposobie włączania obrazów z portalu Marketplace, zobacz artykuł dotyczący [sposobu określania obrazów portalu Marketplace dostępnych dla twórców laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Ustawienie konta laboratorium | Instrukcje |
| -------------------- | ----- |
| Obraz z witryny Marketplace | Włącz obraz systemu Windows 10 Pro do użycia w ramach konta laboratorium. |

### <a name="lab-settings"></a>Ustawienia laboratorium
Rozmiar maszyny wirtualnej, która jest zalecana dla klas PLTW, zależy od typów obciążeń wykonywanych przez uczniów w klasie.  Dla powyższych klas zalecamy użycie dużych i małych rozmiarów maszyn wirtualnych GPU (wizualizacji).  Zapoznaj się ze wskazówkami w poniższej tabeli podczas konfigurowania laboratoriów dla klas PLTW.

| Ustawienie laboratorium | Wartość/instrukcje |
| ------------ | ------------------ |
|Rozmiar maszyny wirtualnej| **Mały procesor GPU (wizualizacja)**.  Ta maszyna wirtualna najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX.  Zalecamy korzystanie z tego rozmiaru dla następujących klas PLTW: inżynierii inżynieryjnej i architektury, cyfrowej elektroniki, zintegrowanej produkcji komputerowej; Projektowania i opracowywanie inżynierów.
|Rozmiar maszyny wirtualnej| **Duże**.  Ten rozmiar najlepiej nadaje się w przypadku aplikacji wymagających szybszych procesorów CPU, lepszej wydajności dysków lokalnych, dużych baz danych i dużych pamięci podręcznych pamięci.  Zalecamy korzystanie z tego rozmiaru dla następujących klas PLTW: wprowadzenie do projektowania inżynieryjnego, zasady inżynierii, podstawowe informacje o nauce komputerowej, zasady analizy komputera i informacje o nauce komputerowej.

### <a name="licensing-server"></a>Serwer licencjonowania
Większość oprogramowania, które jest używane w powyższych klasach PLTW, *_nie_* może wymagać dostępu do serwera licencji.  Należy jednak uzyskać dostęp do serwera licencji, jeśli planujesz używać modelu licencjonowania sieci usługi Autodesk dla następującego oprogramowania:
-   Revit
-   Inwentarz
-   Kamera zapasowa

Aby korzystać z licencjonowania sieci w oprogramowaniu Autodesk, [PLTW zawiera szczegółowe instrukcje](https://www.pltw.org/pltw-software) dotyczące instalowania Menedżera licencji programu Autodesk na serwerze licencjonowania.  Ten serwer licencjonowania zazwyczaj znajduje się w sieci lokalnej lub hostowanej na maszynie wirtualnej platformy Azure w ramach usługi Azure Virtual Network (VNet).

Po skonfigurowaniu serwera licencji należy połączyć sieć [wirtualną](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) z [kontem laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). Komunikacja równorzędna sieci musi zostać wykonana _before * utworzenie laboratorium, aby maszyny wirtualne laboratorium mogły uzyskać dostęp do serwera licencji i inne sposoby.

Wygenerowane pliki licencji programu Autodesk osadzają adres MAC serwera licencjonowania.  Jeśli zdecydujesz się hostować serwer licencjonowania przy użyciu maszyny wirtualnej platformy Azure, pamiętaj, aby upewnić się, że adres MAC serwera licencji nie jest zmieniany.   W przeciwnym razie, jeśli adres MAC ulegnie zmianie, należy ponownie wygenerować pliki licencjonowania.  Postępuj zgodnie z poniższymi wskazówkami, aby uniemożliwić zmianę adresu MAC:

- [Ustaw statyczny prywatny adres IP i Mac](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource#static-private-ip-and-mac-address) dla maszyny wirtualnej platformy Azure, która hostuje serwer licencjonowania.
- Upewnij się, że skonfigurowano sieć wirtualną konta laboratorium i serwera licencjonowania w region\location, która ma wystarczającą pojemność maszyn wirtualnych, dzięki czemu nie trzeba będzie przenosić tych zasobów do nowego region\location w przyszłości.

Aby uzyskać więcej informacji, zapoznaj się z artykułem [jak skonfigurować serwer licencjonowania jako zasób udostępniony](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource) .

### <a name="template-machine"></a>Komputer szablonu
Niektóre z plików instalacyjnych, które są potrzebne do PLTW, są duże i mogą być kopiowane po ich pobraniu do maszyny szablonu laboratorium.

Zamiast pobierać pliki instalacji na maszynę szablonu i instalować wszystko, zalecamy tworzenie obrazów PLTW w środowisku fizycznym.  Następnie możesz zaimportować obrazy do galerii obrazów udostępnionych, aby można było używać tych obrazów niestandardowych do tworzenia laboratoriów.  Aby uzyskać szczegółowe informacje, zapoznaj się z następującym artykułem: [przekazywanie obrazu niestandardowego do galerii obrazów udostępnionych](https://docs.microsoft.com/azure/lab-services/upload-custom-image-shared-image-gallery).

Poniższe zalecenia dotyczą najważniejszych zadań związanych z konfigurowaniem laboratorium:

1. W środowisku fizycznym Utwórz obraz dla klasy.

    a.  Aby pobrać pliki instalacyjne i zainstalować wymagane oprogramowanie, użyj szczegółowych czynności PLTW.

    > [!NOTE]    
    > Gdy instalujesz aplikacje Autodesk, komputer, na którym instalujesz program Autodesk, musi być w stanie komunikować się z serwerem licencjonowania (Kreator instalacji usługi Autodesk wyświetli monit o podanie nazwy komputera, na którym jest hostowany serwer licencji).  Jeśli serwer licencjonowania jest obsługiwany na maszynie wirtualnej platformy Azure, może być konieczne poczekanie na zainstalowanie programu Autodesk na komputerze z szablonem laboratorium, aby Kreator instalacji usługi Autodesk mógł uzyskać dostęp do serwera licencjonowania

    b.  [Zainstaluj i skonfiguruj usługę OneDrive](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-onedrive) (lub inne opcje tworzenia kopii zapasowych, które mogą być używane przez szkołę).
    
    c.  [Zainstaluj i skonfiguruj aktualizacje systemu Windows](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-updates).

1.  Przekaż obraz niestandardowy do [galerii obrazów udostępnionych, która jest dołączona do Twojego konta laboratorium](https://docs.microsoft.com/azure/lab-services/how-to-attach-detach-shared-image-gallery).

1.  Utwórz laboratorium i wybierz obraz niestandardowy przekazany w poprzednim kroku.

1.  Po utworzeniu laboratorium Uruchom i Połącz się z maszyną szablonu, aby sprawdzić, czy obraz działa zgodnie z oczekiwaniami.

1.  Na koniec Opublikuj maszynę szablonu, aby utworzyć maszyny wirtualne uczniów.

## <a name="student-devices"></a>Urządzenia ucznia
Studenci mogą łączyć się z maszynami wirtualnymi laboratorium z komputerów Windows\Mac i Chromebooks.  Poniżej znajdują się linki do instrukcji dla każdej z tych opcji:

- [Łączenie z systemu Windows](https://docs.microsoft.com/azure/lab-services/how-to-use-classroom-lab#connect-to-the-vm)
- [Połącz z komputera Mac](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-mac-remote-desktop)
- [Łączenie z Chromebook](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-chromebook-remote-desktop)

## <a name="cost"></a>Cost (Koszt)
Przyjrzyjmy się możliwemu szacunkowi kosztów dla powyższych klas PLTW.  To oszacowanie nie obejmuje kosztu uruchomienia serwera licencjonowania lub korzystania z galerii obrazów udostępnionych.  Będziemy używać klasy 25 studentów.  Zaplanowana godzina klasy wynosi 20 godzin.  Ponadto każdy student otrzymuje limit 10 godzin dla prac domowych lub przydziałów poza zaplanowanym czasem klasy.  Zapoznaj się z poniższymi szacunkami kosztów dla rozmiaru **dużych** i **małych procesorów GPU (wizualizacji)** .

- **Duża maszyna wirtualna**

    25 studentów x (20 zaplanowanych godzin + 10 godzin przydziału) x 70 jednostek laboratoryjnych x 0,01 USD za godzinę = 525,00 USD

- **Mały procesor GPU (wizualizacja)**

    25 studentów x (20 zaplanowanych godzin + 10 godzin przydziału) x 160 jednostek laboratoryjnych x 0,01 USD za godzinę = 1200,00 USD

> [!IMPORTANT] 
> Oszacowanie kosztów odbywa się tylko na przykład.  Aby uzyskać aktualne informacje o cenach, zobacz [Cennik usługi Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Wiele klas PLTW korzysta z aplikacji, do których dostęp uzyskuje się za pośrednictwem przeglądarki, na przykład spisu aplikacji MIT.  Te aplikacje oparte na przeglądarce nie wymagają szybkiego procesora CPU ani procesora GPU i można uzyskać do nich dostęp z dowolnego urządzenia z połączeniem internetowym.  Gdy uczniowie korzystają z tych typów aplikacji, zalecamy korzystanie z przeglądarki na urządzeniu fizycznym zamiast korzystania z przeglądarki na maszynach wirtualnych laboratorium.  Dzięki temu będzie można utrzymywać koszty tylko przy użyciu maszyn wirtualnych laboratorium dla aplikacji, które wymagają szybkiego procesora CPU lub procesora GPU.

## <a name="next-steps"></a>Następne kroki
Następne kroki są wspólne do konfigurowania dowolnego laboratorium:

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users). 