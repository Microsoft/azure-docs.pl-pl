---
title: Włącz wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services | Microsoft Docs
description: W tym artykule dowiesz się, jak skonfigurować wirtualizację zagnieżdżoną na komputerze szablonu w Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 319695088e967dc3156ecab5c1b9458e77fcc186
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91251478"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Włącz wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services

Obecnie Azure Lab Services umożliwia skonfigurowanie jednego szablonu maszyny wirtualnej w laboratorium i udostępnienie pojedynczej kopii dla każdego z użytkowników. Jeśli jesteś nauczycieliami sieciowymi, bezpieczeństwem lub klasami IT, może być konieczne udostępnienie wszystkim uczniom środowiska, w którym wiele maszyn wirtualnych może komunikować się ze sobą za pośrednictwem sieci.

Wirtualizacja zagnieżdżona umożliwia tworzenie środowiska z obsługą wiele maszyn wirtualnych w ramach maszyny wirtualnej szablonu laboratorium. Opublikowanie szablonu zapewni każdemu użytkownikowi w laboratorium z maszyną wirtualną skonfigurowaną z wieloma maszynami wirtualnymi.  W tym artykule opisano sposób konfigurowania wirtualizacji zagnieżdżonej na komputerze szablonu w Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Co to jest wirtualizacja zagnieżdżona?

Wirtualizacja zagnieżdżona umożliwia tworzenie maszyn wirtualnych w ramach maszyny wirtualnej. Wirtualizacja zagnieżdżona odbywa się za pośrednictwem funkcji Hyper-V i jest dostępna tylko na maszynach wirtualnych z systemem Windows.

Aby uzyskać więcej informacji na temat wirtualizacji zagnieżdżonej, zobacz następujące artykuły:

- [Wirtualizacja zagnieżdżona na platformie Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Jak włączyć wirtualizację zagnieżdżoną na maszynie wirtualnej platformy Azure](../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Zagadnienia do rozważenia

Przed skonfigurowaniem laboratorium z wirtualizacją zagnieżdżoną poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę.

- Podczas tworzenia nowego laboratorium wybierz rozmiar maszyny wirtualnej **Średni (Wirtualizacja zagnieżdżona)** lub **duża (Wirtualizacja zagnieżdżona)** . Te rozmiary maszyn wirtualnych obsługują wirtualizację zagnieżdżoną.
- Wybierz rozmiar, który będzie zapewniał dobrą wydajność zarówno dla maszyn wirtualnych hosta, jak i klienta.  Należy pamiętać, że w przypadku korzystania z wirtualizacji wybrany rozmiar musi być odpowiedni dla nie tylko jednego komputera, ale host, a także wszystkie maszyny funkcji Hyper-V z systemem współbieżnie.
- Maszyny wirtualne klienta nie będą miały dostępu do zasobów platformy Azure, takich jak serwery DNS, w sieci wirtualnej platformy Azure.
- Maszyna wirtualna hosta wymaga, aby komputer kliencki miał łączność z Internetem.
- Klienckie maszyny wirtualne są licencjonowane jako niezależne maszyny. Zobacz temat [Licencjonowanie firmy Microsoft](https://www.microsoft.com/licensing/default) , aby uzyskać informacje o licencjonowaniu systemów operacyjnych i produktów firmy Microsoft. Zapoznaj się z umowami licencyjnymi dotyczącymi wszelkich innych programów używanych przed skonfigurowaniem komputera szablonu.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu

W tym artykule przyjęto założenie, że utworzono konto laboratorium i laboratorium.  Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md). Aby uzyskać więcej informacji na temat tworzenia laboratorium, zobacz [Samouczek dotyczący konfigurowania środowiska laboratoryjnego](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Wybierz opcję **duże (Wirtualizacja zagnieżdżona)** lub **Średni (Wirtualizacja zagnieżdżona)** dla rozmiaru maszyny wirtualnej podczas tworzenia laboratorium.  Wirtualizacja zagnieżdżona nie będzie działała inaczej.  

Aby nawiązać połączenie z maszyną szablonu, zobacz [Tworzenie i zarządzanie szablonem klasy](how-to-create-manage-template.md).

Aby włączyć wirtualizację zagnieżdżoną, należy wykonać kilka zadań.  

- **Włącz rolę funkcji Hyper-V**. Rola funkcji Hyper-V musi być włączona na potrzeby tworzenia i uruchamiania maszyn wirtualnych funkcji Hyper-V na maszynie wirtualnej usług laboratoryjnych.
- **Włącz protokół DHCP**.  Jeśli maszyna wirtualna usług laboratoryjnych ma włączoną rolę DHCP, do maszyn wirtualnych funkcji Hyper-V można automatycznie przypisać adres IP.
- **Tworzenie sieci NAT dla maszyn wirtualnych funkcji Hyper-V**.  Sieć NAT jest skonfigurowana tak, aby umożliwić maszynom wirtualnym funkcji Hyper-V dostęp do Internetu.  Maszyny wirtualne funkcji Hyper-V mogą komunikować się ze sobą.

>[!NOTE]
>Sieć NAT utworzona na maszynie wirtualnej usługi Lab Services umożliwi maszynie wirtualnej funkcji Hyper-V dostęp do Internetu i innych maszyn wirtualnych funkcji Hyper-V na tej samej maszynie wirtualnej usługi Lab Services.  Maszyna wirtualna funkcji Hyper-V nie będzie w stanie uzyskać dostępu do zasobów platformy Azure, takich jak serwery DNS, w sieci wirtualnej platformy Azure.

Zadania wymienione powyżej można wykonać przy użyciu skryptu lub narzędzi systemu Windows.  Więcej informacji można znaleźć w poniższych sekcjach.

### <a name="using-script-to-enable-nested-virtualization"></a>Używanie skryptu do włączania wirtualizacji zagnieżdżonej

Aby użyć zautomatyzowanej instalacji wirtualizacji zagnieżdżonej z systemem Windows Server 2016 lub Windows Server 2019, zobacz [Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w Azure Lab Services przy użyciu skryptu](how-to-enable-nested-virtualization-template-vm-using-script.md). Do zainstalowania roli funkcji Hyper-V zostaną użyte skrypty z [skryptów funkcji Hyper-v usługi Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) .  Skrypty również skonfigurują sieć, tak aby maszyny wirtualne funkcji Hyper-V mogły mieć dostęp do Internetu.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Korzystanie z narzędzi systemu Windows w celu włączenia wirtualizacji zagnieżdżonej

Ustawienia wirtualizacji zagnieżdżonej dla systemu Windows Server 2016 lub Windows Server 2019 przy użyciu ról systemu Windows i narzędzi administracyjnych można znaleźć [w temacie Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w Azure Lab Services ręcznie](how-to-enable-nested-virtualization-template-vm-ui.md).  Instrukcje obejmują również sposób konfigurowania sieci, tak aby maszyny wirtualne funkcji Hyper-V mogły mieć dostęp do Internetu.
