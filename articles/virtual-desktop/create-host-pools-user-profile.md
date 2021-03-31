---
title: Udział kontenera profilu FSLogix pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować kontener profilu FSLogix dla puli hostów usług pulpitu wirtualnego systemu Windows przy użyciu udziału plików opartego na maszynie wirtualnej.
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 35b692033cc16f276b48bc6d973b27d994c1082a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88002586"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Tworzenie kontenera profilu dla puli hostów za pomocą udziału plików

Usługa pulpitu wirtualnego systemu Windows oferuje kontenery profilu FSLogix jako zalecane rozwiązanie profilu użytkownika. Nie zalecamy korzystania z rozwiązania profilu użytkownika (UPD), które będzie przestarzałe w przyszłych wersjach pulpitu wirtualnego systemu Windows.

W tym artykule opisano sposób konfigurowania udziału kontenera FSLogix profile dla puli hostów przy użyciu udziału plików opartego na maszynach wirtualnych. Zdecydowanie zalecamy używanie Azure Files zamiast udziałów plików. Więcej dokumentacji FSLogix można znaleźć w [witrynie FSLogix](https://docs.fslogix.com/).

>[!NOTE]
>Jeśli szukasz materiału do porównania o różnych opcjach magazynu kontenera profilu FSLogix na platformie Azure, zobacz [Opcje magazynu dla kontenerów profilów FSLogix](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Utwórz nową maszynę wirtualną, która będzie pełnić rolę udziału plików

Podczas tworzenia maszyny wirtualnej należy ją umieścić w tej samej sieci wirtualnej co maszyny wirtualne puli hostów lub w sieci wirtualnej, która ma łączność z maszynami wirtualnymi puli hostów. Maszynę wirtualną można utworzyć na wiele sposobów:

- [Tworzenie maszyny wirtualnej na podstawie obrazu z galerii platformy Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Tworzenie maszyny wirtualnej na podstawie obrazu niezarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Po utworzeniu maszyny wirtualnej Dołącz ją do domeny, wykonując następujące czynności:

1. [Połącz się z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) przy użyciu poświadczeń podanych podczas tworzenia maszyny wirtualnej.
2. Na maszynie wirtualnej Uruchom **Panel sterowania** , a następnie wybierz pozycję **system**.
3. Wybierz pozycję **Nazwa komputera**, wybierz pozycję **Zmień ustawienia**, a następnie wybierz pozycję **Zmień.**
4. Wybierz pozycję **domena** , a następnie wprowadź domenę Active Directory w sieci wirtualnej.
5. Uwierzytelnianie przy użyciu konta domeny, które ma uprawnienia do komputerów przyłączania do domeny.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Przygotuj maszynę wirtualną do działania jako udział plików dla profilów użytkowników

Poniżej znajdują się ogólne instrukcje dotyczące przygotowania maszyny wirtualnej do działania jako udział plików dla profilów użytkowników:

1. Dodaj pulpit wirtualny systemu Windows Active Directory użytkowników do [Active Directory grupy zabezpieczeń](/windows/security/identity-protection/access-control/active-directory-security-groups/). Ta grupa zabezpieczeń zostanie użyta do uwierzytelnienia użytkowników pulpitu wirtualnego systemu Windows na utworzoną maszynę wirtualną udziału plików.
2. [Nawiąż połączenie z maszyną wirtualną udziału plików](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Na maszynie wirtualnej udziału plików Utwórz folder na **dysku C** , który będzie używany jako udział profilu.
4. Kliknij prawym przyciskiem myszy nowy folder, wybierz pozycję **Właściwości**, wybierz pozycję **udostępnianie**, a następnie wybierz pozycję **Udostępnianie zaawansowane...**.
5. Wybierz pozycję **Udostępnij ten folder**, wybierz pozycję **uprawnienia...**, a następnie wybierz pozycję **Dodaj.**
6. Wyszukaj grupę zabezpieczeń, do której dodano użytkowników pulpitu wirtualnego systemu Windows, a następnie upewnij się, że grupa ma **pełną kontrolę**.
7. Po dodaniu grupy zabezpieczeń kliknij prawym przyciskiem myszy folder, wybierz polecenie **Właściwości**, wybierz pozycję **udostępnianie**, a następnie skopiuj **ścieżkę sieciową** do użycia w przyszłości.

Aby uzyskać więcej informacji o uprawnieniach, zobacz [dokumentację usługi FSLogix](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Konfigurowanie kontenera profilu FSLogix

Aby skonfigurować maszyny wirtualne przy użyciu oprogramowania FSLogix, wykonaj następujące czynności na każdym komputerze zarejestrowanym w puli hostów:

1. [Połącz się z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) przy użyciu poświadczeń podanych podczas tworzenia maszyny wirtualnej.
2. Uruchom przeglądarkę internetową i przejdź do [tego linku](https://go.microsoft.com/fwlink/?linkid=2084562) , aby pobrać agenta FSLogix.
3. Przejdź do wersji \\ \\ Win32 \\ lub \\ \\ x64 \\ w pliku zip, a następnie uruchom program **FSLogixAppsSetup** w celu zainstalowania agenta FSLogix.  Aby dowiedzieć się więcej na temat sposobu instalowania programu FSLogix, zobacz [pobieranie i Instalowanie FSLogix](/fslogix/install-ht/).
4. Przejdź do **pliku Program Files**  >  **FSLogix**  >  **Apps** , aby potwierdzić, że Agent jest zainstalowany.
5. Z menu Start Uruchom polecenie **regedit** jako administrator. Przejdź do **komputera \\ HKEY_LOCAL_MACHINE \\ oprogramowania \\ FSLogix**.
6. Utwórz klucz o nazwie **Profile**.
7. Utwórz następujące wartości dla klucza profile:

| Nazwa                | Typ               | Dane/wartość                        |
|---------------------|--------------------|-----------------------------------|
| Enabled (Włączony)             | DWORD              | 1                                 |
| VHDLocations        | Wartość ciągu wielociągowego | "Ścieżka sieciowa udziału plików"     |

>[!IMPORTANT]
>Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389 dla użytkowników w celu uzyskania dostępu do maszyn wirtualnych puli hostów. Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy użycie [dostępu just in Time do maszyny wirtualnej](../security-center/security-center-just-in-time.md).
