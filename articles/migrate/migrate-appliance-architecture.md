---
title: Architektura urządzenia usługi Azure Migrate
description: Zawiera omówienie urządzenia Azure Migrate używanego w ocenie i migracji serwera.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 42d4a722be25eec4b3e27012350346018fdba0f3
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754117"
---
# <a name="azure-migrate-appliance-architecture"></a>Architektura urządzenia usługi Azure Migrate

W tym artykule opisano architekturę i procesy urządzeń Azure Migrate. Urządzenie Azure Migrate jest lekkim urządzeniem wdrożonym lokalnie, aby odnajdywać maszyny wirtualne i serwery fizyczne do migracji na platformę Azure. 

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Urządzenie Azure Migrate jest używane w następujących scenariuszach.

**Scenariusz** | **Narzędzie** | **Sposób użycia** 
--- | --- | ---
**Ocena maszyny wirtualnej VMware** | Azure Migrate: Ocena serwera | Odnajdywanie maszyn wirtualnych VMware.<br/><br/> Odkryj aplikacje maszynowe i zależności.<br/><br/> Zbierz metadane maszyn i metadane wydajności i Wyślij do platformy Azure.
**Migracja maszyn wirtualnych VMware (bez agentów)** | Azure Migrate: Migracja serwera | Odnajdywanie maszyn wirtualnych VMware<br/><br/>  Replikowanie maszyn wirtualnych VMware z [migracją bez agenta](server-migrate-overview.md).
**Ocena maszyny wirtualnej funkcji Hyper-V** | Azure Migrate: Ocena serwera | Odnajdywanie maszyn wirtualnych funkcji Hyper-V.<br/><br/> Zbierz metadane maszyn i metadane wydajności i Wyślij do platformy Azure.
**Maszyna fizyczna** |  Azure Migrate: Ocena serwera |  Odnajdywanie serwerów fizycznych.<br/><br/> Zbierz metadane maszyn i metadane wydajności i Wyślij do platformy Azure.

## <a name="appliance-components"></a>Składniki urządzenia

Urządzenie ma wiele składników.

- **Aplikacja zarządzania**: jest to aplikacja internetowa do wprowadzania danych przez użytkownika podczas wdrażania urządzenia. Używane podczas oceniania maszyn do migracji na platformę Azure.
- **Agent odnajdywania**: Agent zbiera dane konfiguracji maszyny. Używane podczas oceniania maszyn do migracji na platformę Azure. 
- **Agent modułu zbierającego**: Agent zbiera dane wydajności. Używane podczas oceniania maszyn do migracji na platformę Azure.
- **Agent dra**: organizuje REPLIKACJĘ maszyny wirtualnej i koordynuje komunikację między replikowanymi maszynami i platformą Azure. Używane tylko w przypadku replikowania maszyn wirtualnych VMware na platformę Azure przy użyciu migracji bez wykorzystania agentów.
- **Brama**: wysyła zreplikowane dane na platformę Azure. Używane tylko w przypadku replikowania maszyn wirtualnych VMware na platformę Azure przy użyciu migracji bez wykorzystania agentów.
- **Usługa Autoaktualizacja**: aktualizuje składniki urządzenia (jest uruchamiany co 24 godziny).



## <a name="appliance-deployment"></a>Wdrażanie urządzenia

- Urządzenie Azure Migrate można skonfigurować przy użyciu szablonu dla [funkcji Hyper-v](how-to-set-up-appliance-hyper-v.md) lub oprogramowania [VMware](how-to-set-up-appliance-vmware.md) lub przy użyciu Instalatora skryptów programu PowerShell dla oprogramowania [VMware/Hyper-v](deploy-appliance-script.md)i [serwerów fizycznych](how-to-set-up-appliance-physical.md). 
- Wymagania dotyczące obsługi urządzeń i wymagania wstępne dotyczące wdrażania są podsumowane w [macierzy obsługi urządzeń](migrate-appliance.md).


## <a name="appliance-registration"></a>Rejestracja urządzenia

Podczas konfigurowania urządzenia należy zarejestrować urządzenie w Azure Migrate i wystąpiły akcje podsumowania w tabeli.

