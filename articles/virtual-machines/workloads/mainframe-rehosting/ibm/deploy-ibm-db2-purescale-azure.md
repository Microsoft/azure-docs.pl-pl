---
title: Wdrażanie programu IBM DB2 pureScale na platformie Azure
description: Dowiedz się, jak wdrożyć przykładową architekturę ostatnio używanej do migrowania przedsiębiorstwa z środowiska IBM DB2 działającego w systemie z/OS do programu IBM DB2 pureScale na platformie Azure.
author: njray
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 481816ca0c1fecfee9396f6cc40582695b0952f2
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550199"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Wdrażanie programu IBM DB2 pureScale na platformie Azure

W tym artykule opisano, jak wdrożyć [przykładową architekturę](ibm-db2-purescale-azure.md) używaną ostatnio przez klienta w celu migrowania ze środowiska IBM DB2 działającego w systemie z/OS do programu IBM DB2 PureScale na platformie Azure.

Aby wykonać kroki używane do migracji, zobacz Skrypty instalacji w repozytorium [DB2onAzure](https://aka.ms/db2onazure) w witrynie GitHub. Skrypty te są oparte na architekturze typowego, średniego obciążenia przetwarzania transakcji online (OLTP).

## <a name="get-started"></a>Rozpoczęcie pracy

Aby wdrożyć tę architekturę, Pobierz i uruchom skrypt deploy.sh znaleziony w repozytorium [DB2onAzure](https://aka.ms/db2onazure) w witrynie GitHub.

Repozytorium ma także skrypty do konfigurowania pulpitu nawigacyjnego usługi Grafana. Możesz użyć pulpitu nawigacyjnego do wysyłania zapytań do Prometheus, systemu monitorowania i alertów Open Source zawartych w programie DB2.

> [!NOTE]
> Skrypt deploy.sh na kliencie tworzy prywatne klucze SSH i przekazuje je do szablonu wdrożenia za pośrednictwem protokołu HTTPS. W celu zapewnienia większego bezpieczeństwa zalecamy używanie [Azure Key Vault](../../../../key-vault/general/overview.md) do przechowywania wpisów tajnych, kluczy i haseł.

## <a name="how-the-deployment-script-works"></a>Jak działa skrypt wdrożenia

Skrypt deploy.sh tworzy i konfiguruje zasoby platformy Azure dla tej architektury. Skrypt poprosi o subskrypcję platformy Azure i maszyny wirtualne używane w środowisku docelowym, a następnie wykonuje następujące operacje:

-   Konfiguruje grupę zasobów, sieć wirtualną i podsieci na platformie Azure na potrzeby instalacji.

-   Konfiguruje sieciowe grupy zabezpieczeń i SSH dla środowiska.

-   Konfiguruje wiele kart sieciowych zarówno w magazynie udostępnionym, jak i na maszynach wirtualnych DB2 pureScale.

-   Tworzy maszyny wirtualne magazynu udostępnionego. Jeśli używasz Bezpośrednie miejsca do magazynowania lub innego rozwiązania do magazynowania, zobacz [bezpośrednie miejsca do magazynowania omówienie](/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

-   Tworzy maszynę wirtualną serwera przesiadkowego.

-   Tworzy maszyny wirtualne DB2 pureScale.

-   Tworzy maszynę wirtualną z monitorem, która pureScale polecenie ping. Pomiń tę część wdrożenia, jeśli Twoja wersja programu DB2 pureScale nie wymaga monitora.

-   Tworzy maszynę wirtualną z systemem Windows do użycia na potrzeby testowania, ale nie instaluje żadnych elementów.

Następnie skrypty wdrażania konfigurują wirtualną sieć San (sieci vSAN) iSCSI dla magazynu udostępnionego na platformie Azure. W tym przykładzie iSCSI nawiązuje połączenie z udostępnionym klastrem magazynu. W oryginalnym rozwiązaniu klienta użyto GlusterFS. Jednak firma IBM nie obsługuje już tego podejścia. Aby zachować pomoc techniczną od firmy IBM, musisz użyć obsługiwanego systemu plików zgodnego z technologią iSCSI. Firma Microsoft oferuje Bezpośrednie miejsca do magazynowania (S2D) jako opcję.

To rozwiązanie udostępnia również opcję instalowania obiektów docelowych iSCSI jako pojedynczy węzeł systemu Windows. protokół iSCSI udostępnia udostępniony interfejs magazynu blokowego za pośrednictwem protokołu TCP/IP, który umożliwia procedury Instalatora programu DB2 pureScale używanie interfejsu urządzenia do nawiązywania połączenia z magazynem udostępnionym.

Skrypty wdrażania uruchamiają następujące ogólne kroki:

1.  Skonfiguruj udostępniony klaster magazynu na platformie Azure. Ten krok obejmuje co najmniej dwa węzły systemu Linux.

2.  Skonfiguruj interfejs iSCSI Direct na docelowych serwerach z systemem Linux dla klastra magazynu udostępnionego.

3.  Skonfiguruj inicjator iSCSI na maszynach wirtualnych z systemem Linux. Inicjator będzie uzyskiwać dostęp do udostępnionego klastra magazynu przy użyciu obiektu docelowego iSCSI. Aby uzyskać szczegółowe informacje dotyczące instalacji, zobacz [jak skonfigurować obiekt docelowy iSCSI i inicjator w systemie Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) w dokumentacji RootUsers.

4.  Zainstaluj udostępnioną warstwę magazynu dla interfejsu iSCSI.

Po utworzeniu urządzenia iSCSI przez skrypty ostatnim krokiem jest zainstalowanie programu DB2 pureScale. W ramach instalacji bazy danych DB2 pureScale w klastrze GlusterFS jest kompilowane i instalowana [Skala spektrum firmy IBM](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (dawniej znana jako GPFS). Ten klastrowany system plików umożliwia programowi DB2 pureScale współdzielenie danych między maszynami wirtualnymi, na których działa aparat programu DB2 pureScale. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [skalowania usługi IBM spektrum](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) w witrynie sieci Web firmy IBM.

## <a name="db2-purescale-response-file"></a>Plik odpowiedzi bazy danych DB2 pureScale

Repozytorium GitHub zawiera plik odpowiedzi DB2server. rsp, odpowiedź (. RSP), który umożliwia generowanie zautomatyzowanego skryptu instalacji programu DB2 pureScale. W poniższej tabeli wymieniono opcje pureScale programu DB2, których plik odpowiedzi używa do instalacji. Plik odpowiedzi można dostosować zgodnie z potrzebami środowiska.

> [!NOTE]
> Przykładowy plik odpowiedzi DB2server. rsp znajduje się w repozytorium [DB2onAzure](https://aka.ms/db2onazure) w witrynie GitHub. Jeśli używasz tego pliku, musisz go zmodyfikować, zanim będzie można go użyć w Twoim środowisku.

| Nazwa ekranu               | Pole                                        | Wartość                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Powitanie                   |                                              | Nowa instalacja                                                                                           |
| Wybierz produkt          |                                              | 11.1.3.3 wersja programu DB2. Wersje serwera z programem DB2 pureScale                                              |
| Konfigurowanie             | Katalog                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Wybierz typ instalacji                 | Normalne                                                                                               |
|                           | Akceptuję warunki firmy IBM                     | Zaznaczono                                                                                               |
| Właściciel wystąpienia            | Istniejący użytkownik na przykład, nazwa użytkownika        | DB2sdin1                                                                                              |
| Użytkownik z ogranicznikiem               | Istniejący użytkownik, nazwa użytkownika                     | DB2sdfe1                                                                                              |
| System plików klastra       | Ścieżka urządzenia udostępnionego partycji dysku            | /dev/dm-2                                                                                             |
|                           | Punkt instalacji                                  | /DB2sd \_ 1804a                                                                                         |
|                           | Dysk udostępniony dla danych                         | /dev/dm-1                                                                                             |
|                           | Punkt instalacji (dane)                           | /DB2fs/datafs1                                                                                        |
|                           | Udostępniony dysk do dziennika                          | /dev/dm-0                                                                                             |
|                           | Punkt instalacji (Dziennik)                            | /DB2fs/logfs1                                                                                         |
|                           | Wczesnego usługi klastra DB2. Ścieżka urządzenia | /dev/dm-3                                                                                             |
| Lista hostów                 | D1 [eth1], D2 [eth1], CF1 [eth1], CF2 [eth1] |                                                                                                       |
|                           | Preferowany podstawowy CF                         | cf1                                                                                                   |
|                           | Preferowany pomocniczy CF                       | cf2                                                                                                   |
| Plik odpowiedzi i podsumowanie | Pierwsza opcja                                 | Zainstaluj wersję programu DB2 Server z funkcją IBM DB2 pureScale i Zapisz moje ustawienia w pliku odpowiedzi |
|                           | Nazwa pliku odpowiedzi                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Uwagi dotyczące tego wdrożenia

- Wartości/dev-dm0,/dev-DM1,/dev-DM2 i/dev-dm3 mogą ulec zmianie po ponownym uruchomieniu maszyny wirtualnej, w której odbywa się konfiguracja (D0 w skrypcie zautomatyzowanym). Aby znaleźć odpowiednie wartości, można wydać następujące polecenie przed ukończeniem pliku odpowiedzi na serwerze, na którym zostanie uruchomiona Instalator:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- Skrypty Instalatora używają aliasów dysków iSCSI, aby można było łatwo znaleźć rzeczywiste nazwy.

- Gdy skrypt Instalatora jest uruchamiany w d0, wartości **/dev/DM- \*** mogą być różne w odniesieniu do D1, cf0 i CF1. Różnica w wartości nie ma wpływu na konfigurację programu DB2 pureScale.

## <a name="troubleshooting-and-known-issues"></a>Znane problemy i rozwiązywanie problemów

Repozytorium GitHub zawiera bazę wiedzy, która jest utrzymywana przez autorów. Zawiera listę potencjalnych problemów, które mogą wystąpić, i rozwiązania, które można wypróbować. Na przykład, znane problemy mogą wystąpić w następujących przypadkach:

-   Próbujesz uzyskać dostęp do adresu IP bramy.

-   Kompilujesz ogólną licencję publiczną (GPL).

-   Uzgadnianie zabezpieczeń między hostami nie powiodło się.

-   Instalator programu DB2 wykrywa istniejący system plików.

-   Skalowanie w usłudze IBM spektrum jest ręczne.

-   Instalujesz DB2 pureScale, gdy jest już utworzona Skala spektrum firmy IBM.

-   Usuwasz skalę programu DB2 pureScale i IBM spektrum.

Aby uzyskać więcej informacji na temat tych i innych znanych problemów, zobacz plik kb.md w repozytorium [DB2onAzure](https://aka.ms/DB2onAzure) .

## <a name="next-steps"></a>Następne kroki

-   [Tworzenie wymaganych użytkowników dla instalacji funkcji programu DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt — Utwórz wystąpienie polecenia](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Rozwiązanie danych klastrów DB2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Przewodnik po podnoszenia i przesunięć wirtualnego centrum danych platformy Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
