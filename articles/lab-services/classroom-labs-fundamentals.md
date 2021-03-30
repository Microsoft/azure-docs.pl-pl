---
title: Podstawy architektury w Azure Lab Services | Microsoft Docs
description: W tym artykule omówiono podstawowe zasoby używane przez usługi laboratoryjne i podstawową architekturę laboratorium.
author: emaher
ms.topic: overview
ms.date: 09/16/2020
ms.author: enewman
ms.openlocfilehash: 71d59e8bcf7b4078255d6b119e9d62f366e46033
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96437324"
---
# <a name="architecture-fundamentals-in-azure-lab-services"></a>Podstawy architektury w Azure Lab Services

Azure Lab Services to rozwiązanie SaaS (oprogramowanie jako usługa), co oznacza, że zasoby potrzebne przez usługi laboratoryjne są obsługiwane przez użytkownika. W tym artykule omówiono podstawowe zasoby używane przez usługi laboratoryjne i podstawową architekturę laboratorium.  

Azure Lab Services zapewnia kilka obszarów, które umożliwiają korzystanie z własnych zasobów w połączeniu z usługami Lab Services.  Aby uzyskać więcej informacji o korzystaniu z maszyn wirtualnych w sieci, zobacz jak [równorzędne sieci wirtualne](how-to-connect-peer-virtual-network.md).  Aby ponownie wykorzystać obrazy z galerii obrazów udostępnionych, zobacz How [to Attach a Shared Image Gallery](how-to-attach-detach-shared-image-gallery.md).

Poniżej znajduje się Podstawowa architektura laboratorium z klasą.  Konto laboratorium jest hostowane w Twojej subskrypcji. Maszyny wirtualne uczniów, wraz z zasobami wymaganymi do obsługi maszyn wirtualnych, są hostowane w ramach subskrypcji należącej do usługi laboratoryjnej. Zapoznaj się z bardziej szczegółowymi informacjami o tym, co jest w subskrypcji usługi laboratorium.

![Architektura Basic Labs](./media/classroom-labs-fundamentals/labservices-basic-architecture.png)

## <a name="hosted-resources"></a>Zasoby hostowane

Zasoby wymagane do uruchomienia laboratorium zajęć są hostowane w jednej z subskrypcji platformy Azure zarządzanych przez firmę Microsoft.  Zasoby obejmują szablonową maszynę wirtualną instruktora, maszynę wirtualną dla każdego ucznia oraz elementy związane z siecią, takie jak moduł równoważenia obciążenia, Sieć wirtualna i sieciowa Grupa zabezpieczeń.  Te subskrypcje są monitorowane w przypadku podejrzanych działań.  Należy pamiętać, że to monitorowanie jest wykonywane zewnętrznie do maszyn wirtualnych za pomocą rozszerzenia maszyny wirtualnej lub monitorowania wzorca sieci.  Jeśli funkcja [zamykania po rozłączeniu](how-to-enable-shutdown-disconnect.md) jest włączona, na maszynie wirtualnej jest włączone rozszerzenie diagnostyczne. Rozszerzenie umożliwia informowanie usług laboratoryjnych o zdarzeniu odłączenia sesji protokołu RDP (Remote Desktop Protocol).

## <a name="virtual-network"></a>Virtual Network

Każde laboratorium jest odizolowane od własnej sieci wirtualnej.  Jeśli laboratorium ma [równorzędną sieć wirtualną](how-to-connect-peer-virtual-network.md), każde laboratorium jest izolowane przez własną podsieć.  Studenci nawiązują połączenie z maszyną wirtualną za pomocą modułu równoważenia obciążenia.  Żaden z maszyn wirtualnych ucznia nie ma publicznego adresu IP; mają one tylko prywatny adres IP.  Parametry połączenia dla ucznia będą publicznym adresem IP modułu równoważenia obciążenia i losowy port z zakresu od 49152 do 65535.  Reguły ruchu przychodzącego w module równoważenia obciążenia przekazują połączenie, w zależności od systemu operacyjnego, do portu 22 (SSH) lub portu 3389 (RDP) odpowiedniej maszyny wirtualnej. SIECIOWEJ grupy zabezpieczeń uniemożliwia zewnątrz ruch na innych portach.

## <a name="access-control-to-the-virtual-machines"></a>Kontrola dostępu do maszyn wirtualnych

Usługi laboratoryjne obsługują Umiejętność wykonywania akcji takich jak uruchamianie i zatrzymywanie na maszynach wirtualnych.  Kontroluje również dostęp do informacji o połączeniu z maszyną wirtualną.

Usługi laboratoryjne obsługują również rejestrację studentów do usługi. Obecnie istnieją dwa różne ustawienia dostępu: ograniczone i nieograniczone. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zarządzania użytkownikami laboratorium](how-to-configure-student-usage.md#send-invitations-to-users) . Dostęp ograniczony oznacza, że usługi laboratoryjne sprawdzają, czy studenci są dodawani jako użytkownik przed zezwoleniem na dostęp. Nieograniczone oznacza, że każdy użytkownik może zarejestrować się, o ile ma link rejestracji, i ma pojemność w laboratorium. Nieograniczone może być przydatne dla zdarzeń hackathon.

Maszyny wirtualne uczniów, które są hostowane w laboratorium zajęć, mają nazwę użytkownika i hasło ustawione przez twórcę laboratorium.  Alternatywnie, twórca laboratorium może zezwolić zarejestrowanym uczniom na wybranie własnego hasła przy pierwszym logowaniu.  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach dostępnych w usługach Lab Services, zobacz temat [Azure Lab Services koncepcje](classroom-labs-concepts.md) i [Azure Lab Services Omówienie](classroom-labs-overview.md).
