---
title: Jak działają migawki Azure NetApp Files | Microsoft Docs
description: Wyjaśnia, jak działają migawki Azure NetApp Files, w tym sposoby tworzenia migawek, sposobów przywracania migawek i używania migawek w ustawieniach replikacji między regionami.
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
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 526ef0af08833954aef4136716930cec0df40eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625251"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Jak działają migawki usługi Azure NetApp Files

W tym artykule opisano sposób działania migawek Azure NetApp Files. Azure NetApp Files technologia migawek zapewnia stabilność, skalowalność i szybszą możliwość odzyskiwania bez wpływu na wydajność. Technologia Azure NetApp Files Snapshot stanowi podstawę rozwiązań do ochrony danych, w tym przywracania pojedynczych plików, przywracania i klonowania woluminów oraz replikacji między regionami. 

Aby zapoznać się z instrukcjami dotyczącymi korzystania z migawek woluminów, zobacz [Zarządzanie migawkami przy użyciu Azure NetApp Files](azure-netapp-files-manage-snapshots.md). Aby zapoznać się z zagadnieniami dotyczącymi zarządzania migawkami w ramach replikacji między regionami, zobacz [wymagania i zagadnienia dotyczące korzystania z replikacji między regionami](cross-region-replication-requirements-considerations.md).

## <a name="what-volume-snapshots-are"></a>Jakie migawki woluminów są  

Migawka Azure NetApp Files jest obrazem systemu plików w czasie (woluminu). Może to być idealne rozwiązanie do tworzenia kopii zapasowych online. Za pomocą migawki można [utworzyć nowy wolumin](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), [przywrócić plik](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)lub [przywrócić wolumin](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert). W przypadku określonych danych aplikacji przechowywanych na woluminach Azure NetApp Files mogą być wymagane dodatkowe kroki w celu zapewnienia spójności aplikacji.  

Migawki o niskim obciążeniu są dostępne w ramach unikatowych funkcji podnieśania poziomu technologii wirtualizacji woluminów, która jest częścią Azure NetApp Files. Podobnie jak w przypadku bazy danych, ta warstwa używa wskaźników do rzeczywistych bloków danych na dysku. Jednak, w przeciwieństwie do bazy danych, nie zapisuje istniejących bloków; zapisuje zaktualizowane dane w nowym bloku i zmienia wskaźnik. Migawka Azure NetApp Files po prostu operuje wskaźnikami bloków, tworząc "zamrożone" widok tylko do odczytu woluminu, który umożliwia aplikacjom dostęp do starszych wersji plików i hierarchii katalogów bez specjalnego programowania. Rzeczywiste bloki danych nie są kopiowane. W związku z tym migawki są wydajne w czasie koniecznym do ich utworzenia; są one niemal natychmiast, niezależnie od rozmiaru woluminu. Migawki są również wydajne w miejscu do magazynowania. Same nie zajmują miejsca, a są zachowywane tylko bloki różnic między migawkami i aktywnym woluminem. 

Poniższe diagramy ilustrują pojęcia:  

![Diagramy pokazujące kluczowe koncepcje migawek](../media/azure-netapp-files/snapshot-concepts.png)

W diagramach jest wykonywana migawka na rysunku 1a. Na rysunku 1b zmienione dane są zapisywane w *nowym bloku* , a wskaźnik jest aktualizowany. Jednak wskaźnik migawki nadal wskazuje *wcześniej zapisany blok*, dając na żywo i historyczny widok danych. Inna migawka jest wykonywana na rysunku 1c. Teraz masz dostęp do trzech generacji danych (dane dynamiczne, migawki 2 i migawka 1, w kolejności od wieku), bez wprowadzania miejsca na woluminie, którego wymagają trzy pełne kopie. 

Migawka pobiera tylko kopię metadanych woluminu (*tabela inode*). Tworzenie nie trwa zaledwie kilka sekund, niezależnie od rozmiaru woluminu, użytej pojemności lub poziomu aktywności na woluminie. Wykonanie migawki woluminu 100-TiB ma taki sam czas (obok zera), co powoduje utworzenie migawki woluminu 100-GiB. Po utworzeniu migawki zmiany plików danych są odzwierciedlane w aktywnej wersji plików, jak zwykle.

