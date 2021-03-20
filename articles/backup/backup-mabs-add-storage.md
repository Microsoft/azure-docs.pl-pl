---
title: Użyj Nowoczesny magazyn kopii zapasowych z Azure Backup Server
description: Dowiedz się więcej o nowych funkcjach w Azure Backup Server. W tym artykule opisano sposób uaktualniania instalacji serwera kopii zapasowej.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: b077296e58e1193e454a686a392d802e905500a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91292901"
---
# <a name="add-storage-to-azure-backup-server"></a>Dodawanie magazynu do usługi Azure Backup Server

Azure Backup Server v2 i nowszych obsługuje Nowoczesny magazyn kopii zapasowych oferujące oszczędności magazynu wynoszące 50%, kopie zapasowe, które są trzy razy szybsze i wydajniejszy magazyn. Oferuje również magazyn z obsługą obciążeń.

> [!NOTE]
> Aby korzystać z Nowoczesny magazyn kopii zapasowych, należy uruchomić polecenie Backup Server v2 lub V3 w systemie Windows Server 2016 lub V3 w systemie Windows Server 2019.
> W przypadku uruchomienia programu Backup Server v2 w starszej wersji systemu Windows Server Azure Backup Server nie może korzystać z Nowoczesny magazyn kopii zapasowych. Zamiast tego chroni obciążenia w taki sposób, w jaki działa serwer zapasowy v1. Aby uzyskać więcej informacji, zobacz [macierz ochrony](backup-mabs-protection-matrix.md)wersji serwera kopii zapasowej.
>
> Aby osiągnąć ulepszone wykonywanie kopii zapasowych, zalecamy wdrożenie serwera usługi MAB v3 z magazynem warstwowym w systemie Windows Server 2019. Aby uzyskać instrukcje dotyczące konfigurowania magazynu warstwowego, zapoznaj się z artykułem programu DPM "[set up the MB with Tiered Storage](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage)".

## <a name="volumes-in-backup-server"></a>Woluminy na serwerze kopii zapasowej

Serwer kopii zapasowej w wersji 2 lub nowszej akceptuje woluminy magazynu. Po dodaniu woluminu serwer kopii zapasowej sformatuje wolumin do systemu plików ReFS, który wymaga Nowoczesny magazyn kopii zapasowych. Aby dodać wolumin i rozszerzyć go później, jeśli jest to konieczne, zalecamy użycie tego przepływu pracy:

1. Skonfiguruj serwer kopii zapasowej na maszynie wirtualnej.
2. Utwórz wolumin na dysku wirtualnym w puli magazynów:
    1. Dodaj dysk do puli magazynów i Utwórz dysk wirtualny przy użyciu układu prostego.
    2. Dodaj dodatkowe dyski i rozwiń dysk wirtualny.
    3. Utwórz woluminy na dysku wirtualnym.
3. Dodaj woluminy do serwera zapasowego.
4. Skonfiguruj magazyn obsługujący obciążenie.

## <a name="create-a-volume-for-modern-backup-storage"></a>Tworzenie woluminu dla Nowoczesny magazyn kopii zapasowych

Użycie serwera zapasowego w wersji 2 lub nowszej z woluminami jako magazyn dyskowy może ułatwić zachowanie kontroli nad magazynem. Wolumin może być pojedynczym dyskiem. Jeśli jednak chcesz w przyszłości zwiększyć magazyn, Utwórz wolumin poza dyskiem utworzonym przy użyciu funkcji miejsca do magazynowania. Może to pomóc w rozwinięciu woluminu dla magazynu kopii zapasowych. Ta sekcja zawiera najlepsze rozwiązania dotyczące tworzenia woluminów przy użyciu tej konfiguracji.

