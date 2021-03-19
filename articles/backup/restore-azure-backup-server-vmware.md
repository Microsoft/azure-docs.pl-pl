---
title: Przywracanie maszyn wirtualnych VMware przy użyciu usługi Azure Backup Server
description: Użyj Azure Backup Server (serwera usługi MAB), aby przywrócić maszyny wirtualne programu VMware działające na serwerze VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: b3f61aa828db39aeb11b1ce46a850d9a5b868653
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88263524"
---
# <a name="restore-vmware-virtual-machines"></a>Przywróć maszyny wirtualne VMware

W tym artykule wyjaśniono, jak używać Microsoft Azure Backup Server (serwera usługi MAB) do przywracania punktów odzyskiwania maszyn wirtualnych programu VMware. Aby zapoznać się z omówieniem dotyczącym korzystania z usługi serwera usługi MAB do odzyskiwania danych, zobacz [odzyskiwanie chronionych danych](./backup-azure-alternate-dpm-server.md). W konsola administratora serwera usługi MAB istnieją dwa sposoby znajdowania odzyskiwalnych danych — wyszukiwanie i przeglądanie. Podczas odzyskiwania danych można lub nie chcieć przywracać danych ani maszyn wirtualnych do tej samej lokalizacji. Z tego powodu serwera usługi MAB obsługuje trzy opcje odzyskiwania kopii zapasowych maszyn wirtualnych VMware:

* **Odzyskiwanie do oryginalnej lokalizacji (OLR)** — umożliwia przywrócenie chronionej maszyny wirtualnej do jej oryginalnej lokalizacji przy użyciu funkcji OLR. Maszynę wirtualną można przywrócić do jej oryginalnej lokalizacji tylko wtedy, gdy nie dodano ani nie usunięto dysków, ponieważ wystąpiła kopia zapasowa. Jeśli dyski zostały dodane lub usunięte, musisz użyć odzyskiwania do lokalizacji alternatywnej.

* **Odzyskiwanie do lokalizacji alternatywnej (ALR)** — gdy brakuje oryginalnej maszyny wirtualnej lub nie chcesz zakłócać oryginalnej maszyny wirtualnej, ODZYSKAj maszynę wirtualną do lokalizacji alternatywnej. Aby odzyskać maszynę wirtualną do lokalizacji alternatywnej, należy podać lokalizację hosta ESXi, puli zasobów, folderu oraz magazynu danych magazynu i ścieżki. Aby ułatwić odróżnienie przywróconej maszyny wirtualnej od oryginalnej maszyny wirtualnej, serwera usługi MAB dołącza "-odzyskany" do nazwy maszyny wirtualnej.

* **Odzyskiwanie poszczególnych lokalizacji plików (ILR)** — Jeśli chroniona maszyna wirtualna jest maszyną wirtualną z systemem Windows Server, można odzyskać pojedyncze pliki/foldery w ramach maszyny wirtualnej za pomocą funkcji ILR serwera usługi MAB. Aby odzyskać poszczególne pliki, zapoznaj się z procedurą w dalszej części tego artykułu.

## <a name="restore-a-recovery-point"></a>Przywracanie punktu odzyskiwania

1. W konsola administratora serwera usługi MAB wybierz pozycję **Widok odzyskiwania**.

