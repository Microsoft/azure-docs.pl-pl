---
title: Obsługiwane systemy operacyjne, aparaty kontenerów — Azure IoT Edge
description: Dowiedz się, które systemy operacyjne mogą uruchamiać demona Azure IoT Edge i środowisko uruchomieniowe oraz obsługiwane aparaty kontenerów dla urządzeń produkcyjnych
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 27d078bc1dc61079d44110999b70c5195c9c8a2a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874224"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge obsługiwane systemy

Ten artykuł zawiera szczegółowe informacje o systemach i składnikach obsługiwanych przez IoT Edge, niezależnie od tego, czy są one oficjalne czy w wersji zapoznawczej.

Jeśli wystąpią problemy podczas korzystania z usługi Azure IoT Edge, istnieje kilka sposobów poszukiwania pomocy technicznej. Wypróbuj jeden z następujących kanałów, aby uzyskać pomoc techniczną:

**Raportowanie błędów** — większość programowania, która przechodzi do produktu Azure IoT Edge, odbywa się w IoT Edge projekcie typu open source. Usterki można zgłaszać na [stronie problemy](https://github.com/azure/iotedge/issues) projektu. Poprawki są szybko wprowadzane do projektu w programie do aktualizacji produktów.

**Zespół pomocy technicznej firmy Microsoft** — użytkownicy z [planem pomocy](https://azure.microsoft.com/support/plans/) technicznej mogą skontaktować się z zespołem pomocy technicznej firmy Microsoft, tworząc bilet pomocy technicznej bezpośrednio z [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Żądania funkcji** — produkt Azure IoT Edge śledzi żądania funkcji za pośrednictwem [strony użytkownika](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Aparaty kontenerów

Moduły Azure IoT Edge są implementowane jako kontenery, dlatego IoT Edge potrzebuje aparatu kontenera, aby je uruchomić. Firma Microsoft udostępnia aparat kontenera, aparat Moby, aby spełnić to wymaganie. Ten aparat kontenera jest oparty na projekcie Moby Open Source. Rozwiązania Docker CE i Docker EE to inne popularne aparaty kontenerów. Są one również oparte na projekcie Moby Open Source i są zgodne z Azure IoT Edge. Firma Microsoft zapewnia najlepszą pomoc techniczną dla systemów korzystających z tych aparatów kontenerów; Jednak firma Microsoft nie może wydać w nich poprawek. Z tego powodu firma Microsoft zaleca używanie aparatu Moby w systemach produkcyjnych.

<br>
<center>

![Aparat Moby jako środowisko uruchomieniowe kontenera](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Systemy operacyjne

Azure IoT Edge działa w większości systemów operacyjnych, które mogą uruchamiać kontenery; jednak nie wszystkie te systemy są równie obsługiwane. Systemy operacyjne są pogrupowane w warstwy, które reprezentują poziom oczekiwanych przez użytkowników pomocy technicznej.

* Obsługiwane są systemy warstwy 1. W przypadku systemów warstwy 1 Firma Microsoft:
  * Czy ten system operacyjny jest w zautomatyzowanych testach
  * dostarcza dla nich pakiety instalacyjne
* Systemy warstwy 2 są zgodne z Azure IoT Edge i mogą być używane stosunkowo łatwo. W przypadku systemów warstwy 2:
  * Firma Microsoft zakończyła nieformalne testowanie na platformach lub wie, że partner pomyślnie uruchomił Azure IoT Edge na platformie
  * Pakiety instalacyjne dla innych platform mogą współpracować z tymi platformami

Rodzina systemu operacyjnego hosta musi być zawsze zgodna z rodziną systemu operacyjnego gościa używanego wewnątrz kontenera modułu. Innymi słowy, można używać tylko kontenerów systemu Linux w kontenerach systemów Linux i Windows w systemie Windows. W przypadku korzystania z systemu Windows obsługiwane są tylko przetwórz izolowane kontenery, a nie kontenery izolowane funkcji Hyper-V.  

<br>
<center>

![System operacyjny hosta jest zgodny z systemem operacyjnym gościa](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Warstwa 1

Systemy wymienione w poniższej tabeli są obsługiwane przez firmę Microsoft, ogólnie dostępne lub w publicznej wersji zapoznawczej, i są testowane z każdą nową wersją. 

| System operacyjny | Procesor | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Rozciągaj i ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), kompilacja 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), kompilacja 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), kompilacja 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), kompilacja 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