1. W Menedżer serwera wybierz pozycję **usługi plików i magazynowania**  >  **woluminy**  >  **Pule magazynów**. W obszarze **dyski fizyczne** wybierz pozycję **Nowa pula magazynu**.

    ![Utwórz nową pulę magazynów](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. W polu listy rozwijanej **zadania** wybierz pozycję **nowy dysk wirtualny**.

    ![Dodawanie dysku wirtualnego](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Wybierz pulę magazynów, a następnie wybierz pozycję **Dodaj dysk fizyczny**.

    ![Dodawanie dysku fizycznego](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Wybierz dysk fizyczny, a następnie wybierz opcję **Zwiększ dysk wirtualny**.

    ![Zwiększ dysk wirtualny](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Wybierz dysk wirtualny, a następnie wybierz pozycję **Nowy wolumin**.

    ![Utwórz nowy wolumin](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. W oknie dialogowym **Wybierz serwer i dysk** wybierz serwer i nowy dysk. Następnie wybierz pozycję **Dalej**.

    ![Wybierz serwer i dysk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Dodawanie woluminów do magazynu dyskowego serwera kopii zapasowej

> [!NOTE]
>
> - Dodaj tylko jeden dysk do puli, aby zachować liczbę kolumn na 1. Następnie można dodać dyski odpowiednio do wymagań.
> - W przypadku dodania wielu dysków do puli magazynów w ramach języka go liczba dysków jest przechowywana jako liczba kolumn. Po dodaniu większej liczby dysków może to być tylko wielokrotność liczby kolumn.

Aby dodać wolumin do serwera kopii zapasowej, w okienku **zarządzania** ponownie Przeskanuj magazyn, a następnie wybierz pozycję **Dodaj**. Zostanie wyświetlona lista wszystkich woluminów dostępnych do dodania do magazynu serwera kopii zapasowych. Po dodaniu dostępnych woluminów do listy wybranych woluminów można nadać im przyjazną nazwę ułatwiającą zarządzanie nimi. Aby sformatować te woluminy do systemu plików ReFS, aby serwer kopii zapasowych mógł korzystać z zalet Nowoczesny magazyn kopii zapasowych, wybierz **przycisk OK**.

![Dodaj dostępne woluminy](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Konfigurowanie magazynu obsługującego obciążenie

W przypadku magazynu z obsługą obciążeń można wybrać woluminy, które umożliwiają preferencyjne przechowywanie niektórych rodzajów obciążeń. Można na przykład ustawić drogie woluminy obsługujące dużą liczbę operacji wejścia/wyjścia na sekundę (IOPS) do przechowywania tylko obciążeń wymagających częstych, dużych kopii zapasowych. Przykładem jest SQL Server z dziennikami transakcji. Inne obciążenia, których kopie zapasowe są tworzone rzadziej, takich jak maszyny wirtualne, mogą być tworzone z niedrogimi woluminami.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Magazyn obsługujący obciążenia można skonfigurować za pomocą polecenia cmdlet programu PowerShell Update-DPMDiskStorage, które aktualizuje właściwości woluminu w puli magazynów na Azure Backup Server.

Składnia:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Poniższy zrzut ekranu przedstawia polecenie cmdlet Update-DPMDiskStorage w oknie programu PowerShell.

![Update-DPMDiskStorage polecenie w oknie programu PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Zmiany wprowadzane przy użyciu programu PowerShell są odzwierciedlone na serwerze kopii zapasowej konsola administratora.

![Dyski i woluminy w konsola administratora](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrowanie starszej wersji magazynu do Nowoczesny magazyn kopii zapasowych

Po przeprowadzeniu uaktualnienia do programu Backup Server v2 lub instalacji systemu operacyjnego w systemie Windows Server 2016 zaktualizuj grupy ochrony, aby użyć Nowoczesny magazyn kopii zapasowych. Domyślnie grupy ochrony nie są zmieniane. Są one nadal działać, ponieważ zostały początkowo skonfigurowane.

Aktualizowanie grup ochrony w celu korzystania z funkcji Modern Backup Storage jest opcjonalne. Aby zaktualizować grupę ochrony, Zatrzymaj ochronę wszystkich źródeł danych przy użyciu opcji Zachowaj dane. Następnie Dodaj źródła danych do nowej grupy ochrony.

1. W konsola administratora wybierz funkcję **ochrony** . Na liście **członek grupy ochrony** kliknij prawym przyciskiem myszy element członkowski, a następnie wybierz polecenie **Zatrzymaj ochronę elementu członkowskiego**.

   ![Zatrzymaj ochronę elementu członkowskiego](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. W oknie dialogowym **usuwanie z grupy** Sprawdź zajęte miejsce na dysku i dostępne wolne miejsce dla puli magazynów. Domyślnie punkty odzyskiwania pozostają na dysku i wygasają zgodnie ze skojarzonymi z nimi zasadami przechowywania. Wybierz przycisk **OK**.

   Jeśli chcesz natychmiast przywrócić zajęte miejsce na dysku do puli magazynu, zaznacz pole wyboru **Usuń replikę na dysku** , aby usunąć dane kopii zapasowej (i punkty odzyskiwania) skojarzone z tym elementem członkowskim.

   ![Okno dialogowe Usuwanie z grupy](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Utwórz grupę ochrony korzystającą z Nowoczesny magazyn kopii zapasowych. Uwzględnij niechronione źródła danych.

## <a name="add-disks-to-increase-legacy-storage"></a>Dodawanie dysków w celu zwiększenia ilości miejsca w starszej wersji

Jeśli chcesz używać starszej wersji magazynu z serwerem kopii zapasowych, może być konieczne dodanie dysków w celu zwiększenia starszej wersji magazynu.

Aby dodać magazyn dyskowy:

1. W Konsola administratora wybierz pozycję **Zarządzanie**  >  **Disk Storage**  >  **Dodaj**.

    ![Okno dialogowe Dodawanie Disk Storage](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. W oknie dialogowym **dodawanie Disk Storage** wybierz pozycję **Dodaj dyski**.

3. Na liście dostępnych dysków wybierz dyski, które chcesz dodać, wybierz pozycję **Dodaj**, a następnie wybierz przycisk **OK**.

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu serwera kopii zapasowej należy dowiedzieć się, jak przygotować serwer lub rozpocząć ochronę obciążeń.

- [Przygotowywanie obciążeń serwera kopii zapasowej](backup-azure-microsoft-azure-backup.md)
- [Wykonywanie kopii zapasowej serwera VMware przy użyciu usługi Backup Server](backup-azure-backup-server-vmware.md)
- [Używanie usługi Backup Server do tworzenia kopii zapasowej programu SQL Server](backup-azure-sql-mabs.md)
