---
title: Przegląd aprowizacji systemu Linux
description: Omówienie sposobu przenoszenia obrazów maszyn wirtualnych z systemem Linux lub tworzenia nowych obrazów do użycia na platformie Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6dafa400f2ce2421db6775084befc0abeab70a04
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563222"
---
# <a name="azure-linux-vm-provisioning"></a>Inicjowanie obsługi maszyn wirtualnych z systemem Linux na platformie Azure
Podczas tworzenia maszyny wirtualnej na podstawie uogólnionego obrazu (galerii obrazów udostępnionych lub zarządzanego obrazu) płaszczyzna kontroli umożliwi utworzenie maszyny wirtualnej i przekazanie parametrów i ustawień do maszyny wirtualnej. Ta nazwa jest nazywana *obsługą* maszyny wirtualnej. Podczas aprowizacji platforma wykonuje wymagane przez maszynę wirtualną Tworzenie wartości parametrów (nazwa hosta, nazwa użytkownika, hasło, klucze SSH, customData), które są dostępne dla maszyny wirtualnej w trakcie jej uruchamiania. 

Agent aprowizacji rozszerzania wewnątrz obrazu będzie interfejsem z platformą, łącząc się z wieloma niezależnymi interfejsami aprowizacji), ustaw właściwości i sygnał na platformę, która została ukończona. 

Agenci aprowizacji mogą być [agentem systemu Azure Linux](../extensions/agent-linux.md)lub funkcją [Cloud-init](./using-cloud-init.md). Są to [wymagania wstępne](create-upload-generic.md) dotyczące tworzenia uogólnionych obrazów.

Agenci aprowizacji oferują pomoc techniczną dla wszystkich poświadczonej [dystrybucji systemu Azure Linux](./endorsed-distros.md), a w wielu przypadkach dystrybucji obrazy będą dostarczane z programem Cloud-init i agentem systemu Linux. Dzięki temu można korzystać z usługi Cloud-init do obsługi aprowizacji, a następnie Agent systemu Linux zapewnia pomoc techniczną do obsługi [rozszerzeń platformy Azure](../extensions/features-windows.md). Zapewnianie pomocy technicznej dla rozszerzeń oznacza, że maszyna wirtualna będzie kwalifikować się do obsługi dodatkowych usług platformy Azure, takich jak resetowanie hasła maszyny wirtualnej, monitorowanie platformy Azure, Azure Backup, szyfrowanie dysków Azure itp.

Po zakończeniu aprowizacji Usługa Cloud-init zostanie uruchomiona na każdym rozruchu. Usługa Cloud-init będzie monitorować zmiany w maszynie wirtualnej, takie jak zmiany w sieci, Instalowanie i formatowanie dysku tymczasowych oraz uruchamianie agenta systemu Linux. Agent systemu Linux jest ciągle uruchamiany na serwerze, szukając "stanu celu" (nowej konfiguracji) z platformy Azure, więc po każdym zainstalowaniu rozszerzeń agent będzie mógł je przetworzyć.

Gdy obecnie istnieją dwa agenci aprowizacji, Usługa Cloud-init powinna być wybranym agentem aprowizacji, a Agent systemu Linux powinien zostać zainstalowany na potrzeby obsługi rozszerzenia. Dzięki temu można korzystać z optymalizacji platform i można wyłączyć/usunąć agenta systemu Linux, aby uzyskać szczegółowe informacje na temat tworzenia obrazów bez agenta i sposobu ich usuwania. Zapoznaj się z tą [dokumentacją](disable-provisioning.md).

Jeśli masz jądro systemu Linux, które nie może obsługiwać jednego z agentów, ale chcesz ustawić niektóre właściwości tworzenia maszyny wirtualnej, takie jak nazwa hosta, customData, nazwa użytkownika, hasło, klucze SSH, w tym dokumencie omówiono sposób [tworzenia uogólnionych obrazów bez agenta](no-agent.md)i spełniania wymagań platformy.


## <a name="provisioning-agent-responsibilities"></a>Obowiązki agentów aprowizacji

**Inicjowanie obsługi obrazu**
  
- Tworzenie konta użytkownika
- Konfigurowanie typów uwierzytelniania SSH
- Wdrażanie kluczy publicznych SSH i par kluczy
- Ustawianie nazwy hosta
- Publikowanie nazwy hosta na platformie DNS platformy
- Raportowanie odcisku palca klucza hosta SSH do platformy
- Zarządzanie dyskami zasobów
- Formatowanie i Instalowanie dysku zasobów
- Wykorzystywanie i przetwarzanie `customData`
 
**Sieć**
  
- Zarządza trasami w celu poprawy zgodności z serwerami DHCP platformy
- Zapewnia stabilność nazwy interfejsu sieciowego

**Jądro**
  
- Konfiguruje wirtualną architekturę NUMA (Wyłącz dla jądra <`2.6.37` )
- Zużywa entropię funkcji Hyper-V dla `/dev/random`
- Konfiguruje limity czasu SCSI dla urządzenia głównego (co może być zdalne)

**Diagnostyka**
  
- Przekierowanie konsoli do portu szeregowego

## <a name="communication"></a>Komunikacja
Przepływ informacji z platformy do agenta odbywa się za pośrednictwem dwóch kanałów:

- Dysk DVD z dołączonym czasem rozruchu dla wdrożeń IaaS. Dysk DVD zawiera plik konfiguracji zgodny z OVF, który zawiera wszystkie informacje o aprowizacji, inne niż rzeczywiste pary kluczy SSH.
- Punkt końcowy TCP uwidaczniający interfejs API REST używany do uzyskiwania wdrożenia i konfiguracji topologii.


## <a name="azure-provisioning-agent-requirements"></a>Wymagania dotyczące agenta aprowizacji platformy Azure
Agent systemu Linux i Usługa Cloud-init są zależne od niektórych pakietów systemowych w celu poprawnego działania:
- Python 2.6 lub nowsza wersja
- OpenSSL 1.0 lub nowsza wersja
- OpenSSH 5.3 lub nowsza wersja
- Narzędzia systemu plików: `sfdisk`, `fdisk`, `mkfs`, `parted`
- Narzędzia do haseł: chpasswd, sudo
- Narzędzia do przetwarzania tekstu: SED, grep
- Narzędzia sieciowe: trasa IP
- Obsługa jądra na potrzeby instalowania systemów plików UDF.

## <a name="next-steps"></a>Następne kroki

W razie potrzeby można [wyłączyć Inicjowanie obsługi administracyjnej i usunąć agenta systemu Linux](disable-provisioning.md).
