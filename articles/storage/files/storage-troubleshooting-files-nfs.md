---
title: Rozwiązywanie problemów z udziałem plików NFS systemu Azure — Azure Files
description: Rozwiązywanie problemów z udziałem plików NFS systemu Azure.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4c87887f77d5f227fe4d4cdee220397289878d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99574469"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Rozwiązywanie problemów z udziałami plików NFS systemu Azure

W tym artykule wymieniono niektóre typowe problemy związane z udziałami plików NFS systemu Azure. Zapewnia potencjalne przyczyny i obejścia w przypadku napotkania tych problemów.

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" nie powiodła się: nieprawidłowy argument (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Przyczyna 1: idmapping nie jest wyłączona
Azure Files nie zezwala na alfanumeryczne UID/GID. Idmapping musi być wyłączona. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Przyczyna 2: idmapping została wyłączona, ale po napotkaniu nieprawidłowej nazwy pliku/katalogu otrzymasz ponowną obsługę
Nawet jeśli idmapping została prawidłowo wyłączona, ustawienia dotyczące wyłączania idmapping są zastępowane w niektórych przypadkach. Na przykład gdy Azure Files napotka nieprawidłową nazwę pliku, spowoduje to wysłanie błędu. Gdy widzisz ten konkretny kod błędu, klient systemu plików NFS w wersji 4,1 z systemem Linux zdecyduje się ponownie włączyć idmapping, a przyszłe żądania są wysyłane ponownie z alfanumerycznym identyfikatorem UID/GID. Aby zapoznać się z listą nieobsługiwanych znaków w Azure Files, zobacz ten [artykuł](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata). Dwukropek jest jednym z nieobsługiwanych znaków. 

### <a name="workaround"></a>Obejście
Sprawdź, czy usługa idmapping jest wyłączona i nic nie jest ponownie włączane, a następnie wykonaj następujące czynności:

- Odinstaluj udział
- Wyłącz mapowanie identyfikatorów przy użyciu # echo Y >/sys/module/NFS/Parameters/nfs4_disable_idmapping
- Instalowanie udziału z powrotem
- W przypadku uruchomienia rsync należy uruchomić rsync z argumentem "— liczbowy-ID" z katalogu, który nie ma żadnej nieprawidłowej nazwy Dir/File.

## <a name="unable-to-create-an-nfs-share"></a>Nie można utworzyć udziału NFS

### <a name="cause-1-subscription-is-not-enabled"></a>Przyczyna 1: subskrypcja nie jest włączona

Twoja subskrypcja mogła nie zostać zarejestrowana na potrzeby wersji zapoznawczej systemu Azure Files NFS. Aby włączyć tę funkcję, należy uruchomić kilka dodatkowych polecenia cmdlet z Cloud Shell lub lokalnego terminalu.

> [!NOTE]
> Ukończenie rejestracji może potrwać do 30 minut.


#### <a name="solution"></a>Rozwiązanie

Użyj następującego skryptu, aby zarejestrować funkcję i dostawcę zasobów, Zastąp `<yourSubscriptionIDHere>` przed uruchomieniem skryptu:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Przyczyna 2: nieobsługiwane ustawienia konta magazynu

System plików NFS jest dostępny tylko na kontach magazynu z następującą konfiguracją:

