---
title: Obsługiwane systemy operacyjne, aparaty kontenerów — Azure IoT Edge
description: Dowiedz się, w których systemach operacyjnych można Azure IoT Edge demona i środowisko uruchomieniowe oraz obsługiwane aparaty kontenerów dla urządzeń produkcyjnych
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67532fce2cac0ec9d05b4caa069e63014b813bd8
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576350"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge obsługiwane systemy

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Ten artykuł zawiera szczegółowe informacje o tym, które systemy i składniki są obsługiwane IoT Edge, oficjalnie lub w wersji zapoznawczej.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli podczas korzystania z usługi Azure IoT Edge występują problemy, istnieje kilka sposobów szukania pomocy technicznej. Wypróbuj jeden z następujących kanałów, aby uzyskać pomoc techniczną:

**Zgłaszanie** usterek — większość developmentów, które trafiają do produktu Azure IoT Edge, odbywa się w IoT Edge projektu open source. Usterki można zgłaszać [na stronie problemów](https://github.com/azure/iotedge/issues) projektu. Usterki związane Azure IoT Edge dla systemu Linux w systemie Windows można zgłaszać na [stronie problemów iotedge-eflow](https://github.com/azure/iotedge-eflow/issues). Poprawki szybko nasuwają się od projektów do aktualizacji produktu.

**Zespół pomocy technicznej firmy Microsoft** — użytkownicy, którzy mają [plan](https://azure.microsoft.com/support/plans/) pomocy technicznej, mogą angażować zespół pomocy technicznej firmy Microsoft, tworząc bilet pomocy technicznej bezpośrednio z Azure Portal [.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)

**Żądania funkcji** — Azure IoT Edge śledzi żądania funkcji za pośrednictwem strony [User Voice produktu.](https://feedback.azure.com/forums/907045-azure-iot-edge)

## <a name="container-engines"></a>Aparaty kontenerów

Azure IoT Edge są implementowane jako kontenery, IoT Edge do ich uruchomienia potrzebuje aparatu kontenerów. Firma Microsoft udostępnia aparat kontenerów, moby-engine, aby spełnić to wymaganie. Ten aparat kontenerów jest oparty na projekcie open source Moby. Docker CE i Docker EE to inne popularne aparaty kontenerów. Są one również oparte na projekcie open source Moby i są zgodne z Azure IoT Edge. Firma Microsoft zapewnia najlepszą pomoc techniczną dla systemów korzystających z tych aparatów kontenerów. Firma Microsoft nie może jednak wysyłać poprawek dotyczących ich problemów. Z tego powodu firma Microsoft zaleca używanie aparatu Moby w systemach produkcyjnych.

<br>
<center>

![Aparat Moby jako środowisko uruchomieniowe kontenera](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Systemy operacyjne

Azure IoT Edge działa w większości systemów operacyjnych, w których można uruchamiać kontenery; Jednak nie wszystkie te systemy są jednakowo obsługiwane. Systemy operacyjne są pogrupowane w warstwy reprezentujące poziom pomocy technicznej, jaki mogą oczekiwać użytkownicy.

* Systemy warstwy 1 są obsługiwane. W przypadku systemów warstwy 1 firma Microsoft:
  * ma ten system operacyjny w testach automatycznych
  * udostępnia dla nich pakiety instalacyjne
* Systemy warstwy 2 są zgodne z Azure IoT Edge i mogą być używane stosunkowo łatwo. W przypadku systemów warstwy 2:
  * Firma Microsoft wykonała nieformalne testowanie na platformach lub zna partnera, który pomyślnie Azure IoT Edge na platformie
  * Pakiety instalacyjne dla innych platform mogą działać na tych platformach

Rodzina systemu operacyjnego hosta musi zawsze odpowiadać rodzinie systemu operacyjnego gościa używanego wewnątrz kontenera modułu.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Innymi słowy, kontenerów systemu Linux można używać tylko w kontenerach systemu Linux i Windows w systemie Windows. W przypadku korzystania z kontenerów systemu Windows obsługiwane są tylko izolowane kontenery procesów, a nie kontenery izolowane funkcji Hyper-V.  

IoT Edge dla systemu Linux w systemie Windows używa IoT Edge na maszynie wirtualnej z systemem Linux uruchomionej na hoście z systemem Windows. W ten sposób można uruchamiać moduły systemu Linux na urządzeniu z systemem Windows.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>Warstwa 1

Systemy wymienione w poniższych tabelach są obsługiwane przez firmę Microsoft, ogólnie dostępne lub w publicznej wersji zapoznawczej, i są testowane w każdej nowej wersji.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge obsługuje moduły sbudowaną jako kontenery systemu Linux lub Windows. Kontenery systemu Linux można wdrażać na urządzeniach z systemem Linux lub wdrażać na urządzeniach z systemem Windows przy użyciu IoT Edge dla systemu Linux w systemie Windows. Kontenery systemu Windows można wdrażać tylko na urządzeniach z systemem Windows.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Azure IoT Edge wersji 1.2 obsługuje tylko moduły sbudowaną jako kontenery systemu Linux.

Obecnie nie jest obsługiwany sposób uruchamiania programu IoT Edge wersji 1.2 na urządzeniach z systemem Windows. [IoT Edge dla systemu Linux w](iot-edge-for-linux-on-windows.md) systemie Windows jest zalecanym sposobem uruchamiania IoT Edge na urządzeniach z systemem Windows, ale obecnie działa tylko w IoT Edge 1.1. Aby uzyskać więcej informacji, zapoznaj się [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) tego artykułu.

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Kontenery systemu Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Moduły sbudowaną jako kontenery systemu Linux można wdrażać na urządzeniach z systemem Linux lub Windows. W przypadku urządzeń z systemem Linux IoT Edge uruchomieniowe jest instalowane bezpośrednio na urządzeniu hosta. W przypadku urządzeń z systemem Windows maszyna wirtualna z systemem Linux wstępnie IoT Edge uruchomieniowym na urządzeniu hosta.

[IoT Edge dla systemu Linux w systemie Windows](iot-edge-for-linux-on-windows.md) jest obecnie w publicznej wersji zapoznawczej, ale jest to zalecany sposób uruchamiania IoT Edge na urządzeniach z systemem Windows.

| System operacyjny | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza |
| Windows 10 Pro | Publiczna wersja zapoznawcza |  |  |
| Windows 10 Enterprise | Publiczna wersja zapoznawcza |  |  |
| Windows 10 IoT Enterprise | Publiczna wersja zapoznawcza |  |  |
| Windows Server 2019 | Publiczna wersja zapoznawcza |  |  |

Wszystkie systemy operacyjne Windows muszą mieć wersję 1809 (kompilacja 17763) lub nowszą.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| System operacyjny | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Publiczna wersja zapoznawcza |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Obsługa systemu Ubuntu Server 16.04 zakończyła się wraz z wydaniem IoT Edge wersji 1.1.

#### <a name="windows-containers"></a>Kontenery systemu Windows

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1.1 LTS to kanał ostatniej wersji, który będzie obsługiwać kontenery systemu Windows. Począwszy od wersji 1.2, kontenery systemu Windows nie będą obsługiwane. Rozważ użycie lub przejście do IoT Edge [systemu Linux w systemie Windows](iot-edge-for-linux-on-windows.md) w celu uruchamiania IoT Edge na urządzeniach z systemem Windows.

Moduły sbudowaną jako kontenery systemu Windows można wdrażać tylko na urządzeniach z systemem Windows.

| System operacyjny | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![sprawdzanie1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![sprawdzanie1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![sprawdzanie1](./media/tutorial-c-module/green-check.png) |  |  |

Wszystkie systemy operacyjne Windows muszą mieć wersję 1809 (kompilacja 17763). Określonej kompilacji systemu Windows jest wymagana IoT Edge systemie Windows, ponieważ wersja kontenerów systemu Windows musi dokładnie odpowiadać wersji urządzenia hosta z systemem Windows. Kontenery systemu Windows obecnie używają tylko kompilacji 17763.

>[!NOTE]
>Windows 10 IoT Core zakończyło się wraz z wydaniem IoT Edge wersji 1.1.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1.1 LTS to kanał ostatniej wersji, który obsługuje kontenery systemu Windows. Począwszy od wersji 1.2, kontenery systemu Windows nie są obsługiwane.

Informacje o obsługiwanych systemach operacyjnych dla kontenerów systemu Windows można znaleźć IoT Edge [wersji 1.1](?view=iotedge-2018-06&preserve-view=true) tego artykułu.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>Warstwa 2

Systemy wymienione w poniższej tabeli są uznawane za zgodne Azure IoT Edge, ale nie są aktywnie testowane ani konserwowane przez firmę Microsoft.

| System operacyjny | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Po |  | ![Raspberry Pi OS 1 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS 1 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Kroki instalacji systemu Ubuntu Server 18.04 w te Azure IoT Edge instalowania lub odinstalowywania systemu [Linux](how-to-install-iot-edge.md) powinny działać bez żadnych zmian w systemie Ubuntu 20.04.

## <a name="releases"></a>Wydania

IoT Edge i informacje o wersji są dostępne na [stronie wersji azure-iotedge.](https://github.com/Azure/azure-iotedge/releases) Ta sekcja zawiera informacje z tych informacji o wersji, które ułatwiają wizualizację składników poszczególnych wersji.

W poniższej tabeli wymieniono składniki zawarte w każdej wersji, począwszy od wersji 1.2.0. Składniki wymienione w tej tabeli można instalować lub aktualizować pojedynczo i są wstecznie zgodne ze starszymi wersjami.

| Release | a przek os. | edgeHub<br>edgeAgent | usługa tożsamości a języka |
| ------- | ---------- | -------------------- | ---------------------- |
| **1.2** | 1.2.0      | 1.2.0                | 1.2.0                  |

W poniższej tabeli wymieniono składniki zawarte w poszczególnych wersjach aż do wersji 1.1 LTS. Składniki wymienione w tej tabeli można instalować lub aktualizować pojedynczo i są wstecznie zgodne ze starszymi wersjami.

| Release | iotedge | edgeHub<br>edgeAgent | z o.o. | Moby |
|--|--|--|--|--|
| **1.1 LTS**<sup>1</sup> | 1.1.0<br>1.1.1<br><br> | 1.1.0<br>1.1.1<br>1.1.2 | 1.1.0<br>1.1.1<br><br> |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1.1 to pierwszy kanał wydań z długoterminową obsługą (LTS). Ta wersja nie wprowadziła żadnych nowych funkcji, ale będzie otrzymywać aktualizacje zabezpieczeń i poprawki regresji. IoT Edge 1.1 LTS używa programu .NET Core 3.1 i będzie obsługiwany do 3 grudnia 2022 r. w celu dopasowania cyklu życia wersji .NET Core i [.NET 5.](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)

>[!IMPORTANT]
>Wraz z wydaniem długoterminowego kanału pomocy technicznej zalecamy, aby wszyscy aktualni klienci korzystający z wersji 1.0.x uaktualnili swoje urządzenia do wersji 1.1.x, aby otrzymać bieżącą pomoc techniczną.

IoT Edge korzysta z zestawu SDK Microsoft.Azure.Devices.Client. Aby uzyskać więcej informacji, zobacz repozytorium [GitHub zestawu SDK](https://github.com/Azure/azure-iot-sdk-csharp) języka C# usługi Azure IoT lub zawartość referencyjną zestawu [Azure SDK dla platformy .NET](/dotnet/api/overview/azure/iot/client). Na poniższej liście przedstawiono wersję zestawu SDK klienta, z których są testowane poszczególne wersje:

| Wersja usługi IoT Edge | Wersja zestawu SDK Microsoft.Azure.Devices.Client |
|------------------|--------------------------------------------|
| 1.2.0            | 1.33.4-NestedEdge
| 1.1 (LTS)        | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge można uruchamiać na maszynach wirtualnych. Używanie maszyny wirtualnej jako urządzenia IoT Edge jest typowe, gdy klienci chcą rozszerzyć istniejącą infrastrukturę o analizę brzegową. Rodzina systemu operacyjnego maszyny wirtualnej hosta musi być dopasowana do rodziny systemu operacyjnego gościa używanego wewnątrz kontenera modułu. To wymaganie jest takie samo jak Azure IoT Edge uruchamiane bezpośrednio na urządzeniu. Azure IoT Edge jest niezależny od podstawowej technologii wirtualizacji i działa na maszynach wirtualnych obsługiwanych przez platformy takie jak Hyper-V i vSphere.

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![Azure IoT Edge na maszynie wirtualnej](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![Azure IoT Edge na maszynie wirtualnej](./media/support/edge-on-vm.png)

</center>

:::moniker-end

## <a name="minimum-system-requirements"></a>Minimalne wymagania systemowe

Azure IoT Edge działa świetnie na urządzeniach tak małych jak Raspberry Pi3 do sprzętu klasy serwerowej. Wybór odpowiedniego sprzętu dla scenariusza zależy od obciążeń, które chcesz uruchomić. Podejmowanie ostatecznej decyzji o urządzeniu może być skomplikowane. Można jednak łatwo rozpocząć tworzenie prototypów rozwiązania na tradycyjnych laptopach lub komputerach stacjonarnych.

Doświadczenie podczas tworzenia prototypów pomoże w wyborze ostatecznego urządzenia. Pytania, które należy wziąć pod uwagę, obejmują:

* Ile modułów jest w obciążeniu?
* Ile warstw współużytkuje kontenery modułów?
* W jakim języku są napisane moduły?
* Ile danych będą przetwarzane moduły?
* Czy moduły wymagają wyspecjalizowanego sprzętu do przyspieszania obciążeń?
* Jakie są żądane charakterystyki wydajności rozwiązania?
* Jaki jest budżet sprzętu?
