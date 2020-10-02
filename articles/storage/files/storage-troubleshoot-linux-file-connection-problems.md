---
title: Rozwiązywanie problemów z Azure Files w systemie Linux | Microsoft Docs
description: Rozwiązywanie problemów z Azure Files w systemie Linux. Zobacz typowe problemy związane z Azure Files podczas łączenia się z klientami z systemem Linux i zobacz możliwe rozwiązania.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 40fb5a1623175445065f0546403661a1f6eb399f
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629441"
---
# <a name="troubleshoot-azure-files-problems-in-linux-smb"></a>Rozwiązywanie problemów z Azure Files w systemie Linux (SMB)

W tym artykule wymieniono typowe problemy dotyczące Azure Files podczas łączenia się z klientami z systemem Linux. Zapewnia również możliwe przyczyny i rozwiązania tych problemów. 

Oprócz kroków opisanych w tym artykule można użyć programu [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux) , aby upewnić się, że klient systemu Linux ma odpowiednie wymagania wstępne. AzFileDiagnostics automatyzuje wykrywanie większości objawów wymienionych w tym artykule. Pomaga skonfigurować środowisko w celu uzyskania optymalnej wydajności. Te informacje można również znaleźć w oknie [Rozwiązywanie problemów z udziałami Azure Files](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). Narzędzie do rozwiązywania problemów zawiera kroki ułatwiające problemy z łączeniem, mapowaniem i instalowaniem udziałów Azure Files.

> [!IMPORTANT]
> Zawartość tego artykułu dotyczy tylko udziałów SMB. Aby uzyskać szczegółowe informacje o udziałach NFS, zobacz [Rozwiązywanie problemów z udziałami plików NFS systemu Azure](storage-troubleshooting-files-nfs.md).

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nie można nawiązać połączenia z udziałem plików platformy Azure lub instalować go

### <a name="cause"></a>Przyczyna

Typowe przyczyny tego problemu:

- Używasz niezgodnego klienta dystrybucji systemu Linux. Zalecamy używanie następujących dystrybucji systemu Linux do nawiązywania połączenia z udziałem plików platformy Azure:

|   | SMB 2.1 <br>(Instalacja na maszynach wirtualnych w tym samym regionie platformy Azure) | SMB 3.0 <br>(Instalacje z poziomu lokalnego i obejmującego wiele regionów) |
| --- | :---: | :---: |
| **Serwer Ubuntu** | 14.04 + | 16.04 + |
| **RHEL** | 7 + | 7.5 + |
| **CentOS** | 7 + |  7.5 + |
| **Debian** | 8 + |   |
| **openSUSE** | 13.2 + | 42.3 + |
| **SUSE Linux Enterprise Server** | 12 | 12 SP3 + |

- Narzędzia CIFS (CIFS-Utilities) nie są zainstalowane na kliencie.
- Minimalna wersja protokołu SMB/CIFS 2,1 nie jest zainstalowana na kliencie.
- Szyfrowanie SMB 3,0 nie jest obsługiwane przez klienta. Powyższa tabela zawiera listę dystrybucji systemu Linux, które obsługują instalowanie z lokalnego i międzyregionowego przy użyciu szyfrowania. Inne dystrybucje wymagają jądra 4.11 i nowszych wersji.
- Próbujesz nawiązać połączenie z kontem magazynu za pośrednictwem portu TCP 445, co nie jest obsługiwane.
- Próbujesz nawiązać połączenie z udziałem plików platformy Azure z maszyny wirtualnej platformy Azure, a maszyna wirtualna nie znajduje się w tym samym regionie co konto magazynu.
- Jeśli na koncie magazynu jest włączone ustawienie [wymagany bezpieczny transfer]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) , Azure Files będzie zezwalać tylko na połączenia korzystające z protokołu SMB 3,0 z szyfrowaniem.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj [Narzędzia do rozwiązywania problemów w celu Azure Files instalowania błędów w systemie Linux](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux). To narzędzie:

* Pomaga sprawdzić poprawność działającego środowiska klienta.
* Wykrywa niezgodną konfigurację klienta, która spowodowałaby niepowodzenie dostępu dla Azure Files.
* Zapewnia wskazówki dotyczące samoobsługowego rozwiązywania problemów.
* Zbiera dane śledzenia diagnostyki.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Błąd instalacji (13): odmowa uprawnień" podczas instalowania udziału plików platformy Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Przyczyna 1: nieszyfrowany kanał komunikacyjny

