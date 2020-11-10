---
title: Konfigurowanie szyfrowania Kerberos NFSv 4.1 dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób konfigurowania szyfrowania NFSv 4.1 Kerberos dla Azure NetApp Files i wpływu na wydajność.
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
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: 1ffbcc24a2ee386be1a8ce50b55375d5bd458df1
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410100"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Skonfiguruj szyfrowanie Kerberos NFSv 4.1 dla Azure NetApp Files

Azure NetApp Files obsługuje szyfrowanie klienta NFS w trybach protokołu Kerberos (krb5, krb5i i Krb5p) przy użyciu szyfrowania AES-256. W tym artykule opisano konfiguracje wymagane do użycia woluminu NFSv 4.1 z szyfrowaniem Kerberos.

## <a name="requirements"></a>Wymagania

Do szyfrowania klienta NFSv 4.1 mają zastosowanie następujące wymagania: 

* Active Directory Domain Services (AD DS) połączenie w celu ułatwienia biletów protokołu Kerberos 
* Tworzenie rekordów A/PTR systemu DNS dla adresów IP klienta i Azure NetApp Files systemu plików NFS
* Klient systemu Linux  
    Ten artykuł zawiera wskazówki dotyczące klientów RHEL i Ubuntu.  Inni klienci będą współpracować z podobnymi krokami konfiguracji. 
* Dostęp do serwera NTP  
    Można użyć jednego z najczęściej używanych kontrolerów domeny kontrolera domena usługi Active Directory (AD DC).

## <a name="create-an-nfs-kerberos-volume"></a>Tworzenie woluminu systemu plików NFS Kerberos

