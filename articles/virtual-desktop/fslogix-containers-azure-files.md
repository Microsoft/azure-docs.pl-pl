---
title: Pliki kontenerów profilu FSLogix pulpitu wirtualnego systemu Windows — Azure
description: W tym artykule opisano kontenery profilu FSLogix w ramach usług pulpitu wirtualnego systemu Windows i usługi Azure Files.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b977944f3a531365487b9655820e5d8f85e13d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99225919"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Kontenery profili FSLogix i pliki platformy Azure

Usługa pulpitu wirtualnego systemu Windows zaleca kontenery profilu FSLogix jako rozwiązanie profilu użytkownika. Produkt FSLogix zaprojektowano pod kątem roamingu profilów w zdalnych środowiskach obliczeniowych, takich jak Windows Virtual Desktop. Przechowuje on kompletny profil użytkownika w jednym kontenerze. Po zalogowaniu ten kontener jest dynamicznie dołączany do środowiska obliczeniowego przy użyciu natywnie obsługiwanego wirtualnego dysku twardego (VHD) oraz wirtualnego dysku twardego funkcji Hyper-V (VHDX). Profil użytkownika jest natychmiast dostępny i pojawia się w systemie tak samo jak natywny profil użytkownika. W tym artykule opisano, jak kontenery profilu FSLogix używane z funkcją Azure Files na pulpicie wirtualnym systemu Windows.

>[!NOTE]
>Jeśli szukasz materiału do porównania o różnych opcjach magazynu kontenera profilu FSLogix na platformie Azure, zobacz [Opcje magazynu dla kontenerów profilów FSLogix](store-fslogix-profile.md).

## <a name="user-profiles"></a>Profile użytkowników

Profil użytkownika zawiera elementy danych dotyczące poszczególnych osób, w tym informacje o konfiguracji, takie jak ustawienia pulpitu, trwałe połączenia sieciowe i ustawienia aplikacji. Domyślnie system Windows tworzy profil użytkownika lokalnego, który jest ściśle zintegrowany z systemem operacyjnym.

Profil użytkownika zdalnego zawiera partycję między danymi użytkownika i systemem operacyjnym. Umożliwia wymianę lub zmianę systemu operacyjnego bez wpływu na dane użytkownika. W przypadku usługi Host sesji Pulpit zdalny (Desktop) i infrastruktury pulpitów wirtualnych (VDI) system operacyjny można wymienić z następujących powodów:

- Uaktualnienie systemu operacyjnego
- Zastąpienie istniejącej maszyny wirtualnej (VM)
- Użytkownik jest częścią w puli (nietrwałym) hosta usług pulpitu zdalnego lub środowiska VDI

Produkty firmy Microsoft pracują z kilkoma technologiami dla profilów użytkowników zdalnych, w tym z następującymi technologiami:
- Profile użytkowników mobilnych (RUP)
- Dyski profilu użytkownika (UPD)
- Roaming stanu przedsiębiorstwa (ESR)