Ze względów bezpieczeństwa połączenia z udziałami plików platformy Azure są blokowane, jeśli kanał komunikacyjny nie jest szyfrowany i jeśli próba połączenia nie pochodzi z tego samego centrum danych, w którym znajdują się udziały plików platformy Azure. Nieszyfrowane połączenia w tym samym centrum danych też mogą być blokowane, jeśli ustawienie [Wymagany bezpieczny transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest włączone na koncie magazynu. Szyfrowany kanał komunikacyjny jest udostępniany tylko wtedy, gdy system operacyjny klienta użytkownika obsługuje szyfrowanie protokołu SMB.

Aby dowiedzieć się więcej, zobacz [Wymagania wstępne dotyczące instalowania udziału plików platformy Azure za pomocą systemu Linux i pakietu cifs utils](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

1. Nawiąż połączenie z klientem obsługującym szyfrowanie SMB lub Połącz się z maszyną wirtualną w tym samym centrum danych co konto usługi Azure Storage, które jest używane na potrzeby udziału plików platformy Azure.
2. Upewnij się, że ustawienie [wymagany bezpieczny transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest wyłączone na koncie magazynu, jeśli klient nie obsługuje szyfrowania SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 2: na koncie magazynu są włączone reguły sieci wirtualnej lub zapory 

Jeśli reguły sieci wirtualnej i zapory są skonfigurowane na koncie magazynu, dla ruchu sieciowego będzie następować odmowa dostępu, chyba że dostęp będzie dozwolony dla adresu IP klienta lub sieci wirtualnej.

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>Przekroczono limit przydziału dysku "[odmowa uprawnień] podczas próby otwarcia pliku

W systemie Linux pojawia się komunikat o błędzie podobny do następującego:

**\<filename> [odmowa uprawnień] Przekroczono limit przydziału dysku**

### <a name="cause"></a>Przyczyna

Osiągnięto górny limit współbieżnych otwartych dojść, które są dozwolone dla pliku lub katalogu.

Istnieje limit przydziału otwartych dojść do 2 000 dla jednego pliku lub katalogu. Jeśli masz otwarte dojścia do 2 000, zostanie wyświetlony komunikat o błędzie informujący o osiągnięciu limitu przydziału.

### <a name="solution"></a>Rozwiązanie

Zmniejsz liczbę równoczesnych dojść otwartych, zamykając niektóre uchwyty, a następnie spróbuj ponownie wykonać operację.

Aby wyświetlić otwarte uchwyty dla udziału plików, katalogu lub pliku, należy użyć polecenia cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) programu PowerShell.  

Aby zamknąć otwarte uchwyty dla udziału plików, katalogu lub pliku, należy użyć polecenia cmdlet programu PowerShell [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) .

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w programie AZ PowerShell module w wersji 2,4 lub nowszej. Aby zainstalować najnowszy moduł AZ PowerShell module, zobacz [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Kopiowanie plików do i z Azure Files w systemie Linux

- Jeśli nie masz wymaganego minimalnego rozmiaru operacji we/wy, zalecamy użycie 1 MiB jako rozmiaru we/wy w celu uzyskania optymalnej wydajności.
- Użyj odpowiedniej metody copy:
    - Użyj [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) do dowolnego transferu między dwoma udziałami plików.
    - Użycie opcji CP lub DD z równoległością może zwiększyć szybkość kopiowania, a liczba wątków zależy od przypadku użycia i obciążenia. W poniższych przykładach użyto sześciu: 
    - przykład CP (CP użyje domyślnego rozmiaru bloku systemu plików jako rozmiaru fragmentu): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &` .
    - DD przykład (to polecenie jawnie ustawia rozmiar fragmentu na 1 MiB): `find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Narzędzia firm trzecich typu open source, takie jak:
        - [GNU Parallel](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) — sortuje pliki i pakuje je na partycje.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) — używa narzędzi Fpart i Copy do duplikowania wielu wystąpień w celu migrowania danych z src_dir do dst_url.
        - [Wiele](https://github.com/pkolano/mutil) wielowątkowych CP i md5sum opartych na GNU Coreutils.
- Ustawienie rozmiaru pliku z wyprzedzeniem, zamiast każdorazowego zapisu rozszerzającego zapis, pomaga zwiększyć szybkość kopiowania w scenariuszach, w których rozmiar pliku jest znany. Jeśli należy unikać rozszerzania zapisów, można ustawić docelowy rozmiar pliku za pomocą `truncate - size <size><file>` polecenia. Następnie `dd if=<source> of=<target> bs=1M conv=notrunc` polecenie skopiuje plik źródłowy bez konieczności wielokrotnego aktualizowania rozmiaru pliku docelowego. Na przykład można ustawić rozmiar pliku docelowego dla każdego pliku, który ma zostać skopiowany (Załóżmy, że udział jest zainstalowany w ramach/mnt/Share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - a następnie skopiuj pliki bez rozszerzania zapisów równolegle: `$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Błąd instalacji (115): operacja jest teraz w toku" podczas instalowania Azure Files przy użyciu protokołu SMB 3,0

### <a name="cause"></a>Przyczyna

Niektóre dystrybucje systemu Linux nie obsługują jeszcze funkcji szyfrowania w protokole SMB 3.0. Użytkownicy mogą dostawać komunikat o błędzie „115” w przypadku próby instalacji usługi Azure Files przy użyciu protokołu SMB 3.0 z powodu braku funkcji. Protokół SMB 3.0 z pełnym szyfrowaniem jest obsługiwany tylko wtedy, gdy używasz systemu Ubuntu 16.04 lub nowszego.

### <a name="solution"></a>Rozwiązanie

Funkcja szyfrowania dla protokołu SMB 3.0 dla systemu Linux została wprowadzona w jądrze 4.11. Ta funkcja umożliwia instalowanie udziału plików platformy Azure ze środowiska lokalnego lub z innego regionu platformy Azure. Niektóre dystrybucje systemu Linux mogą mieć przewoźnych zmian z jądra 4,11 do starszych wersji jądra systemu Linux, które utrzymują. Aby pomóc w ustaleniu, czy wersja systemu Linux obsługuje protokół SMB 3,0 z szyfrowaniem, zapoznaj się z tematem [używanie Azure Files z systemem Linux](storage-how-to-use-files-linux.md). 

Jeśli Twój klient SMB w systemie Linux nie obsługuje szyfrowania, zainstaluj usługę Azure Files przy użyciu protokołu SMB 2.1 z maszyny wirtualnej platformy Azure z systemem Linux, która znajduje się w tym samym centrum danych co udział plików. Upewnij się, że ustawienie [Wymagany bezpieczny transfer]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest wyłączone na koncie magazynu. 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Błąd "Brak dostępu" podczas próby uzyskania dostępu do udziału plików platformy Azure lub usunięcie go  
W przypadku próby uzyskania dostępu do udziału plików platformy Azure w portalu lub usunięcia go z niego może zostać wyświetlony następujący błąd:

Brak dostępu  
Kod błędu: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 1: na koncie magazynu są włączone reguły sieci wirtualnej lub zapory

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Przyczyna 2: Twoje konto użytkownika nie ma dostępu do konta magazynu

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przejdź do konta magazynu, na którym znajduje się udział plików platformy Azure, kliknij pozycję **Kontrola dostępu (IAM)** i sprawdź, czy konto użytkownika ma dostęp do konta magazynu. Aby dowiedzieć się więcej, zobacz [jak zabezpieczyć konto magazynu za pomocą Access Control opartego na rolach (RBAC)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Nie można usunąć pliku lub katalogu z udziału plików platformy Azure

### <a name="cause"></a>Przyczyna
Ten problem występuje zwykle, gdy plik lub katalog ma otwarte dojście. 

### <a name="solution"></a>Rozwiązanie

Jeśli klienci SMB zamknęli wszystkie otwarte dojścia, a problem nadal wystąpi, wykonaj następujące czynności:

- Użyj polecenia cmdlet programu PowerShell [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) , aby wyświetlić otwarte dojścia.

- Użyj polecenia cmdlet [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) programu PowerShell, aby zamknąć otwarte dojścia. 

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w programie AZ PowerShell module w wersji 2,4 lub nowszej. Aby zainstalować najnowszy moduł AZ PowerShell module, zobacz [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Niska wydajność w udziale usługi Azure File zainstalowanym na maszynie wirtualnej z systemem Linux

### <a name="cause-1-caching"></a>Przyczyna 1: buforowanie

Jedną z możliwych przyczyn niskiej wydajności jest wyłączenie buforowania. Buforowanie może być przydatne, Jeśli uzyskujesz dostęp do pliku wielokrotnie, w przeciwnym razie może to być obciążenie. Sprawdź, czy używasz pamięci podręcznej przed jej wyłączeniem.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

Aby sprawdzić, czy buforowanie jest wyłączone, poszukaj pozycji **cache =** entry.

**Pamięć podręczna = brak** wskazuje, że buforowanie jest wyłączone. Zainstaluj ponownie udział przy użyciu domyślnego polecenia mount lub jawnie dodając **pamięć podręczną = Strict** do polecenia instalacji, aby upewnić się, że jest włączony tryb buforowania domyślny lub "Strict".

W niektórych scenariuszach opcja instalacji **serverino** może spowodować, że polecenie **ls** zostanie wykonane względem każdego wpisu katalogu. To zachowanie powoduje spadek wydajności podczas tworzenia listy dużych katalogów. Opcje instalacji można sprawdzić we wpisie **/etc/fstab** :

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Możesz również sprawdzić, czy poprawne opcje są używane, uruchamiając polecenie  **sudo Mount | grep CIFS** i sprawdzając jego dane wyjściowe. Poniżej przedstawiono przykładowe dane wyjściowe:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Jeśli **pamięć podręczna = Strict** lub **serverino** nie jest obecna, Odinstaluj i Zainstaluj Azure Files ponownie, uruchamiając polecenie instalacji z [dokumentacji](../storage-how-to-use-files-linux.md). Następnie ponownie sprawdź, czy wpis **/etc/fstab** ma poprawne opcje.

### <a name="cause-2-throttling"></a>Przyczyna 2: ograniczanie przepustowości

Istnieje możliwość ograniczenia przepustowości, a Twoje żądania są wysyłane do kolejki. Można to sprawdzić, wykorzystując [metryki usługi Azure Storage w Azure monitor](../common/storage-metrics-in-azure-monitor.md).

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Upewnij się, że aplikacja znajduje się w obszarze celu [skalowania Azure Files](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Sygnatury czasowe zostały utracone w kopiowaniu plików z systemu Windows do Linux

W przypadku platform Linux/UNIX polecenie **CP-p** kończy się niepowodzeniem, jeśli inny użytkownik ma plik 1 i plik 2.

### <a name="cause"></a>Przyczyna

Flaga Force **f** w COPYFILE powoduje wykonanie **wiersza CP-p-f** w systemie UNIX. To polecenie nie może również zachować sygnatury czasowej nieposiadanego pliku.

### <a name="workaround"></a>Obejście

Użyj użytkownika konta magazynu w celu skopiowania plików:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nie można uzyskać dostępu do " &lt; Path &gt; ": błąd wejścia/wyjścia

Podczas próby wyświetlenia listy plików w udziale plików platformy Azure przy użyciu polecenia ls polecenie zawiesza się podczas tworzenia listy plików. Zostanie wyświetlony następujący błąd:

**ls: nie można uzyskać dostępu do " &lt; Path &gt; ": błąd wejścia/wyjścia**


### <a name="solution"></a>Rozwiązanie
Uaktualnij jądro systemu Linux do następujących wersji, które mają rozwiązanie tego problemu:

- 4.4.87 i nowsze
- 4.9.48 i nowsze
- 4.12.11 i nowsze
- Wszystkie wersje, które są większe niż lub równe 4,13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nie można utworzyć linków symbolicznych — ln: nie można utworzyć linku symbolicznego ": operacja nie jest obsługiwana

### <a name="cause"></a>Przyczyna
Domyślnie instalowanie udziałów plików platformy Azure w systemie Linux przy użyciu protokołu CIFS nie powoduje włączenia obsługi linków symbolicznych (linków symbolicznych). Zobaczysz błąd podobny do tego:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Rozwiązanie
Klient z systemem Linux CIFS nie obsługuje tworzenia linków symbolicznych w stylu systemu Windows za pośrednictwem protokołu SMB 2 lub 3. Obecnie klient systemu Linux obsługuje inny styl linków symbolicznych o nazwie [Minshall + francuski linków symbolicznych](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) dla operacji tworzenia i obserwowania. Klienci, którzy potrzebują linków symbolicznych, mogą korzystać z opcji instalacji "mfsymlinks". Zalecamy "mfsymlinks", ponieważ jest to również format używany przez komputery Mac.

Aby użyć linków symbolicznych, Dodaj następujący na końcu polecenia instalacji CIFS:

```
,mfsymlinks
```

Dlatego polecenie wygląda następująco:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Następnie można utworzyć linków symbolicznych zgodnie z sugestią w witrynie [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Błąd instalacji (112): host nie działa" ze względu na limit czasu ponownego połączenia

Błąd instalacji „112” występuje na kliencie z systemem Linux, gdy klient był bezczynny przez długi czas. Po dłuższym czasie bezczynności klient rozłączy się i upłynie limit czasu połączenia.  

### <a name="cause"></a>Przyczyna

Połączenie może być bezczynne z następujących przyczyn:

-   Błędy komunikacji sieciowej, które uniemożliwiają ponowne ustanowienie połączenia TCP z serwerem, gdy jest używana domyślna opcja „miękkiej” instalacji
-   Najnowsze poprawki ponownego nawiązywania połączenia, które nie są obecne w starszych jądrach

### <a name="solution"></a>Rozwiązanie

Ten problem z ponownym nawiązywaniem połączenia w jądrze systemu Linux jest już poprawiony jako część następujących zmian:

- [Poprawka ponownego nawiązywania połączenia w celu nie odraczania ponownego łączenia sesji protokołu smb3 długo po ponownym połączeniu gniazda](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Wywoływanie usługi echo natychmiast po ponownym połączeniu gniazda](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Naprawienie potencjalnego uszkodzenia pamięci podczas ponownego nawiązywania połączenia](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: naprawianie możliwego podwójnego blokowania obiektu mutex podczas ponownego nawiązywania połączenia (dla jądra v 4.9 i nowszego)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Te zmiany mogą jednak nie być jeszcze przeniesione do wszystkich dystrybucji systemu Linux. Jeśli używasz popularnej dystrybucji systemu Linux, możesz zaewidencjonować [Azure Files użycia z systemem Linux](storage-how-to-use-files-linux.md) , aby zobaczyć, która wersja dystrybucji ma niezbędne zmiany jądra.

### <a name="workaround"></a>Obejście

Ten problem można obejść, określając twardą instalację. Twarda instalacja wymusza na kliencie oczekiwanie, aż połączenie zostanie nawiązane lub zostanie jawnie przerwane. Można z niego korzystać, aby zapobiegać błędom powodowanym przekroczeniem limitów czasu sieci. To obejście może jednak doprowadzić do niekończących się oczekiwań. Przygotuj się na zatrzymywanie połączeń zgodnie z wymaganiami.

Jeśli nie możesz wykonać uaktualnienia do najnowszych wersji jądra, możesz obejść ten problem, zachowując w udziale plików platformy Azure plik, do którego będziesz zapisywać co 30 sekund lub mniej. Musi to być operacja zapisu, taka jak ponowne zapisanie daty utworzenia lub modyfikacji pliku. W przeciwnym razie możesz otrzymać wyniki z pamięci podręcznej, a Twoja operacja może nie wyzwolić ponownego nawiązywania połączenia.

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>"CIFS VFS: Error-22 przy IOCTL do uzyskiwania listy interfejsów" podczas instalowania udziału plików platformy Azure przy użyciu protokołu SMB 3,0

### <a name="cause"></a>Przyczyna
Ten błąd jest rejestrowany, ponieważ Azure Files [obecnie nie obsługuje wielokanałowości SMB](https://docs.microsoft.com/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

### <a name="solution"></a>Rozwiązanie
Ten błąd może zostać zignorowany.


### <a name="unable-to-access-folders-or-files-which-name-has-a-space-or-a-dot-at-the-end"></a>Nie można uzyskać dostępu do folderów lub plików, których nazwa zawiera spację lub kropkę na końcu

Nie można uzyskać dostępu do folderów lub plików z udziału plików platformy Azure, gdy jest on zainstalowany w systemie Linux, polecenia, takie jak du i LS i/lub aplikacje innych firm, mogą zakończyć się niepowodzeniem z powodu błędu "nie ma takiego pliku lub katalogu" podczas uzyskiwania dostępu do udziału, jednak można przekazać pliki do tych folderów za pośrednictwem portalu.

### <a name="cause"></a>Przyczyna

Foldery lub pliki zostały przekazane z systemu, który koduje znaki na końcu nazwy do innego znaku, pliki przekazane z komputera Macintosh mogą mieć znak "0xF028" lub "0xF029" zamiast 0x20 (Space) lub 0X2E (kropka).

### <a name="solution"></a>Rozwiązanie

Użyj opcji mapchars w udziale podczas instalowania udziału w systemie Linux: 

Zamiast:

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

używanych

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino,mapchars
```


## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem.
