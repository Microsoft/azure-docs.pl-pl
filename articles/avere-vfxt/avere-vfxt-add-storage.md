---
title: Konfigurowanie magazynu avere vFXT — Azure
description: Dowiedz się, jak dodać system przechowywania zaplecza dla klastra w programie avere vFXT for Azure. Jeśli utworzono kontener obiektów blob platformy Azure z klastrem, jest on gotowy do użycia.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: c17d3c7cd2cf6fe5bca725cf94344b2d2cb50bf2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001528"
---
# <a name="configure-storage"></a>Konfigurowanie magazynu

Ten krok powoduje skonfigurowanie systemu magazynu zaplecza dla klastra vFXT.

> [!TIP]
> Jeśli utworzono nowy kontener obiektów blob platformy Azure wraz z klastrem avere vFXT, ten kontener jest już skonfigurowany i gotowy do użycia.

Wykonaj te instrukcje, jeśli nie utworzono nowego kontenera obiektów blob z klastrem lub jeśli chcesz dodać dodatkowy sprzęt lub system magazynu w chmurze.

Istnieją dwa główne zadania:

1. [Utwórz podstawowy plik](#create-a-core-filer), który łączy klaster vFXT z istniejącym systemem magazynu lub kontenerem konta usługi Azure Storage.

1. [Utwórz połączenie przestrzeni nazw](#create-a-junction), które określa ścieżkę, którą będą instalować klienci.

W tych krokach użyto panelu sterowania avere. Aby dowiedzieć się, jak z niej korzystać [, przeczytaj dostęp do klastra vFXT](avere-vfxt-cluster-gui.md) .

## <a name="create-a-core-filer"></a>Tworzenie podstawowego pliku

"Podstawowy system plików" to vFXT termin dla systemu magazynu zaplecza. Magazyn może być sprzętowym urządzeniem NAS, takim jak NetApp lub Isilon, lub może być magazynem obiektów w chmurze. Więcej informacji na temat plików podstawowych można znaleźć w [podręczniku ustawień klastra avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Aby dodać podstawowy plik, wybierz jeden z dwóch głównych typów plików podstawowych:

* [Podstawowy plik nas](#nas-core-filer) — opis sposobu dodawania plików w usłudze nas Core
* [Podstawowy plik usługi Azure Storage w chmurze](#azure-blob-storage-cloud-core-filer) — opis sposobu dodawania kontenera magazynu obiektów blob platformy Azure jako podstawowego pliku w chmurze

### <a name="nas-core-filer"></a>Podstawowy plik NAS

Podstawowy plik usługi NAS może być lokalnym urządzeniem NetApp lub Isilon albo punktem końcowym usługi NAS w chmurze. System magazynu musi mieć niezawodne połączenie o dużej szybkości z klastrem avere vFXT — na przykład połączenie 1GBps ExpressRoute (nie sieć VPN) i musi nadawać głównemu dostępowi do używanego eksportu serwerów NAS.

Wykonaj następujące kroki, aby dodać podstawowy plik NAS:

1. W panelu sterowania avere kliknij kartę **Ustawienia** w górnej części ekranu.

1. Kliknij pozycję **podstawowe** pliki  >  **zarządzanymi plikami głównymi** po lewej stronie.

1. Kliknij pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający stronę Dodawanie nowej podstawowej klasy plików z kursorem nad przyciskiem Utwórz](media/avere-vfxt-add-core-filer-start.png)

1. Wprowadź wymagane informacje w Kreatorze:

   * Nazwij podstawowy plik.
   * Podaj w pełni kwalifikowaną nazwę domeny (FQDN), jeśli jest dostępna. W przeciwnym razie podaj adres IP lub nazwę hosta, który jest rozpoznawany jako plik podstawowy.
   * Wybierz klasę pliku z listy. Jeśli nie masz pewności, wybierz pozycję **inne**.

     ![Zrzut ekranu przedstawiający stronę Dodawanie nowego podstawowego pliku z nazwą pliku podstawowego i jego w pełni kwalifikowaną nazwą domeny](media/avere-vfxt-add-core-filer.png)
  
   * Kliknij przycisk **dalej** i wybierz zasady pamięci podręcznej.
   * Kliknij pozycję **Dodaj plik**.
   * Aby uzyskać bardziej szczegółowe informacje, zobacz [Dodawanie nowego pliku usługi nas Core](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) w podręczniku ustawień klastra avere.

Następnie przejdź do [tworzenia rozgałęzienia](#create-a-junction).  

### <a name="azure-blob-storage-cloud-core-filer"></a>Podstawowy plik usługi Azure Blob Storage Cloud

Aby korzystać z usługi Azure Blob Storage jako magazynu zaplecza klastra vFXT, potrzebny jest pusty kontener do dodania jako podstawowy plik.

Dodanie magazynu obiektów BLOB do klastra wymaga następujących zadań:

* Utwórz konto magazynu (krok 1 poniżej)
* Tworzenie pustego kontenera obiektów BLOB (kroki 2-3)
* Dodaj klucz dostępu do magazynu jako poświadczenie w chmurze dla klastra vFXT (kroki 4-6)
* Dodawanie kontenera obiektów BLOB jako podstawowego pliku dla klastra vFXT (kroki 7-9)
* Tworzenie rozgałęzienia przestrzeni nazw używanej przez klientów do instalowania podstawowego pliku programu ([Utwórz połączenie](#create-a-junction), takie samo jak w przypadku sprzętu i magazynu w chmurze)

> [!TIP]
> Jeśli tworzysz nowy kontener obiektów BLOB podczas tworzenia avere vFXT dla klastra platformy Azure, szablon wdrożenia automatycznie skonfiguruje kontener jako plik podstawowy. (Jest to również prawdziwe, jeśli używasz skryptu tworzenia, który jest dostępny na żądanie). Nie trzeba później konfigurować podstawowego pliku.
>
> Narzędzie do tworzenia klastra wykonuje następujące zadania konfiguracji:
>
> * Tworzy nowy kontener obiektów BLOB na podanym koncie magazynu
> * Definiuje kontener jako podstawowy plik
> * Tworzy połączenie przestrzeni nazw z kontenerem
> * Tworzy punkt końcowy usługi magazynu w sieci wirtualnej klastra

Aby dodać magazyn obiektów BLOB po utworzeniu klastra, wykonaj następujące kroki.

1. Utwórz konto magazynu ogólnego przeznaczenia w wersji 2 przy użyciu następujących ustawień:

   * **Subskrypcja** — taka sama jak klaster vFXT
   * **Grupa zasobów** — taka sama jak grupa klastra vFXT (opcjonalnie)
   * **Lokalizacja** — taka sama jak klaster vFXT
   * **Wydajność** — standardowa (Usługa Premium Storage nie jest obsługiwana)
   * **Rodzaj konta** — ogólnego przeznaczenia w wersji 2 (StorageV2)
   * **Replikacja** — Magazyn lokalnie nadmiarowy (LRS)
   * **Warstwa dostępu** — gorąca
   * **Wymagany bezpieczny transfer** — Wyłącz tę opcję (wartość nie jest wartością domyślną)
   * **Sieci wirtualne** — niewymagane

   Możesz użyć Azure Portal lub kliknąć przycisk "wdróż na platformie Azure" poniżej.

   [![przycisk służący do tworzenia konta magazynu](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Po utworzeniu konta przejdź do strony konto magazynu.

   ![Nowe konto magazynu w Azure Portal](media/avere-vfxt-new-storage-acct.png)

1. Utwórz nowy kontener obiektów blob: kliknij pozycję **kontenery** na stronie Przegląd, a następnie kliknij pozycję **+ kontener**. Użyj dowolnej nazwy kontenera i upewnij się, że dostęp jest ustawiony na wartość **Private**.

   ![Strona obiektów blob magazynu z przyciskiem + kontener w kółku i nowy kontener tworzony na stronie podręcznej](media/avere-vfxt-new-blob.png)

1. Pobierz klucz konta usługi Azure Storage, klikając pozycję **klucze dostępu** w obszarze **Ustawienia**. Skopiuj jeden z podanych kluczy.

   ![Azure Portal graficzny interfejs użytkownika do kopiowania klucza](media/avere-vfxt-copy-storage-key.png)

1. Otwórz Panel sterowania avere dla klastra. Kliknij przycisk **Ustawienia**, a następnie otwórz pozycję **Cluster**  >  **poświadczenia w chmurze** klastra w okienku nawigacji po lewej stronie. Na stronie poświadczenia chmury kliknij pozycję **Dodaj poświadczenie**.

   ![Kliknij przycisk Dodaj poświadczenia na stronie Konfiguracja poświadczeń w chmurze](media/avere-vfxt-new-credential-button.png)

1. Wypełnij poniższe informacje, aby utworzyć poświadczenia dla podstawowego pliku w chmurze:

   | Pole | Wartość |
   | --- | --- |
   | Nazwa poświadczenia | nazwa opisowa |
   | Typ usługi | (Wybierz klucz dostępu do usługi Azure Storage) |
   | Dzierżawa | nazwa konta magazynu |
   | Subskrypcja | subscription ID |
   | Klucz dostępu do magazynu | Klucz konta usługi Azure Storage (skopiowany w poprzednim kroku) |

   Kliknij przycisk **Prześlij**.

   ![Formularz ukończonych poświadczeń w chmurze w panelu sterowania avere](media/avere-vfxt-new-credential-submit.png)

1. Następnie Utwórz podstawowy plik. W lewej części panelu sterowania avere kliknij pozycję **podstawowe** pliki  >   **zarządzania plikami podstawowymi**.

1. Kliknij przycisk **Utwórz** na stronie **Zarządzanie ustawieniami plików podstawowych** .

1. Wypełnij Kreatora:

   * Wybierz typ pliku **Cloud**.
   * Nazwij nowy plik podstawowy i kliknij przycisk **dalej**.
   * Zaakceptuj domyślne zasady pamięci podręcznej i przejdź do trzeciej strony.
   * W obszarze **Typ usługi** wybierz pozycję **Azure Storage**.
   * Wybierz utworzone wcześniej poświadczenie.
   * Ustaw **zawartość zasobnika** na **pustą**
   * Zmień **weryfikację certyfikatu** na **wyłączony**
   * Zmień **tryb kompresji** na **Brak**
   * Kliknij przycisk **Dalej**.
   * Na czwartej stronie Wprowadź nazwę kontenera w polu **Nazwa zasobnika** jako *storage_account_name* / *container_name*.
   * Opcjonalnie Ustaw dla opcji **typ szyfrowania** **wartość Brak**.  Usługa Azure Storage jest domyślnie szyfrowana.
   * Kliknij pozycję **Dodaj plik**.

   Aby uzyskać bardziej szczegółowe informacje, przeczytaj temat [Dodawanie nowego pliku w chmurze](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) w podręczniku konfiguracji klastra avere.

Strona zostanie odświeżona lub można odświeżyć stronę, aby wyświetlić nowy plik podstawowy.

Następnie należy [utworzyć połączenie](#create-a-junction).

## <a name="create-a-junction"></a>Tworzenie połączenia

Połączenie jest ścieżką utworzoną dla klientów. Klienci instalują ścieżkę i docierają do wybranego miejsca docelowego.

Można na przykład utworzyć mapowanie do `/vfxt/files` NetApp podstawowego pliku `/vol0/data` eksportu i `/project/resources` podkatalogu.

Więcej informacji o połączeniach można znaleźć w [sekcji przestrzeń nazw podręcznika konfiguracji klastra avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Wykonaj następujące kroki w interfejsie avere panelu sterowania:

* **VServer**  >  W lewym górnym rogu kliknij pozycję vserver **obszar nazw** .
* Podaj ścieżkę przestrzeni nazw rozpoczynającą się od znaku/(ukośnik), na przykład ``/vfxt/data`` .
* Wybierz podstawowy plik.
* Wybierz eksport pliku podstawowego.
* Kliknij przycisk **Dalej**.

  ![Zrzut ekranu przedstawiający stronę "Dodawanie nowego połączenia" z polami zakończono dla połączenia, podstawowego pliku i eksportu](media/avere-vfxt-add-junction.png)

Połączenie zostanie wyświetlone po kilku sekundach. Utwórz dodatkowe połączenia zgodnie z wymaganiami.

Po utworzeniu połączenia klienci używają ścieżki przestrzeni nazw, aby uzyskać dostęp do plików z systemu magazynu.

## <a name="next-steps"></a>Następne kroki

* [Instalowanie klastra Avere vFXT](avere-vfxt-mount-clients.md)
* Poznaj wydajne metody [przenoszenia danych do nowego kontenera obiektów BLOB](avere-vfxt-data-ingest.md)