Wymienione powyżej systemy operacyjne Windows są wymaganiami dotyczącymi urządzeń z kontenerami systemu Windows w systemie Windows, które są jedyną obsługiwaną konfiguracją dla środowiska produkcyjnego. Pakiety instalacyjne Azure IoT Edge dla systemu Windows umożliwiają używanie kontenerów z systemem Linux w systemie Windows; Jednak ta konfiguracja służy tylko do tworzenia i testowania. Aby uzyskać więcej informacji, zobacz [używanie IoT Edge w systemie Windows do uruchamiania kontenerów systemu Linux](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Warstwa 2

Systemy wymienione w poniższej tabeli są uważane za zgodne z Azure IoT Edge, ale nie są aktywnie przetestowane ani obsługiwane przez firmę Microsoft.

| System operacyjny | Procesor | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Opiekun — wbudowany system operacyjny Linux](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Opiekun — wbudowany system operacyjny Linux w systemie operacyjnym + AMD64](./media/tutorial-c-module/green-check.png) | ![Opiekun — wbudowany system operacyjny Linux dla systemu operacyjnego i ARM32v7](./media/tutorial-c-module/green-check.png) | ![Opiekun — wbudowany system operacyjny Linux dla systemu operacyjnego i ARM64](./media/tutorial-c-module/green-check.png) |
| [Opiekun systemu operacyjnego w wersji Embedded Linux](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Opiekun — wbudowany system operacyjny Linux w wersji + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Opiekun — osadzony system operacyjny Linux w systemie operacyjnym i ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Rzeka wiatr 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20,04 <sup>2</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 systemów, w tym Raspian Buster, użyj wersji OpenSSL, która nie jest obsługiwana przez IoT Edge. Przed zainstalowaniem IoT Edge Użyj następującego polecenia:

```bash
sudo apt-get install libssl1.0.2
```

<sup>2</sup> Debian 9 pakietów z [repozytorium Azure IoT Edge wydań](https://github.com/Azure/azure-iotedge/releases) powinien wymusić wyjście z pudełka Ubuntu 20,04.

## <a name="releases"></a>Wydania

IoT Edge zasoby wydania i informacje o wersji są dostępne na stronie [wydania usługi Azure iotedge](https://github.com/Azure/azure-iotedge/releases) . Ta sekcja zawiera informacje zawarte w tych informacjach o wersji, które ułatwiają łatwą wizualizację składników poszczególnych wersji.

Składniki IoT Edge można zainstalować lub zaktualizować osobno i są wstecznie zgodne ze składnikami ze starszych wersji. W poniższej tabeli wymieniono składniki zawarte w poszczególnych wersjach:

| Release | Demon zabezpieczeń | Centrum brzegowe<br>Agent graniczny | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

IoT Edge używa zestawu SDK Microsoft. Azure. Device. Client. Aby uzyskać więcej informacji, zobacz [repozytorium usługi Azure IoT C# SDK](https://github.com/Azure/azure-iot-sdk-csharp) w witrynie GitHub lub [zawartość referencyjną Azure SDK dla platformy .NET](/dotnet/api/overview/azure/iot/client). Na poniższej liście przedstawiono wersję zestawu SDK klienta, dla którego są testowane poszczególne wersje:

* **IoT Edge 1.0.9**: 1.21.1 zestawu SDK klienta
* **IoT Edge 1.0.8**: 1.20.3 zestawu SDK klienta
* **IoT Edge 1.0.7**: 1.20.1 zestawu SDK klienta
* **IoT Edge 1.0.6**: 1.17.1 zestawu SDK klienta
* **IoT Edge 1.0.5**: 1.17.1 zestawu SDK klienta

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge można uruchamiać na maszynach wirtualnych. Użycie maszyny wirtualnej jako urządzenia IoT Edge jest powszechne, gdy klienci chcą rozszerzyć istniejącą infrastrukturę za pomocą funkcji analizy brzegowej. Rodzina systemu operacyjnego hosta maszyny wirtualnej musi być zgodna z rodziną systemu operacyjnego gościa używanego wewnątrz kontenera modułu. To wymaganie jest takie samo, jak w przypadku uruchamiania Azure IoT Edge bezpośrednio na urządzeniu. Azure IoT Edge jest niezależny od podstawowej technologii wirtualizacji i działa na maszynach wirtualnych opartych na platformach takich jak Hyper-V i vSphere.

<br>
<center>

![Azure IoT Edge w maszynie wirtualnej](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimalne wymagania systemowe

Azure IoT Edge działa doskonale na urządzeniach jako Raspberry PI3 na sprzęcie klasy serwerów. Wybór odpowiedniego sprzętu dla danego scenariusza zależy od obciążeń, które chcesz uruchomić. Podejmowanie ostatecznej decyzji o urządzeniu może być skomplikowane; można jednak łatwo zacząć tworzyć prototypy rozwiązania na tradycyjnych laptopach lub komputerach stacjonarnych.

Środowisko pracy podczas tworzenia prototypów ułatwi wybór ostatecznego urządzenia. Należy wziąć pod uwagę następujące pytania:

* Ile modułów znajduje się w obciążeniu?
* Ile warstw ma udział w kontenerach modułów?
* W jakim języku są zapisywane Twoje moduły?
* Ile danych będzie przetwarzanych przez moduły?
* Czy moduły wymagają dowolnego wyspecjalizowanego sprzętu w celu przyspieszenia obciążeń?
* Jakie są odpowiednie charakterystyki wydajności rozwiązania?
* Jaki jest Twój budżet sprzętu?