**Akcja** | **Szczegóły** | **Uprawnienia**
--- | --- | ---
**Zarejestruj dostawców źródeł** | Dostawcy zasobów są zarejestrowani w ramach subskrypcji wybranej podczas konfigurowania urządzenia: Microsoft. OffAzure, Microsoft. zmigrować i Microsoft. kluczy.<br/><br/> Rejestracja dostawcy zasobów umożliwia skonfigurowanie subskrypcji do pracy z dostawcą zasobów. | Do zarejestrowania dostawców zasobów należy mieć rolę współautor lub właściciela w ramach subskrypcji.
**Tworzenie aplikacji usługi Azure AD — komunikacja** | Azure Migrate tworzy aplikację Azure Active Directory (Azure AD) do komunikacji (uwierzytelnianie i autoryzacja) między agentami działającymi na urządzeniu i ich usługami działającymi na platformie Azure.<br/><br/> Ta aplikacja nie ma uprawnień do wykonywania wywołań Azure Resource Manager lub uzyskiwania dostępu do usługi Azure RBAC na żadnym z zasobów. | [Te uprawnienia](./tutorial-discover-vmware.md#prepare-an-azure-user-account) będą potrzebne do Azure Migrate tworzenia aplikacji.
**Tworzenie aplikacji usługi Azure AD — Magazyn kluczy** | Ta aplikacja jest tworzona tylko w przypadku migrowania maszyn wirtualnych VMware na platformę Azure bez wykorzystania agentów.<br/><br/> Służy on wyłącznie do uzyskiwania dostępu do magazynu kluczy utworzonego w ramach subskrypcji użytkownika w celu migracji bez wykorzystania agentów.<br/><br/> Ma dostęp do usługi Azure RBAC w magazynie kluczy platformy Azure (utworzonym w dzierżawie klienta), gdy odnajdywanie jest inicjowane z urządzenia. | [Te uprawnienia](./tutorial-discover-vmware.md#prepare-an-azure-user-account) będą potrzebne do Azure Migrate tworzenia aplikacji.



## <a name="collected-data"></a>Zebrane dane

Dane zbierane przez klienta programu dla wszystkich scenariuszy wdrażania są podsumowane w [macierzy obsługi urządzeń](migrate-appliance.md).

## <a name="discovery-and-collection-process"></a>Proces odnajdywania i kolekcjonowania

![Architektura](./media/migrate-appliance-architecture/architecture1.png)

Urządzenie komunikuje się z serwerami vCenter i hostami/klastrami funkcji Hyper-V przy użyciu następującego procesu.

1. **Rozpocznij odnajdowanie**:
    - Po rozpoczęciu odnajdywania na urządzeniu funkcji Hyper-V komunikuje się z hostami funkcji Hyper-V na porcie WinRM 5985 (HTTP).
    - Po rozpoczęciu odnajdywania na urządzeniu VMware program domyślnie komunikuje się z serwerem vCenter na porcie TCP 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można go skonfigurować w aplikacji sieci Web urządzenia.
2. **Zbierz dane dotyczące metadanych i wydajności**:
    - Urządzenie używa sesji model wspólnych informacji (CIM) do zbierania danych maszyny wirtualnej funkcji Hyper-V z hosta funkcji Hyper-V na porcie 5985.
    - Urządzenie domyślnie komunikuje się z portem 443, aby zebrać dane maszyn wirtualnych VMware z vCenter Server.
3. **Wyślij dane**: urządzenie wysyła zebrane dane w celu Azure Migrate oceny serwera i migracji serwera Azure Migrate przez port SSL 443. Urządzenie może nawiązać połączenie z platformą Azure za pośrednictwem Internetu lub za pośrednictwem ExpressRoute (wymaga komunikacji równorzędnej firmy Microsoft).
    - W przypadku danych dotyczących wydajności urządzenie zbiera dane użycia w czasie rzeczywistym.
        - Dane dotyczące wydajności są zbierane co 20 sekund dla programu VMware i co 30 sekund dla funkcji Hyper-V dla każdej metryki wydajności.
        - Zebrane dane są zestawiane w celu utworzenia jednego punktu danych przez 10 minut.
        - Wartość szczytowego wykorzystania jest wybierana ze wszystkich 20/30-sekundowych punktów danych i wysyłana do platformy Azure w celu obliczenia oceny.
        - W oparciu o wartość percentylu określoną we właściwościach oceny (pięćdziesiąt/90/używany 95./99), dziesięć minutowe punkty są sortowane w kolejności rosnącej, a odpowiednia wartość percentylu jest używana do obliczania oceny
    - W przypadku migracji serwera urządzenie uruchamia zbieranie danych maszyny wirtualnej i replikuje je na platformie Azure.
4. **Ocenianie i migrowanie**: teraz można tworzyć oceny z metadanych zebranych przez urządzenie przy użyciu funkcji oceny serwera Azure Migrate. Ponadto można rozpocząć Migrowanie maszyn wirtualnych VMware przy użyciu migracji serwera Azure Migrate, aby organizować replikację maszyn wirtualnych bez agentów.

## <a name="appliance-upgrades"></a>Uaktualnienia urządzeń

Urządzenie zostanie uaktualnione w miarę aktualizowania Azure Migrate agentów działających na urządzeniu. Dzieje się tak automatycznie, ponieważ automatyczna aktualizacja jest domyślnie włączona na urządzeniu. Można zmienić to ustawienie domyślne, aby ręcznie zaktualizować agentów.

Aby wyłączyć automatyczne aktualizowanie w rejestrze, należy ustawić klucz "Autostart" HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance wartość 0 (DWORD).


## <a name="next-steps"></a>Następne kroki

[Zapoznaj](migrate-appliance.md) się z matrycą obsługi urządzeń.