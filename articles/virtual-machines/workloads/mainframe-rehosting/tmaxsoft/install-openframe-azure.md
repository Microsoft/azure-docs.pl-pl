---
title: Zainstaluj program TmaxSoft OpenFrame na platformie Azure Virtual Machines
description: Dowiedz się, jak skonfigurować środowisko OpenFrame na platformie Azure odpowiednie do tworzenia, demonstracji, testowania lub obciążeń produkcyjnych.
services: virtual-machines
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 2ca5f0311102c27c1233098a18ff16701b99ea2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950794"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Zainstaluj program TmaxSoft OpenFrame na platformie Azure

Dowiedz się, jak skonfigurować środowisko OpenFrame na platformie Azure odpowiednie do tworzenia, demonstracji, testowania lub obciążeń produkcyjnych. Ten samouczek przeprowadzi Cię przez każdy krok.

OpenFrame zawiera wiele składników, które tworzą środowisko emulacji mainframe na platformie Azure. Na przykład OpenFrame Usługi online zastąpić oprogramowanie mainframe, takie jak IBM Customer Information Control System (CICS) i OpenFrame Batch, ze składnikiem TJES, zastępuje podsystem wpisów zadań programu IBM mainframe (JES).

OpenFrame współpracuje z dowolnymi relacyjnymi bazami danych, w tym Oracle Database, Microsoft SQL Server, IBM DB2 i MySQL. Ta instalacja programu OpenFrame używa relacyjnej bazy danych TmaxSoft Tibero. Zarówno OpenFrame, jak i Tibero działają w systemie operacyjnym Linux. Ten samouczek instaluje CentOS 7,3, chociaż można używać innych obsługiwanych dystrybucji systemu Linux. Serwer aplikacji OpenFrame oraz baza danych Tibero są instalowane na jednej maszynie wirtualnej.

Samouczek przeprowadzi Cię przez proces instalacji składników OpenFrame Suite. Niektóre muszą być instalowane osobno.

Główne składniki OpenFrame:

- Wymagane pakiety instalacyjne.
- Baza danych Tibero.
- Open Database Connectivity (ODBC) jest używany przez aplikacje w OpenFrame do komunikacji z bazą danych Tibero.
- OpenFrame Base, oprogramowanie pośredniczące, które zarządza całym systemem.
- OpenFrame Batch, rozwiązanie, które zastępuje systemy wsadowe komputera mainframe.
- TACF, moduł usługi, który kontroluje dostęp użytkowników do systemów i zasobów.
- Sortowanie, narzędzie do sortowania dla transakcji wsadowych.
- OFCOBOL, kompilator, który interpretuje programy COBOL komputera mainframe.
- OFASM, kompilator, który interpretuje programy asemblera programu mainframe.
- OpenFrame serwer typu C (OSC), rozwiązanie, które zastępuje oprogramowanie firmy mainframe i IBM CICS.
- Rozwiązanie Java Enterprise User Solution (JEUS), serwer aplikacji sieci Web, który jest certyfikowany dla środowiska Java Enterprise Edition 6.
- OFGW, składnik bramy OpenFrame, który udostępnia odbiornik 3270.
- OFManager, rozwiązanie, które udostępnia funkcje OpenFrame i zarządzania w środowisku sieci Web.

Inne wymagane składniki OpenFrame:

- OSI, rozwiązanie, które zastępuje oprogramowanie mainframe i kontroler domeny IMS.
- TJES, rozwiązanie, które zapewnia środowisko JES komputera mainframe.
- OFTSAM, rozwiązanie, które umożliwia używanie plików SAM (V) SAM w otwartym systemie.
- OFHiDB, rozwiązanie zastępujące bazę danych IMS firmy mainframe.
- OFPLI, kompilator, który interpretuje programy PL/I firmy mainframe.
- PROTRIEVE, rozwiązanie, które wykonuje program mainframe Language CA-Easytrieve.
- OFMiner, rozwiązanie, które analizuje elementy mainframe, a następnie migruje je do platformy Azure.

## <a name="architecture"></a>Architektura

Na poniższej ilustracji przedstawiono przegląd składników architektury OpenFrame 7,0 zainstalowanych w tym samouczku:

