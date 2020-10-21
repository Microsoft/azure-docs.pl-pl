---
title: Instalowanie klientów w klastrze plików Microsoft Azure FXT Edge
description: Jak komputery klienckie NFS mogą instalować hybrydową pamięć podręczną magazynu usługi Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: fa1f9da2c60aaf4c552916d16c266e984bf08892
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340516"
---
# <a name="tutorial-mount-the-cluster"></a>Samouczek: Instalowanie klastra

W tym samouczku przedstawiono sposób instalowania klientów NFS w klastrze plików usługi Azure FXT Edge. Klienci instalują ścieżki wirtualnego obszaru nazw przypisane podczas dodawania magazynu zaplecza.

Ten samouczek uczy się:

> [!div class="checklist"]
>
> * Strategie równoważenia obciążenia dla klientów w zakresie adresów IP dostępnych dla klientów
> * Jak utworzyć ścieżkę instalacji z rozgałęzienia adresu IP i przestrzeni nazw dla klienta
> * Argumenty, które mają być używane w poleceniu instalacji

Ukończenie tego samouczka zajmuje około 45 minut.

## <a name="steps-to-mount-the-cluster"></a>Procedura instalacji klastra

Wykonaj następujące kroki, aby połączyć komputery klienckie z klastrem plików usługi Azure FXT Edge.

