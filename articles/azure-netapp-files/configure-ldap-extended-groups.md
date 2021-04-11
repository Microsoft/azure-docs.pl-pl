---
title: Konfiguracja dodaje katalog LDAP z rozszerzonymi grupami Azure NetApp Files dostęp do woluminu systemu plików NFS | Microsoft Docs
description: W tym artykule opisano zagadnienia i kroki umożliwiające włączenie protokołu LDAP z rozszerzonymi grupami podczas tworzenia woluminu NFS przy użyciu Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: 2031cbf07d700307ae1e11c516f9fc736bce5080
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499016"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>Konfiguracja dodaje protokół LDAP z rozszerzonymi grupami dla dostępu do woluminu NFS

Podczas [tworzenia woluminu NFS](azure-netapp-files-create-volumes.md)można włączyć funkcję LDAP z rozszerzonymi grupami (opcja **LDAP** ) dla woluminu. Ta funkcja umożliwia Active Directory użytkownikom protokołu LDAP i grupom rozszerzonym (do 1024 grup) dostęp do woluminu.  

W tym artykule opisano zagadnienia i kroki umożliwiające włączenie protokołu LDAP z grupami rozszerzonymi podczas tworzenia woluminu NFS.  

## <a name="considerations"></a>Zagadnienia do rozważenia

* *Nie* można włączyć protokołu LDAP over TLS, jeśli używasz Azure Active Directory Domain Services (AADDS).  

* W przypadku włączenia funkcji LDAP z rozszerzonymi grupami [woluminy Kerberos](configure-kerberos-encryption.md) z obsługą protokołu LDAP nie będą poprawnie wyświetlały własności plików dla użytkowników LDAP. Plik lub katalog utworzony przez użytkownika LDAP będzie domyślnie `root` jako właściciel, a nie rzeczywisty użytkownik LDAP. Jednak `root` konto może ręcznie zmienić własność pliku przy użyciu polecenia `chown <username> <filename>` . 

* Po utworzeniu woluminu nie można zmodyfikować ustawienia opcji LDAP (włączone lub wyłączone).  

* W poniższej tabeli opisano ustawienia czasu wygaśnięcia (TTL) dla pamięci podręcznej LDAP. Przed próbą uzyskania dostępu do pliku lub katalogu za pomocą klienta należy poczekać na odświeżenie pamięci podręcznej. W przeciwnym razie na kliencie zostanie wyświetlony komunikat Odmowa dostępu. 

    |     Błąd    |     Rozwiązanie    |
    |-|-|
    | Pamięć podręczna |  Domyślny limit czasu |
    | Lista członkostwa w grupie  | 24-godzinny czas wygaśnięcia  |
    | Grupy systemu UNIX  | 24-godzinny czas wygaśnięcia, 1 minuty — ujemny czas wygaśnięcia  |
    | Użytkownicy systemu UNIX  | 24-godzinny czas wygaśnięcia, 1 minuty — ujemny czas wygaśnięcia  |

    Pamięć podręczna ma określony przedział czasu o nazwie *Time to Live*. Po upływie limitu czasu wpisy są ustawiane tak, aby stare wpisy nie były pokutujące. *Ujemna wartość czasu wygaśnięcia* polega na tym, że wyszukiwanie, które zakończyło się niepowodzeniem, pomaga uniknąć problemów z wydajnością z powodu zapytań LDAP dla obiektów, które mogą nie istnieć ".        

## <a name="steps"></a>Kroki

1. Funkcja LDAP z rozszerzonymi grupami jest obecnie w wersji zapoznawczej. Przed rozpoczęciem korzystania z tej funkcji po raz pierwszy należy zarejestrować funkcję:  

    1. Zarejestruj funkcję:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. Sprawdź stan rejestracji funkcji: 

        > [!NOTE]
        > **RegistrationState** może być w stanie od `Registering` do 60 minut przed zmianą na `Registered` . Poczekaj na zakończenie stanu `Registered` przed kontynuowaniem.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    Możesz również użyć [poleceń interfejsu wiersza polecenia platformy Azure](/cli/azure/feature) `az feature register` , `az feature show` Aby zarejestrować funkcję i wyświetlić stan rejestracji. 

2. Woluminy LDAP wymagają konfiguracji Active Directory dla ustawień serwera LDAP. Postępuj zgodnie z instrukcjami w temacie [wymagania dotyczące Active Directory połączeń](create-active-directory-connections.md#requirements-for-active-directory-connections) i [Utwórz połączenie Active Directory](create-active-directory-connections.md#create-an-active-directory-connection) , aby skonfigurować Active Directory połączeń na Azure Portal.  

3. Upewnij się, że Active Directory serwer LDAP działa na Active Directory. Można to zrobić przez zainstalowanie i skonfigurowanie roli [usługi LDS Active Directory (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) na maszynie usługi AD.

4. Użytkownicy NFS muszą mieć określone atrybuty POSIX na serwerze LDAP. Postępuj zgodnie z [zarządzaniem atrybutami LDAP POSIX](create-volumes-dual-protocol.md#manage-ldap-posix-attributes) , aby ustawić wymagane atrybuty.  

5. Jeśli chcesz skonfigurować klienta systemu Linux zintegrowanego z protokołem LDAP, zobacz [Konfigurowanie klienta NFS dla Azure NetApp Files](configure-nfs-clients.md).

6.  Wykonaj kroki opisane w temacie [Tworzenie woluminu NFS dla Azure NetApp Files](azure-netapp-files-create-volumes.md) , aby utworzyć wolumin systemu plików NFS. Podczas procesu tworzenia woluminu na karcie **Protokół** Włącz opcję **LDAP** .   

    ![Zrzut ekranu przedstawiający stronę Tworzenie woluminu z opcją LDAP.](../media/azure-netapp-files/create-nfs-ldap.png)  

7. Opcjonalne — w celu uzyskania dostępu do woluminu systemu plików NFS z włączoną obsługą rozszerzonych grup można włączyć lokalnych użytkowników systemu NFS nieobecnych na serwerze Windows LDAP. W tym celu należy włączyć opcję **Zezwalaj lokalnym użytkownikom NFS przy użyciu protokołu LDAP** w następujący sposób:
    1. Kliknij przycisk **Active Directory połączenia**.  Na istniejącym połączeniu Active Directory kliknij menu kontekstowe (trzy kropki `…` ), a następnie wybierz pozycję **Edytuj**.  
    2. W wyświetlonym oknie **Edytowanie ustawień Active Directory** wybierz opcję **Zezwalaj lokalnym użytkownikom NFS z protokołem LDAP** .  

    ![Zrzut ekranu przedstawiający opcję Zezwalaj użytkownikom lokalnym NFS z protokołem LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>Następne kroki  

* [Tworzenie woluminu NFS dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Tworzenie połączeń Active Directory i zarządzanie nimi](create-active-directory-connections.md)
* [Rozwiązywanie problemów z woluminami LDAP](troubleshoot-ldap-volumes.md)
