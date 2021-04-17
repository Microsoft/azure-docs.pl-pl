---
title: Tożsamość urządzenia i wirtualizacja pulpitu — Azure Active Directory
description: Dowiedz się, jak można używać tożsamości urządzeń usługi Azure AD i VDI
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1d78094effe6919587f24c2262612e4fab347d
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575381"
---
# <a name="device-identity-and-desktop-virtualization"></a>Tożsamość urządzenia i wirtualizacja pulpitu

Administratorzy często wdrażają platformy infrastruktury pulpitu wirtualnego (VDI) hostowania systemów operacyjnych Windows w swoich organizacjach. Administratorzy wdrażają VDI w celu:

- Usprawnij zarządzanie.
- Zmniejszanie kosztów dzięki konsolidacji i centralizacji zasobów.
- Zapewnij użytkownikom mobilnym i swobodę dostępu do pulpitów wirtualnych w dowolnym czasie, z dowolnego miejsca i na dowolnym urządzeniu.

Istnieją dwa podstawowe typy pulpitów wirtualnych:

- Stale
- Nietrwale

Wersje trwałe używają unikatowego obrazu pulpitu dla każdego użytkownika lub puli użytkowników. Te unikatowe pulpity można dostosować i zapisać do użytku w przyszłości. 

Wersje nietrwale używają kolekcji komputerów stacjonarnych, do których użytkownicy mogą uzyskać dostęp zgodnie z potrzebami. Te nietrwale pulpity są przywracane do pierwotnego stanu, w przypadku bieżącego systemu Windows<sup>1 dzieje</sup> się tak, gdy maszyna wirtualna przechodzi przez proces zamykania/ponownego uruchamiania/resetowania systemu operacyjnego, a w przypadku systemu Windows na poziomie<sup>2</sup> dzieje się tak, gdy użytkownik się wyczyszczy.

Wznosi się wzrost liczby nietrwałych wdrożeń infrastruktury VDI, ponieważ praca zdalna nadal jest nową normą. Ponieważ klienci wdrażają nietrwałe infrastruktury VDI, ważne jest, aby zarządzać współczynnikami zmian urządzeń, które mogą być spowodowane częstą rejestracją urządzeń bez konieczności posiadania odpowiedniej strategii zarządzania cyklem życia urządzenia.

> [!IMPORTANT]
> Nie można zarządzać współczynnikami zmian urządzeń, może to prowadzić do zwiększenia wykorzystania limitu przydziału dzierżawy i potencjalnego ryzyka przerwy w działaniu usługi w przypadku przekroowania limitu przydziału dzierżawy. Aby uniknąć takiej sytuacji, należy postępować zgodnie ze wskazówkami udokumentowanymi poniżej podczas wdrażania nietrwałych środowisk VDI.

Ten artykuł zawiera wskazówki firmy Microsoft dla administratorów dotyczące obsługi tożsamości urządzeń i VDI. Aby uzyskać więcej informacji na temat tożsamości urządzenia, zobacz artykuł [Co to jest tożsamość urządzenia.](overview.md)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Przed skonfigurowaniem tożsamości urządzeń w usłudze Azure AD dla środowiska VDI zapoznaj się z obsługiwanymi scenariuszami. W poniższej tabeli pokazano, które scenariusze aprowowania są obsługiwane. Aprowizowanie w tym kontekście oznacza, że administrator może konfigurować tożsamości urządzeń na dużą skalę bez konieczności interakcji użytkownika końcowego.

| Typ tożsamości urządzenia | Infrastruktura tożsamości | Urządzenia z systemem Windows | Wersja platformy VDI | Obsługiwane |
| --- | --- | --- | --- | --- |
| hybrydowym dołączonym do usługi Azure AD. | Federowana<sup>3</sup> | Bieżący system Windows i system Windows na poziomie niżej | Stale | Yes |
|   |   | Bieżący system Windows | Nietrwale | Tak<sup>5</sup> |
|   |   | System Windows niższego poziomu | Nietrwale | Tak<sup>6</sup> |
|   | Zarządzane<sup>4</sup> | Bieżący system Windows i system Windows na poziomie niżej | Stale | Yes |
|   |   | Bieżący system Windows | Nietrwale | Nie |
|   |   | System Windows niższego poziomu | Nietrwale | Tak<sup>6</sup> |
| Dołączone do usługi Azure AD | Federacyjni | Bieżący system Windows | Stale | Nie |
|   |   |   | Nietrwale | Nie |
|   | Zarządzanie | Bieżący system Windows | Stale | Nie |
|   |   |   | Nietrwale | Nie |
| Zarejestrowane w usłudze Azure AD | Federowana/zarządzana | Bieżący/windows na poziomie niżej | Trwałe/nietrwale | Nie dotyczy |

