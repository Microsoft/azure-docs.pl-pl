---
title: Konfigurowanie laboratorium SOLIDWORKS Lab for Engineering with Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium dla kursów inżynieryjnych za pomocą SOLIDWORKS.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 3a7e8c47977f0518a3a3e9f8a6fd2e57454e1c42
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626108"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>Konfigurowanie laboratorium dla klas inżynieryjnych za pomocą SOLIDWORKS

[SolidWorks](https://www.solidworks.com/) udostępnia środowisko projektowe (CAD) 3W do modelowania obiektów stałych i jest używane w różnych typach pól inżynieryjnych.  Dzięki SOLIDWORKS inżynierowie mogą łatwo tworzyć, wizualizować, symulować i dokumentować projekty.

Opcja licencjonowania często używana przez uniwersytety to SOLIDWORKS ".   Po wybraniu tej opcji użytkownicy korzystają z puli licencji zarządzanej przez serwer licencjonowania.  Ten typ licencji jest czasami nazywany "przepływającną" licencją, ponieważ wystarczy mieć wystarczającą ilość licencji na liczbę równoczesnych użytkowników.  Gdy użytkownik korzysta z usługi SOLIDWORKS, ich Licencja przechodzi do centralnej zarządzanej puli licencji, dzięki czemu może być ponownie używana przez innego użytkownika.

W tym artykule przedstawiono sposób konfigurowania klasy korzystającej z SOLIDWORKS 2019 i licencjonowania sieci.

## <a name="license-server"></a>Serwer licencji

W ramach licencjonowania sieci SOLIDWORKS wymagane jest zainstalowanie i aktywowanie Menedżera licencji SolidNetWork na serwerze licencji.  Ten serwer licencji zazwyczaj znajduje się w sieci lokalnej lub prywatnej sieci na platformie Azure.  Aby uzyskać więcej informacji na temat sposobu konfigurowania Menedżera licencji SolidNetWork na serwerze, zobacz [Instalowanie i aktywowanie Menedżera licencji](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) w przewodniku instalacji SolidWorks.  Podczas ustawiania tego ustawienia należy zapamiętać **numer portu** i [**numer seryjny**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) , które są używane, ponieważ będą potrzebne w kolejnych krokach.

Po skonfigurowaniu serwera licencji należy połączyć równorzędną [sieć wirtualną](./how-to-connect-peer-virtual-network.md) z [kontem laboratorium](./tutorial-setup-lab-account.md).  Komunikacja równorzędna sieci musi zostać wykonana przed utworzeniem laboratorium, aby maszyny wirtualne laboratorium mogły uzyskiwać dostęp do serwera licencji i w inny sposób.

> [!NOTE]
> Należy sprawdzić, czy odpowiednie porty są otwarte w zaporze, aby umożliwić komunikację między maszynami wirtualnymi laboratorium a serwerem licencji.  Na przykład zapoznaj się z instrukcjami dotyczącymi [modyfikowania portów komputera Menedżera licencji dla zapory systemu Windows](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) , które pokazują, jak dodać reguły ruchu przychodzącego i wychodzącego do zapory serwera licencji.  Może być również konieczne otwarcie portów na maszynach wirtualnych laboratorium.  Postępuj zgodnie z instrukcjami w artykule dotyczącym [ustawień zapory dla laboratoriów](./how-to-configure-firewall-settings.md) , aby uzyskać więcej informacji na ten temat, w tym o tym, jak uzyskać publiczny adres IP laboratorium.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby rozpocząć pracę z tym laboratorium, musisz zacząć korzystać z subskrypcji platformy Azure i konta laboratorium. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Po otrzymaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz Samouczek dotyczący [konfigurowania konta laboratorium](./tutorial-setup-lab-account.md). Możesz również użyć istniejącego konta laboratorium.

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium

Włącz ustawienia opisane w poniższej tabeli dla konta laboratorium. Aby uzyskać więcej informacji o sposobie włączania obrazów z portalu Marketplace, zobacz artykuł dotyczący [sposobu określania obrazów portalu Marketplace dostępnych dla twórców laboratorium](./specify-marketplace-images.md).

| Ustawienie konta laboratorium | Instrukcje |
| ------------------- | ------------ |
|Obraz z witryny Marketplace| Włącz obraz systemu Windows 10 Pro do użycia w ramach konta laboratorium.|

> [!NOTE]
> Oprócz systemu Windows 10 SOLIDWORKS obsługuje inne wersje systemu Windows.  Aby uzyskać szczegółowe informacje, zobacz [wymagania systemowe SolidWorks](https://www.solidworks.com/sw/support/SystemRequirements.html) .

### <a name="lab-settings"></a>Ustawienia laboratorium

W poniższej tabeli przedstawiono ustawienia, które należy wykonać podczas konfigurowania laboratorium zajęć. Aby uzyskać więcej informacji na temat tworzenia laboratorium klasy, zobacz Samouczek dotyczący konfigurowania platformy dla laboratorium.

| Ustawienia laboratorium | Wartość/instrukcje |
| ------------ | ------------------ |
|Rozmiar maszyny wirtualnej| **Mały procesor GPU (wizualizacja)**.  Ta maszyna wirtualna najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX.|  
|Obraz maszyny wirtualnej| Windows 10 Pro|

> [!NOTE]
> Rozmiar maszyny wirtualnej **małego procesora GPU (wizualizacja)** jest skonfigurowany tak, aby umożliwiał wysoce wydajne środowisko grafiki.  Aby uzyskać więcej informacji o tym rozmiarze maszyny wirtualnej, zobacz artykuł dotyczący [konfigurowania laboratorium z procesorami GPU](./how-to-setup-lab-gpu.md).

> [!WARNING]
> **Przed** utworzeniem laboratorium nie zapomnij [sieci wirtualnej](./how-to-connect-peer-virtual-network.md) dla konta laboratorium w sieci wirtualnej dla serwera licencji.

## <a name="template-virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej szablonu

Kroki opisane w tej sekcji pokazują, jak skonfigurować maszynę wirtualną szablonu przez pobranie plików instalacyjnych SOLIDWORKS i zainstalowanie oprogramowania klienckiego:

1. Uruchom szablon maszyny wirtualnej i Połącz się z maszyną przy użyciu protokołu RDP.

1. Pobierz pliki instalacyjne oprogramowania klienckiego SOLIDWORKS. Dostępne są dwie opcje pobierania:
   - Pobierz z [portalu SolidWorks Customer Portal](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F).
   - Pobierz z katalogu na serwerze.  Jeśli została użyta ta opcja, należy upewnić się, że serwer jest dostępny z szablonu maszyny wirtualnej.  Na przykład ten serwer może znajdować się w tej samej sieci wirtualnej, która jest połączona z kontem laboratorium.
  
    Aby uzyskać szczegółowe informacje, zobacz [Instalacja na poszczególnych komputerach w SolidWorks](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) w podręczniku instalacji SolidWorks.

1. Po pobraniu plików instalacyjnych Zainstaluj oprogramowanie klienckie przy użyciu Menedżera instalacji SOLIDWORKS. Zobacz szczegóły dotyczące [instalowania klienta licencji](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) w podręczniku instalacji SolidWorks.

    > [!NOTE]
    > W oknie dialogowym **Dodawanie serwera** zostanie wyświetlony monit o **numer portu** używany przez serwer licencji oraz nazwę lub adres IP serwera licencji.

## <a name="cost"></a>Koszt

Przyjrzyjmy się możliwemu szacunkowi kosztów dla tej klasy. To oszacowanie nie obejmuje kosztu uruchomienia serwera licencji. Będziemy używać klasy 25 studentów. Zaplanowana godzina klasy wynosi 20 godzin. Ponadto każdy student otrzymuje limit 10 godzin dla prac domowych lub przydziałów poza zaplanowanym czasem klasy. Wybrany rozmiar maszyny wirtualnej to **mały procesor GPU (wizualizacja)**, czyli 160 jednostek laboratorium.

25 studentów \* (20 zaplanowanych godzin + 10 godzin przydziału) \* 160 jednostek laboratoryjnych * 0,01 USD za godzinę = 1200,00 USD

>[!IMPORTANT]
> Oszacowanie kosztów odbywa się tylko na przykład.  Aby uzyskać aktualne informacje o cenach, zobacz [Cennik usługi Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)