1. Zdecyduj, jak równoważyć obciążenie ruchu klienckiego między węzłami klastra. Aby uzyskać szczegółowe informacje, przeczytaj temat [równoważenie obciążenia klienta](#balance-client-load)poniżej.
1. Określ adres IP i ścieżkę rozgałęzienia klastra do zainstalowania.
1. Określ ścieżkę dodaną do klienta dla instalacji.
1. Wydaj [polecenie instalacji](#use-recommended-mount-command-options)z odpowiednimi argumentami.

## <a name="balance-client-load"></a>Równoważ obciążenie klienta

Aby zapewnić zrównoważenie żądań klientów między wszystkimi węzłami w klastrze, należy zainstalować klientów do pełnego zakresu adresów IP skierowanych na klienta. Istnieje kilka sposobów automatyzacji tego zadania.

Aby dowiedzieć się więcej na temat równoważenia obciążenia DNS w przypadku działania okrężnego dla klastra, przeczytaj temat [Konfigurowanie systemu DNS dla klastra usługi Azure FXT Edge](fxt-configure-network.md#configure-dns-for-load-balancing). Aby użyć tej metody, należy zachować serwer DNS, co nie jest wyjaśnione w tych artykułach.

Prostsza Metoda dla małych instalacji polega na użyciu skryptu do przypisywania adresów IP w całym zakresie w czasie instalacji klienta.

Inne metody równoważenia obciążenia mogą być odpowiednie w przypadku dużych lub złożonych systemów. Skontaktuj się z przedstawicielem firmy Microsoft lub Otwórz [żądanie pomocy technicznej](fxt-support-ticket.md) , aby uzyskać pomoc. (Azure Load Balancer nie jest obecnie *obsługiwana* w przypadku usługi Azure FXT Edge.)

## <a name="create-the-mount-command"></a>Utwórz polecenie instalacji

Z poziomu klienta ``mount`` polecenie mapuje serwer wirtualny (vserver) w klastrze plików usługi Azure FXT Edge do ścieżki w lokalnym systemie plików.

Format jest ``mount <FXT cluster path> <local path> {options}``

Istnieją trzy elementy polecenia instalacji:

* ścieżka klastra — kombinacja ścieżki adresu IP i przestrzeni nazw opisana poniżej
* ścieżka lokalna — ścieżka na kliencie
* Opcje polecenia instalacji — (wymienione w [opcji Użyj zalecanych poleceń instalacji](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Utwórz ścieżkę klastra

Ścieżka klastra jest kombinacją *adresu IP* vserver oraz ścieżki do *rozgałęzienia przestrzeni nazw*. Rozgałęzienie przestrzeni nazw jest ścieżką wirtualną zdefiniowaną podczas [dodawania systemu magazynu](fxt-add-storage.md#create-a-junction).

Na przykład jeśli użyto ``/fxt/files`` jako ścieżki przestrzeni nazw, klienci instalują *IP_address*:/FXT/Files do ich lokalnego punktu instalacji.

![Okno dialogowe "Dodawanie nowego połączenia" z/avere/Files w polu Ścieżka przestrzeni nazw](media/fxt-mount/fxt-junction-example.png)

Adres IP jest jednym z adresów IP skierowanych do klienta zdefiniowanych dla vserver. Zakres adresów IP dostępnych dla klientów można znaleźć w dwóch miejscach w panelu sterowania klastra:

* Tabela **VServers** (karta Pulpit nawigacyjny) —

  ![Karta Pulpit nawigacyjny panelu sterowania z kartą VServer wybraną w tabeli dane pod wykresem i sekcją adres IP w kółku](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Strona ustawień **sieci dołączona do klienta** —

  ![Ustawienia > VServer > stronie konfiguracji sieci przeznaczonej dla klientów z okręgiem wokół sekcji Zakres adresów tabeli dla określonego vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Połącz adres IP i ścieżkę przestrzeni nazw, aby utworzyć ścieżkę klastra dla polecenia instalacji.

Przykładowe polecenie instalacji klienta: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Utwórz ścieżkę lokalną

Ścieżka lokalna polecenia instalacji jest do Ciebie. Możesz ustawić dowolną strukturę ścieżki, która ma być częścią wirtualnej przestrzeni nazw. Zaprojektuj przestrzeń nazw i ścieżkę lokalną, która jest wygodna dla przepływu pracy klienta.

Aby uzyskać więcej informacji na temat przestrzeni nazw klienta, zapoznaj się z tematem [przestrzeń nazw](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)przewodnika po konfiguracji klastra.

Oprócz ścieżek należy uwzględnić [Opcje polecenia instalacji](#use-recommended-mount-command-options) opisane poniżej podczas instalowania każdego klienta.

### <a name="use-recommended-mount-command-options"></a>Użyj zalecanych opcji polecenia instalacji

Aby zapewnić bezproblemowe Instalowanie klienta, należy przekazać te ustawienia i argumenty w poleceniu instalacji:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Wymagane ustawienia | Opis |
--- | ---
``hard`` | Instalacje miękkie do klastra usługi Azure FXT Edge są skojarzone z niepowodzeńmi aplikacji i możliwymi utratą danych.
``proto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci NFS.
``mountproto=netid`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci dla operacji instalacji.
``retry=n`` | Ustaw ``retry=30`` , aby uniknąć błędów instalacji przejściowej. (W instalacjach na pierwszym planie zalecana jest inna wartość).

| Ustawienia preferowane  | Opis |
--- | ---
``nointr``            | Jeśli klienci korzystają ze starszych jądra systemu operacyjnego (przed 2008 kwietnia), które obsługują tę opcję, użyj go. Opcja "intr" jest domyślna.

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu klientów można testować przepływ pracy i rozpocząć pracę z klastrem.

Jeśli musisz przenieść dane do nowego pliku w chmurze, skorzystaj ze struktury pamięci podręcznej, używając funkcji pozyskiwania danych równoległych. Niektóre strategie zostały opisane w artykule [przeniesienie danych do klastra vFXT](../avere-vfxt/avere-vfxt-data-ingest.md). (Avere vFXT for Azure to produkt oparty na chmurze, który korzysta z technologii pamięci podręcznej, podobnie jak w przypadku usługi Azure FXT Edge.)

Przeczytaj artykuł [monitorowanie stanu sprzętu usługi Azure FXT Edge](fxt-monitor.md) w przypadku konieczności rozwiązywania problemów ze sprzętem.