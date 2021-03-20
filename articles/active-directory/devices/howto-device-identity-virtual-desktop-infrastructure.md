---
title: Tożsamość urządzenia i Wirtualizacja pulpitu — Azure Active Directory
description: Dowiedz się, w jaki sposób można razem używać tożsamości infrastruktury VDI i urządzeń usługi Azure AD
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
ms.openlocfilehash: c30ad26f079e6353dc4763b9ae968c33882d8ab6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96029351"
---
# <a name="device-identity-and-desktop-virtualization"></a>Tożsamość urządzenia i Wirtualizacja pulpitu

Administratorzy często wdrażają platformy infrastruktury pulpitu wirtualnego (VDI), które obsługują systemy operacyjne Windows w swoich organizacjach. Administratorzy wdrażają infrastrukturę VDI w programie:

- Usprawnianie zarządzania.
- Obniż koszty dzięki konsolidacji i scentralizowaniu zasobów.
- Zapewnianie mobilności użytkowników końcowych i swobody uzyskiwania dostępu do pulpitów wirtualnych w dowolnym miejscu, z dowolnego miejsca i na dowolnym urządzeniu.

Istnieją dwa podstawowe typy pulpitów wirtualnych:

- Stale
- Nietrwałe

Wersje trwałe wykorzystują unikatowy obraz stacjonarny dla każdego użytkownika lub puli użytkowników. Te unikatowe pulpity mogą być dostosowane i zapisywane do użytku w przyszłości. 

Wersje nietrwałe używają kolekcji pulpitów, do których użytkownicy mogą uzyskać dostęp w zależności od wymaganej bazy. Te komputery stacjonarne nietrwałe są przywracane do oryginalnego stanu, w przypadku systemu Windows bieżące<sup>1</sup> dzieje się tak, gdy maszyna wirtualna przechodzi przez proces resetowania/ponownego uruchamiania/systemu operacyjnego, a w przypadku systemu Windows niższego poziomu<sup>2</sup> dzieje się tak, gdy użytkownik wyloguje się.

Istnieje wzrost nietrwałych wdrożeń infrastruktury VDI, ponieważ zdalna współpraca nadal jest nowym standardem. W miarę jak klienci wdrażają nietrwały infrastrukturę VDI, należy upewnić się, że można zarządzać zmianą urządzeń, która może być spowodowana częstą rejestracją urządzeń, bez konieczności właściwej strategii zarządzania cyklem życia urządzeń.

> [!IMPORTANT]
> Niepowodzenie zarządzania zmianą urządzenia może prowadzić do wzrostu nacisku użycia przydziału dzierżawy oraz potencjalnego ryzyka przerwania działania usługi, jeśli limit przydziału dzierżawy został przekroczony. Aby uniknąć tej sytuacji, należy postępować zgodnie z poniższymi wskazówkami dotyczącymi wdrażania nietrwałych środowisk infrastruktury VDI.

W tym artykule omówiono wskazówki firmy Microsoft dla administratorów dotyczące obsługi tożsamości urządzeń i infrastruktury VDI. Aby uzyskać więcej informacji na temat tożsamości urządzeń, zobacz artykuł [co to jest tożsamość urządzenia](overview.md).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Przed skonfigurowaniem tożsamości urządzeń w usłudze Azure AD dla środowiska VDI zapoznaj się z obsługiwanymi scenariuszami. W poniższej tabeli przedstawiono, które scenariusze aprowizacji są obsługiwane. Inicjowanie obsługi w tym kontekście oznacza, że administrator może skonfigurować tożsamość urządzeń na dużą skalę, bez konieczności interakcji z użytkownikiem końcowym.

| Typ tożsamości urządzenia | Infrastruktura tożsamości | Urządzenia z systemem Windows | Wersja platformy VDI | Obsługiwane |
| --- | --- | --- | --- | --- |
| hybrydowym dołączonym do usługi Azure AD. | Federacyjna<sup>3</sup> | System Windows — bieżące i Windows niższe poziomy | Stale | Tak |
|   |   | Bieżące dla systemu Windows | Nietrwałe | Tak<sup>5</sup> |
|   |   | System Windows niższego poziomu | Nietrwałe | Tak<sup>6</sup> |
|   | Zarządzany<sup>4</sup> | System Windows — bieżące i Windows niższe poziomy | Stale | Tak |
|   |   | Bieżące dla systemu Windows | Nietrwałe | Nie |
|   |   | System Windows niższego poziomu | Nietrwałe | Tak<sup>6</sup> |
| Dołączone do usługi Azure AD | Federacyjni | Bieżące dla systemu Windows | Stale | Nie |
|   |   |   | Nietrwałe | Nie |
|   | Zarządzanie | Bieżące dla systemu Windows | Stale | Nie |
|   |   |   | Nietrwałe | Nie |
| Zarejestrowane w usłudze Azure AD | Federacyjne/zarządzane | Windows na poziomie bieżącym/Windows | Trwały/nietrwały | Nie dotyczy |

