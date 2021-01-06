---
title: Konfiguracja udziału plików MSIX aplikacji pulpitu wirtualnego systemu Windows — wersja zapoznawcza — Azure
description: Jak skonfigurować udział plików dla aplikacji MSIX Attach for Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 49a350b77958901aae5e54e82d856e4f3772702e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930790"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>Skonfiguruj udział plików do dołączenia do aplikacji MSIX (wersja zapoznawcza)

> [!IMPORTANT]
> Dołączenie do aplikacji MSIX jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wszystkie obrazy MSIX muszą być przechowywane w udziale sieciowym dostępnym dla użytkowników w puli hostów z uprawnieniami tylko do odczytu.

Dołączenie do aplikacji MSIX (wersja zapoznawcza) nie ma żadnych zależności od typu sieci szkieletowej magazynu, która jest używana przez udział plików. Zagadnienia dotyczące udziału w aplikacji MSIX są takie same jak w przypadku udziału FSLogix. Aby dowiedzieć się więcej o wymaganiach dotyczących magazynu, zobacz temat [Opcje magazynu dla kontenerów profilów FSLogix w systemie Windows Virtual Desktop](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Wymagania dotyczące wydajności

Limity rozmiaru obrazów dołączanych aplikacji MSIX są zależne od typu magazynu używanego do przechowywania plików VHD lub VHDx, a także ograniczeń rozmiaru plików VHD, VHSD lub CIM i systemu plików.

W poniższej tabeli przedstawiono przykład, ile zasobów jest pojedynczy obraz MSIX o pojemności 1 GB, który jest wymagany dla każdej maszyny wirtualnej:

| Zasób             | Wymagania |
|----------------------|--------------|
| Stałe operacje we/wy stanu    | 1 Liczba operacji we/wy       |
| Logowanie za rozruchem maszyny | 10 operacji we/wy      |
| Opóźnienie              | 400 MS       |

Wymagania mogą się znacznie różnić w zależności od tego, ile aplikacji MSIX są przechowywane w obrazie MSIX. W przypadku większych obrazów MSIX należy przydzielić większą przepustowość.

### <a name="storage-recommendations"></a>Zalecenia dotyczące magazynu

Platforma Azure oferuje wiele opcji magazynu, których można użyć do dołączenia do aplikacji MISX. Zalecamy używanie Azure Files lub Azure NetApp Files, ponieważ te opcje oferują najlepszą wartość między kosztami a narzutem związanym z zarządzaniem. W artykule [Opcje magazynu w przypadku kontenerów profilów FSLogix w programie Virtual Desktop systemu Windows](store-fslogix-profile.md) porównano różne zarządzane rozwiązania magazynu Azure w kontekście systemu Windows Virtual Desktop.

### <a name="optimize-msix-app-attach-performance"></a>Optymalizowanie wydajności dołączania aplikacji MSIX

Oto kilka innych rzeczy, które zalecamy, aby zoptymalizować wydajność dołączania aplikacji MSIX:

- Rozwiązanie magazynu używane do dołączenia do aplikacji MSIX powinno znajdować się w tej samej lokalizacji centrum danych co hosty sesji.
- Aby uniknąć wąskich gardeł wydajności, należy wykluczyć następujące pliki VHD, VHDX i CIM ze skanowania oprogramowania antywirusowego:
   
    - <MSIXAppAttachFileShare \> \* . VHD
    - <MSIXAppAttachFileShare \> \* . DYSKU
    - \\\\storageaccount.file.core.windows.net \\ \* \* . VHD
    - \\\\storageaccount.file.core.windows.net \\ \* \* . DYSKU
    - <MSIXAppAttachFileShare>. WSPÓLNYCH
    - \\\\storageaccount.file.core.windows.net \\ \* \* . WSPÓLNYCH

- Oddzielanie sieci szkieletowej magazynu dla aplikacji MSIX Dołącz z kontenerów profilów FSLogix.
- Wszystkie konta systemu maszyn wirtualnych i konta użytkowników muszą mieć uprawnienia tylko do odczytu w celu uzyskania dostępu do udziału plików.
- Wszystkie plany odzyskiwania po awarii dla pulpitu wirtualnego systemu Windows muszą obejmować replikację aplikacji MSIX Dołącz udział plików w ramach pomocniczej lokalizacji trybu failover. Aby dowiedzieć się więcej na temat odzyskiwania po awarii, zobacz [Konfigurowanie planu ciągłości biznesowej i odzyskiwania po awarii](disaster-recovery.md).

## <a name="how-to-set-up-the-file-share"></a>Jak skonfigurować udział plików

Proces instalacji udziału plików dołączania aplikacji MSIX jest w dużym stopniu taki sam jak [proces instalacji udziałów plików profilu FSLogix](create-host-pools-user-profile.md). Należy jednak przypisać użytkownikom różne uprawnienia. Dołączenie do aplikacji MSIX wymaga uprawnień tylko do odczytu w celu uzyskania dostępu do udziału plików.

Jeśli przechowujesz aplikacje MSIX w Azure Files, to w przypadku hostów sesji należy przypisać wszystkie maszyny wirtualne hosta sesji zarówno kontrola dostępu oparta na rolach (RBAC), jak i udział plików nowe uprawnienia systemu plików (NTFS) do udziału.

| Obiekt platformy Azure                      | Wymagana rola                                     | Funkcja roli                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Host sesji (obiekty komputera maszyny wirtualnej)| Współautor udziału SMB danych w pliku magazynu          | Odczyt i wykonywanie, Odczyt, wyświetlanie zawartości folderu  |
| Administratorzy w udziale plików              | Współautor udziału SMB danych w pliku magazynu z podwyższonym poziomem uprawnień | Pełna kontrola                                  |
| Użytkownicy w udziale plików               | Współautor udziału SMB danych w pliku magazynu          | Odczyt i wykonywanie, Odczyt, wyświetlanie zawartości folderu  |

Aby przypisać uprawnienia do maszyn wirtualnych hosta sesji dla konta magazynu i udziału plików:

1. Utwórz grupę zabezpieczeń Active Directory Domain Services (AD DS).

2. Dodaj konta komputerów dla wszystkich maszyn wirtualnych hosta sesji jako członków grupy.

3. Zsynchronizuj grupę AD DS z Azure Active Directory (Azure AD).

4. Tworzenie konta magazynu

5. Utwórz udział plików na koncie magazynu, postępując zgodnie z instrukcjami w temacie [Tworzenie udziału plików platformy Azure](../storage/files/storage-how-to-create-file-share.md#create-file-share).

6. Dołącz konto magazynu do AD DS, postępując zgodnie z instrukcjami w [części pierwszej: włącz AD DS uwierzytelnianie dla udziałów plików platformy Azure](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Przypisz zsynchronizowaną grupę AD DS do usługi Azure AD, a następnie przypisz do konta magazynu rolę współautor udostępniania danych pliku magazynu.

8. Zainstaluj udział plików na dowolnym hoście sesji, postępując zgodnie z instrukcjami w [części 2: przypisywanie uprawnień na poziomie udziału do tożsamości](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Przyznaj uprawnienia NTFS do udziału plików grupie AD DS.

10. Skonfiguruj uprawnienia systemu plików NTFS dla kont użytkowników. Wymagana jest jednostka operacyjna (OU) z AD DS, do której należą konta w maszynie wirtualnej.

Po przypisaniu tożsamości do magazynu postępuj zgodnie z instrukcjami w artykułach w [następnych krokach](#next-steps) , aby udzielić innym wymaganiom tożsamości przypisanej do maszyn wirtualnych.

Należy również upewnić się, że maszyny wirtualne hosta sesji mają nowe uprawnienia systemu plików (NTFS). Musisz mieć kontener jednostek operacyjnych pochodzący z Active Directory Domain Services (AD DS), a użytkownicy muszą należeć do tej jednostki operacyjnej, aby korzystać z tych uprawnień.

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono inne czynności, które należy wykonać po skonfigurowaniu udziału plików:

- Dowiedz się, jak skonfigurować Azure Active Directory Domain Services (AD DS) w [sekcji Tworzenie kontenera profilu przy użyciu Azure Files i AD DS](create-file-share.md).
- Dowiedz się, jak skonfigurować Azure Files i AD DS platformy Azure w [sekcji Tworzenie kontenera profilu za pomocą Azure Files i AD DS platformy Azure](create-profile-container-adds.md).
- Dowiedz się, jak skonfigurować Azure NetApp Files do dołączania aplikacji MSIX w ramach [tworzenia kontenera profilu z Azure NetApp Files i AD DS](create-fslogix-profile-container.md).
- Dowiedz się, jak używać udziału plików opartego na maszynie wirtualnej w [sekcji Tworzenie kontenera profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md).

Gdy skończysz, Oto kilka innych zasobów, które mogą być przydatne:

- Zapoznaj się z naszymi pytaniami dotyczącymi tej funkcji w [TechCommunity pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- Możesz również wystawić opinię na temat pulpitu wirtualnego systemu Windows w [centrum opinii pulpitu wirtualnego systemu Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
- [Dołączanie słownika aplikacji MSIX](app-attach-glossary.md)
- [Załączanie aplikacji MSIX — często zadawane pytania](app-attach-faq.md)