W tym czasie bloki danych, które są wskazywane na podstawie migawki, pozostają stabilne i niezmienne. Ze względu na "Przekierowanie przy zapisie" Azure NetApp Files woluminów woluminy nie wiążą się z obciążeniem ani nie zużywają żadnego miejsca. Można przechowywać do 255 migawek na wolumin w czasie, z których wszystkie są dostępne jako tylko do odczytu i w trybie online, zużywając jako niewielką pojemność jako liczbę zmienionych bloków między poszczególnymi migawkami. Zmodyfikowane bloki są przechowywane w aktywnym woluminie. Bloki wskazywane przez migawki są przechowywane (jako tylko do odczytu) w woluminie do przechowywania, aby można je było przeznaczyć tylko wtedy, gdy wszystkie wskaźniki (w aktywnym woluminie i migawek) zostały wyczyszczone. W związku z tym wykorzystanie woluminu zwiększy się z upływem czasu, przez nowe bloki danych lub (zmodyfikowane) bloki danych przechowywane w migawce.

 Na poniższym diagramie przedstawiono migawki woluminu i zajęte miejsce w czasie: 

![Diagram pokazujący migawki woluminu i zajęte miejsce w czasie](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Ponieważ migawka woluminu rejestruje tylko zmiany bloków od najnowszej migawki, zapewnia następujące korzyści:

* Migawki są ***wydajne dla magazynu***.   
    Migawki zużywają minimalne miejsce do magazynowania, ponieważ nie kopiuje bloków danych całego woluminu. Dwie migawki wykonywane w sekwencji różnią się tylko blokami dodanymi lub zmianami w przedziale czasu między nimi. Ten blok — zachowanie przyrostowe ogranicza skojarzone użycie pojemności magazynu. Wiele alternatywnych implementacji migawek zużywa woluminy magazynu równe aktywnemu systemowi plików, co zwiększa zapotrzebowanie na pojemność magazynu. W zależności od dziennych stawek za aplikację na *poziomie bloku* , Azure NetApp Files migawki zużywają więcej lub mniej mocy, ale tylko w przypadku zmienionych danych. Średnie użycie migawek dziennych z tylko 1-5% pojemności zużywanej objętości dla wielu woluminów aplikacji lub do 20-30% dla woluminów, takich jak SAP HANA woluminów bazy danych. Pamiętaj, aby [monitorować wolumin i użycie migawek](azure-netapp-files-metrics.md#volumes) na potrzeby wykorzystania pojemności migawek względem liczby utworzonych i konserwowanych migawek.   

* Migawki są ***szybkie do tworzenia, replikowania, przywracania i klonowania***.   
    Tworzenie, replikowanie, przywracanie lub klonowanie migawek trwa zaledwie kilka sekund, niezależnie od rozmiaru woluminu i poziomu działań. Migawkę woluminu można utworzyć [na żądanie](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume). Można również użyć [zasad migawek](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) , aby określić, kiedy Azure NetApp Files ma automatycznie tworzyć migawkę i ile migawek ma zachować dla woluminu.  Spójność aplikacji można osiągnąć przez organizowanie migawek z warstwą aplikacji, na przykład za pomocą [Narzędzia AzAcSnap](azacsnap-introduction.md) na potrzeby SAP HANA.

* Migawki nie mają wpływu na ***wydajność*** woluminu.   
    Ze względu na "Przekierowanie przy zapisie", przechowywanie lub zachowywanie migawek Azure NetApp Files nie ma wpływu na wydajność, nawet z dużym obciążeniem danych. Usuwanie migawki również ma niewielki wpływ na wydajność w większości przypadków. 

* Migawki zapewniają ***skalowalność*** , ponieważ mogą być tworzone często i można zachować wiele.   
    Woluminy Azure NetApp Files obsługują do 255 migawek. Możliwość przechowywania dużej liczby niewielkich wpływów, często tworzonych migawek, zwiększa prawdopodobieństwo pomyślnego przywrócenia odpowiedniej wersji danych.

* Migawki zapewniają ***widoczność użytkownika** _ i _ *_odzyskiwanie plików_* *.   
Wysoka wydajność, skalowalność i stabilność technologii migawek Azure NetApp Files, co oznacza, że zapewnia doskonałe kopie zapasowe online dla odzyskiwania opartego na użytkownikach. Migawki mogą być udostępniane dla plików, katalogów lub operacji przywracania woluminu. Dodatkowe rozwiązania umożliwiają kopiowanie kopii zapasowych do magazynu w trybie offline lub [replikowanie wielu regionów](cross-region-replication-introduction.md) w celu przechowywania lub odzyskiwania po awarii.

## <a name="ways-to-create-snapshots"></a>Sposoby tworzenia migawek   

Do tworzenia i konserwowania migawek można używać kilku metod:

* Ręcznie (na żądanie), przy użyciu:   
    * [Azure Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [interfejs API REST](/rest/api/netapp/snapshots), [Azure CLI](/cli/azure/netappfiles/snapshot)lub narzędzia [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)
    * Skrypty (zobacz [przykłady](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* Zautomatyzowane, przy użyciu:
    * Zasady dotyczące migawek, za pomocą narzędzia [Azure Portal](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), [interfejsu API REST](/rest/api/netapp/snapshotpolicies), [wiersza polecenia platformy Azure](/cli/azure/netappfiles/snapshot/policy)lub narzędzi [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy)
    * Narzędzia migawek spójnych z aplikacjami, takie jak [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Jak woluminy i migawki są replikowane w wielu regionach na potrzeby odzyskiwania po awarii  

Azure NetApp Files obsługuje [replikację między regionami](cross-region-replication-introduction.md) na potrzeby odzyskiwania po awarii (Dr). Azure NetApp Files replikacji między regionami używa technologii SnapMirror. Tylko zmienione bloki są wysyłane przez sieć w skompresowanym, wydajnym formacie. Po zainicjowaniu replikacji między różnymi regionami między woluminami cała zawartość woluminu (czyli bloków przechowywanych danych) jest transferowana tylko raz. Ta operacja jest nazywana *transferem bazowym*. Po przeniesieniu początkowym transferowane są tylko zmienione bloki (przechwycone w migawce). Wynikiem jest asynchroniczna replika 1:1 woluminu źródłowego, w tym wszystkie migawki. To zachowanie jest zgodne z mechanizmem replikacji pełnej i przyrostowej. Ta technologia minimalizuje ilość danych wymaganych do replikacji w regionach, co pozwala zaoszczędzić koszty transferu danych. Skraca również czas replikacji. Istnieje możliwość osiągnięcia mniejszego celu punktu odzyskiwania (RPO), ponieważ więcej migawek można tworzyć i przenosić częściej przy użyciu ograniczonych transferów danych. Ponadto nie ma potrzeby stosowania mechanizmów replikacji opartych na hoście, co pozwala uniknąć ponoszenia opłat za maszyny wirtualne i licencje na oprogramowanie.

Na poniższym diagramie przedstawiono ruch migawek w scenariuszach replikacji między regionami: 

![Diagram przedstawiający ruch migawek w scenariuszach replikacji między regionami](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Sposoby przywracania danych z migawek  

Technologia Azure NetApp Files migawka znacznie zwiększa częstotliwość i niezawodność kopii zapasowych. Wiąże się to z minimalnym obciążeniem wydajności i można go bezpiecznie utworzyć na aktywnym woluminie. Migawki Azure NetApp Files pozwalają na niemal natychmiastowe, bezpieczne i opcjonalne przywracanie zarządzane przez użytkownika. W tej sekcji opisano różne sposoby uzyskiwania dostępu do danych lub przywracania ich z migawek Azure NetApp Files.

### <a name="restoring-files-or-directories-from-snapshots"></a>Przywracanie plików lub katalogów z migawek 

Jeśli [widoczność ścieżki migawek](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) nie jest ustawiona na `hidden` , użytkownicy mogą uzyskiwać dostęp bezpośrednio do migawek w celu odzyskiwania po przypadkowym usunięciu, uszkodzeniu lub modyfikacji danych. Zabezpieczenia plików i katalogów są zachowywane w migawce, a migawki są tylko do odczytu według konstrukcji. W związku z tym przywracanie jest bezpieczne i proste. 

Na poniższym diagramie przedstawiono dostęp do pliku lub katalogu do migawki: 

![Diagram pokazujący dostęp do pliku lub katalogu do migawki](../media/azure-netapp-files/snapshot-file-directory-access.png)

Na diagramie migawka 1 zużywa tylko te bloki różnic między aktywnym woluminem a momentem utworzenia migawki. Jednak podczas uzyskiwania dostępu do migawki za pośrednictwem ścieżki migawek woluminu dane będą *wyświetlane* tak, jakby była to pełna pojemność woluminu podczas tworzenia migawki. Dzięki dostępowi do folderów migawek użytkownicy mogą automatycznie przywracać dane przez kopiowanie plików i katalogów z wybranej migawki.

Podobnie migawki w docelowym woluminie replikacji obejmującej wiele regionów są dostępne tylko do odczytu w przypadku odzyskiwania danych w regionie DR.  

Na poniższym diagramie przedstawiono dostęp do migawek w scenariuszach replikacji między regionami: 

![Diagram przedstawiający dostęp do migawek w replikacji między regionami](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Zobacz [przywracanie pliku z migawki przy użyciu klienta](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) dotyczącego przywracania pojedynczych plików lub katalogów z migawek.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Przywracanie (klonowanie) migawki do nowego woluminu

Migawki Azure NetApp Files można przywrócić na oddzielny, niezależny wolumin. Ta operacja jest niemal natychmiastowa, niezależnie od rozmiaru woluminu i zużytej pojemności. Nowo utworzony wolumin jest prawie natychmiast dostępny do uzyskania dostępu, podczas gdy rzeczywisty wolumin i bloki danych migawki są kopiowane. W zależności od rozmiaru woluminu i pojemności ten proces może zająć dużo czasu, w którym nie można usunąć woluminu nadrzędnego i migawki. Jednak do woluminu można już uzyskać dostęp po początkowym utworzeniu, podczas gdy proces kopiowania jest w toku w tle. Ta funkcja umożliwia szybkie tworzenie woluminów na potrzeby odzyskiwania danych lub klonowania woluminów na potrzeby testowania i programowania. W ramach procesu kopiowania danych użycie puli pojemności magazynu zostanie podwojone po zakończeniu przywracania, a nowy wolumin będzie zawierać pełną aktywną pojemność oryginalnej migawki. Po zakończeniu tego procesu wolumin będzie niezależny i nieskojarzony z oryginalnym woluminem, a woluminy źródłowe i migawka mogą być zarządzane lub usunięte niezależnie od nowego woluminu.

Na poniższym diagramie przedstawiono nowy wolumin utworzony przez Przywracanie (klonowanie) migawki:   

![Diagram pokazujący nowy wolumin utworzony przez przywrócenie migawki](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

Te same operacje można wykonać na replikowanych migawkach do woluminu odzyskiwania po awarii. Wszystkie migawki można przywrócić do nowego woluminu, nawet gdy replikacja między regionami pozostaje aktywna lub w toku. Ta funkcja umożliwia nieprzerwane tworzenie środowisk testowych i deweloperskich w regionie odzyskiwania po awarii, co pozwala na użycie danych, natomiast zreplikowane woluminy byłyby używane tylko do celów odzyskiwania po awarii. Ten przypadek użycia umożliwia odizolowanie testów i rozwoju od produkcji, eliminując potencjalny wpływ na środowiska produkcyjne.  

Na poniższym diagramie przedstawiono przywracanie woluminu (klonowanie) przy użyciu migawki woluminu docelowego DR podczas przeprowadzania replikacji między regionami:  

![Diagram przedstawiający przywracanie woluminu przy użyciu migawki woluminu docelowego DR](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Zobacz [przywracanie migawki do nowego woluminu](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) na temat operacji przywracania woluminu.

### <a name="restoring-reverting-a-snapshot-in-place"></a>Przywracanie (Przywracanie) migawki w miejscu

W niektórych przypadkach, ponieważ nowy wolumin będzie zużywał pojemność magazynu, utworzenie nowego woluminu na podstawie migawki może być niepotrzebne lub nie jest wymagane. Aby szybko odzyskać dane po uszkodzeniu danych (na przykład o uszkodzeniu bazy danych lub atakach z wykorzystaniem oprogramowania wymuszającego okup), może być bardziej odpowiednie do przywrócenia migawki w samym woluminie. Tę operację można wykonać przy użyciu funkcji przywracania migawek Azure NetApp Files. Ta funkcja umożliwia szybkie przywrócenie woluminu do stanu, w którym został wykonany określony migawkę. W większości przypadków przywrócenie woluminu jest znacznie szybsze niż przywrócenie pojedynczych plików z migawki do aktywnego systemu plików, szczególnie w przypadku dużych woluminów TiB. 

Przywrócenie migawki woluminu jest niemal chwilowe i trwa zaledwie kilka sekund, nawet w przypadku największych woluminów. Metadane woluminu aktywnego (*tabela inode*) są zastępowane metadanymi migawek od momentu utworzenia migawki, a tym samym wycofywanie woluminu do tego określonego punktu w czasie. Nie trzeba kopiować bloków danych, aby przywracanie zaczęło obowiązywać. W związku z tym większa ilość miejsca jest wydajna niż przywracanie migawki do nowego woluminu. 

Na poniższym diagramie przedstawiono wolumin, który przywraca wcześniejszą migawkę:  

![Diagram przedstawiający wolumin przywracający wcześniejszą migawkę](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> Dane aktywnego systemu plików, które zostały zapisaniu i migawki wykonane po wybranej migawce, zostaną utracone. Operacja przywrócenia migawki spowoduje zamianę wszystkich danych w woluminie przeznaczonym na dane w wybranej migawce. Po wybraniu migawki należy zwrócić uwagę na zawartość migawki i datę utworzenia. Nie można cofnąć operacji przywracania migawki.  

Zobacz [przywracanie woluminu przy użyciu migawki przywracanie](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) informacji o sposobach korzystania z tej funkcji.

## <a name="how-snapshots-are-deleted"></a>Jak są usuwane migawki 

Migawki wykorzystują pojemność magazynu. W związku z tym nie są one zazwyczaj przechowywane przez czas nieokreślony. W celu zapewnienia ochrony, przechowywania i odzyskiwania danych wiele migawek (tworzonych w różnych punktach w czasie) jest zwykle w trybie online, w zależności od wymagań dotyczących punktu odzyskiwania, RTO i umowy SLA dotyczącej przechowywania. Jednak starsze migawki często nie muszą być przechowywane w usłudze magazynu i może być konieczne ich usunięcie w celu zwolnienia miejsca. Każda migawka może zostać usunięta (niekoniecznie w kolejności tworzenia) przez administratora w dowolnym momencie. 

> [!IMPORTANT]
> Operacji usuwania migawki nie można cofnąć. Kopie zapasowe woluminów w trybie offline należy zachować na potrzeby ochrony danych i ich przechowywania. 

Po usunięciu migawki wszystkie wskaźniki z tej migawki do istniejących bloków danych zostaną usunięte. Gdy blok danych nie ma więcej wskaźników wskazujących na niego (przez aktywny wolumin lub inne migawki w woluminie), blok danych jest zwracany do wolnego miejsca na woluminie do użytku w przyszłości. W związku z tym usunięcie migawek zwykle zwalnia więcej pojemności woluminu niż usuwanie danych z aktywnego woluminu, ponieważ bloki danych często są przechwytywane w wcześniej utworzonych migawkach. 

Na poniższym diagramie przedstawiono wpływ usunięcia z woluminu Snapshot 3 z magazynu na dysk:  

![Diagram przedstawiający efekt użycia magazynu przez usunięcie migawki](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Należy koniecznie [monitorować użycie woluminów i migawek](azure-netapp-files-metrics.md#volumes) oraz zrozumieć, jak działa aplikacja, aktywny wolumin i użycie migawek. 

Zobacz [usuwanie migawek](azure-netapp-files-manage-snapshots.md#delete-snapshots) dotyczących sposobu zarządzania usuwaniem migawek. Zobacz [Zarządzanie zasadami migawek](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) o sposobie automatyzacji tego procesu.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie migawkami przy użyciu usługi Azure NetApp Files](azure-netapp-files-manage-snapshots.md)
* [Monitoruj metryki woluminów i migawek](azure-netapp-files-metrics.md#volumes)
* [Rozwiązywanie problemów z zasadami migawek](troubleshoot-snapshot-policies.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Film Azure NetApp Files migawek 101](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Przegląd Azure NetApp Files migawek](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)



