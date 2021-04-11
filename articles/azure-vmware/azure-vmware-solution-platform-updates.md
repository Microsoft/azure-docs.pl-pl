---
title: Aktualizacje platformy dla rozwiązań VMware platformy Azure
description: Dowiedz się więcej na temat aktualizacji platformy dla rozwiązań VMware platformy Azure.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045216"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Aktualizacje platformy dla rozwiązań VMware platformy Azure

Rozwiązanie VMware firmy Azure zastosuje ważne aktualizacje, począwszy od marca 2021. Otrzymasz powiadomienie za pomocą Azure Service Health, które obejmuje oś czasu konserwacji. Aby uzyskać więcej informacji, zobacz [aktualizacje i uaktualnienia chmury prywatnej rozwiązania VMware platformy Azure](concepts-upgrades.md).

## <a name="march-24-2021"></a>24 marca 2021
Wszystkie nowe chmury prywatne rozwiązań VMware platformy Azure są wdrażane przy użyciu oprogramowania VMware vCenter w wersji 6.7 U3l i NSX-T w wersji 3.1.1. Wszystkie istniejące chmury prywatne zostaną zaktualizowane i uaktualnione do **czerwca 2021** do powyższych wersji.

Otrzymasz wiadomość e-mail z zaplanowaną datą i godziną konserwacji. Uaktualnienie można zmienić. W wiadomości e-mail znajdują się także szczegółowe informacje o uaktualnionym składniku, jego wpływie na obciążenia, dostęp do chmury prywatnej i inne usługi platformy Azure.  Godzinę przed uaktualnieniem otrzymasz powiadomienie, a następnie ponownie po jego zakończeniu.

## <a name="march-15-2021"></a>15 marca 2021 

- Usługa Azure VMware Solution Service przeprowadzi konserwację do **19 marca 2021,** aby zaktualizować serwer vCenter w chmurze prywatnej vCenter Server do wersji 3L 6,7 Update.

- Program VMware vCenter będzie niedostępny w tym czasie.  W związku z tym nie będzie można zarządzać maszynami wirtualnymi (zatrzymywanie, uruchamianie, tworzenie, usuwanie) ani skalowanie chmury prywatnej (Dodawanie/usuwanie serwerów i klastrów). Jednak wysoka dostępność VMware (HA) będzie nadal działać w celu ochrony istniejących maszyn wirtualnych. 
 
Aby uzyskać więcej informacji na temat tej wersji programu vCenter, zobacz [Informacje o wersji VMware vCenter Server 6,7 Update 3L](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 marca 2021

- Rozwiązanie VMware dla platformy Azure zastosuje [VMware ESXi 6,7 ESXi670 poprawek 202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) do istniejących danych prywatnych do **15 marca 2021**.

- Udokumentowane Obejścia dotyczące stosu vSphere, zgodnie z [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), zostaną również zastosowane **do 15 marca 2021**.

>[!NOTE]
>Jest to niezakłócone i nie powinno mieć wpływu na usługi lub obciążenia platformy Azure VMware. Podczas konserwacji różne alerty programu VMware, takie jak _utrata łączności sieciowej na DVPorts_ i _utrata nadmiarowości pasma na DVPorts_, są wyświetlane w programie vCenter i automatycznie czyszczone w miarę postępu konserwacji.

## <a name="post-update"></a>Po aktualizacji
Po zakończeniu zostaną wyświetlone nowsze wersje składników programu VMware. Jeśli zauważysz jakiekolwiek problemy lub masz pytania, skontaktuj się z naszym zespołem pomocy technicznej, otwierając bilet pomocy technicznej.