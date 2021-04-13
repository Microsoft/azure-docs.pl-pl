---
title: Windows Virtual Desktop konfigurowanie dołączania aplikacji MSIX udziału plików — Azure
description: Jak skonfigurować dołączanie udziału plików dla aplikacji MSIX na Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d8aaa8d5013c426ac1ab6b367309c51be4929cee
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366406"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>Konfigurowanie udziału plików do dołączania aplikacji MSIX

Wszystkie obrazy MSIX muszą być przechowywane w udziałach sieciowych, do których mogą uzyskiwać dostęp użytkownicy w puli hostów z uprawnieniami tylko do odczytu.

Dołączanie aplikacji MSIX nie ma żadnych zależności od typu sieci szkieletowej magazynu używanej przez udział plików. Zagadnienia dotyczące udziału dołączania aplikacji MSIX są takie same jak w przypadku udziału FSLogix. Aby dowiedzieć się więcej o wymaganiach dotyczących magazynu, zobacz Opcje magazynu dla [kontenerów profilów FSLogix w Windows Virtual Desktop](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Wymagania dotyczące wydajności

Limity rozmiaru obrazu dołączania obrazów w aplikacji MSIX dla systemu zależą od typu magazynu, z których korzystasz do przechowywania plików VHD lub VHDx, a także od ograniczeń rozmiaru plików VHD, VHSD lub CIM oraz systemu plików.

W poniższej tabeli przedstawiono przykład, ile zasobów wymaga jeden obraz MSIX o rozmiarze 1 GB z jedną aplikacją MSIX dla każdej maszyny wirtualnej:

| Zasób             | Wymagania |
|----------------------|--------------|
| We/Wy na poziomie stanu stałego    | 1 we/wy naps       |
| Logowanie przy rozruchu komputera | 10 we/wy na/wy      |
| Opóźnienie              | 400 ms       |

Wymagania mogą się znacznie różnić w zależności od tego, ile aplikacji w pakiecie MSIX jest przechowywanych w obrazie MSIX. W przypadku większych obrazów MSIX należy przydzielić większą przepustowość.

### <a name="storage-recommendations"></a>Zalecenia dotyczące magazynu

Platforma Azure oferuje wiele opcji magazynu, których można użyć do dołączania aplikacji MISX. Zalecamy korzystanie z Azure Files lub Azure NetApp Files, ponieważ te opcje oferują najlepszą wartość między kosztami i narzutami na zarządzanie. W artykule [Opcje magazynu dla kontenerów profilów FSLogix](store-fslogix-profile.md) w usłudze Windows Virtual Desktop porównano różne rozwiązania magazynu zarządzanego dostępne na platformie Azure w kontekście Windows Virtual Desktop.

### <a name="optimize-msix-app-attach-performance"></a>Optymalizowanie wydajności dołączania aplikacji MSIX

Oto kilka innych czynności, które zalecamy, aby zoptymalizować wydajność dołączania aplikacji MSIX:

- Rozwiązanie magazynu, które jest wykorzystywane do dołączania aplikacji MSIX, powinno być w tej samej lokalizacji centrum danych co hosty sesji.
- Aby uniknąć wąskich gardeł wydajności, wyklucz następujące pliki VHD, VHDX i CIM ze skanowania antywirusowego:
   
    - <MSIXAppAttachFileShare \> \* . Vhd
    - <MSIXAppAttachFileShare \> \* . Vhdx
    - \\\\storageaccount.file.core.windows.net \\ udostępnij \* \* . Vhd
    - \\\\storageaccount.file.core.windows.net \\ udostępnij \* \* . Vhdx
    - <MSIXAppAttachFileShare>. Cim
    - \\\\storageaccount.file.core.windows.net \\ udostępnij \* \* . Cim

- Oddziel sieć szkieletową magazynu dla dołączania aplikacji MSIX od kontenerów profilu FSLogix.
- Wszystkie konta systemowe maszyn wirtualnych i konta użytkowników muszą mieć uprawnienia tylko do odczytu, aby uzyskać dostęp do udziału plików.
- Wszystkie plany odzyskiwania po awarii Windows Virtual Desktop muszą obejmować replikowanie udziału plików dołączania aplikacji MSIX w dodatkowej lokalizacji trybu failover. Aby dowiedzieć się więcej na temat odzyskiwania po awarii, zobacz Konfigurowanie planu [ciągłości działania i odzyskiwania po awarii.](disaster-recovery.md)

## <a name="how-to-set-up-the-file-share"></a>Jak skonfigurować udział plików

Proces instalacji udziału plików dołączania aplikacji MSIX jest w dużym stopniu taki sam jak proces instalacji udziałów plików profilu [FSLogix.](create-host-pools-user-profile.md) Musisz jednak przypisać użytkownikom różne uprawnienia. Dołączanie aplikacji MSIX wymaga uprawnień tylko do odczytu w celu uzyskania dostępu do udziału plików.

Jeśli przechowujesz aplikacje MSIX w systemie Azure Files, w przypadku hostów sesji musisz przypisać do udziału wszystkie maszyny wirtualne hosta sesji zarówno z kontrolą dostępu opartą na rolach (RBAC) konta magazynu, jak i z udziałami plików Nowy system plików technologii (NTFS).

| Obiekt platformy Azure                      | Wymagana rola                                     | Funkcja roli                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Host sesji (obiekty komputerów maszyn wirtualnych)| Współautor udziału SMB danych w pliku magazynu          | Odczytywanie i wykonywanie, odczyt, lista zawartości folderu  |
| Administratorzy udziału plików              | Współautor udziału SMB danych w pliku magazynu z podwyższonym poziomem uprawnień | Pełna kontrola                                  |
| Użytkownicy w udziałach plików               | Współautor udziału SMB danych w pliku magazynu          | Odczytywanie i wykonywanie, odczyt, lista zawartości folderu  |

Aby przypisać uprawnienia maszyn wirtualnych hosta sesji do konta magazynu i udziału plików:

1. Utwórz Active Directory Domain Services zabezpieczeń (AD DS).

2. Dodaj konta komputerów dla wszystkich maszyn wirtualnych hosta sesji jako członków grupy.

3. Zsynchronizuj AD DS z Azure Active Directory (Azure AD).

4. Tworzenie konta magazynu

5. Utwórz udział plików w ramach konta magazynu, zgodnie z instrukcjami w tece Create an Azure file share (Tworzenie [udziału plików platformy Azure).](../storage/files/storage-how-to-create-file-share.md#create-file-share)

6. Dołącz do konta magazynu, aby AD DS zgodnie z instrukcjami w części pierwszej: włączanie uwierzytelniania AD DS [dla udziałów plików platformy Azure.](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)

7. Przypisz zsynchronizowaną grupę AD DS do usługi Azure AD i przypisz do konta magazynu rolę Współautor udziału SMB danych pliku magazynu.

8. Zainstaluj udział plików na dowolnym hoście sesji, zgodnie z instrukcjami w części [drugiej:](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)przypisywanie uprawnień na poziomie udziału do tożsamości .

9. Przyznaj uprawnienia NTFS do udziału plików AD DS grupy.

10. Skonfiguruj uprawnienia systemu plików NTFS dla kont użytkowników. Musisz mieć jednostkę operacyjną pochodzącą z jednostki AD DS, do której należą konta na maszynie wirtualnej.

Po przypisaniu tożsamości do magazynu postępuj zgodnie z instrukcjami [](#next-steps) w artykułach w Następne kroki, aby udzielić innych wymaganych uprawnień do tożsamości przypisanej do maszyn wirtualnych.

Należy również upewnić się, że maszyny wirtualne hosta sesji mają uprawnienia systemu plików New Technology File System (NTFS). Aby korzystać z tych uprawnień, musisz mieć kontener jednostek operacyjnych, który pochodzi z usługi Active Directory Domain Services (AD DS), a użytkownicy muszą być członkami tej jednostki operacyjnej.

## <a name="next-steps"></a>Następne kroki

Oto inne czynności, które należy wykonać po skonfigurowaniu udziału plików:

- Dowiedz się, jak skonfigurować Azure Active Directory Domain Services (AD DS) na stronie Create a profile container with Azure Files and AD DS (Tworzenie kontenera profilu za pomocą Azure Files [i AD DS).](create-file-share.md)
- Dowiedz się, jak skonfigurować Azure Files i Azure AD DS na stronie Create a profile container with Azure Files and Azure AD DS (Tworzenie kontenera profilu za pomocą Azure Files [i Azure AD DS).](create-profile-container-adds.md)
- Dowiedz się, jak skonfigurować dołączanie Azure NetApp Files MSIX na stronie Create a profile container with Azure NetApp Files and AD DS (Tworzenie kontenera profilu za [pomocą Azure NetApp Files i AD DS).](create-fslogix-profile-container.md)
- Dowiedz się, jak używać udziału plików opartego na maszynie wirtualnej w temacie Create a profile container for a host pool using a file share (Tworzenie kontenera profilu dla puli [hostów przy użyciu udziału plików).](create-host-pools-user-profile.md)

Po zakończeniu możesz znaleźć inne zasoby, które mogą okazać się przydatne:

- Zadaj naszemu społeczności pytania dotyczące tej funkcji na stronie [Windows Virtual Desktop TechCo w witrynie](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- Opinię na temat Windows Virtual Desktop możesz również Windows Virtual Desktop [centrum opinii.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
- [Słownik dołączania aplikacji MSIX](app-attach-glossary.md)
- [Dołączanie aplikacji MSIX — często zadawane pytania](app-attach-faq.md)