1.  Wykonaj kroki opisane w temacie [Tworzenie woluminu NFS dla Azure NetApp Files](azure-netapp-files-create-volumes.md) , aby utworzyć wolumin nfsv 4.1.   

    Na stronie Tworzenie woluminu Ustaw wersję systemu plików NFS na **nfsv 4.1** i ustaw opcję Kerberos na **włączone**.

    > [!IMPORTANT] 
    > Nie można zmodyfikować wyboru włączenia protokołu Kerberos po utworzeniu woluminu.

    ![Tworzenie woluminu Kerberos NFSv 4.1](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Wybierz pozycję **Eksportuj zasady** , aby dopasować żądany poziom dostępu i opcji zabezpieczeń (Kerberos 5, Kerberos 5i lub Kerberos 5p) dla woluminu.   

    Aby uzyskać wpływ na wydajność protokołu Kerberos, zobacz [wpływ na wydajność protokołu Kerberos w systemie nfsv 4.1](#kerberos_performance).  

    Możesz również zmodyfikować metody zabezpieczeń protokołu Kerberos dla woluminu, klikając pozycję Eksportuj zasady w okienku nawigacji Azure NetApp Files.

3.  Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć wolumin nfsv 4.1.

## <a name="configure-the-azure-portal"></a>Konfigurowanie witryny Azure portal 

1.  Postępuj zgodnie z instrukcjami w temacie [Create a Active Directory Connection](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection).  

    Kerberos wymaga utworzenia co najmniej jednego konta komputera w Active Directory. Informacje o koncie, które zapewniasz, są używane do tworzenia kont dla woluminów Kerberos *i* nfsv 4.1. Ten komputer jest tworzony automatycznie podczas tworzenia woluminu.

2.  W obszarze **Kerberos obszaru** wprowadź **nazwę serwera usług AD** i adres **IP centrum dystrybucji kluczy** .

    Serwer usług AD i adres IP centrum dystrybucji kluczy mogą być tym samym serwerem. Te informacje służą do tworzenia konta komputera nazw SPN używanego przez Azure NetApp Files. Po utworzeniu konta komputera Azure NetApp Files będzie używać rekordów serwera DNS do lokalizowania dodatkowych serwerów centrum dystrybucji kluczy zgodnie z wymaganiami. 

    ![Obszar Kerberos](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Kliknij przycisk **Dołącz** , aby zapisać konfigurację.

## <a name="configure-active-directory-connection"></a>Skonfiguruj połączenie Active Directory 

Konfiguracja programu NFSv 4.1 Kerberos tworzy dwa konta komputerów w Active Directory:
* Konto komputera dla udziałów SMB
* Konto komputera dla NFSv 4.1 — można zidentyfikować to konto w postaci prefiksu `NFS-` . 

Po utworzeniu pierwszego woluminu Kerberos z NFSv 4.1 Ustaw typ szyfrowania dla konta komputera za pomocą następującego polecenia programu PowerShell:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Konfigurowanie klienta NFS 

Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie klienta NFS dla Azure NetApp Files](configure-nfs-clients.md) , aby skonfigurować klienta systemu plików NFS.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Instalowanie woluminu Kerberos systemu plików NFS

1. Na stronie **woluminy** Wybierz wolumin systemu plików NFS, który chcesz zainstalować.

2. Aby wyświetlić instrukcje, wybierz pozycję **instrukcje instalacji** z woluminu.

    Na przykład: 

    ![Instrukcje dotyczące instalacji dla woluminów Kerberos](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Utwórz katalog (punkt instalacji) dla nowego woluminu.  

4. Dla konta komputera Ustaw domyślny typ szyfrowania AES 256:  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * To polecenie należy uruchomić tylko raz dla każdego konta komputera.
    * To polecenie można uruchomić z kontrolera domeny lub z komputera z zainstalowanym programem [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) . 
    * `$NFSCOMPUTERACCOUNT`Zmienna to konto komputera utworzone w Active Directory podczas wdrażania woluminu Kerberos. Jest to konto, które jest poprzedzone prefiksem `NFS-` . 
    * `$ANFSERVICEACCOUNT`Zmienna to nieuprzywilejowane konto użytkownika Active Directory z delegowanymi kontrolkami w jednostce organizacyjnej, w której utworzono konto komputera. 

5. Zainstaluj wolumin na hoście: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT`Zmienna to `host:/export` ścieżka znaleziona w instrukcjach instalacji.
    * `$ANFMOUNTPOINT`Zmienna jest folderem utworzonym przez użytkownika na hoście z systemem Linux.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Wpływ na wydajność protokołu Kerberos w systemie NFSv 4.1 

Ta sekcja ułatwia zrozumienie wpływu protokołu Kerberos na NFSv 4.1.

### <a name="available-security-options"></a>Dostępne opcje zabezpieczeń 

Dostępne są następujące opcje zabezpieczeń dla woluminów NFSv 4.1: 

* **s = sys** używa lokalnych identyfikatorów systemu UNIX i GIDs przy użyciu AUTH_SYS do uwierzytelniania operacji NFS.
* **sek = krb5** korzysta z protokołu Kerberos V5 zamiast lokalnych identyfikatorów systemu UNIX i GIDs do uwierzytelniania użytkowników.
* **s = krb5i** korzysta z protokołu Kerberos V5 do uwierzytelniania użytkowników i przeprowadza sprawdzanie integralności operacji NFS przy użyciu zabezpieczonych sum kontrolnych, aby zapobiec manipulowaniu danymi.
* **s = Krb5p** używa protokołu Kerberos V5 do uwierzytelniania użytkowników i sprawdzania integralności. Szyfruje ruch NFS, aby zapobiec wykrywaniem ruchu. Ta opcja jest najbezpieczniejszym ustawieniem, ale również obejmuje najwięcej kosztów.

### <a name="performance-vectors-tested"></a>Przetestowane wektory wydajności

W tej sekcji opisano pojedynczy wpływ na wydajność po stronie klienta różnych `sec=*` opcji.

* Przetestowano wpływ na wydajność na dwóch poziomach: niska współbieżność (niskie obciążenie) i wysoka współbieżność (górne limity operacji we/wy i przepływności).  
* Przetestowano trzy typy obciążeń:  
    * Niewielka Losowa operacja odczytu/zapisu (przy użyciu FIO)
    * Operacja odczytu/zapisu dużej operacji sekwencyjnej (przy użyciu FIO)
    * Duże obciążenie metadanych w postaci wygenerowanej przez aplikacje, takie jak Git

### <a name="expected-performance-impact"></a>Oczekiwany wpływ na wydajność 

Istnieją dwa obszary fokusu: lekkie obciążenie i górny limit. Poniższa lista zawiera opis ustawienia zabezpieczeń wpływ na wydajność według ustawień zabezpieczeń i scenariusza według scenariusza. Wszystkie porównania są wykonywane względem `sec=sys` parametru Security. Test został wykonany na pojedynczym woluminie przy użyciu jednego klienta. 

Wpływ na wydajność krb5:

* Niska współbieżność (r/w):
    * Opóźnienie sekwencyjne zwiększyło 0,3 ms.
    * Opóźnienie losowych operacji we/wy zwiększyło 0,2 ms.
    * Opóźnienie operacji we/wy metadanych wzrosło 0,2 ms.
* Wysoka współbieżność (r/w): 
    * Krb5 nie ma wpływu na maksymalną przepustowość sekwencyjną.
    * Maksymalna liczba losowych operacji we/wy zmniejszyła się o 30% w przypadku czystych operacji odczytu, które łączą cały wpływ na zero, gdy obciążenie przesunie się na czysty zapis. 
    * Maksymalna liczba obciążeń metadanych zmniejszyła się o 30%.

Wpływ na wydajność krb5i: 

* Niska współbieżność (r/w):
    * Opóźnienie sekwencyjne zwiększyło 0,5 ms.
    * Opóźnienie losowych operacji we/wy zwiększyło 0,2 ms.
    * Opóźnienie operacji we/wy metadanych wzrosło 0,2 ms.
* Wysoka współbieżność (r/w): 
    * Maksymalna przepływność sekwencyjna spadła o 70% całości niezależnie od mieszaniny obciążenia.
    * Maksymalna liczba losowych operacji we/wy zmniejszyła się o 50% w przypadku czysto czytanych obciążeń o całkowitym wpływie, które zmniejszają się do 25%, gdy obciążenie przesunie się na czysty zapis. 
    * Maksymalna liczba obciążeń metadanych zmniejszyła się o 30%.

Wpływ na wydajność Krb5p:

* Niska współbieżność (r/w):
    * Opóźnienie sekwencyjne zwiększyło 0,8 MS.
    * Opóźnienie losowych operacji we/wy zwiększyło 0,2 ms.
    * Opóźnienie operacji we/wy metadanych wzrosło 0,2 ms.
* Wysoka współbieżność (r/w): 
    * Maksymalna przepływność sekwencyjna spadła o 85% całości niezależnie od mieszaniny obciążenia. 
    * Maksymalna liczba losowych operacji we/wy zmniejszyła się o 65% w przypadku czysto czytanych obciążeń o całkowitym wpływie, które zmniejszają 43%, ponieważ obciążenie przesunie się na czysty zapis. 
    * Maksymalna liczba obciążeń metadanych zmniejszyła się o 30%.

## <a name="next-steps"></a>Następne kroki  

* [Często zadawane pytania dotyczące Azure NetApp Files](azure-netapp-files-faqs.md)
* [Tworzenie woluminu NFS dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Utwórz połączenie Active Directory](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [Konfigurowanie klienta sieciowego systemu plików dla usługi Azure NetApp Files](configure-nfs-clients.md) 