2. Za pomocą okienka przeglądania Przeglądaj lub Filtruj, aby znaleźć maszynę wirtualną, którą chcesz odzyskać. Po wybraniu maszyny wirtualnej lub folderu punkty odzyskiwania w okienku zostaną wyświetlone dostępne punkty odzyskiwania.

    ![Dostępne punkty odzyskiwania](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. W polu **punkty odzyskiwania dla** Użyj menu kalendarz i listy rozwijanej, aby wybrać datę utworzenia punktu odzyskiwania. Pogrubienie dat kalendarza ma dostępne punkty odzyskiwania.

4. Na Wstążce narzędzi wybierz polecenie **Odzyskaj** , aby otworzyć **Kreatora odzyskiwania**.

    ![Kreator odzyskiwania, przeglądanie wyboru odzyskiwania](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Wybierz pozycję **dalej** , aby przejść do ekranu **Określanie opcji odzyskiwania** .

6. Aby włączyć ograniczanie przepustowości sieci, na ekranie **Określ opcje odzyskiwania** wybierz pozycję **Modyfikuj**. Aby wyłączyć funkcję ograniczania przepustowości sieci, wybierz pozycję **dalej**. Dla maszyn wirtualnych VMware nie są dostępne żadne inne opcje na tym ekranie kreatora. Jeśli zdecydujesz się zmodyfikować przepustowość sieci, w oknie dialogowym ograniczenia wybierz opcję **Włącz ograniczenie przepustowości sieci** , aby je włączyć. Po włączeniu Skonfiguruj **Ustawienia** i **harmonogram pracy**.

7. Na ekranie **Wybieranie typu odzyskiwania** wybierz, czy chcesz odzyskać do oryginalnego wystąpienia, czy też do nowej lokalizacji. Następnie wybierz przycisk **Dalej**.

     * W przypadku wybrania opcji **Odzyskaj do oryginalnego wystąpienia** nie trzeba wprowadzać więcej opcji w kreatorze. Dane dla oryginalnego wystąpienia są używane.

     * Jeśli wybierzesz opcję **Odzyskaj jako maszynę wirtualną na dowolnym hoście**, na ekranie **określ miejsce docelowe** podaj informacje dotyczące **hosta ESXi, puli zasobów, folderu** i **ścieżki**.

      ![Wybierz typ odzyskiwania](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Na ekranie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz polecenie **Odzyskaj** , aby rozpocząć proces odzyskiwania. Ekran **stan odzyskiwania** pokazuje postęp operacji odzyskiwania.

## <a name="restore-an-individual-file-from-a-vm"></a>Przywracanie pojedynczego pliku z maszyny wirtualnej

Można przywrócić pojedyncze pliki z chronionego punktu odzyskiwania maszyny wirtualnej. Ta funkcja jest dostępna tylko dla maszyn wirtualnych z systemem Windows Server. Przywracanie pojedynczych plików jest podobne do przywracania całej maszyny wirtualnej, z tą różnicą, że przed rozpoczęciem procesu odzyskiwania przejdziesz do dysku VMDK i wyszukasz żądane pliki. Aby odzyskać pojedynczy plik lub wybrać pliki z maszyny wirtualnej z systemem Windows Server:

>[!NOTE]
>Przywracanie pojedynczego pliku z maszyny wirtualnej jest dostępne tylko dla punktów odzyskiwania maszyn wirtualnych i dysków z systemem Windows.

1. W konsola administratora serwera usługi MAB wybierz pozycję Widok **odzyskiwania** .

2. Za pomocą okienka **przeglądania** Przeglądaj lub Filtruj, aby znaleźć maszynę wirtualną, którą chcesz odzyskać. Po wybraniu maszyny wirtualnej lub folderu **punkty odzyskiwania w okienku** zostaną wyświetlone dostępne punkty odzyskiwania.

    ![Okienko "punkty odzyskiwania dla"](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. W okienku **punkty odzyskiwania dla:** Użyj kalendarza, aby wybrać datę zawierającą żądane punkty odzyskiwania. W zależności od sposobu skonfigurowania zasad tworzenia kopii zapasowych daty mogą mieć więcej niż jeden punkt odzyskiwania. Po wybraniu dnia, w którym wykonano punkt odzyskiwania, upewnij się, że wybrano prawidłowy **czas odzyskiwania**. Jeśli wybrana data ma wiele punktów odzyskiwania, wybierz punkt odzyskiwania, wybierając go z menu rozwijanego godzina odzyskiwania. Po wybraniu punktu odzyskiwania zostanie wyświetlona lista elementów możliwych do odzyskania w okienku **ścieżka:** .

4. Aby znaleźć pliki do odzyskania, w okienku **ścieżki** kliknij dwukrotnie element w kolumnie element możliwy do **odzyskania** , aby go otworzyć. Wybierz plik, pliki lub foldery, które chcesz odzyskać. Aby zaznaczyć wiele elementów, naciśnij klawisz **Ctrl** podczas zaznaczania każdego elementu. Użyj okienka **ścieżka** , aby przeszukać listę plików lub folderów wyświetlanych w kolumnie element możliwy do **odzyskania** . **Lista wyszukiwania poniżej** nie przeszukiwania do podfolderów. Aby przeszukać podfoldery, kliknij dwukrotnie folder. Użyj przycisku do **góry** , aby przejść z folderu podrzędnego do folderu nadrzędnego. Można wybrać wiele elementów (plików i folderów), ale muszą one znajdować się w tym samym folderze nadrzędnym. Nie można odzyskać elementów z wielu folderów w ramach tego samego zadania odzyskiwania.

    ![Przegląd wyboru odzyskiwania](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Po wybraniu elementów do odzyskania na Wstążce narzędzia konsola administratora wybierz pozycję **Odzyskaj** , aby otworzyć **Kreatora odzyskiwania**. W Kreatorze odzyskiwania ekran **Przegląd opcji odzyskiwania** pokazuje wybrane elementy do odzyskania.

6. Aby włączyć ograniczanie przepustowości sieci, na ekranie **Określ opcje odzyskiwania** wybierz pozycję **Modyfikuj**. Aby wyłączyć funkcję ograniczania przepustowości sieci, wybierz pozycję **dalej**. Dla maszyn wirtualnych VMware nie są dostępne żadne inne opcje na tym ekranie kreatora. Jeśli zdecydujesz się zmodyfikować przepustowość sieci, w oknie dialogowym ograniczenia wybierz opcję **Włącz ograniczenie przepustowości sieci** , aby je włączyć. Po włączeniu Skonfiguruj **Ustawienia** i **harmonogram pracy**.
7. Na ekranie **Wybieranie typu odzyskiwania** wybierz pozycję **dalej**. Pliki lub foldery można odzyskiwać tylko do folderu sieciowego.
8. Na ekranie **określ miejsce docelowe** wybierz pozycję **Przeglądaj** , aby znaleźć lokalizację sieciową dla plików lub folderów. SERWERA usługi MAB tworzy folder, w którym są kopiowane wszystkie odzyskane elementy. Nazwa folderu ma prefiks, MABS_day-miesiąc — rok. W przypadku wybrania lokalizacji odzyskiwanych plików lub folderów zostaną podane szczegóły dotyczące tej lokalizacji (lokalizacji docelowej, ścieżki docelowej i dostępnego miejsca).

    ![Określ lokalizację do odzyskania plików](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. Na stronie **Określ opcje odzyskiwania** wybierz ustawienia zabezpieczeń, które mają zostać zastosowane. Można wybrać opcję modyfikacji ograniczenia przepustowości sieci, ale ograniczenie przepustowości jest domyślnie wyłączone. Ponadto odzyskiwanie i **powiadamianie** **sieci San** nie jest włączone.
10. Na ekranie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz polecenie **Odzyskaj** , aby rozpocząć proces odzyskiwania. Ekran **stan odzyskiwania** pokazuje postęp operacji odzyskiwania.

## <a name="next-steps"></a>Następne kroki

Informacje dotyczące rozwiązywania problemów podczas korzystania z Azure Backup Server można znaleźć w [przewodniku rozwiązywania problemów dotyczących Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
