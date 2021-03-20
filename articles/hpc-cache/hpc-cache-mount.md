---
title: Instalowanie pamięci podręcznej platformy Azure HPC
description: Jak połączyć klientów z usługą Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7f1d8d34d6351fc344fdb101ac8e9a96678df9d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91651432"
---
# <a name="mount-the-azure-hpc-cache"></a>Instalowanie pamięci podręcznej usługi Azure HPC Cache

Po utworzeniu pamięci podręcznej klienci systemu plików NFS mogą uzyskać do niej dostęp za pomocą prostego `mount` polecenia. Polecenie łączy określoną ścieżkę docelową magazynu w pamięci podręcznej platformy Azure HPC do katalogu lokalnego na komputerze klienckim.

Polecenie instalacji składa się z następujących elementów:

* Jeden z adresów pamięci podręcznej (na liście na stronie Przegląd pamięci podręcznej)
* Ścieżka wirtualnej przestrzeni nazw ustawiona dla miejsca docelowego magazynu (na liście na stronie przestrzeni nazw pamięci podręcznej)
* Ścieżka lokalna do użycia na kliencie
* Parametry polecenia, które optymalizują powodzenie tego rodzaju instalacji systemu plików NFS

Na stronie **instrukcje instalacji** dla pamięci podręcznej są zbierane informacje i zalecane opcje, a także jest tworzone polecenie instalacji prototypowej, które można skopiować. Aby uzyskać szczegółowe informacje, przeczytaj artykuł [Korzystanie z instrukcji instalacji](#use-the-mount-instructions-utility) .

## <a name="prepare-clients"></a>Przygotowywanie klientów

Upewnij się, że klienci mogą zainstalować pamięć podręczną Azure HPC, postępując zgodnie z instrukcjami w tej sekcji.

### <a name="provide-network-access"></a>Zapewnianie dostępu do sieci

Komputery klienckie muszą mieć dostęp sieciowy do sieci wirtualnej i podsieci prywatnej pamięci podręcznej.

Można na przykład utworzyć maszyny wirtualne klienta w tej samej sieci wirtualnej lub użyć punktu końcowego, bramy lub innego rozwiązania w sieci wirtualnej w celu uzyskania dostępu spoza zewnątrz. (Należy pamiętać, że żadne inne niż pamięć podręczna nie powinna być hostowana w podsieci pamięci podręcznej).

### <a name="install-utilities"></a>Zainstaluj narzędzia

Zainstaluj odpowiednie oprogramowanie z systemem Linux, aby umożliwić obsługę polecenia instalacji systemu plików NFS:

* W przypadku Red Hat Enterprise Linux lub SuSE: `sudo yum install -y nfs-utils`
* Dla Ubuntu lub Debian: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Utwórz ścieżkę lokalną

Utwórz ścieżkę katalogu lokalnego na każdym kliencie, aby nawiązać połączenie z pamięcią podręczną. Utwórz ścieżkę dla każdej ścieżki przestrzeni nazw, którą chcesz zainstalować.

Przykład: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Strona [instrukcje instalacji](#use-the-mount-instructions-utility) w Azure Portal zawiera polecenie prototypu, które można skopiować.

Po połączeniu komputera klienckiego z pamięcią podręczną należy skojarzyć tę ścieżkę z wirtualną ścieżką przestrzeni nazw, która reprezentuje eksport docelowy magazynu. Utwórz katalogi dla każdej z wirtualnych ścieżek przestrzeni nazw, które będą używane przez klienta.

## <a name="use-the-mount-instructions-utility"></a>Korzystanie z narzędzia instrukcje instalacji

Korzystając ze strony **instrukcje instalacji** w Azure Portal, można utworzyć polecenie instalacji możliwej do kopiowania. Otwórz stronę w sekcji **Konfigurowanie** widoku pamięci podręcznej w portalu.

Przed użyciem polecenia na kliencie upewnij się, że klient spełnia wymagania wstępne i że oprogramowanie wymagane do użycia polecenia NFS, zgodnie z `mount` powyższym opisem w artykule [Przygotowywanie klientów](#prepare-clients).

![zrzut ekranu wystąpienia pamięci podręcznej platformy Azure HPC w portalu z załadowana stroną Konfigurowanie > instalacji](media/mount-instructions.png)

Postępuj zgodnie z tą procedurą, aby utworzyć polecenie instalacji.

1. Dostosuj pole **ścieżka klienta** . To pole zawiera przykładowe polecenie, za pomocą którego można utworzyć ścieżkę lokalną na kliencie. Klient uzyskuje dostęp do zawartości z pamięci podręcznej platformy Azure HPC lokalnie w tym katalogu.

   Kliknij pole i zmodyfikuj polecenie, aby zawierało żądaną nazwę katalogu. Nazwa pojawia się na końcu ciągu po `sudo mkdir -p`

   ![zrzut ekranu przedstawiający pole ścieżki klienta z kursorem umieszczonym na końcu](media/mount-edit-client.png)

   Po zakończeniu edycji pola polecenie instalacji u dołu strony jest aktualizowane z nową ścieżką klienta.

1. Wybierz z listy **adres instalacji pamięci podręcznej** . To menu zawiera listę wszystkich [punktów instalacji klienta](#find-mount-command-components)pamięci podręcznej.

   Równoważ obciążenie klienta na wszystkich dostępnych adresów instalacji, aby zwiększyć wydajność pamięci podręcznej.

   ![zrzut ekranu przedstawiający pole adresu instalacji pamięci podręcznej z selektorem zawierającym trzy adresy IP do wyboru](media/mount-select-ip.png)

1. Wybierz **ścieżkę do wirtualnej przestrzeni nazw** , która ma być używana dla klienta. Ścieżki te łączą się z eksportami w systemie magazynu zaplecza.

   ![Zrzut ekranu pokazujący pole "ścieżka wirtualnej przestrzeni nazw" z otwartym selektorem.](media/mount-select-target.png)

   Ścieżki wirtualnych przestrzeni nazw można wyświetlać i zmieniać na stronie portalu **przestrzeni nazw** . Przeczytaj temat [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md) , aby zobaczyć, jak.

   Aby dowiedzieć się więcej o funkcji zagregowanej przestrzeni nazw pamięci podręcznej platformy Azure HPC, przeczytaj temat [Planowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md).

1. Pole **polecenia instalacji** w kroku trzy automatycznie wypełnia przy użyciu dostosowanego polecenia instalacji, które używa adresu instalacji, ścieżki wirtualnego obszaru nazw i ścieżki klienta ustawionej w poprzednich polach.

   Kliknij symbol kopiowania po prawej stronie pola, aby automatycznie skopiować go do Schowka.

   ![zrzut ekranu przedstawiający pole polecenia instalacji prototypowej, pokazujący tekst aktywowany przycisku "Kopiuj do schowka"](media/mount-command-copy.png)

1. Użyj skopiowanego polecenia mount na komputerze klienckim, aby połączyć go z pamięcią podręczną platformy Azure HPC. Można wydać polecenie bezpośrednio z wiersza polecenia klienta lub dodać polecenie instalacji w skrypcie lub szablonie instalacji klienta.

## <a name="understand-mount-command-syntax"></a>Omówienie składni polecenia instalacji

Polecenie instalacji ma następującą postać:

> sudo zainstaluj {*Options*} *cache_mount_address*:/*namespace_path* *local_path*

Przykład:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Po pomyślnym wykonaniu tego polecenia zawartość eksportu magazynu będzie widoczna w ``hpccache`` katalogu na kliencie.

### <a name="mount-command-options"></a>Opcje polecenia instalacji

W przypadku niezawodnego instalowania klienta należy przekazać te ustawienia i argumenty w poleceniu instalacji:

> Instalacja-o twarde, proto = TCP, mountproto = TCP, retry = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| Zalecane ustawienia poleceń instalacji | Opis |
--- | ---
``hard`` | Instalacje miękkie do pamięci podręcznej platformy Azure HPC są skojarzone z niepowodzeńmi aplikacji i możliwymi utratą danych.
``proto=tcp`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci NFS.
``mountproto=tcp`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci dla operacji instalacji.
``retry=<value>`` | Ustaw ``retry=30`` , aby uniknąć błędów instalacji przejściowej. (W instalacjach na pierwszym planie zalecana jest inna wartość).

### <a name="find-mount-command-components"></a>Znajdź składniki poleceń instalacji

Jeśli chcesz utworzyć polecenie instalacji bez używania strony z **instrukcjami instalacji** , możesz znaleźć adresy instalacji na stronie **Przegląd** pamięci podręcznej i ścieżki wirtualnego obszaru nazw na stronie **przestrzeni nazw** .

![zrzut ekranu strony Przegląd wystąpienia pamięci podręcznej platformy Azure HPC z wyróżnionym polem wyboru na liście adresy instalacji w prawym dolnym rogu](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Adresy instalacji pamięci podręcznej są zgodne z interfejsami sieciowymi w podsieci pamięci podręcznej. W grupie zasobów te karty sieciowe są wyświetlane z nazwami kończącymi się na `-cluster-nic-` i numerami. Nie zmieniaj ani nie usuwaj tych interfejsów lub pamięć podręczna stanie się niedostępna.

Ścieżki wirtualnych przestrzeni nazw są wyświetlane na stronie ustawień **przestrzeni nazw** pamięci podręcznej.

![zrzut ekranu przedstawiający stronę Ustawienia portalu > obszaru nazw z wyróżnionym polem dookoła pierwszej kolumny tabeli: "ścieżka przestrzeni nazw"](media/view-namespace-paths.png)

## <a name="next-steps"></a>Następne kroki

* Aby przenieść dane do miejsc docelowych magazynu pamięci podręcznej, przeczytaj temat [Wypełnij nową usługę Azure Blob Storage](hpc-cache-ingest.md).
