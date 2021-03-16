---
title: Aktualizacje platformy dla rozwiązań VMware platformy Azure
description: Dowiedz się więcej na temat aktualizacji platformy dla rozwiązań VMware platformy Azure.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 4f4c697f345cca093a83eab2f915aaf9e80ab10f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563134"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Aktualizacje platformy dla rozwiązań VMware platformy Azure

Ważne aktualizacje rozwiązania VMware platformy Azure zostaną zastosowane od marca 2021. Otrzymasz powiadomienie za pomocą Azure Service Health zawierającego oś czasu konserwacji. Ten artykuł zawiera informacje o tym, czego można oczekiwać podczas tej operacji konserwacji i zmian w chmurze prywatnej.

## <a name="march-15-2021"></a>15 marca 2021 

- Usługa Azure VMware Solution Service będzie przeprowadzać konserwację w celu zaktualizowania programu vCenter Server w chmurze prywatnej vCenter Server do wersji 6,7 aktualizacji 3L do 19 marca 2021.

- W tym czasie program VMware vCenter będzie niedostępny i nie będzie można zarządzać maszynami wirtualnymi (Zatrzymaj, Uruchom, Utwórz, Usuń). Wysoka dostępność VMware (HA) będzie nadal działać w celu zapewnienia ochrony istniejących maszyn wirtualnych. Skalowanie chmury prywatnej (Dodawanie/usuwanie serwerów i klastrów) będzie również niedostępne.
 
Aby uzyskać więcej informacji na temat tej wersji programu vCenter, zobacz [Informacje o wersji VMware vCenter Server 6,7 Update 3L](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 marca 2021

- Rozwiązania VMware dla platformy Azure będą stosować poprawki do ESXi w istniejących chmurach prywatnych do [VMware ESXi 6,7, poprawki Release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) do 15 marca 2021.

- Udokumentowane Obejścia dotyczące stosu vSphere, zgodnie z [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), zostaną również zastosowane do 15 marca 2021.

>[!NOTE]
>Jest to niezakłócone i nie powinno mieć wpływu na usługi lub obciążenia platformy Azure VMware. Podczas konserwacji różne alerty programu VMware, takie jak _utrata łączności sieciowej na DVPorts_ i _utrata nadmiarowości pasma na DVPorts_, są wyświetlane w programie vCenter i automatycznie czyszczone w miarę postępu konserwacji.

## <a name="post-update"></a>Po aktualizacji
Po zakończeniu zostaną wyświetlone nowsze wersje składników programu VMware. Jeśli zauważysz jakiekolwiek problemy lub masz pytania, skontaktuj się z naszym zespołem pomocy technicznej, otwierając bilet pomocy technicznej.