<sup>1</sup> **bieżące urządzenia systemu Windows** reprezentują system Windows 10, Windows Server 2016 V1803 lub nowszy i system Windows Server 2019.
<sup>dwa</sup> urządzenia **niskiego poziomu systemu Windows** reprezentują systemy Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2. Aby uzyskać informacje o pomocy technicznej w systemie Windows 7, zobacz temat [Obsługa systemu Windows 7 kończy się](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Aby uzyskać informacje na temat pomocy technicznej dotyczącej systemu Windows Server 2008 R2, zobacz [przygotowanie do systemu Windows server 2008 koniec wsparcia](https://www.microsoft.com/cloud-platform/windows-server-2008).

<sup>3</sup> środowisko infrastruktury tożsamości **federacyjnych** reprezentuje środowisko z dostawcą tożsamości, takim jak AD FS lub inne dostawcy tożsamości innych firm.

<sup>4</sup> **zarządzane** środowisko infrastruktury tożsamości reprezentuje środowisko z usługą Azure AD jako dostawcę tożsamości wdrożonego za pomocą jednej [synchronizacji skrótów haseł (PHS)](../hybrid/whatis-phs.md) lub [uwierzytelniania przekazywanego (PTA)](../hybrid/how-to-connect-pta.md) z [bezproblemowym logowaniem jednokrotnym](../hybrid/how-to-connect-sso.md).

<sup>5</sup> **Obsługa nietrwałości dla bieżącego systemu Windows** wymaga dodatkowej uwagi, jak opisano poniżej w sekcji wskazówki. W tym scenariuszu jest wymagany system Windows 10 1803, Windows Server 2019 lub Windows Server (półroczny kanał) w wersji 1803

<sup>6</sup> **Obsługa nietrwałości dla systemu Windows niższego poziomu** wymaga dodatkowej uwagi, jak opisano poniżej w sekcji wskazówki.


## <a name="microsofts-guidance"></a>Wskazówki firmy Microsoft

Administratorzy powinni zapoznać się z następującymi artykułami, na podstawie ich infrastruktury tożsamości, aby dowiedzieć się, jak skonfigurować sprzężenie hybrydowe usługi Azure AD.

- [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md)
- [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska zarządzanego](hybrid-azuread-join-managed-domains.md)

W przypadku wdrażania nietrwałego infrastruktury VDI firma Microsoft zaleca, aby administratorzy IT implementują poniższe wskazówki. Niewykonanie tej czynności spowoduje, że katalog będzie miał wiele starych urządzeń przyłączonych do hybrydowej usługi Azure AD zarejestrowanych z nietrwałej platformy VDI, co spowodowało zwiększenie nacisku na limit przydziału dzierżawy i ryzyko przerwania działania usługi z powodu wykroczenia limitu przydziału dzierżawy.

- Jeśli korzystasz z narzędzia przygotowywania systemu (sysprep.exe) i jeśli do instalacji używasz obrazu sprzed systemu Windows 10 1809, upewnij się, że obraz nie pochodzi z urządzenia, które jest już zarejestrowane w usłudze Azure AD jako dołączona hybrydowa usługa Azure AD.
- Jeśli korzystasz z migawki maszyny wirtualnej w celu utworzenia dodatkowych maszyn wirtualnych, upewnij się, że migawka nie pochodzi z maszyny wirtualnej, która jest już zarejestrowana w usłudze Azure AD jako sprzężenie hybrydowe usługi Azure AD.
- Utwórz i użyj prefiksu dla nazwy wyświetlanej (np. NPVDI-) komputera, który wskazuje pulpit jako nietrwały oparty na infrastrukturze VDI.
- Dla systemu Windows niższego poziomu:
   - Zaimplementuj polecenie **autoworkplacejoin/Leave** w ramach skryptu wylogowywania. To polecenie powinno być wyzwalane w kontekście użytkownika i powinno zostać wykonane, zanim użytkownik wyloguje się całkowicie i nadal ma łączność sieciową.
- W przypadku systemu Windows w środowisku federacyjnym (np. AD FS):
   - Zaimplementuj **dsregcmd/Join** w ramach sekwencji rozruchowej maszyny wirtualnej.
   - **Nie** wykonuj dsregcmd/Leave w ramach procesu zamykania/ponownego uruchamiania maszyny wirtualnej.
- Zdefiniuj i Implementuj proces [zarządzania przestarzałymi urządzeniami](manage-stale-devices.md).
   - Gdy masz strategię do identyfikowania nietrwałych hybrydowych urządzeń z usługą Azure AD (np. przy użyciu prefiksu nazwy wyświetlanej komputera), należy bardziej agresywnie wyczyścić te urządzenia, aby upewnić się, że katalog nie jest zużywany na wiele starych urządzeń.
   - W przypadku nietrwałych wdrożeń infrastruktury VDI na bieżącym i wyłączonym poziomie systemu Windows należy usunąć urządzenia, które mają **ApproximateLastLogonTimestamp** starsze niż 15 dni.
 
## <a name="next-steps"></a>Następne kroki

[Konfigurowanie dołączania hybrydowego Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md)