<sup>1 Bieżące</sup> **urządzenia z systemem Windows** Windows 10, Windows Server 2016 w wersji 1803 lub wyższej oraz Windows Server 2019.
<sup>2</sup> Urządzenia z systemem **Windows na** 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2. Aby uzyskać informacje o pomocy technicznej w systemie Windows 7, zobacz [Obsługa systemu Windows 7 kończy się](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Aby uzyskać informacje o pomocy technicznej dotyczące systemu Windows Server 2008 R2, zobacz Przygotowanie do zakończenia obsługi systemu [Windows Server 2008.](https://www.microsoft.com/cloud-platform/windows-server-2008)

<sup>3</sup> Środowisko **infrastruktury tożsamości federacji** reprezentuje środowisko z dostawcą tożsamości, takim jak AD FS lub inny dostawca tożsamości innej firmy.

<sup>4</sup> **Środowisko** infrastruktury tożsamości zarządzanej reprezentuje środowisko z usługą Azure AD jako dostawcą tożsamości wdrożonym przy użyciu synchronizacji skrótów haseł [(PHS)](../hybrid/whatis-phs.md) lub uwierzytelniania [pass-through (PTA)](../hybrid/how-to-connect-pta.md) z bezproblemowym logowaniem [pojedynczym.](../hybrid/how-to-connect-sso.md)

<sup>5 Obsługa</sup> **braku trwałości dla bieżącego systemu Windows** wymaga dodatkowej uwagi, zgodnie z poniższymi instrukcjami w sekcji ze wskazówkami. Ten scenariusz wymaga Windows 10 wersji 1803, Windows Server 2019 lub Windows Server (półroczny kanał) począwszy od wersji 1803

<sup>6</sup> **Obsługa nietrwale dla** systemu Windows na poziomie spoza systemu Windows wymaga dodatkowej uwagi, zgodnie z poniższymi instrukcjami w sekcji ze wskazówkami.


## <a name="microsofts-guidance"></a>Wskazówki firmy Microsoft

Administratorzy powinni odwołać się do następujących artykułów, w oparciu o infrastrukturę tożsamości, aby dowiedzieć się, jak skonfigurować dołączanie hybrydowe do usługi Azure AD.

- [Konfigurowanie dołączania Azure Active Directory hybrydowego dla środowiska federacji](hybrid-azuread-join-federated-domains.md)
- [Konfigurowanie dołączania Azure Active Directory hybrydowego dla środowiska zarządzanego](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>Nietrwale trwała VDI

W przypadku wdrażania nietrwale trwałej wirtualnej maszyny wirtualnej firma Microsoft zaleca, aby administratorzy IT zaimplementowali poniższe wskazówki. Niewykonanie tej sytuacji spowoduje, że katalog będzie miał wiele nieaktywnego urządzenia przyłączone hybrydowo do usługi Azure AD, które zostały zarejestrowane z nietrwale działającej platformy VDI, co spowoduje zwiększone ciśnienie limitu przydziału dzierżawy i ryzyko przerwy w działaniu usługi z powodu przekroowania limitu przydziału dzierżawy.

- Jeśli korzystasz z narzędzia przygotowywania systemu (sysprep.exe) i do instalacji używasz obrazu wersji Windows 10 1809 z wersji wstępnej, upewnij się, że obraz nie pochodzi z urządzenia, które zostało już zarejestrowane w usłudze Azure AD jako przyłączone do hybrydowej usługi Azure AD.
- Jeśli tworzysz dodatkowe maszyny wirtualne przy użyciu migawki maszyny wirtualnej, upewnij się, że migawka nie pochodzi z maszyny wirtualnej, która została już zarejestrowana w usłudze Azure AD jako dołączenie hybrydowe do usługi Azure AD.
- Utwórz i użyj prefiksu dla nazwy wyświetlanej (np. NPVDI-) komputera, który wskazuje pulpit jako nietrwale oparty na VDI.
- W przypadku systemu Windows na poziomie niższą:
   - Implementowanie **autoworkplacejoin /leave** polecenia w ramach skryptu wylogowania. To polecenie powinno zostać wyzwolone w kontekście użytkownika i powinno zostać wykonane przed całkowitego wylogowania użytkownika i w czasie, gdy nadal istnieje łączność sieciowa.
- W przypadku bieżącego systemu Windows w środowisku federowym (np. AD FS):
   - **Zaim implementuj dsregcmd /join** jako część sekwencji/kolejności rozruchu maszyny wirtualnej i przed rozpoczęciem użytkownika.
   - **NIE uruchamiaj** polecenia dsregcmd /leave w ramach procesu zamykania/ponownego uruchamiania maszyny wirtualnej.
- Zdefiniuj i zaim implementuj [proces zarządzania nieaktywne urządzenia.](manage-stale-devices.md)
   - Po identyfikować nietrwale hybrydowe urządzenia przyłączone do usługi Azure AD (np. przy użyciu prefiksu nazwy wyświetlanej komputera), należy bardziej agresywnie czyścić te urządzenia, aby upewnić się, że katalog nie jest zużywany z dużą ilością nieaktywnego urządzenia.
   - W przypadku nietrwałych wdrożeń infrastruktury VDI w bieżącym i nietrwałym systemie Windows należy usunąć urządzenia z sygnaturą **ApproximateLastLogonTimestamp** starszą niż 15 dni.

> [!NOTE]
> Jeśli chcesz zapobiec przyłączeniu urządzenia do nietrwale trwałej maszyny wirtualnej, upewnij się, że ustawiono następujący klucz rejestru:  
> `HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001`    
>
> Upewnij się, że używasz Windows 10 w wersji 1803 lub wyższej.  
>
> Roaming wszystkich danych w ścieżce `%localappdata%` nie jest obsługiwany. Jeśli zdecydujesz się na przeniesienie zawartości w obszarze , upewnij się, że zawartość następujących folderów i kluczy rejestru nigdy nie opuszcza urządzenia `%localappdata%` pod żadnym warunkiem.  Na przykład: Narzędzia do migracji profilów muszą pominąć następujące foldery i klucze:
>
> * `%localappdata%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy`
> * `%localappdata%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy`
> * `%localappdata%\Packages\<any app package>\AC\TokenBroker`
> * `%localappdata%\Microsoft\TokenBroker`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\IdentityCRL`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\AAD`
>


### <a name="persistent-vdi"></a>Trwałe VDI

Podczas wdrażania trwałych VDI firma Microsoft zaleca, aby administratorzy IT zaimplementowali poniższe wskazówki. Niewykonanie tej metody spowoduje problemy z wdrażaniem i uwierzytelnianiem. 

- Jeśli korzystasz z narzędzia przygotowywania systemu (sysprep.exe), a do instalacji używasz obrazu wersji wstępnej Windows 10 1809, upewnij się, że obraz nie pochodzi z urządzenia, które zostało już zarejestrowane w usłudze Azure AD jako przyłączone hybrydowo do usługi Azure AD.
- Jeśli tworzysz dodatkowe maszyny wirtualne przy użyciu migawki maszyny wirtualnej, upewnij się, że migawka nie pochodzi z maszyny wirtualnej, która została już zarejestrowana w usłudze Azure AD jako dołączenie hybrydowe do usługi Azure AD.

Ponadto zalecamy zaimplementowanie procesu zarządzania [nieaktywnego urządzenia.](manage-stale-devices.md) Dzięki temu w przypadku okresowego resetowania maszyn wirtualnych katalog nie będzie zużywany z dużą ilością nieaktywnego urządzenia.
 
## <a name="next-steps"></a>Następne kroki

[Konfigurowanie dołączania Azure Active Directory hybrydowego dla środowiska federatora](hybrid-azuread-join-federated-domains.md)
