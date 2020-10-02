---
title: Tworzenie rejestru schematu usługi Azure Event Hubs
description: W tym artykule pokazano, jak utworzyć rejestr schematu w przestrzeni nazw platformy Azure Event Hubs.
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 90556e0843cda94dc79330321d027f8d28eb7d20
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652197"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>Tworzenie rejestru schematu usługi Azure Event Hubs (wersja zapoznawcza)
W tym artykule pokazano, jak utworzyć grupę schematów ze schematami w rejestrze schematu hostowanym przez usługę Azure Event Hubs. Aby zapoznać się z omówieniem funkcji rejestru schematu platformy Azure Event Hubs, zobacz [usługa Azure Schema Registry w Event Hubs](schema-registry-overview.md).

> [!NOTE]
> - Funkcja **Rejestr schematu** jest obecnie w **wersji zapoznawczej**i nie jest zalecana w przypadku obciążeń produkcyjnych.
> - Funkcja jest dostępna tylko w warstwach **standardowa** i **dedykowana** , a nie w warstwie **podstawowa** .

## <a name="prerequisites"></a>Wymagania wstępne
[Utwórz przestrzeń nazw Event Hubs](event-hubs-create.md#create-an-event-hubs-namespace). Możesz również użyć istniejącej przestrzeni nazw. 

## <a name="create-a-schema-group"></a>Tworzenie grupy schematów
1. Przejdź do strony **Event Hubs przestrzeni nazw** . 
1. Wybierz pozycję **Rejestr schematu** w menu po lewej stronie. Aby utworzyć grupę schematów, wybierz pozycję **+ Grupa schematu** na pasku narzędzi. 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="Strona rejestr schematu":::
1. Na stronie **Tworzenie grupy schematów** wykonaj następujące czynności:
    1. Wprowadź **nazwę** grupy schematów.
    1. W **polu Typ serializacji**wybierz format serializacji, który jest stosowany do wszystkich schematów w grupie schematów. Obecnie jedynym obsługiwanym typem jest **Avro** , więc wybierz opcję **Avro**. 
    1. Wybierz **tryb zgodności** dla wszystkich schematów w grupie. W przypadku **Avro**są obsługiwane tryby zgodności do przodu i do tyłu. 
    1. Następnie wybierz pozycję **Utwórz** , aby utworzyć grupę schematów. 
1. Wybierz nazwę **grupy schematów** na liście grup schematów.

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="Strona rejestr schematu":::    
1. Zostanie wyświetlona strona **grupy schematów** dla grupy.

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="Strona rejestr schematu":::
    

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

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="Strona rejestr schematu":::
1. Zostanie wyświetlona strona **Przegląd schematu** dla schematu. 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="Strona rejestr schematu":::    
1. Jeśli istnieje wiele wersji schematu, zobaczysz je na liście rozwijanej **wersje** . Wybierz wersję do przełączenia do tego schematu wersji. 

## <a name="create-a-new-version-of-schema"></a>Utwórz nową wersję schematu

1. Zaktualizuj schemat w polu tekstowym, a następnie wybierz pozycję **Weryfikuj**. W poniższym przykładzie dodano nowe pole `id` do schematu. 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="Strona rejestr schematu":::    
    
1. Przejrzyj stan sprawdzania poprawności i zmiany, a następnie wybierz pozycję **Zapisz**. 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="Strona rejestr schematu":::     
1. Zobaczysz, że `2` wybrano **wersję** na stronie **Przegląd schematu** . 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="Strona rejestr schematu":::    
1. Wybierz, `1` Aby zobaczyć wersję 1 schematu. 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="Strona rejestr schematu":::    


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o rejestrze schematu, zobacz [usługa Azure Schema Registry w Event Hubs](schema-registry-overview.md).

