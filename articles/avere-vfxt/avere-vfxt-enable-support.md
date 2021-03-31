---
title: Włączanie obsługi avere vFXT — Azure
description: Dowiedz się, jak włączyć automatyczne przekazywanie danych pomocy technicznej dotyczącej klastra z programu avere vFXT for Azure, aby pomóc w zapewnieniu obsługi klienta.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 93b99aa624a21d9312297e4279b1dcf053c79ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272732"
---
# <a name="enable-support-uploads"></a>Włączanie przekazywania pomocy technicznej

Avere vFXT for Azure może automatycznie przekazywać dane pomocy technicznej dotyczące klastra. Te operacje przekazywania umożliwiają personelowi pomocy technicznej dostarczenie najlepszej możliwej usługi klienta.

## <a name="steps-to-enable-uploads"></a>Kroki umożliwiające przekazywanie

Wykonaj następujące kroki w panelu sterowania avere, aby aktywować pomoc techniczną. (Aby dowiedzieć się, jak otworzyć Panel sterowania, zobacz [dostęp do klastra vFXT](avere-vfxt-cluster-gui.md) ).

1. Przejdź do karty **Ustawienia** w górnej części strony.
1. Kliknij link **Pomoc techniczna** po lewej stronie i zaakceptuj zasady ochrony prywatności.

   ![Zrzut ekranu przedstawiający panel sterowania avere i okno podręczne z przyciskiem Potwierdź, aby zaakceptować zasady ochrony prywatności](media/avere-vfxt-privacy-policy.png)

1. Na stronie Konfiguracja pomocy technicznej Otwórz sekcję **Informacje o kliencie** , klikając trójkąt po lewej stronie.
1. Kliknij przycisk ponownie **Sprawdź poprawność przekazywania informacji** .
1. Ustaw nazwę obsługi klastra w polu **unikatowa nazwa klastra**. Upewnij się, że ta nazwa jednoznacznie identyfikuje klaster, aby obsługiwał personel.
1. Zaznacz pola wyboru dotyczące **monitorowania statystyk**, **przekazywania informacji ogólnych** i **przekazywania informacji o awarii**.
1. Kliknij przycisk **Prześlij**.

   ![Zrzut ekranu przedstawiający sekcję ukończonych informacji o kliencie strony ustawień pomocy technicznej](media/avere-vfxt-support-info.png)

1. Kliknij trójkąt po lewej stronie **bezpiecznej proaktywnej pomocy technicznej (SPS)** , aby rozwinąć sekcję.
1. Zaznacz pole wyboru **Włącz połączenie programu SPS**.
1. Kliknij przycisk **Prześlij**.

   ![Zrzut ekranu przedstawiający sekcję ukończona obsługa bezpiecznych proaktywnie na stronie ustawień obsługi](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Następne kroki

Jeśli konieczne jest dodanie lokalnego lub istniejącego systemu magazynu w chmurze do klastra, postępuj zgodnie z instrukcjami w temacie [Konfigurowanie magazynu](avere-vfxt-add-storage.md).

Aby rozpocząć dołączanie klientów do klastra, przeczytaj artykuł [Instalowanie klastra avere vFXT](avere-vfxt-mount-clients.md).
