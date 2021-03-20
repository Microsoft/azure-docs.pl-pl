---
title: Laboratoria w Azure Lab Services — często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące laboratoriów w Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 757af8f30e9a71a3889d9f625c87a002af2e1302
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96437188"
---
# <a name="labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Laboratoria w Azure Lab Services — często zadawane pytania
Uzyskaj odpowiedzi na niektóre często zadawane pytania dotyczące laboratoriów w Azure Lab Services. 

## <a name="quotas"></a>Przydziały

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Czy przydziały na użytkownika, czy na tydzień czy na cały czas trwania laboratorium? 
Przydział ustawiony dla laboratorium jest przeznaczony dla każdego ucznia na cały czas trwania laboratorium. [Zaplanowana godzina działania maszyn wirtualnych](how-to-create-schedules.md) nie jest uwzględniana w stosunku do przydziału przydzielonego dla użytkownika. Limit przydziału jest przeznaczony dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych.  Aby uzyskać więcej informacji na temat przydziałów, zobacz [Ustawianie przydziałów dla użytkowników](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Jeśli nauczycieli włącza na maszynie wirtualnej ucznia, czy ma to wpływ na limit przydziału ucznia? 
Nie. Żadnej. Gdy nauczycieli włącza na maszynie wirtualnej ucznia, nie ma to wpływu na przydział przydzielony studentowi. 

## <a name="schedules"></a>Harmonogramy

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Czy wszystkie maszyny wirtualne w laboratorium są uruchamiane automatycznie po ustawieniu harmonogramu? 
Nie. Nie wszystkie maszyny wirtualne. Tylko maszyny wirtualne, które są przypisane do użytkowników zgodnie z harmonogramem. Maszyny wirtualne, które nie są przypisane do użytkownika, nie są automatycznie uruchamiane. Jest to zaprojektowane. 

## <a name="lab-accounts"></a>Konta laboratorium

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Dlaczego nie mogę utworzyć laboratorium z powodu niedostępności zakresu adresów? 

Laboratoria mogą tworzyć maszyny wirtualne laboratorium w zakresie adresów IP określonym podczas tworzenia konta laboratorium w Azure Portal. Po podaniu zakresu adresów każde laboratorium, które jest tworzone po przypisaniu 512 adresów IP dla maszyn wirtualnych laboratorium. Zakres adresów dla konta laboratorium musi być wystarczająco duży, aby pomieścić wszystkie laboratoria, które mają zostać utworzone w ramach konta laboratorium. 

Na przykład jeśli masz blok/19-10.0.0.0/19, ten zakres adresów uwzględnia 8192 adresy IP i 16 laboratoriów (8192/512 = 16 laboratoriów). W takim przypadku tworzenie laboratorium kończy się niepowodzeniem na siedemnastu tworzenia laboratorium.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Jakie zakresy portów należy otworzyć w ustawieniach zapory organizacji, aby połączyć się z maszynami wirtualnymi laboratorium za pośrednictwem protokołu RDP/SSH?

Porty to: 49152 – 65535. Laboratoria znajdują się za modułem równoważenia obciążenia. Każde laboratorium ma jeden publiczny adres IP, a każda maszyna wirtualna w laboratorium ma unikatowy port. 

Możesz również zobaczyć prywatny adres IP każdej maszyny wirtualnej na karcie **Pula maszyn wirtualnych** na stronie głównej dla laboratorium w Azure Portal. W przypadku ponownego opublikowania laboratorium, publiczny adres IP laboratorium nie ulegnie zmianie, ale prywatny IP i numer portu każdej maszyny wirtualnej w laboratorium można zmienić. Więcej informacji można znaleźć w artykule: [Ustawienia zapory dla Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Jaki zakres publicznych adresów IP należy otworzyć w ustawieniach zapory organizacji w celu połączenia z maszynami wirtualnymi laboratorium za pośrednictwem protokołu RDP/SSH?
Zobacz [zakresy adresów IP i Tagi usług platformy Azure — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519), która udostępnia publiczny zakres adresów IP dla centrów danych na platformie Azure. Możesz otworzyć adresy IP dla regionów, w których znajdują się konta w laboratorium.

## <a name="virtual-machine-images"></a>Obrazy maszyn wirtualnych

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Dlaczego jako twórca laboratorium nie można włączyć dodatkowych opcji obrazu na liście rozwijanej obrazy maszyn wirtualnych podczas tworzenia nowego laboratorium?

Gdy administrator doda Cię jako twórcę laboratorium do konta laboratorium, otrzymujesz uprawnienia do tworzenia laboratoriów. Ale nie masz uprawnień do edytowania żadnych ustawień w ramach konta laboratorium, w tym listy włączonych obrazów maszyn wirtualnych. Aby włączyć dodatkowe obrazy, skontaktuj się z administratorem konta laboratorium, aby to zrobić, lub poproś administratora o dodanie Cię jako roli współautor do konta laboratorium. Rola współautor udzieli uprawnień do edytowania listy obrazów maszyn wirtualnych na koncie laboratorium.

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>Czy mogę dołączyć dodatkowe dyski do maszyny wirtualnej?
Nie. nie jest możliwe dołączenie dodatkowych dysków do maszyny wirtualnej w laboratorium z klasą. 

## <a name="users"></a>Użytkownicy

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Ilu użytkowników może być w laboratorium zajęć?
Do laboratorium zajęć można dodać maksymalnie 400 użytkowników. 

## <a name="blog-post"></a>Wpis w blogu
Zasubskrybuj [blog Azure Lab Services](https://aka.ms/azlabs-blog).

## <a name="update-notifications"></a>Powiadomienia o aktualizacjach
Zasubskrybuj [Aktualizacje usług Lab Services](https://azure.microsoft.com/updates/?product=lab-services) , aby otrzymywać informacje o nowych funkcjach usługi Lab Services.

## <a name="general"></a>Ogólne
### <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli w tym miejscu nie udzielono odpowiedzi na moje pytanie?
Jeśli pytania nie ma na liście, daj nam znać, aby pomóc Ci znaleźć odpowiedź.

- Opublikuj pytanie na końcu tego często zadawanych pytań. 
- Aby dotrzeć do szerszego grona użytkowników, Opublikuj pytanie na [Azure Lab Services — forum społeczności Tech](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices). 
- W przypadku żądań funkcji Prześlij swoje żądania i pomysły dotyczące [Azure Lab Services — User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

