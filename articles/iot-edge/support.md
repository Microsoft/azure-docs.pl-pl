---
title: Obsługiwane systemy operacyjne, aparaty kontenerów — Azure IoT Edge
description: Dowiedz się, które systemy operacyjne mogą uruchamiać demona Azure IoT Edge i środowisko uruchomieniowe oraz obsługiwane aparaty kontenerów dla urządzeń produkcyjnych
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75beb214682536b996cf4896588ea32d568579f1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045993"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge obsługiwane systemy

Ten artykuł zawiera szczegółowe informacje o systemach i składnikach obsługiwanych przez IoT Edge, niezależnie od tego, czy są one oficjalne czy w wersji zapoznawczej.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli wystąpią problemy podczas korzystania z usługi Azure IoT Edge, istnieje kilka sposobów poszukiwania pomocy technicznej. Wypróbuj jeden z następujących kanałów, aby uzyskać pomoc techniczną:

**Zgłoś błędy** — większość programowania, która przechodzi do produktu Azure IoT Edge, odbywa się w IoT Edge projekcie typu open source. Usterki można zgłaszać na [stronie problemy](https://github.com/azure/iotedge/issues) projektu. Poprawki są szybko wprowadzane do projektu w programie do aktualizacji produktów.

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

IoT Edge dla systemu Linux w systemie Windows używa IoT Edge w maszynie wirtualnej z systemem Linux działającym na hoście z systemem Windows. W ten sposób można uruchamiać moduły systemu Linux na urządzeniu z systemem Windows.

### <a name="tier-1"></a>Warstwa 1

Systemy wymienione w poniższych tabelach są obsługiwane przez firmę Microsoft, ogólnie dostępne lub w publicznej wersji zapoznawczej, i są testowane z każdą nową wersją.

Azure IoT Edge obsługuje moduły skompilowane jako kontenery systemu Linux lub Windows. Kontenery systemu Linux można wdrażać na urządzeniach z systemem Linux lub wdrażać na urządzeniach z systemami Windows przy użyciu IoT Edge dla systemu Linux w systemie Windows. Kontenery systemu Windows można wdrażać tylko na urządzeniach z systemem Windows.

#### <a name="linux-containers"></a>Kontenery systemu Linux

Moduły utworzone jako kontenery systemu Linux można wdrożyć na urządzeniach z systemem Linux lub Windows. W przypadku urządzeń z systemem Linux środowisko uruchomieniowe IoT Edge jest instalowane bezpośrednio na urządzeniu hosta. W przypadku urządzeń z systemem Windows maszyna wirtualna z systemem Linux prekompilowana przy użyciu środowiska uruchomieniowego IoT Edge jest uruchamiana na urządzeniu hosta.

[IoT Edge dla systemu Linux w systemie Windows](iot-edge-for-linux-on-windows.md) jest obecnie w publicznej wersji zapoznawczej, ale jest to zalecany sposób uruchamiania IoT Edge na urządzeniach z systemem Windows.

| System operacyjny | Procesor | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Rozciąganie systemu operacyjnego Raspberry Pi |  | ![Raspberry Pi rozciągnięcia systemu operacyjnego i ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza |
| Windows 10 Pro | Publiczna wersja zapoznawcza |  |  |
| Windows 10 Enterprise | Publiczna wersja zapoznawcza |  |  |
| Windows 10 IoT Enterprise | Publiczna wersja zapoznawcza |  |  |
| Windows Server 2019 | Publiczna wersja zapoznawcza |  |  |

Wszystkie systemy operacyjne Windows muszą mieć wersję 1809 (kompilacja 17763) lub nowszą.

>[!NOTE]
>Obsługa programu Ubuntu Server 16,04 została zakończona w wersji IoT Edge 1,1.

#### <a name="windows-containers"></a>Kontenery systemu Windows

>[!IMPORTANT]
>IoT Edge 1,1 LTS to ostatni kanał wydania, który będzie obsługiwał kontenery systemu Windows. Począwszy od wersji 1,2, kontenery systemu Windows nie będą obsługiwane. Rozważ użycie lub przechodzenie do [IoT Edge dla systemu Linux w systemie Windows](iot-edge-for-linux-on-windows.md) w celu uruchomienia IoT Edge na urządzeniach z systemem Windows.

Moduły skompilowane jako kontenery systemu Windows można wdrażać tylko na urządzeniach z systemem Windows.

| System operacyjny | Procesor | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

Wszystkie systemy operacyjne Windows muszą być w wersji 1809 (kompilacja 17763). Konkretna kompilacja systemu Windows jest wymagana do IoT Edge w systemie Windows, ponieważ wersja kontenerów systemu Windows musi być dokładnie zgodna z wersją urządzenia hosta z systemem Windows. Kontenery systemu Windows używają obecnie tylko kompilacji 17763.

>[!NOTE]
>Pomoc techniczna dotycząca systemu Windows 10 IoT Core zakończyła IoT Edge się w wersji 1,1.

### <a name="tier-2"></a>Warstwa 2

Systemy wymienione w poniższej tabeli są uważane za zgodne z Azure IoT Edge, ale nie są aktywnie przetestowane ani obsługiwane przez firmę Microsoft.

| System operacyjny | Procesor | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20,04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Opiekun — wbudowany system operacyjny Linux](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Opiekun — wbudowany system operacyjny Linux w systemie operacyjnym + AMD64](./media/tutorial-c-module/green-check.png) | ![Opiekun — wbudowany system operacyjny Linux dla systemu operacyjnego i ARM32v7](./media/tutorial-c-module/green-check.png) | ![Opiekun — wbudowany system operacyjny Linux dla systemu operacyjnego i ARM64](./media/tutorial-c-module/green-check.png) |
| [Opiekun systemu operacyjnego w wersji Embedded Linux](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Opiekun — wbudowany system operacyjny Linux w wersji + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Opiekun — osadzony system operacyjny Linux w systemie operacyjnym i ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Rzeka wiatr 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Buster |  | ![Raspberry Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> procedura instalacji programu Ubuntu Server 18,04 w temacie [Instalowanie lub odinstalowywanie Azure IoT Edge dla systemu Linux](how-to-install-iot-edge.md) powinna być niezależna od zmian Ubuntu 20,04.

## <a name="releases"></a>Wydania

IoT Edge zasoby wydania i informacje o wersji są dostępne na stronie [wydania usługi Azure iotedge](https://github.com/Azure/azure-iotedge/releases) . Ta sekcja zawiera informacje zawarte w tych informacjach o wersji, które ułatwiają łatwą wizualizację składników poszczególnych wersji.

Składniki IoT Edge można zainstalować lub zaktualizować osobno i są wstecznie zgodne ze składnikami ze starszych wersji. W poniższej tabeli wymieniono składniki zawarte w poszczególnych wersjach:

| Release | Demon zabezpieczeń | Centrum brzegowe<br>Agent graniczny | Libiothsm | Moby |
|--|--|--|--|--|
| **1.1.0 LTS**<sup>1</sup> | 1.1.0 | 1.1.0 | 1.1.0 |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1,1 to pierwszy długi okres pomocy technicznej (LTS). Ta wersja nie wprowadziła żadnych nowych funkcji, ale otrzyma poprawki błędów i poprawki zabezpieczeń. IoT Edge 1,1 LTS używa platformy .NET Core 3,1 i będzie obsługiwana do 3 grudnia 2022 w celu dopasowania do [cyklu życia platformy .NET Core i .NET 5](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

>[!IMPORTANT]
>W przypadku korzystania z długotrwałego kanału obsługi zalecamy, aby wszyscy obecni klienci z systemem 1.0. x uaktualnili swoje urządzenia do wersji 1.1. x w celu uzyskania ciągłej pomocy technicznej.

IoT Edge używa zestawu SDK Microsoft. Azure. Devices. Client. Aby uzyskać więcej informacji, zobacz [repozytorium usługi Azure IoT C# SDK](https://github.com/Azure/azure-iot-sdk-csharp) w witrynie GitHub lub [zawartość referencyjną Azure SDK dla platformy .NET](/dotnet/api/overview/azure/iot/client). Na poniższej liście przedstawiono wersję zestawu SDK klienta, dla którego są testowane poszczególne wersje:

| Wersja usługi IoT Edge | Wersja zestawu SDK Microsoft. Azure. Devices. Client |
|------------------|--------------------------------------------|
| 1.1.0 (LTS)      | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

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
