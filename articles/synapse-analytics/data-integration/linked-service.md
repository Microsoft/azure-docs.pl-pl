---
title: Udostępnianie i zabezpieczanie połączonej usługi w usłudze Azure Synapse Analytics
description: Dowiedz się, jak aprowizować i zabezpieczać połączony serwis sieciowej za pomocą zarządzanej sieci wirtualnej
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430554"
---
# <a name="securing-a-linked-service-with-private-links"></a>Zabezpieczanie połączonej usługi za pomocą łączy prywatnych 

W tym artykule dowiesz się, jak zabezpieczyć połączony serwis w Synapse za pomocą prywatnego punktu końcowego.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure:** Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* **Konto usługi Azure Storage:** używasz usługi Azure Data Lake Gen 2 jako *źródłowego* magazynu danych. Jeśli nie masz konta magazynu, zobacz [Tworzenie konta usługi Azure Storage,](../../storage/blobs/data-lake-storage-quickstart-create-account.md) aby uzyskać kroki, aby je utworzyć. Upewnij się, że konto magazynu ma filtrowanie IP Synapse Studio w celu uzyskania dostępu do niego i że zezwalasz tylko **wybranym sieciom** na dostęp do konta magazynu. Ustawienie pod **zaporami i sieciami wirtualnymi** powinny wyglądać jak na poniższym obrazie.

![Zabezpieczone konto magazynu](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Tworzenie połączonej usługi za pomocą łączy prywatnych

W usłudze Azure Synapse Analytics usługa połączona to miejsce definiowania informacji o połączeniu z innymi usługami. W tej sekcji dodasz usługę Azure Synapse Analytics i usługę Azure Data Lake Gen 2 jako połączone usługi.

1. Otwórz studio Synapse platformy Azure i przejdź do karty **Zarządzanie.**
1. W **obszarze Połączenia zewnętrzne**wybierz pozycję Połączone **usługi**.
1. Aby dodać usługę połączony, kliknij przycisk **Nowy**.
1. Wybierz kafelek Usługi Azure Data Lake Storage Gen2 z listy i kliknij przycisk **Kontynuuj**.
1. Upewnij się, że **włączysz tworzenie interaktywne**. Włączenie może potrwać około 1 minuty. 
1. Wprowadź poświadczenia uwierzytelniania. Klucz konta, jednostki usługi i tożsamości zarządzanej są obecnie obsługiwane typy uwierzytelniania. Kliknij przycisk Testuj połączenie, aby sprawdzić, czy poświadczenia są poprawne.
1. Wybierz **opcję Test połączenia**, powinno zakończyć się niepowodzeniem, ponieważ konto magazynu nie umożliwia dostępu do niego bez utworzenia i zatwierdzenia prywatnego punktu końcowego. W komunikacie o błędzie powinien zostać wyświetlony łącze do utworzenia **prywatnego punktu końcowego,** który można wykonać, aby przejść do następnej części. Jeśli zastosujesz się do tego linku, pomiń następną część.
1. Po zakończeniu wybierz pozycję **Utwórz**.

## <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

W przypadku, gdy nie klikniesz hiperłącza podczas testowania połączenia powyżej, postępuj zgodnie z poniższą ścieżką. Teraz musisz utworzyć zarządzany prywatny punkt końcowy, który połączy się z połączaną usługą utworzoną powyżej.

1. Przejdź do karty **Zarządzanie.**
1. Przejdź do sekcji **Zarządzane sieci wirtualne.**
1. Wybierz **+ Nowy** w obszarze Zarządzany prywatny punkt końcowy.
1. Wybierz kafelek Usługi Azure Data Lake Storage Gen2 z listy i wybierz pozycję **Kontynuuj**.
1. Wprowadź nazwę utworzonego powyżej konta magazynu.
1. Wybierz **pozycję Utwórz**
1. Po kilku sekundach odczekania powinno być widoczne, że utworzony link prywatny wymaga zatwierdzenia.

## <a name="approval-of-a-private-link"></a>Zatwierdzenie łącza prywatnego
1. Wybierz utworzony powyżej prywatny punkt końcowy. Możesz zobaczyć hiperłącze, które pozwoli Ci zatwierdzić prywatny punkt końcowy na poziomie konta magazynu. *Alternatywą jest przejście bezpośrednio do konta usługi Azure portal Storage i przejście do **bloku Połączenia prywatnego punktu końcowego.***
1. Zaznacz prywatny punkt końcowy utworzony w Studio i wybierz pozycję **Zatwierdź**.
1. Dodaj opis i kliknij **przycisk tak**
1. Wróć do Synapse Studio w sekcji **Zarządzane sieci wirtualne** na karcie **Zarządzanie.**
1. Powinno upłynąć około 1 minuty, aby uzyskać zatwierdzenie odzwierciedlone dla prywatnego punktu końcowego.

## <a name="check-the-connection-works"></a>Sprawdź, czy połączenie działa
1. Przejdź do karty **Zarządzanie** i wybierz utworzoną usługę połączony.
1. Upewnij się, że **tworzenie interaktywne** jest aktywne.
1. Wybierz pozycję **Testuj połączenie**. Powinno się zobaczyć połączenie zakończyć się pomyślnie.

Teraz nawiązałeś bezpieczne i prywatne połączenie między Synapse a powiązaną usługą!

## <a name="next-steps"></a>Następne kroki

Aby rozwinąć dalsze zrozumienie zarządzanego prywatnego punktu końcowego w analizie synapse, zobacz [artykuł Pojęć wokół prywatnego punktu końcowego Synapse Managed.](data-integration-data-lake.md)

Aby uzyskać więcej informacji na temat integracji danych dla Synapse Analytics, zobacz [Ingesting danych w artykule Usługi Data Lake.](data-integration-data-lake.md)