- Warstwa — Premium
- Rodzaj konta — FileStorage
- Regiony — [Lista obsługiwanych regionów](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>Rozwiązanie

Postępuj zgodnie z instrukcjami w naszym artykule: [jak utworzyć udział NFS](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Przyczyna 3: konto magazynu zostało utworzone przed ukończeniem rejestracji

Aby konto magazynu korzystało z funkcji, należy ją utworzyć po zakończeniu rejestracji w ramach subskrypcji dla systemu plików NFS. Rejestracja może potrwać do 30 minut.

#### <a name="solution"></a>Rozwiązanie

Po zakończeniu rejestracji postępuj zgodnie z instrukcjami w naszym artykule: [jak utworzyć udział NFS](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Nie można nawiązać połączenia z udziałem plików systemu Azure NFS ani go zainstalować

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Przyczyna 1: żądanie pochodzi od klienta w niezaufanej sieci/niezaufanym adresie IP

W przeciwieństwie do protokołu SMB system plików NFS nie ma uwierzytelniania opartego na użytkownikach. Uwierzytelnianie dla udziału jest oparte na konfiguracji reguły zabezpieczeń sieci. W związku z tym, aby zapewnić, że tylko bezpieczne połączenia są nawiązywane z udziałem NFS, należy użyć punktu końcowego usługi lub prywatnych punktów końcowych. Aby uzyskać dostęp do udziałów z zasobów lokalnych oprócz prywatnych punktów końcowych, należy skonfigurować sieć VPN lub ExpressRoute. Adresy IP dodane do listy dozwolonych kont magazynu dla zapory są ignorowane. Aby skonfigurować dostęp do udziału NFS, należy użyć jednej z następujących metod:


- [Punkt końcowy usługi](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Dostępne przez publiczny punkt końcowy
    - Dostępne tylko w tym samym regionie.
    - Komunikacja równorzędna sieci wirtualnych nie zapewnia dostępu do udziału.
    - Każda sieć wirtualna lub podsieć musi zostać dodana pojedynczo do listy dozwolonych.
    - W przypadku dostępu lokalnego punkty końcowe usługi mogą być używane z ExpressRoute, punkt-lokacja i sieci VPN typu lokacja-lokacja, ale zalecamy korzystanie z prywatnego punktu końcowego, ponieważ jest bardziej bezpieczny.

Na poniższym diagramie przedstawiono łączność przy użyciu publicznych punktów końcowych.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagram łączności między publicznymi punktami końcowymi." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Prywatny punkt końcowy](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - Dostęp jest bezpieczniejszy niż punkt końcowy usługi.
    - Dostęp do udziału NFS za pośrednictwem prywatnego linku jest dostępny w ramach i poza regionem świadczenia usługi Azure na koncie magazynu (między regionami, lokalnymi)
    - Komunikacja równorzędna sieci wirtualnych z sieciami wirtualnymi hostowanymi w prywatnym punkcie końcowym daje dostęp do klientów w równorzędnych sieciach wirtualnych.
    - Prywatne punkty końcowe mogą być używane z ExpressRoute, punkt-lokacja i sieci VPN typu lokacja-lokacja.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagram łączności między prywatnymi punktami końcowymi." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Przyczyna 2: włączono bezpieczny transfer

Szyfrowanie podwójne nie jest jeszcze obsługiwane w przypadku udziałów NFS. Platforma Azure udostępnia warstwę szyfrowania dla wszystkich danych przesyłanych między centrami datasites za pomocą MACSec. Udziały NFS są dostępne tylko z zaufanych sieci wirtualnych i za pośrednictwem tuneli VPN. W udziałach NFS nie jest dostępne żadne dodatkowe szyfrowanie warstwy transportu.

#### <a name="solution"></a>Rozwiązanie

W bloku Konfiguracja konta magazynu jest wymagane wyłączenie bezpiecznego transferu.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Zrzut ekranu przedstawiający blok Konfiguracja konta magazynu, który jest wymagany do bezpiecznego transferu.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Przyczyna 3: nie zainstalowano pakietu Common NFS
Przed uruchomieniem polecenia mount Zainstaluj pakiet, uruchamiając polecenie dystrybucji.

Aby sprawdzić, czy pakiet NFS jest zainstalowany, uruchom polecenie: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Rozwiązanie

Jeśli pakiet nie jest zainstalowany, zainstaluj pakiet w dystrybucji.

##### <a name="ubuntu-or-debian"></a>Ubuntu lub debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

Użyj Menedżera pakietów DNF: `sudo dnf install nfs-common` .

Starsze wersje Red Hat Enterprise Linux i CentOS korzystają z Menedżera pakietów yum: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Użyj Menedżera pakietów użyciu narzędzia zypper: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>Przyczyna 4: blokowanie przez zaporę portu 2049

Protokół NFS komunikuje się z serwerem za pośrednictwem portu 2049, upewnij się, że ten port jest otwarty dla konta magazynu (serwer NFS).

#### <a name="solution"></a>Rozwiązanie

Sprawdź, czy na kliencie jest otwarty port 2049, uruchamiając następujące polecenie: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Jeśli port nie jest otwarty, otwórz go.

## <a name="ls-list-files-shows-incorrectinconsistent-results"></a>LS (lista plików) zawiera nieprawidłowe/niespójne wyniki

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>Przyczyna: niespójność między buforowanymi wartościami i wartościami metadanych pliku serwera po otwarciu dojścia do pliku
Czasami polecenie "list Files" wyświetla niezerowy rozmiar w oczekiwany sposób, a w przypadku bardzo następnych plików list zamiast tego jest wyświetlany rozmiar 0 lub bardzo stara sygnatura czasowa. Jest to znany problem spowodowany niespójnym buforowaniem wartości metadanych plików, gdy plik jest otwarty. Aby rozwiązać ten problem, można użyć jednego z następujących obejść:

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>Obejście 1: Aby uzyskać rozmiar pliku do pobrania, należy użyć do c zamiast ls-l
Użycie funkcji s-c zawsze spowoduje pobranie najnowszej wartości z serwera i nie będzie miało żadnych niespójności.

#### <a name="workaround-2-use-noac-mount-flag"></a>Obejście 2: Użyj flagi instalacji "noac"
Zainstaluj ponownie system plików przy użyciu flagi "noac" z poleceniem instalacji. Zawsze zostaną pobrane wszystkie wartości metadanych z serwera. W przypadku użycia tego obejścia może wystąpić pewne niewielkie obciążenie wydajności dla wszystkich operacji metadanych.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem.
