---
title: Azure Portal — Ogranicz dostęp do programu Import/Export do dysków zarządzanych przy użyciu linków prywatnych
description: Włącz prywatne linki dla dysków zarządzanych przy użyciu Azure Portal. Umożliwienie bezpiecznego eksportowania i importowania dysków w sieci wirtualnej.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b80100216003e91fde54b5e555bafb755c942810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682923"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Użyj Azure Portal, aby ograniczyć dostęp do usługi Import/Export dla dysków zarządzanych przy użyciu linków prywatnych

Obsługa linków prywatnych dla dysków zarządzanych umożliwia ograniczenie eksportu i importu dysków zarządzanych w taki sposób, aby były one wykonywane tylko w ramach sieci wirtualnej platformy Azure. Można wygenerować identyfikator URI powiązanego sygnatury dostępu współdzielonego (SAS) dla niedołączonych dysków zarządzanych i migawek do eksportowania danych do innego regionu na potrzeby rozszerzania regionalnego, odzyskiwania po awarii i odczytywania danych do analizy śledczej. Możesz również użyć identyfikatora URI sygnatury dostępu współdzielonego, aby bezpośrednio przekazać dysk VHD do pustego dysku z lokalnego. Ruch sieciowy między klientami w sieci wirtualnej i dyskami zarządzanymi odbywa się tylko przez sieć wirtualną oraz link prywatny w sieci szkieletowej firmy Microsoft, eliminując narażenie na publiczny Internet.

Można utworzyć zasób dostępu do dysku i połączyć go z siecią wirtualną w tej samej subskrypcji, tworząc prywatny punkt końcowy. Należy skojarzyć dysk lub migawkę z dostępem do dysku do eksportowania i importowania danych za pośrednictwem linków prywatnych. Ponadto należy ustawić właściwość NetworkAccessPolicy dysku lub migawki na `AllowPrivate` . 

Właściwość NetworkAccessPolicy można ustawić tak, aby `DenyAll` uniemożliwić każdyowi generowanie identyfikatora URI sygnatury dostępu współdzielonego dla dysku lub migawki. Wartość domyślna właściwości NetworkAccessPolicy to `AllowAll` .

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>Tworzenie zasobu dostępu do dysku

1. Zaloguj się do Azure Portal i przejdź do **dostępu do dysku** za pomocą [tego linku](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > Aby przejść do bloku dostęp do dysku, należy użyć [podanego linku](https://aka.ms/disksprivatelinks) . Nie jest on obecnie widoczny w portalu publicznym bez użycia linku.

1. Wybierz pozycję **+ Dodaj** , aby utworzyć nowy zasób dostępu do dysku.
1. W bloku Tworzenie wybierz swoją subskrypcję, grupę zasobów, wprowadź nazwę i wybierz region.
1. Wybierz pozycję **Przejrzyj i utwórz**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Zrzut ekranu bloku tworzenia dostępu do dysku. Wypełnij odpowiednią nazwę, wybierz region, wybierz grupę zasobów i wykonaj operację":::

Po utworzeniu zasobu przejdź bezpośrednio do niego.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Zrzut ekranu przedstawiający przycisk Przejdź do zasobu w portalu":::

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Teraz, gdy masz zasób dostępu do dysku, możesz go użyć, aby obsługiwać dostęp do eksportu/importu dysku, odbywa się to za pomocą prywatnych punktów końcowych. W związku z tym należy utworzyć prywatny punkt końcowy i skonfigurować go na potrzeby dostępu do dysku.

1. Z zasobów dostępu do dysku wybierz pozycję **połączenia prywatne punktów końcowych**.
1. Wybierz pozycję **+ prywatny punkt końcowy**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Zrzut ekranu przedstawiający blok przegląd dla zasobu dostępu do dysku. Są wyróżnione prywatne połączenia punktów końcowych.":::

1. Wybieranie grupy zasobów
1. Wypełnij pola Nazwa i wybierz ten sam region, w którym został utworzony zasób dostępu do dysku.
1. Wybierz pozycję **Dalej: zasób >**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Zrzut ekranu przedstawiający przepływ pracy tworzenia prywatnego punktu końcowego, pierwszy blok. Jeśli nie wybierzesz odpowiedniego regionu, możesz napotkać problemy później.":::

1. W bloku **zasób** wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu**.
1. W obszarze **Typ zasobu** wybierz pozycję **Microsoft. COMPUTE/diskAccesses**
1. Dla **zasobu** wybierz utworzony wcześniej zasób dostępu do dysku
1. Pozostaw **docelowy zasób podrzędny** jako **dyski**
1. Wybierz kolejno pozycje **Dalej: Configuration >**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Zrzut ekranu przedstawiający przepływ pracy tworzenia prywatnego punktu końcowego, drugi blok. Ze wszystkimi wyróżnionymi wartościami (typ zasobu, zasób, cel podrzędny)":::

1. Wybierz sieć wirtualną, do której chcesz ograniczyć eksport dysku do programu, inne sieci wirtualne nie będą mogły wyeksportować dysku.

    > [!NOTE]
    > Jeśli dla wybranej podsieci jest włączona Grupa zabezpieczeń sieci, zostanie ona wyłączona tylko dla prywatnych punktów końcowych w tej podsieci. Inne zasoby w tej podsieci nadal będą mieć wymuszanie sieciowej grupy zabezpieczeń.

1. Wybierz odpowiednią podsieć
1. Wybierz pozycję **Przejrzyj i utwórz**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Zrzut ekranu przedstawiający prywatny przepływ pracy tworzenia punktów końcowych, trzeci blok. Wyróżniono sieć wirtualną i podsieć.":::

## <a name="enable-private-endpoint-on-your-disk"></a>Włącz prywatny punkt końcowy na dysku

1. Przejdź do dysku, który chcesz skonfigurować
1. Wybieranie **sieci**
1. Wybierz pozycję **prywatny punkt końcowy (za pomocą dostępu do dysku)** i wybierz utworzony wcześniej dostęp do dysku.
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Zrzut ekranu przedstawiający blok sieć dysku zarządzanego. Wyróżnij wybór prywatnego punktu końcowego oraz dostęp do wybranego dysku. Zapisanie spowoduje skonfigurowanie dysku dla tego dostępu.":::

Ukończono Konfigurowanie linków prywatnych, których można użyć podczas importowania/eksportowania dysku zarządzanego.

## <a name="next-steps"></a>Następne kroki

- [Często zadawane pytania dotyczące linków prywatnych](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Eksportowanie/kopiowanie zarządzanych migawek jako dysku VHD do konta magazynu w innym regionie przy użyciu programu PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)