UPD i RUP są najczęściej używanymi technologiami dla profilów użytkowników w środowiskach Pulpit zdalny hosta sesji (hosta) i wirtualnych dysków twardych (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Wyzwania z poprzednimi technologiami profilu użytkownika

Istniejące i starsze rozwiązania firmy Microsoft dla profilów użytkowników mają różne wyzwania. Żadne poprzednie rozwiązanie nie zostało obsłużone przez cały profil użytkownika, który jest dostarczony ze środowiskiem hosta sesji usług pulpitu zdalnego lub infrastruktury VDI. Na przykład UPD nie może obsłużyć dużych plików OST, a RUP nie utrwala nowoczesnych ustawień.

#### <a name="functionality"></a>Funkcjonalność

W poniższej tabeli przedstawiono zalety i ograniczenia dotyczące wcześniejszych technologii profilu użytkownika.

| Technologia | Ustawienia nowoczesne | Ustawienia Win32 | Ustawienia systemu operacyjnego | Dane użytkowników | Obsługiwane w jednostce SKU serwera | Magazyn zaplecza na platformie Azure | Magazyn zaplecza lokalnego | Obsługa wersji | Kolejny czas logowania |Uwagi|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Dyski profilu użytkownika (UPD)** | Tak | Tak | Tak | Tak | Tak | Nie | Tak | Win 7 + | Tak | |
| **Profil użytkownika mobilnego (RUP), tryb konserwacji** | Nie | Tak | Tak | Tak | Tak| Nie | Tak | Win 7 + | Nie | |
| **Enterprise State Roaming (ESR)** | Tak | Nie | Tak | Nie | Zobacz uwagi | Tak | Nie | Win 10 | Nie | Funkcje w jednostce SKU serwera, ale bez interfejsu użytkownika pomocniczego |
| **Wirtualizacja środowiska użytkownika (UE-V)** | Tak | Tak | Tak | Nie | Tak | Nie | Tak | Win 7 + | Nie |  |
| **Pliki w chmurze w usłudze OneDrive** | Nie | Nie | Nie | Tak | Zobacz uwagi | Zobacz uwagi  | Zobacz uwagi | Win 10 RS3 | Nie | Nie przetestowano w jednostce SKU serwera. Magazyn zaplecza na platformie Azure zależy od klienta synchronizacji. Magazyn zaplecza Premium wymaga klienta synchronizacji. |

#### <a name="performance"></a>Wydajność

UPD wymaga [bezpośrednie miejsca do magazynowania (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) , aby rozwiązać wymagania dotyczące wydajności. UPD używa protokołu SMB (Server Message Block). Kopiuje profil do maszyny wirtualnej, w której użytkownik jest rejestrowany.

#### <a name="cost"></a>Koszt

Gdy klastry funkcji S2D osiągnąą wymaganą wydajność, koszt jest kosztowny dla klientów korporacyjnych, ale szczególnie kosztownych dla małych i średnich klientów. W przypadku tego rozwiązania firmy płacisz za dyski magazynu, a także koszt maszyn wirtualnych korzystających z dysków dla udziału.

#### <a name="administrative-overhead"></a>Koszty administracyjne

Klastry funkcji S2D wymagają systemu operacyjnego, który jest instalowany, aktualizowany i konserwowany w stanie bezpiecznym. Procesy te i złożoność konfigurowania odzyskiwania po awarii programu S2D są przeznaczone tylko dla przedsiębiorstw z dedykowanym personelem działu IT.

## <a name="fslogix-profile-containers"></a>Kontenery profilu FSLogix

19 listopada 2018 [firma Microsoft pozyskali FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix rozwiązuje wiele wyzwań dotyczących kontenera profilu. Klucz między nimi:

- **Wydajność:** [Kontenery profilu FSLogix](/fslogix/configure-profile-container-tutorial/) są wysoce wydajne i rozwiązują problemy z wydajnością, które mają historycznie zablokowany tryb wymiany programu Exchange.
- **Usługa OneDrive:** Bez kontenerów profilów FSLogix usługa OneDrive dla firm nie jest obsługiwana w środowiskach nietrwałych hosta usług pulpitu zdalnego lub infrastruktury VDI. Na [stronie pomocy technicznej usługi ONEDRIVE VDI](/onedrive/sync-vdi-support) znajdziesz informacje o sposobie ich działania. Aby uzyskać więcej informacji, zobacz [Korzystanie z klienta synchronizacji na pulpitach wirtualnych](/deployoffice/rds-onedrive-business-vdi/).
- **Dodatkowe foldery:** FSLogix zapewnia możliwość rozbudowania profilów użytkowników w celu uwzględnienia dodatkowych folderów.

Od momentu nabycia firma Microsoft rozpoczęła wymianę istniejących rozwiązań profilu użytkownika, takich jak UPD, z kontenerami profilu FSLogix.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Files integrację z usługą domenową Azure Active Directory

Wydajność i funkcje kontenerów profilów FSLogix korzystają z chmury. 7 sierpnia 2019 pliki Microsoft Azure ogłoszone ogólnie dostęp do [Azure Files uwierzytelniania przy użyciu usługi domeny Azure Active Directory (AD DS)](../storage/files/storage-files-active-directory-overview.md). Odnosząc się do kosztów i obciążeń administracyjnych, Azure Files z uwierzytelnianiem za pomocą usługi Azure AD DS to rozwiązanie Premium dla profilów użytkowników w usłudze pulpitów wirtualnych systemu Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>Najlepsze rozwiązania dotyczące pulpitu wirtualnego systemu Windows

Pulpit wirtualny systemu Windows oferuje pełną kontrolę nad rozmiarem, typem i liczbą maszyn wirtualnych używanych przez klientów. Aby uzyskać więcej informacji, zobacz [co to jest pulpit wirtualny systemu Windows?](overview.md).

Aby zapewnić, że środowisko pulpitu wirtualnego systemu Windows jest zgodne z najlepszymi rozwiązaniami:

- Konto magazynu Azure Files musi znajdować się w tym samym regionie co maszyny wirtualne hosta sesji.
- Uprawnienia Azure Files powinny być zgodne z uprawnieniami opisanymi w [kontenerach wymagań — profile](/fslogix/fslogix-storage-config-ht).
- Każda maszyna wirtualna puli hostów musi być zbudowana z tego samego typu maszyn wirtualnych o takim samym typie i rozmiarze na podstawie tego samego obrazu wzorcowego.
- Każda maszyna wirtualna puli hostów musi znajdować się w tej samej grupie zasobów, aby ułatwić zarządzanie, skalowanie i aktualizowanie.
- W celu uzyskania optymalnej wydajności rozwiązanie magazynu i kontener profilu FSLogix powinny znajdować się w tej samej lokalizacji centrum danych.
- Konto magazynu zawierające obraz wzorcowy musi znajdować się w tym samym regionie i w ramach subskrypcji, w której są obsługiwane maszyny wirtualne.

## <a name="next-steps"></a>Następne kroki

Poniższe przewodniki umożliwiają skonfigurowanie środowiska pulpitu wirtualnego systemu Windows.

- Aby rozpocząć tworzenie rozwiązania do wirtualizacji pulpitu, zobacz [Tworzenie dzierżawy w systemie Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
- Aby utworzyć pulę hostów w ramach dzierżawy pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli hostów za pomocą witryny Azure Marketplace](create-host-pools-azure-marketplace.md).
- Aby skonfigurować w pełni zarządzane udziały plików w chmurze, zobacz [Konfigurowanie udziału Azure Files](/azure/storage/files/storage-files-active-directory-enable/).
- Aby skonfigurować kontenery profilu FSLogix, zobacz [Tworzenie kontenera profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md).
- Aby przypisać użytkowników do puli hostów, zobacz [Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows](manage-app-groups.md).
- Aby uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z przeglądarki sieci Web, zobacz [nawiązywanie połączenia z pulpitem wirtualnym systemu Windows](connect-web.md).
