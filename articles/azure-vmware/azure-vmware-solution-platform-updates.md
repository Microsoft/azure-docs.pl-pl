---
title: Aktualizacje platformy dla rozwiązań VMware platformy Azure
description: Dowiedz się więcej na temat aktualizacji platformy dla rozwiązań VMware platformy Azure.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 73bd1d088f9055ebd80a28c6247ea9dfa6229093
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586237"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Aktualizacje platformy dla rozwiązań VMware platformy Azure

Ważne aktualizacje rozwiązania VMware platformy Azure zostaną zastosowane od marca 2021. Otrzymasz powiadomienie za pomocą Azure Service Health zawierającego oś czasu konserwacji. Aby uzyskać więcej informacji o procesach i funkcjach uaktualniania kluczy w rozwiązaniu VMware platformy Azure, zobacz [aktualizacje i uaktualnienia chmury prywatnej rozwiązania Azure VMware](concepts-upgrades.md).

## <a name="march-15-2021"></a>15 marca 2021 

- Usługa Azure VMware Solution Service przeprowadzi konserwację do 19 marca 20201, aby zaktualizować program vCenter Server w chmurze prywatnej do vCenter Server 6,7 Update wersja 3L.

- W tym czasie program VMware vCenter będzie niedostępny i nie będzie można zarządzać maszynami wirtualnymi (Zatrzymaj, Uruchom, Utwórz, Usuń). Skalowanie chmury prywatnej (Dodawanie/usuwanie serwerów i klastrów) będzie również niedostępne. Wysoka dostępność VMware (HA) będzie nadal działać w celu zapewnienia ochrony istniejących maszyn wirtualnych. 
 
Aby uzyskać więcej informacji na temat tej wersji programu vCenter, zobacz [Informacje o wersji VMware vCenter Server 6,7 Update 3L](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 marca 2021

- Rozwiązania VMware dla platformy Azure zastosują poprawki do 15 marca 2021, aby ESXi w istniejących chmurach prywatnych do [VMware ESXi 6,7, wydanie poprawek ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html).

- Udokumentowane Obejścia dotyczące stosu vSphere, zgodnie z [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), zostaną również zastosowane do 15 marca 2021.

>[!NOTE]
>Jest to niezakłócone i nie powinno mieć wpływu na usługi lub obciążenia platformy Azure VMware. Podczas konserwacji różne alerty programu VMware, takie jak _utrata łączności sieciowej na DVPorts_ i _utrata nadmiarowości pasma na DVPorts_, są wyświetlane w programie vCenter i automatycznie czyszczone w miarę postępu konserwacji.

## <a name="post-update"></a>Po aktualizacji
Po zakończeniu zostaną wyświetlone nowsze wersje składników programu VMware. Jeśli zauważysz jakiekolwiek problemy lub masz pytania, skontaktuj się z naszym zespołem pomocy technicznej, otwierając bilet pomocy technicznej.





