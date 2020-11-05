---
title: Zabezpieczanie połączonej usługi
description: Dowiedz się, jak zainicjować i zabezpieczyć połączoną usługę za pomocą zarządzanej sieci wirtualnej
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: feabfe505601276269c2774e3b39dd5cbaa32087
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359973"
---
# <a name="secure-a-linked-service-with-private-links"></a>Zabezpieczanie połączonej usługi za pomocą linków prywatnych

W tym artykule dowiesz się, jak zabezpieczyć połączoną usługę w Synapse za pomocą prywatnego punktu końcowego.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure** : Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Konto usługi Azure Storage** : używasz Azure Data Lake Gen 2 jako magazynu danych *źródłowych* . Jeśli nie masz konta magazynu, zobacz [Tworzenie konta usługi Azure Storage](../../storage/blobs/data-lake-storage-quickstart-create-account.md) , aby uzyskać instrukcje. Upewnij się, że konto magazynu ma filtrowanie adresów IP programu Synapse Studio, aby uzyskać do niego dostęp, i czy zezwolisz tylko **wybranym sieci** na dostęp do konta magazynu. Ustawienia w obszarze **zapory i sieci wirtualne** bloku powinny wyglądać jak na poniższej ilustracji.

![Zabezpieczone konto magazynu](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Tworzenie połączonej usługi z linkami prywatnymi

W usłudze Azure Synapse Analytics, połączona Usługa polega na definiowaniu informacji o połączeniu z innymi usługami. W tej sekcji dodasz usługę Azure Synapse Analytics i Azure Data Lake Gen 2 jako połączone usługi.

1. Otwórz usługę Azure Synapse Studio i przejdź na kartę **Zarządzanie** .
1. W obszarze **połączenia zewnętrzne** wybierz pozycję **połączone usługi**.
1. Aby dodać połączoną usługę, wybierz pozycję **Nowy**.
1. Wybierz z listy kafelek Azure Data Lake Storage Gen2 i wybierz pozycję **Kontynuuj**.
1. Upewnij się, że włączono funkcję **interaktywnego tworzenia**. Włączenie tej możliwości może potrwać około 1 minuty. 
1. Wprowadź poświadczenia uwierzytelniania. Klucz konta, nazwa główna usługi i tożsamość zarządzana są obecnie obsługiwanymi typami uwierzytelniania. Wybierz pozycję Testuj połączenie, aby sprawdzić, czy Twoje poświadczenia są poprawne.
1. Wybierz **Test connection** , powinna zakończyć się niepowodzeniem, ponieważ konto magazynu nie umożliwia dostępu do niego bez tworzenia i zatwierdzania prywatnego punktu końcowego. W komunikacie o błędzie powinien zostać wyświetlony link umożliwiający utworzenie **prywatnego punktu końcowego** , który można wykonać, aby przejść do następnej części. Jeśli użyjesz tego linku, Pomiń następną część.
1. Po zakończeniu wybierz pozycję **Utwórz**.

## <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

Jeśli nie wybrano w hiperłączu, gdy testujesz powyższe połączenie, postępuj zgodnie z następującą ścieżką. Utwórz zarządzany prywatny punkt końcowy, który zostanie połączony z powyższą utworzoną usługą.

1. Przejdź na kartę **Zarządzanie** .
1. Przejdź do sekcji **zarządzane sieci wirtualne** .
1. Wybierz pozycję **+ Nowy** w obszarze zarządzany prywatny punkt końcowy.
1. Wybierz z listy kafelek Azure Data Lake Storage Gen2 i wybierz pozycję **Kontynuuj**.
1. Wprowadź nazwę utworzonego powyżej konta magazynu.
1. Wybierz pozycję **Utwórz**
1. Po odczekaniu kilku sekund powinna zostać wyświetlona, że utworzone łącze prywatne wymaga zatwierdzenia.

## <a name="private-link-approval"></a>Zatwierdzenie linku prywatnego
1. Wybierz utworzony powyżej prywatny punkt końcowy. Można wyświetlić hiperłącze, które umożliwi zatwierdzenie prywatnego punktu końcowego na poziomie konta magazynu. *Alternatywą jest przejście bezpośrednio do konta magazynu Azure Portal i przejście do bloku **połączenia prywatnego punktu końcowego** .*
1. Zaznacz prywatny punkt końcowy utworzony w Studio i wybierz pozycję **Zatwierdź**.
1. Dodaj opis i wybierz pozycję **tak**
1. Wróć do Synapse Studio w sekcji **zarządzane sieci wirtualne** na karcie **Zarządzanie** .
1. Zatwierdzenie zatwierdzenia dla prywatnego punktu końcowego powinno potrwać około 1 minuty.

## <a name="check-the-connection-works"></a>Sprawdź, czy połączenie działa
1. Przejdź do karty **Zarządzanie** i wybierz utworzoną usługę połączoną.
1. Upewnij się, że **interaktywna autorstwo** jest aktywna.
1. Wybierz **Test connection**. Powinno zostać wyświetlone połączenie.

Nawiązano połączenie bezpiecznego i prywatnego między Synapse i połączoną usługą.

## <a name="next-steps"></a>Następne kroki


Aby opracowywać dalsze informacje o zarządzanym prywatnym punkcie końcowym w usłudze Synapse Analytics, zobacz [zarządzane prywatne punkty końcowe](../security/synapse-workspace-managed-private-endpoints.md).


Aby uzyskać więcej informacji na temat integracji danych na potrzeby usługi Synapse Analytics, zobacz artykuł pozyskiwanie [danych w Data Lake](data-integration-data-lake.md) artykule.
