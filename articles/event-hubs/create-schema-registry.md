---
title: Tworzenie rejestru schematu usługi Azure Event Hubs
description: W tym artykule pokazano, jak utworzyć rejestr schematu w przestrzeni nazw platformy Azure Event Hubs.
ms.topic: how-to
ms.date: 12/03/2020
ms.custom: references_regions
ms.openlocfilehash: 45791cd69772be97ca6768184ed17179e04ad9dc
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576812"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>Tworzenie rejestru schematu usługi Azure Event Hubs (wersja zapoznawcza)
W tym artykule pokazano, jak utworzyć grupę schematów ze schematami w rejestrze schematu hostowanym przez usługę Azure Event Hubs. Aby zapoznać się z omówieniem funkcji rejestru schematu platformy Azure Event Hubs, zobacz [usługa Azure Schema Registry w Event Hubs](schema-registry-overview.md).

> [!NOTE]
> - Funkcja **Rejestr schematu** jest obecnie w **wersji zapoznawczej** i nie jest zalecana w przypadku obciążeń produkcyjnych.
> - Funkcja jest dostępna tylko w warstwach **standardowa** i **dedykowana** , a nie w warstwie **podstawowa** .
> - Jeśli centrum zdarzeń znajduje się w **sieci wirtualnej**, nie będziesz w stanie tworzyć schematów w Azure Portal, chyba że będziesz uzyskiwać dostęp do portalu z maszyny wirtualnej w tej samej sieci wirtualnej. 

## <a name="prerequisites"></a>Wymagania wstępne
[Utwórz przestrzeń nazw Event Hubs](event-hubs-create.md#create-an-event-hubs-namespace). Możesz również użyć istniejącej przestrzeni nazw. 

## <a name="create-a-schema-group"></a>Tworzenie grupy schematów
1. Przejdź do strony **Event Hubs przestrzeni nazw** . 
1. Wybierz pozycję **Rejestr schematu** w menu po lewej stronie. Aby utworzyć grupę schematów, wybierz pozycję **+ Grupa schematu** na pasku narzędzi. 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="Strona rejestr schematu":::
1. Na stronie **Tworzenie grupy schematów** wykonaj następujące czynności:
    1. Wprowadź **nazwę** grupy schematów.
    1. W **polu Typ serializacji** wybierz format serializacji, który jest stosowany do wszystkich schematów w grupie schematów. Obecnie jedynym obsługiwanym typem jest **Avro** , więc wybierz opcję **Avro**. 
    1. Wybierz **tryb zgodności** dla wszystkich schematów w grupie. W przypadku **Avro** są obsługiwane tryby zgodności do przodu i do tyłu. 
    1. Następnie wybierz pozycję **Utwórz** , aby utworzyć grupę schematów. 
1. Wybierz nazwę **grupy schematów** na liście grup schematów.

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="Wybierz grupę schematów na liście":::    
1. Zostanie wyświetlona strona **grupy schematów** dla grupy.

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="Strona grupy schematów":::
    

## <a name="add-a-schema-to-the-schema-group"></a>Dodaj schemat do grupy schematów
W tej sekcji dodasz schemat do grupy schematów przy użyciu Azure Portal. 

1. Na stronie **Grupa schematu** wybierz pozycję **+ schemat** na pasku narzędzi. 
1. Na stronie **Tworzenie schematu** wykonaj następujące czynności:
    1. Wprowadź **nazwę** schematu.
    1. W polu tekstowym wprowadź następujący **schemat** . Możesz również wybrać plik ze schematem.
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. Wybierz przycisk **Utwórz**. 
1. Wybierz **schemat** z listy schematów. 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="Wybierz schemat":::
1. Zostanie wyświetlona strona **Przegląd schematu** dla schematu. 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="Strona przegląd schematu":::    
1. Jeśli istnieje wiele wersji schematu, zobaczysz je na liście rozwijanej **wersje** . Wybierz wersję do przełączenia do tego schematu wersji. 

## <a name="create-a-new-version-of-schema"></a>Utwórz nową wersję schematu

1. Zaktualizuj schemat w polu tekstowym, a następnie wybierz pozycję **Weryfikuj**. W poniższym przykładzie dodano nowe pole `id` do schematu. 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="Aktualizuj schemat":::    
    
1. Przejrzyj stan sprawdzania poprawności i zmiany, a następnie wybierz pozycję **Zapisz**. 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="Przejrzyj stan walidacji, zmiany i Zapisz":::     
1. Zobaczysz, że `2` wybrano **wersję** na stronie **Przegląd schematu** . 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="Nowa wersja schematu":::    
1. Wybierz, `1` Aby zobaczyć wersję 1 schematu. 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="Wybierz wersję":::    


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o rejestrze schematu, zobacz [usługa Azure Schema Registry w Event Hubs](schema-registry-overview.md).

