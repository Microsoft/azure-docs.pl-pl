---
title: Tworzenie konta usługi Azure Media Services za pomocą witryny Azure Portal | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki tworzenia konta usługi Azure Media Services za pomocą portalu Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 53f0063d1458c218324d867c1942ae0e044f10d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011922"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Tworzenie konta usługi Media Services przy użyciu witryny Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Portal Azure umożliwia szybkie utworzenie konta usługi Azure Media Services (AMS). Korzystając z konta, możesz uzyskiwać dostęp do usługi Media Services, która umożliwia przechowywanie, szyfrowanie, kodowanie i przesyłanie strumieniowe zawartości oraz zarządzanie nią na platformie Azure. Podczas tworzenia konta usługi Media Services możesz również utworzyć skojarzone konto magazynu (lub użyć istniejącego konta). Jeśli usuniesz konto usługi Media Services, obiekty blob na powiązanym koncie magazynu nie zostaną usunięte.

Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu znajdujących się w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych opóźnień i kosztów danych wychodzących.

W tym artykule przedstawiono sposób tworzenia konta usługi Media Services przy użyciu witryny Azure Portal.

> [!NOTE]
> Aby uzyskać informacje na temat dostępności funkcji Azure Media Services w różnych regionach, zobacz [funkcje usługi AMS w regionach](availability-regions-v-2.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-ams-account"></a>Tworzenie konta AMS

W tej sekcji opisano kroki w procesie tworzenia konta usługi AMS.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/).
2. Kliknij pozycję **+ Utwórz**  >  **nośnik** zasobów  >  **Media Services**.
3. Na stronie **TWORZENIE KONTA USŁUGI MEDIA SERVICES** wprowadź wymagane wartości.

   1. W polu **Nazwa konta** wprowadź nazwę nowego konta usługi AMS. Nazwa konta usługi Media Services składa się z małych liter i cyfr (bez spacji) i może zawierać od 3 do 24 znaków.
   2. W subskrypcji wybierz jedną z różnych subskrypcji Azure, do których masz dostęp.
   3. W polu **Grupa zasobów** wybierz nowy lub istniejący zasób.  Grupa zasobów jest kolekcją zasobów, które mają ten sam cykl życia, uprawnienia i zasady. Dowiedz się więcej [tutaj](../../azure-resource-manager/management/overview.md#resource-groups).
   4. W polu **Lokalizacja** wybierz region geograficzny używany do przechowywania nośników i rekordów metadanych dla konta usługi Media Services. Ten region będzie służyć do przetwarzania i przesyłania strumieniowego multimediów. Na liście rozwijanej są wyświetlane tylko regiony dostępne w usłudze Media Services. 
   5. W polu **Konto magazynu** wybierz konto magazynu, aby udostępnić magazyn obiektów Blob dla zawartości multimedialnej z konta usługi Media Services. Można wybrać istniejące konto magazynu w tym samym regionie geograficznym co konto usługi Media Services albo utworzyć konto magazynu. Nowe konto magazynu jest tworzone w tym samym regionie. Reguły dotyczące nazw kont magazynów są takie same, jak w przypadku kont usługi Media Services.

       Więcej informacji o magazynie można znaleźć [tutaj](../../storage/common/storage-introduction.md).
   6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**, aby wyświetlić postęp wdrażania konta.
4. Kliknij opcję **Utwórz** w dolnej części formularza.

    Po pomyślnym utworzeniu konta zostanie załadowana strona przeglądu. W tabeli punktów końcowych przesyłania strumieniowego konto będzie mieć domyślny punkt końcowy przesyłania strumieniowego w stanie **Zatrzymany**. 

    >[!NOTE]
    >Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
   
## <a name="to-manage-your-ams-account"></a>Zarządzanie kontem usługi AMS

W celu zarządzania kontem usługi AMS (na przykład programowego łączenia się z interfejsem API usługi AMS, przekazywania plików wideo, kodowania zasobów, konfigurowania ochrony zawartości, monitorowania postępów zadań) wybierz opcję **Ustawienia** po lewej stronie portalu. W obszarze **Ustawienia** przejdź do jednego z dostępnych bloków (na przykład: **Dostęp do interfejsu API**, **Zasoby**, **Zadania**, **Ochrona zawartości**).

## <a name="next-steps"></a>Następne kroki

Teraz możesz przekazać pliki na konto usługi AMS. Więcej informacji znajduje się na stronie [Przekazywanie plików](media-services-portal-upload-files.md).

Jeśli planujesz uzyskiwać dostęp do interfejsu API usługi AMS programowo, zobacz temat [Dostęp do interfejsu API usługi Azure Media Services przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
