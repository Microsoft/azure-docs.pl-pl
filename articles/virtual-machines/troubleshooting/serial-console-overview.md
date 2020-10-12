---
title: Konsola szeregowa Azure | Microsoft Docs
description: Konsola szeregowa Azure umożliwia łączenie się z maszyną wirtualną, gdy SSH lub RDP nie są dostępne.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 28c5a3085d84b25deb7c5ee09a9c9cc4d7a06819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87374069"
---
# <a name="azure-serial-console"></a>Konsola szeregowa platformy Azure

Konsola szeregowa w Azure Portal zapewnia dostęp do konsoli opartej na tekście dla maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych z systemem Linux lub Windows. To połączenie szeregowe łączy się z portem szeregowym ttyS0 lub COM1 wystąpienia zestawu skalowania maszyn wirtualnych, zapewniając dostęp niezależny od sieci lub stanu systemu operacyjnego. Dostęp do konsoli szeregowej można uzyskać tylko przy użyciu Azure Portal i jest to dozwolone tylko dla tych użytkowników, którzy mają rolę dostępu współautora lub wyższą dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

Konsola szeregowa działa w taki sam sposób w przypadku maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych. W tym dokumencie wszystkie wzmianki dotyczące maszyn wirtualnych będą niejawnie obejmować wystąpienia zestawu skalowania maszyn wirtualnych, chyba że określono inaczej.

Konsola szeregowa jest ogólnie dostępna w globalnych regionach platformy Azure i w publicznej wersji zapoznawczej w Azure Government. Nie jest jeszcze dostępna w chmurze platformy Azure w Chinach.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Wymagania wstępne dotyczące uzyskiwania dostępu do konsoli szeregowej platformy Azure
Aby uzyskać dostęp do konsoli szeregowej na maszynie wirtualnej lub wystąpieniu zestawu skalowania maszyn wirtualnych, potrzebne są następujące elementy:

- Diagnostyka rozruchu musi być włączona dla maszyny wirtualnej
- Konto użytkownika, które używa uwierzytelniania hasła, musi istnieć na maszynie wirtualnej. Można utworzyć użytkownika opartego na hasłach za pomocą funkcji [resetowania hasła](../extensions/vmaccess.md#reset-password) rozszerzenia dostępu do maszyny wirtualnej. Wybierz pozycję **zresetuj hasło** w sekcji **Pomoc techniczna i rozwiązywanie problemów** .
- Konto platformy Azure z dostępem do konsoli szeregowej musi mieć [rolę współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) zarówno dla maszyny wirtualnej, jak i konta magazynu [diagnostyki rozruchu](boot-diagnostics.md) .
- Wdrożenia klasyczne nie są obsługiwane. W maszynie wirtualnej lub wystąpieniu zestawu skalowania maszyn wirtualnych musi być używany Azure Resource Manager model wdrażania.

> [!NOTE]
> Konsola szeregowa jest obecnie niezgodna z zarządzanym kontem magazynu diagnostyki rozruchu. Aby korzystać z konsoli szeregowej, upewnij się, że korzystasz z niestandardowego konta magazynu.

## <a name="get-started-with-the-serial-console"></a>Wprowadzenie do konsoli szeregowej
Konsola szeregowa dla maszyn wirtualnych i zestawu skalowania maszyn wirtualnych jest dostępna tylko za pośrednictwem Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Konsola szeregowa dla Virtual Machines
Konsola szeregowa dla maszyn wirtualnych jest tak prosta jak kliknięcie **konsola szeregowa** w sekcji **Pomoc techniczna i rozwiązywanie problemów** w Azure Portal.
  1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

  1. Przejdź do opcji **wszystkie zasoby** i wybierz maszynę wirtualną. Zostanie otwarta strona przegląd dla maszyny wirtualnej.

  1. Przewiń w dół do sekcji **Pomoc techniczna i rozwiązywanie problemów** i wybierz pozycję **konsola szeregowa**. Zostanie otwarte nowe okienko z konsolą szeregową i rozpocznie się nawiązywanie połączenia.

     ![Okno konsoli szeregowej systemu Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Konsola szeregowa dla Virtual Machine Scale Sets
Konsola szeregowa jest dostępna dla zestawów skalowania maszyn wirtualnych, które są dostępne dla każdego wystąpienia w zestawie skalowania. Przed wyświetleniem przycisku **konsola szeregowa** należy przejść do poszczególnych wystąpień zestawu skalowania maszyn wirtualnych. Jeśli zestaw skalowania maszyn wirtualnych nie ma włączonej diagnostyki rozruchu, należy zaktualizować model zestawu skalowania maszyn wirtualnych, aby umożliwić diagnostykę rozruchu, a następnie uaktualnić wszystkie wystąpienia do nowego modelu w celu uzyskania dostępu do konsoli szeregowej.
  1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

  1. Przejdź do opcji **wszystkie zasoby** i wybierz zestaw skalowania maszyn wirtualnych. Zostanie otwarta strona przegląd zestawu skalowania maszyn wirtualnych.

  1. Przejdź do **wystąpień**

  1. Wybierz wystąpienie zestawu skalowania maszyn wirtualnych

  1. W sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **konsola szeregowa**. Zostanie otwarte nowe okienko z konsolą szeregową i rozpocznie się nawiązywanie połączenia.

     ![Konsola szeregowa zestawu skalowania maszyn wirtualnych z systemem Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>TLS 1,2 w konsoli szeregowej
Konsola szeregowa używa protokołu TLS 1,2 na całej trasie, aby zabezpieczyć całą komunikację w ramach usługi. Konsola szeregowa jest zależna od konta magazynu diagnostyki rozruchu zarządzanego przez użytkownika, a protokół TLS 1,2 musi być skonfigurowany oddzielnie dla konta magazynu. Instrukcje w tym celu znajdują się [tutaj](../../storage/common/transport-layer-security-configure-minimum-version.md).

## <a name="advanced-uses-for-serial-console"></a>Zaawansowane zastosowania konsoli szeregowej
Poza dostępem konsoli do maszyny wirtualnej można również użyć konsoli szeregowej platformy Azure dla następujących zasobów:
* Wysyłanie [polecenia żądania systemowego do maszyny wirtualnej](./serial-console-nmi-sysrq.md)
* Wysyłanie [przerwania niemaskowalnego do maszyny wirtualnej](./serial-console-nmi-sysrq.md)
* Bezpieczne [Ponowne uruchamianie lub wymuszone wymuszanie napędu maszyny wirtualnej](./serial-console-power-options.md)


## <a name="next-steps"></a>Następne kroki
Dodatkowa dokumentacja konsoli szeregowej jest dostępna na pasku bocznym.
- Więcej informacji można znaleźć w [konsoli szeregowej dla maszyn wirtualnych z systemem Linux](./serial-console-linux.md).
- Więcej informacji można znaleźć w [konsoli szeregowej dla maszyn wirtualnych z systemem Windows](./serial-console-windows.md).
