---
title: Instalowanie pamięci podręcznej HPC platformy Azure
description: Jak połączyć klientów z usługą Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458393"
---
# <a name="mount-the-azure-hpc-cache"></a>Instalowanie pamięci podręcznej HPC platformy Azure

Po utworzeniu pamięci podręcznej klienci NFS `mount` mogą uzyskać do niej dostęp za pomocą prostego polecenia. Polecenie łączy ścieżkę docelową określonego magazynu w pamięci podręcznej HPC platformy Azure z katalogiem lokalnym na komputerze klienckim.

Polecenie mount składa się z następujących elementów:

* Jeden z adresów instalacji pamięci podręcznej (wymieniony na stronie przeglądu pamięci podręcznej)
* Ścieżka wirtualnego obszaru nazw ustawiona podczas tworzenia obiektu docelowego magazynu
* Ścieżka lokalna do użycia na kliencie
* Parametry poleceń, które optymalizują powodzenie tego rodzaju instalacji NFS

Strona **Instrukcje instalacji** pamięci podręcznej zbiera informacje i zalecane opcje dla Ciebie i tworzy polecenie instalacji prototypu, które można skopiować. Przeczytaj [artykuł Użyj narzędzia instrukcji montażu, aby](#use-the-mount-instructions-utility) uzyskać szczegółowe informacje.

## <a name="prepare-clients"></a>Przygotowywanie klientów

Upewnij się, że klienci są w stanie zainstalować pamięć podręczną HPC platformy Azure, postępuje zgodnie z wytycznymi w tej sekcji.

### <a name="provide-network-access"></a>Zapewnienie dostępu do sieci

Maszyny klienckie muszą mieć dostęp sieciowy do sieci wirtualnej i podsieci prywatnej pamięci podręcznej.

Na przykład utwórz maszyny wirtualne klienta w tej samej sieci wirtualnej lub użyj punktu końcowego, bramy lub innego rozwiązania w sieci wirtualnej, aby uzyskać dostęp z zewnątrz. (Należy pamiętać, że nic innego niż sama pamięć podręczna powinna być hostowana wewnątrz podsieci pamięci podręcznej).

### <a name="install-utilities"></a>Instalowanie narzędzi

Zainstaluj odpowiednie oprogramowanie narzędziowe systemu Linux do obsługi polecenia do montażu NFS:

* Dla Red Hat Enterprise Linux lub SuSE:`sudo yum install -y nfs-utils`
* Dla Ubuntu lub Debiana:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Tworzenie ścieżki lokalnej

Utwórz ścieżkę katalogu lokalnego na każdym kliencie, aby połączyć się z pamięcią podręczną. Utwórz ścieżkę dla każdej ścieżki obszaru nazw, którą chcesz zainstalować.

Przykład: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Strona [Instrukcje instalacji](#use-the-mount-instructions-utility) w witrynie Azure Portal zawiera polecenie prototypu, które można skopiować.

Po podłączeniu komputera klienckiego do pamięci podręcznej, można skojarzyć tę ścieżkę z wirtualnej ścieżki obszaru nazw, która reprezentuje eksportu docelowego magazynu. Utwórz katalogi dla każdej ścieżki wirtualnego obszaru nazw, których będzie używał klient.

## <a name="use-the-mount-instructions-utility"></a>Użyj narzędzia instrukcji montażu

Za pomocą strony **Instrukcje instalacji** w witrynie Azure Portal można utworzyć polecenie instalacji do kopiowania. Otwórz stronę z sekcji **Konfigurowanie** widoku pamięci podręcznej w portalu.

Przed użyciem polecenia na kliencie upewnij się, że klient spełnia wymagania wstępne i ma `mount` oprogramowanie potrzebne do użycia polecenia NFS, jak opisano powyżej w [programie Prepare clients](#prepare-clients).

![zrzut ekranu przedstawiający wystąpienie pamięci podręcznej HPC platformy Azure w portalu z załadowaną stroną Instrukcje konfigurowania > montażu](media/mount-instructions.png)

Wykonaj tę procedurę, aby utworzyć polecenie mount.

1. Dostosuj pole **Ścieżka klienta.** To pole zawiera przykładowe polecenie, za pomocą którego można utworzyć ścieżkę lokalną na kliencie. Klient uzyskuje dostęp do zawartości z pamięci podręcznej HPC azure lokalnie w tym katalogu.

   Kliknij to pole i edytuj polecenie, aby zawierało odpowiednią nazwę katalogu. Nazwa pojawia się na końcu ciągu po`sudo mkdir -p`

   ![zrzut ekranu pola ścieżki klienta z kursorem umieszczonym na końcu](media/mount-edit-client.png)

   Po zakończeniu edycji pola polecenie instalacji u dołu strony zostanie zaktualizowane wraz z nową ścieżką klienta.

1. Wybierz **adres instalacji pamięci podręcznej** z listy. To menu zawiera listę wszystkich [punktów instalacji klienta](#find-mount-command-components)pamięci podręcznej .

   Równoważenie obciążenia klienta we wszystkich dostępnych adresach instalacji w celu uzyskania lepszej wydajności pamięci podręcznej.

   ![zrzut ekranu pola adresu instalacji pamięci podręcznej z selektorem z trzema adresami IP do wyboru](media/mount-select-ip.png)

1. Wybierz **ścieżkę wirtualnego obszaru nazw, która** będzie używana dla klienta. Ścieżki te łączą się z eksportem w systemie magazynu zaplecza.

   ![zrzut ekranu przedstawiający pole ścieżki obszaru nazw z otwartym selektorem](media/mount-select-target.png)

   Ścieżki wirtualnego obszaru nazw można wyświetlać i zmieniać na stronie Portal obiektów docelowych magazynu. Przeczytaj [Dodaj obiekty docelowe magazynu,](hpc-cache-add-storage.md) aby zobaczyć, jak to zrobić.

   Aby dowiedzieć się więcej o zagregowanej przestrzeni nazw usługi Azure HPC Cache, przeczytaj artykuł [Planowanie zagregowanego obszaru nazw](hpc-cache-namespace.md).

1. Pole **Polecenia Mount** w kroku trzecim automatycznie wypełnia się dostosowanym poleceniem instalacji, które używa adresu instalacji, ścieżki wirtualnej przestrzeni nazw i ścieżki klienta ustawionej w poprzednich polach.

   Kliknij symbol kopiowania po prawej stronie pola, aby automatycznie skopiować go do schowka.

   ![zrzut ekranu przedstawiający pole ścieżki obszaru nazw z otwartym selektorem](media/mount-command-copy.png)

1. Użyj skopiowanego polecenia mount na komputerze klienckim, aby połączyć je z pamięcią podręczną HPC platformy Azure. Można wydać polecenie bezpośrednio z wiersza polecenia klienta lub dołączyć polecenie mount do skryptu lub szablonu konfiguracji klienta.

## <a name="understand-mount-command-syntax"></a>Opis składni polecenia montowania

Polecenie mount ma następujący formularz:

> sudo mount {*options*} *cache_mount_address*:/*namespace_path* *local_path*

Przykład:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Po pomyślnym powiódł się to polecenie, zawartość eksportu ``hpccache`` magazynu będzie widoczna w katalogu na kliencie.

### <a name="mount-command-options"></a>Opcje polecenia Montuj

W przypadku niezawodnego montażu klienta należy przekazać te ustawienia i argumenty w poleceniu instalacji:

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Zalecane ustawienia polecenia montowania | |
--- | ---
``hard`` | Miękkie instalacje do pamięci podręcznej HPC usługi Azure są skojarzone z awariami aplikacji i możliwością utraty danych.
``proto=tcp`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci NFS.
``mountproto=tcp`` | Ta opcja obsługuje odpowiednią obsługę błędów sieciowych dla operacji instalacji.
``retry=<value>`` | Ustaw, ``retry=30`` aby uniknąć przejściowych błędów instalacji. (W instalacjach pierwszego planu zalecana jest inna wartość).

### <a name="find-mount-command-components"></a>Znajdowanie składników polecenia mount

Jeśli chcesz utworzyć polecenie instalacji bez użycia strony **Instrukcje instalacji,** możesz znaleźć adresy instalacji na stronie **Przegląd** pamięci podręcznej i ścieżki wirtualnego obszaru nazw na stronie **docelowej magazynu.**

![zrzut ekranu przedstawiający stronę Przegląd wystąpienia usługi Azure HPC Cache z polem wyróżnienia wokół listy adresów instalacji w prawym dolnym przycisku](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Adresy instalacji pamięci podręcznej odpowiadają interfejsom sieciowym wewnątrz podsieci pamięci podręcznej. W grupie zasobów te karty sieciowe są `-cluster-nic-` wyświetlane z nazwami kończącymi się i liczbą. Nie należy zmieniać ani usuwać tych interfejsów, ponieważ pamięć podręczna stanie się niedostępna.

Ścieżki wirtualnego obszaru nazw są wyświetlane na stronie szczegółów każdego obiektu docelowego magazynu. Kliknij nazwę obiektu docelowego magazynu, aby wyświetlić jej szczegóły, w tym skojarzone z nią zagregowane ścieżki obszaru nazw.

![zrzut ekranu strony szczegółów obiektu docelowego magazynu (nagłówek "Aktualizuj miejsce docelowe magazynu"). Wokół wpisu w kolumnie Ścieżka wirtualnego obszaru nazw tabeli znajduje się pole podświetlenia.](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Następne kroki

* Aby przenieść dane do obiektów docelowych magazynu pamięci podręcznej, przeczytaj Artykuł [Wypełnianie nowego magazynu obiektów Blob platformy Azure](hpc-cache-ingest.md).