![Składniki OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Wymagania systemowe platformy Azure

W poniższej tabeli przedstawiono wymagania dotyczące instalacji na platformie Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Wymaganie</th><th>Opis</th></tr>
</thead>
<tbody>
<tr><td>Obsługiwane dystrybucje systemu Linux na platformie Azure
</td>
<td>
Linux x86 2,6 (32-bit, 64-bit)<br/>
Red Hat 7. x<br/>
CentOS 7. x<br/>
</td>
</tr>
<tr><td>Sprzęt
</td>
<td>Rdzenie: 2 (minimum)<br/>
Pamięć: 4 GB (minimum)<br/>
Przestrzeń wymiany: 1 GB (minimum)<br/>
Dysk twardy: 100 GB (minimum)<br/>
</td>
</tr>
<tr><td>Opcjonalne oprogramowanie dla użytkowników systemu Windows
</td>
<td>Instrukcje: używane w tym przewodniku do konfigurowania funkcji maszyny wirtualnej<br/>
WinSCP: popularnego klienta SFTP i klienta FTP, którego możesz użyć<br/>
Zaćmienie dla systemu Windows: platforma programistyczna obsługiwana przez TmaxSoft<br/>
(Microsoft Visual Studio nie jest w tej chwili obsługiwana)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Wymagania wstępne

Zaplanuj kilka dni, aby złożyć wszystkie wymagane oprogramowanie i zakończyć wszystkie procesy ręczne.

Przed rozpoczęciem wykonaj następujące czynności:

- Pobierz nośnik instalacyjny OpenFrame z TmaxSoft. Jeśli jesteś istniejącym klientem TmaxSoft, skontaktuj się z przedstawicielem usługi TmaxSoft w celu uzyskania kopii licencjonowanej. W przeciwnym razie Poproś o wersję próbną z [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Zażądaj dokumentacji OpenFrame, wysyłając wiadomość e-mail na adres <support@tmaxsoft.com> .

- Uzyskaj subskrypcję platformy Azure, jeśli jeszcze jej nie masz. Możesz również utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

- Opcjonalny. Skonfiguruj tunel VPN typu lokacja-lokacja lub serwera przesiadkowego, który ogranicza dostęp do maszyny wirtualnej platformy Azure do dozwolonych użytkowników w organizacji. Ten krok nie jest wymagany, ale jest to najlepsze rozwiązanie.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Konfigurowanie maszyny wirtualnej na platformie Azure dla OpenFrame i Tibero

Środowisko OpenFrame można skonfigurować przy użyciu różnych wzorców wdrożenia, ale Poniższa procedura przedstawia sposób wdrażania serwera aplikacji OpenFrame i bazy danych Tibero na jednej maszynie wirtualnej. W dużych środowiskach i dla obciążeń pokaźną najlepszym rozwiązaniem jest wdrożenie bazy danych oddzielnie na własnej maszynie wirtualnej w celu zapewnienia lepszej wydajności.

**Aby utworzyć maszynę wirtualną**

1. Przejdź do Azure Portal <https://portal.azure.com> i zaloguj się na swoim koncie.

2. Kliknij pozycję **Maszyny wirtualne**.

    ![Lista zasobów w Azure Portal](media/vm-01.png)

3. Kliknij pozycję **Dodaj**.

    ![Dodaj opcję w Azure Portal](media/vm-02.png)

4. Na prawo od **systemów operacyjnych** kliknij przycisk **więcej**.

     ![Więcej opcji w Azure Portal](media/vm-03.png)

5. Kliknij pozycję **CentOS-based 7,3** , aby dokładnie wykonać ten przewodnik lub wybrać inną obsługiwaną dystrybucję systemu Linux.

     ![Opcje systemu operacyjnego w Azure Portal](media/vm-04.png)

6. W obszarze Ustawienia **podstawowe** wprowadź wartość w polu **Nazwa**, **Nazwa użytkownika**, **Typ uwierzytelniania**, **subskrypcja** (płatność zgodnie z rzeczywistym użyciem to styl AWS płatności) i **Grupa zasobów** (Użyj istniejącej grupy lub Utwórz grupę TmaxSoft).

7. Po zakończeniu (łącznie z parą klucza publicznego/prywatnego dla **typu uwierzytelniania**) kliknij przycisk **Prześlij**.

> [!NOTE]
> Jeśli używasz klucza publicznego SSH dla **typu uwierzytelniania**, zapoznaj się z instrukcjami w następnej sekcji, aby wygenerować parę kluczy publiczny/prywatny, a następnie Wznów kroki opisane tutaj.

### <a name="generate-a-publicprivate-key-pair"></a>Generuj parę kluczy publiczny/prywatny

W przypadku korzystania z systemu operacyjnego Windows należy PuTTYgen, aby wygenerować parę kluczy publiczny/prywatny.

Klucz publiczny może być swobodnie współużytkowany, ale klucz prywatny powinien być przechowywany całkowicie i nigdy nie powinien być współużytkowany z inną stroną. Po wygenerowaniu kluczy należy wkleić **klucz publiczny SSH** do konfiguracji — w efekcie przekazanie go do maszyny wirtualnej z systemem Linux. Jest ona przechowywana w autoryzowanych \_ kluczach w \~ katalogu/.SSH katalogu macierzystego konta użytkownika. Maszyna wirtualna z systemem Linux jest w stanie rozpoznać i zweryfikować połączenie po podaniu skojarzonego **klucza prywatnego SSH** w kliencie SSH (w naszym przypadku).

W przypadku uzyskiwania dostępu do maszyny wirtualnej przez nowych użytkowników: 

- Każda nowa osoba generuje własne klucze publiczne/prywatne przy użyciu PuTTYgen.
- Osoby osobno przechowują własne klucze prywatne i wysyłają informacje o kluczu publicznym do administratora maszyny wirtualnej.
- Administrator wkleja zawartość klucza publicznego do \~ \_ pliku kluczy/.SSH/Authorized.
- Nowa osoba nawiązuje połączenie za pośrednictwem wyprodukowania.

**Aby wygenerować parę kluczy publiczny/prywatny**

1.  Pobierz PuTTYgen z <https://www.putty.org/> i zainstaluj go przy użyciu wszystkich ustawień domyślnych.

2.  Aby otworzyć PuTTYgen, Znajdź katalog instalacji w języku C: \\ Program Files \\ .

    ![Interfejs do wyprodukowania](media/puttygen-01.png)

3.  Kliknij pozycję **Generate** (Generuj).

    ![Zrzut ekranu przedstawiający okno dialogowe Generator kluczy, w którym jest wyróżniony przycisk Generuj.](media/puttygen-02.png)

4.  Po generacji Zapisz klucz publiczny i klucz prywatny. Wklej zawartość klucza publicznego z sekcji **klucz publiczny SSH** w okienku **Tworzenie \> podstawowych maszyn wirtualnych** (pokazane w krokach 6 i 7 w poprzedniej sekcji).

    ![Okno dialogowe Generator klucza](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Konfigurowanie funkcji maszyny wirtualnej

1. W Azure Portal w bloku **Wybierz rozmiar** wybierz odpowiednie ustawienia sprzętu komputera z systemem Linux. *Minimalne* wymagania dotyczące instalacji zarówno Tibero, jak i OpenFrame są 2 procesorami i 4 GB pamięci RAM, jak pokazano w tym przykładzie instalacji:

    ![Tworzenie maszyny wirtualnej — podstawy](media/create-vm-01.png)

2. Kliknij pozycję **3 ustawienia** i Użyj ustawień domyślnych, aby skonfigurować funkcje opcjonalne.
3. Przejrzyj szczegóły dotyczące płatności.

    ![Tworzenie maszyny wirtualnej — zakup](media/create-vm-02.png)

4. Prześlij wybrane opcje. Na platformie Azure rozpocznie się Wdrażanie maszyny wirtualnej. Ten proces zazwyczaj trwa kilka minut.

5. Po wdrożeniu maszyny wirtualnej jest wyświetlany jej pulpit nawigacyjny pokazujący wszystkie ustawienia, które zostały wybrane podczas konfiguracji. Zanotuj **publiczny adres IP**.

    ![Tmax na pulpicie nawigacyjnym platformy Azure](media/create-vm-03.png)

6. Otwórz program PuTTY.

7. W polu **Nazwa hosta** wpisz swoją nazwę użytkownika i publiczny adres IP, który został skopiowany. Na przykład **nazwa_użytkownika \@ przywołującym element publicip**.

    ![Zrzut ekranu przedstawiający okno dialogowe Konfiguracja i wyróżnienie pola Nazwa hosta (lub adres IP).](media/putty-01.png)

8. W polu **Kategoria** kliknij pozycję **połączenie \> SSH \> AUTH**. Podaj ścieżkę do pliku **klucza prywatnego** .

    ![Okno dialogowe Konfiguracja konfiguracji](media/putty-02.png)

9. Kliknij przycisk **Otwórz** , aby uruchomić okno pozostało. Jeśli się powiedzie, nastąpi połączenie z nową maszyną wirtualną CentOS działającą na platformie Azure.

10. Aby zalogować się jako użytkownik główny, wpisz **sudo bash**.

    ![Logowanie użytkownika root w oknie polecenia](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Skonfiguruj środowisko i pakiety

Teraz, gdy maszyna wirtualna została utworzona i użytkownik jest zalogowany, należy wykonać kilka kroków instalacji i zainstalować wymagane pakiety preinstalacji.

1. Zamapuj nazwę **ofdemo** na lokalny adres IP przy użyciu protokołu VI, aby edytować plik hosts ( `vi /etc/hosts` ). Przy założeniu, że nasz adres IP to 192.168.96.148 ofdemo, jest to przed zmianą:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Jest to po zmianie:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Tworzenie grup i użytkowników:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Zmień hasło dla użytkownika oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Aktualizowanie parametrów jądra w/etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Dynamiczne odświeżanie parametrów jądra bez ponownego uruchamiania:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Pobierz wymagane pakiety: Upewnij się, że serwer jest połączony z Internetem, pobierz następujące pakiety, a następnie zainstaluj je:

     - dos2unix
     - glibc
     - glibc. i686 glibc. x86 \_ 64
     - libaio
     - ncurses

          > [!NOTE]
          > Po zainstalowaniu pakietu ncurses Utwórz następujące linki symboliczne:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - zatoce
     - w zatoce — c + +
     - libaio-devel. x86 \_ 64
     - strace
     - ltrace
     - GDB

7. W przypadku instalacji środowiska Java RPM wykonaj następujące czynności:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Instalowanie bazy danych Tibero

Tibero udostępnia kilka kluczowych funkcji w środowisku OpenFrame na platformie Azure:

- Tibero jest używany jako wewnętrzny magazyn danych OpenFrame dla różnych funkcji systemu.
- Pliki VSAM, w tym KSDS, RRDS i ESDS, używają wewnętrznej bazy danych programu Tibero do przechowywania danych.
- Repozytorium danych TACF jest przechowywane w Tibero.
- Informacje o katalogu OpenFrame są przechowywane w Tibero.
- Baza danych Tibero może służyć jako zamiennik dla programu IBM DB2 do przechowywania danych aplikacji.

**Aby zainstalować Tibero**

1. Sprawdź, czy plik instalatora binarnego Tibero jest obecny i sprawdź numer wersji.
2. Skopiuj oprogramowanie Tibero na konto użytkownika Tibero (oframe). Na przykład:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Otwórz profil. bash \_ w VI ( `vi .bash_profile` ) i wklej następujący element:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Aby uruchomić profil bash, wpisz w wierszu polecenia:

    ```
    source .bash_profile
    ```

5. Wygeneruj plik TIP (plik konfiguracji dla Tibero), a następnie otwórz go w VI. Na przykład:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Zmodyfikuj \$ TB \_ : Home/Client/config/tbdsn. TBR i umieść 127.0.0.1 zamiast oflocalhost, jak pokazano

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Utwórz bazę danych. Wyświetlane są następujące dane wyjściowe:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Aby odzyskać Tibero, najpierw zamknij go przy użyciu `tbdown` polecenia. Na przykład:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Teraz Tibero rozruchu przy użyciu polecenia `tbboot` . Na przykład:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Aby utworzyć obszar tabel, uzyskaj dostęp do bazy danych przy użyciu narzędzia SYS User (sys/Tmax), a następnie Utwórz wymagany obszar tabel dla woluminu domyślnego i TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Teraz wpisz następujące polecenia SQL:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Tibero rozruchu i sprawdź, czy są uruchomione procesy Tibero:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Dane wyjściowe:

![Tibero dane wyjściowe](media/tibero-01.png)

## <a name="install-odbc"></a>Zainstaluj ODBC

Aplikacje w programie OpenFrame komunikują się z bazą danych Tibero przy użyciu interfejsu API ODBC zapewnianego przez projekt unixODBC Open Source.

Aby zainstalować ODBC:

1. Sprawdź, czy plik Instalatora unixODBC-2.3.4. tar. gz jest obecny, lub Użyj `wget unixODBC-2.3.4.tar.gz` polecenia. Na przykład:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Rozpakuj plik binarny. Na przykład:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Przejdź do katalogu unixODBC-2.3.4 i wygeneruj plik reguł programu make przy użyciu informacji o sprawdzaniu maszyn. Na przykład:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Domyślnie unixODBC jest zainstalowany w/usr local, więc `--prefix` przekazuje wartość w celu zmiany lokalizacji. Podobnie pliki konfiguracji są domyślnie instalowane w/etc, więc `--sysconfdir` przekazuje wartość żądanej lokalizacji.

4. Wykonaj plik reguł programu make: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Skopiuj plik wykonywalny w katalogu programu po kompilacji. Na przykład:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Użyj VI, aby edytować profil bash ( `vi ~/.bash_profile` ) i dodać następujące elementy:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Zastosuj ODBC. Edytuj odpowiednio następujące pliki. Na przykład:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Utwórz link symboliczny i sprawdź poprawność połączenia z bazą danych Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Wyświetlane są następujące dane wyjściowe:

![Dane wyjściowe ODBC przedstawiające połączenie z bazą danych SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Zainstaluj bazę OpenFrame Base

Podstawowy serwer aplikacji jest instalowany przed indywidualnymi usługami, których OpenFrame używa do zarządzania systemem na platformie Azure, w tym procesów serwera obsługi transakcji.

**Aby zainstalować bazę OpenFrame Base**

1. Upewnij się, że instalacja Tibero zakończyła się pomyślnie, a następnie sprawdź, czy \_ \_ jest dostępny następujący plik konfiguracyjny OpenFrame Base7 0 \_ Linux \_ x86 \_ 64. bin.

2. Zaktualizuj profil bash o następujące informacje dotyczące Tibero:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Wykonaj profil bash:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Upewnij się, że procesy Tibero są uruchomione. Na przykład:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Przed zainstalowaniem upewnij się, że Tibero został uruchomiony.

5. Wygeneruj licencję w witrynie [TechNet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) i umieść w odpowiednim folderze OpenFrame podstawowe, partię, TACF, licencje OSC:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Pobierz pliki binarne OpenFrame Base i Base. Properties:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Uruchom Instalatora przy użyciu pliku Base. Properties. Na przykład:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Po zakończeniu instalacji zostanie wyświetlony komunikat ukończono instalację.

8. Sprawdź strukturę katalogów podstawowych OpenFrame przy użyciu `ls -ltr` polecenia. Na przykład:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Uruchom bazę OpenFrame:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![dane wyjściowe polecenia tmboot](media/base-02.png)

10. Sprawdź, czy stan procesu jest gotowy przy użyciu polecenia tmadmin w si. RDY jest wyświetlany w kolumnie **stan** dla każdego z procesów:

     ![dane wyjściowe polecenia tmadmin](media/base-03.png)

11. Zamknij bazę OpenFrame:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Zainstaluj partię OpenFrame

Usługa Batch OpenFrame składa się z kilku składników, które symulują środowiska wsadowe komputera mainframe i służą do uruchamiania zadań wsadowych na platformie Azure.

**Aby zainstalować partię**

1. Upewnij się, że instalacja podstawowa zakończyła się pomyślnie, a następnie sprawdź, czy plik \_ \_ konfiguracyjny OpenFrame Batch7 0 \_ Fix2 \_ systemu MVS \_ Linux \_ x86 \_ 64. bin jest obecny:

2. W wierszu polecenia wpisz polecenie, `vi batch.properties` Aby edytować plik Batch. Properties przy użyciu VI.

3. Zmodyfikuj parametry w następujący sposób:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Aby uruchomić Instalatora wsadowego, wpisz w wierszu polecenia:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Po zakończeniu instalacji uruchom zainstalowane pakiety OpenFrame, wpisując `tmboot` w wierszu polecenia.

    ![tmboot dane wyjściowe](media/tmboot-01.png)

6. Wpisz `tmadmin` w wierszu polecenia, aby sprawdzić proces OpenFrame.

    ![Ekran administratora Tmax](media/tmadmin-01.png)

7. Wykonaj następujące polecenia:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Użyj `tmdown` polecenia, aby uruchomić i zamknąć zadanie wsadowe:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Zainstaluj TACF

TACF Manager to moduł usługi OpenFrame, który kontroluje dostęp użytkowników do systemów i zasobów przy użyciu zabezpieczeń RACF.

**Aby zainstalować TACF**

1. Sprawdź, czy plik \_ \_ konfiguracyjny OpenFrame Tacf7 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin i TACF. Properties są obecne.
2. Upewnij się, że instalacja wsadowa zakończyła się pomyślnie, a następnie użyj VI, aby otworzyć plik TACF. Properties ( `vi tacf.properties` ).
3. Modyfikuj parametry TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Po zakończeniu działania Instalatora TACF Zastosuj zmienne środowiskowe TACF. W wierszu polecenia wpisz polecenie:

     ```
     source \~/.bash\_profile
     ```

5. Uruchom Instalatora TACF. W wierszu polecenia wpisz polecenie:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Dane wyjściowe wyglądają następująco:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. W wierszu polecenia wpisz polecenie, `tmboot` Aby ponownie uruchomić OpenFrame. Dane wyjściowe wyglądają następująco:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Sprawdź, czy stan procesu jest gotowy `tmadmin` do użycia w `si` poleceniu. Na przykład:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     W kolumnie **stan** pojawia się RDY:

    ![RDY w kolumnie Stan](media/tmboot-02.png)

8. Wykonaj następujące polecenia:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Zamknij serwer przy użyciu `tmdown` polecenia. Dane wyjściowe wyglądają następująco:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Zasortuj

Sortowanie jest narzędziem używanym w transakcjach wsadowych do sortowania danych.

**Aby zainstalować sortowanie**

1. Upewnij się, że instalacja wsadowa zakończyła się pomyślnie, a następnie sprawdź, czy jest obecny plik Instalatora **\_ 2sp3-linux64-2123-opt. tar. gz** .

2. Uruchom Instalatora przy użyciu pliku właściwości. W wierszu polecenia wpisz polecenie:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Przenieś katalog prosorts do lokalizacji głównej. W wierszu polecenia wpisz polecenie:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Utwórz podkatalog licencji i skopiuj do niego plik licencji. Na przykład:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Otwórz plik bash. profile w VI ( `vi .bash_profile` ) i zaktualizuj go w następujący sposób:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Aby uruchomić profil bash, w wierszu polecenia wpisz: `. .bash_profile`

7. Utwórz plik konfiguracji. Na przykład:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Utwórz łącze symboliczne. Na przykład:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Sprawdź posortowaną instalację, wykonując `prosort -h` polecenie. Na przykład:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Zainstaluj OFCOBOL

OFCOBOL to kompilator OpenFrame, który interpretuje programy COBOL firmy mainframe. 

**Aby zainstalować OFCOBOL**

1. Upewnij się, że instalacja usługi Batch/online powiodła się, a następnie sprawdź, czy \_ \_ \_ istnieje plik Instalatora OpenFrame COBOL3 0 40 \_ Linux \_ x86 \_ 64. bin.

2. Aby uruchomić Instalatora OFCOBOL, w wierszu polecenia wpisz:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Przeczytaj umowę licencyjną i naciśnij klawisz ENTER, aby kontynuować.

4. Zaakceptuj umowę licencyjną. Po zakończeniu instalacji zostanie wyświetlona następująca wartość:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Otwórz profil bash w VI ( `vi .bash_profile` ) i sprawdź, czy został zaktualizowany przy użyciu zmiennych OFCOBOL.
6. Wykonaj profil bash. W wierszu polecenia wpisz polecenie:

     ```
      source ~/.bash_profile
     ```

7. Skopiuj licencję OFCOBOL do zainstalowanego folderu. Na przykład:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Przejdź do pliku konfiguracji OpenFrame tjclrun. conf i otwórz go w VI. Na przykład:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Oto sekcja SYSLIB przed zmianą:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Oto sekcja SYSLIB po zmianie:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Przejrzyj \_ \_ plik dziennika OpenFrame COBOL INSTALLLOG. log w VI i sprawdź, czy nie występują żadne błędy. Na przykład:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Użyj `ofcob --version` polecenia i sprawdź numer wersji, aby zweryfikować instalację. Na przykład:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Uruchom ponownie OpenFrame przy użyciu `tmdown/tmboot` polecenia.

## <a name="install-ofasm"></a>Zainstaluj OFASM

OFASM to kompilator OpenFrame, który interpretuje programy asemblera programu mainframe.

**Aby zainstalować OFASM**

1. Upewnij się, że instalacja usługi Batch/online powiodła się, a następnie sprawdź, czy \_ \_ \_ istnieje plik Instalatora OpenFrame ASM3 0 Linux \_ x86 \_ 64. bin.

2. Uruchom Instalatora. Na przykład:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Przeczytaj umowę licencyjną i naciśnij klawisz ENTER, aby kontynuować.
4. Zaakceptuj umowę licencyjną.
5. Sprawdź, czy profil bash został zaktualizowany przy użyciu zmiennych OFASM. Na przykład:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Otwórz plik konfiguracji OpenFrame tjclrun. conf w VI i edytuj go w następujący sposób:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Oto sekcja [SYSLIB] *przed* zmianą:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Oto sekcja [SYSLIB] *po* zmianie:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Otwórz plik OpenFrame \_ ASM \_ INSTALLLOG. log w VI i sprawdź, czy nie wystąpiły żadne błędy. Na przykład:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Uruchom ponownie program OpenFrame, wydając jedno z następujących poleceń:

     ```
     tmdown / tmboot
     ```

     —lub—

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Zainstaluj program OSC

OSC to środowisko OpenFrame podobne do programu IBM CICS, które obsługuje wieloprędkościowe transakcje OLTP i inne funkcje zarządzania.

**Aby zainstalować OSC**

1. Upewnij się, że instalacja podstawowa zakończyła się pomyślnie, a następnie sprawdź, czy \_ \_ \_ plik konfiguracyjny OpenFrame OSC7 0 Fix2 \_ Linux \_ x86 \_ 64. bin jest obecny.
2. Edytuj następujące parametry w pliku OSC. Properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Uruchom Instalatora przy użyciu pliku właściwości, jak pokazano poniżej:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Po zakończeniu zostanie wyświetlony komunikat "Instalacja zakończona".

4. Sprawdź, czy profil bash został zaktualizowany przy użyciu zmiennych OSC.
5. Przejrzyj plik OpenFrame \_ OSC7 \_ 0 \_ Fix2 \_ INSTALLLOG. log. Powinna to wyglądać następująco:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Użyj VI, aby otworzyć plik konfiguracyjny ofsys. Seq. Na przykład:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. W \# sekcji podstawowe i \# wsadowe Edytuj parametry jak pokazano.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Skopiuj plik licencji. Na przykład:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Aby uruchomić i zamknąć OSC, zainicjuj współdzieloną pamięć regionu CICS, wpisując `osctdlinit OSCOIVP1` w wierszu polecenia.

10. Uruchom `oscboot` , aby uruchomić komputer OSC. Dane wyjściowe wyglądają następująco:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Aby sprawdzić, czy proces jest gotowy, użyj `tmadmin` polecenia w si. Wszystkie procesy powinny wyświetlać RDY w kolumnie **stan** .

    ![Procesy wyświetlające RDY](media/tmadmin-02.png)

12. Zamknij program OSC przy użyciu `oscdown` polecenia.

## <a name="install-jeus"></a>Zainstaluj JEUS

JEUS (rozwiązanie użytkownika w języku Java Enterprise) zawiera warstwę prezentacji serwera aplikacji sieci Web OpenFrame.

Przed zainstalowaniem JEUS Zainstaluj pakiet Apache Ant, który udostępnia biblioteki i narzędzia wiersza polecenia, które są konieczne do zainstalowania JEUS.

**Aby zainstalować Apache Ant**

1. Pobierz plik binarny ANT przy użyciu `wget` polecenia. Na przykład:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Użyj `tar` Narzędzia, aby wyodrębnić plik binarny i przenieść go do odpowiedniej lokalizacji. Na przykład:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Aby uzyskać wydajność, Utwórz link symboliczny:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Otwórz profil bash w VI ( `vi .bash_profile` ) i zaktualizuj go przy użyciu następujących zmiennych:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Zastosuj zmodyfikowaną zmienną środowiskową. Na przykład:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Aby zainstalować JEUS**

1. Rozwiń Instalatora przy użyciu `tar` Narzędzia. Na przykład:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Utwórz folder **Jeus** ( `mkdir jeus7` ) i rozpakuj plik binarny.
3. Przejdź do katalogu **instalacyjnego** (lub użyj parametru JEUS dla własnego środowiska). Na przykład:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Wykonaj `ant clean-all` przed wykonaniem kompilacji. Dane wyjściowe wyglądają następująco:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Utwórz kopię zapasową pliku Domain-config-Template. Properties. Na przykład:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Otwórz plik Domain-config-Template. Properties w VI:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Zmień `jeus.password=jeusadmin nodename=Tmaxsoft` na `jeus.password=tmax1234 nodename=ofdemo`

8. Wykonaj `ant install` polecenie, aby skompilować JEUS.
9.  Zaktualizuj \_ plik bash profilu za pomocą zmiennych JEUS, jak pokazano:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Wykonaj profil bash. Na przykład:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Opcjonalne*. Utwórz alias do łatwego zamykania i rozruchu składników JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Aby sprawdzić instalację, uruchom serwer administratora domeny, jak pokazano poniżej:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Sprawdź poprawność logowania w sieci Web, używając składni:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Na przykład <http://192.168.92.133:9736/webadmin/login.> zostanie wyświetlony ekran logowania:
    
     ![Ekran logowania JEUS webadmin](media/jeus-01.png)

     > [!NOTE]
     > Jeśli występują problemy z zabezpieczeniami portów, otwórz port 9736 lub wyłącz zaporę ( `systemctl stop firewall` ).

14. Aby zmienić nazwę hosta dla serwer1, kliknij pozycję **Zablokuj & Edytuj**, a następnie kliknij pozycję **serwer1**. W oknie serwer Zmień nazwę hosta w następujący sposób:

    1.  Zmień **węzeł nodename** na **ofdemo**.
    2.  Kliknij przycisk **OK** po prawej stronie okna.
    3.  Kliknij przycisk **Zastosuj zmiany** w lewej dolnej części okna i w polu Opis, wprowadź *zmianę nazwy hosta*.

    ![Ekran JEUS webadmin](media/jeus-02.png)

15. Sprawdź, czy konfiguracja zakończyła się pomyślnie na ekranie potwierdzenia.

    ![ekran jeus_domain Server](media/jeus-03.png)

16. Uruchom proces serwera zarządzanego "serwer1" przy użyciu następującego polecenia:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Zainstaluj OFGW

OFGW to brama OpenFrame, która obsługuje komunikację między emulatorem terminalu 3270 i bazą OSI i zarządza sesjami między emulatorem i bramą terminala.

**Aby zainstalować OFGW**

1. Upewnij się, że JEUS został pomyślnie zainstalowany, a następnie sprawdź, \_ czy \_ \_ jest obecny plik Instalatora. bin OFGW7 0 1.
2. Uruchom Instalatora. Na przykład:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Aby uzyskać odpowiednie informacje, użyj następujących lokalizacji:
     -   Katalog macierzysty JEUS
     -   Nazwa domeny JEUS
     -   Nazwa serwera JEUS
     -   Sterownik Tibero
     -   IDENTYFIKATOR węzła Tmax ofdemo

4. Zaakceptuj pozostałe wartości domyślne, a następnie naciśnij klawisz ENTER, aby wyjść z Instalatora.

5. Sprawdź, czy adres URL OFGW działa zgodnie z oczekiwaniami:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Zostanie wyświetlony następujący ekran:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Zainstaluj OFManager

OFManager udostępnia funkcje operacji i zarządzania dla OpenFrame w środowisku sieci Web.

**Aby zainstalować OFManager**

1. Sprawdź, czy \_ plik Instalatora OFManager7 Generic. bin jest obecny.
2. Uruchom Instalatora. Na przykład:

     ```
     OFManager7_Generic.bin
     ```

3.  Naciśnij klawisz ENTER, aby kontynuować, a następnie zaakceptuj umowę licencyjną.
4.  Wybierz folder instalacji.
5.  Zaakceptuj wartości domyślne.
6.  Wybierz Tibero jako bazę danych.
7.  Naciśnij klawisz ENTER, aby wyjść z Instalatora.
8.  Sprawdź, czy adres URL OFManager działa zgodnie z oczekiwaniami:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Zostanie wyświetlony ekran startowy:

![Ekran logowania Menedżera OpenFrame Tmax](media/ofmanager-01.png)

Kończy instalację składników OpenFrame.

## <a name="next-steps"></a>Następne kroki

Jeśli rozważasz migrację komputerów mainframe, nasz ekosystem partnerski jest dostępny, aby Ci pomóc. Aby uzyskać szczegółowe wskazówki dotyczące wybierania rozwiązania partnerskiego, zapoznaj się z informacjami na [platformie modernizacja](https://datamigration.microsoft.com/)organizacji.

-   [Rozpoczynanie pracy z platformą Azure](../../../../index.yml)
-   [Host Integration Server (jego) Dokumentacja](/host-integration-server/)
-   [Przewodnik po podnoszenia i przesunięć wirtualnych centrów danych platformy Azure](/archive/blogs/azurecat/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide)
