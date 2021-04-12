---
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.openlocfilehash: ac83f455d2af82c3f6970077fccb17f07e53cccc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98901111"
---
## <a name="creating-and-running-a-scan"></a>Tworzenie i Uruchamianie skanowania

> [!Note] 
> Poniższe kroki i zrzuty ekranu ilustrują ogólny proces zarządzania skanami dla różnych typów źródeł danych. Opcje mogą się nieco różnić w zależności od typów źródeł danych, z którymi pracujesz.

Aby utworzyć i uruchomić nowe skanowanie, wykonaj następujące czynności:

1. Przejdź do **źródeł**

1. Wybierz zarejestrowane źródło danych.

1. Wybierz pozycję **+ Nowe skanowanie**

1. Wybierz poświadczenie, aby nawiązać połączenie ze źródłem danych. 

   :::image type="content" source="media/manage-scans/set-up-scan.png" alt-text="Konfigurowanie skanowania":::

1. Można ograniczyć skanowanie do określonych części źródła danych, takich jak foldery, kolekcje lub schematy, sprawdzając odpowiednie pozycje na liście.

   :::image type="content" source="media/manage-scans/scope-your-scan.png" alt-text="Określanie zakresu skanowania":::

1. Wybrany zestaw reguł skanowania. Można wybrać jedną z domyślnych ustawień systemowych, istniejące niestandardowe lub utworzyć nową.

   :::image type="content" source="media/manage-scans/scan-rule-set.png" alt-text="Skanuj zestaw reguł":::

1. Wybierz wyzwalacz skanowania. Można skonfigurować harmonogram lub uruchomić skanowanie jeden raz.

   :::image type="content" source="media/manage-scans/trigger-scan.png" alt-text="uruchamiać":::

1. Przejrzyj skanowanie i wybierz pozycję **Zapisz i uruchom**.

## <a name="viewing-your-scans-and-scan-runs"></a>Wyświetlanie skanów i uruchomień skanowania

Aby wyświetlić istniejące skany, wykonaj następujące czynności:

1. Przejdź do centrum zarządzania. Wybierz pozycję **źródła danych** w sekcji **źródła i Skanuj** . 

2. Wybierz żądane źródło danych. Zostanie wyświetlona lista istniejących skanów w tym źródle danych.

3. Wybierz skanowanie, którego wyniki chcesz wyświetlić.

4. Na tej stronie zostaną wyświetlone wszystkie poprzednie uruchomienia skanowania wraz z metrykami i Stanami dla każdego przebiegu skanowania. Zostanie również wyświetlona, czy Twoje skanowanie zostało zaplanowane, czy ręczne, ile zasobów ma zastosowane klasyfikacje, ile łącznych zasobów zostało wykrytych, czasu rozpoczęcia i zakończenia skanowania oraz łącznego czasu trwania skanowania.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Zarządzanie skanowaniem — Edytuj, Usuń lub Anuluj

Aby zarządzać skanowaniem lub usunąć je, wykonaj następujące czynności:

1. Przejdź do centrum zarządzania. Wybierz pozycję **źródła danych** w sekcji **źródła i skanowanie** , a następnie wybierz odpowiednie źródło danych.

2. Wybierz skanowanie, którymi chcesz zarządzać. Skanowanie można edytować, wybierając pozycję **Edytuj**.

3. Możesz usunąć skanowanie, wybierając pozycję **Usuń**. 
