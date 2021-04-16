---
title: Zabezpieczanie połączonej usługi
description: Dowiedz się, jak aprowizować i zabezpieczać usługę połączona przy użyciu zarządzanej sieci wirtualnej
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 6be76878a9a07c5f4a1e2a9348bb7b09cb1b10eb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567591"
---
# <a name="secure-a-linked-service-with-private-links"></a>Zabezpieczanie połączonej usługi za pomocą linków prywatnych

W tym artykule dowiesz się, jak zabezpieczyć usługę połączona w usłudze Synapse przy użyciu prywatnego punktu końcowego.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure:** jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto platformy [Azure.](https://azure.microsoft.com/free/)
* **Konto usługi Azure Storage:** używasz usługi Azure Data Lake Gen 2 jako *źródłowego* magazynu danych. Jeśli nie masz konta magazynu, zobacz Tworzenie konta usługi [Azure Storage,](../../storage/common/storage-account-create.md) aby uzyskać instrukcje dotyczące jego tworzenia. Upewnij się, że konto magazynu ma Synapse Studio IP w celu uzyskiwania  do niego dostępu i że zezwalasz na dostęp do konta magazynu tylko wybranym sieciom. Ustawienie w bloku Zapory i sieci **wirtualne** powinno wyglądać jak na poniższej ilustracji.

![Zabezpieczone konto magazynu](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Tworzenie połączonej usługi z linkami prywatnymi

W Azure Synapse Analytics połączona usługa służy do definiowania informacji o połączeniu z innymi usługami. W tej sekcji dodasz usługi Azure Data Lake Gen 2 Azure Synapse Analytics jako połączone usługi.

1. Otwórz program Azure Synapse Studio i przejdź do **karty Zarządzanie.**
1. W **obszarze Połączenia zewnętrzne** wybierz pozycję Połączone **usługi.**
1. Aby dodać usługę połączona, wybierz pozycję **Nowy.**
1. Wybierz kafelek Azure Data Lake Storage Gen2 z listy i wybierz pozycję **Kontynuuj.**
1. Upewnij się, że **włączyć opcję Tworzenie interakcyjne.** Może to potrwać około 1 minuty. 
1. Wprowadź poświadczenia uwierzytelniania. Klucz konta, jednostkę usługi i tożsamość zarządzaną są obecnie obsługiwanymi typami uwierzytelniania. Wybierz pozycję Testuj połączenie, aby sprawdzić, czy poświadczenia są poprawne.
1. Wybierz **Test connection**, powinno to się nie powieść, ponieważ konto magazynu nie włączy dostępu do niego bez utworzenia i zatwierdzenia prywatnego punktu końcowego. W komunikacie o błędzie powinien zostać  wyświetlony link do tworzenia prywatnego punktu końcowego, który można śledzić, aby przejść do następnej części. Jeśli używasz tego linku, pomiń następną część.
1. Po zakończeniu wybierz pozycję **Utwórz**.

## <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

Jeśli nie wybierzesz hiperlinku podczas testowania powyższego połączenia, postępuj zgodnie z następującą ścieżką. Utwórz zarządzany prywatny punkt końcowy, który połączysz z połączoną usługą utworzoną powyżej.

1. Przejdź do karty **Zarządzanie.**
1. Przejdź do sekcji **Zarządzane sieci** wirtualne.
1. Wybierz **pozycję + Nowy w** obszarze Zarządzany prywatny punkt końcowy.
1. Wybierz kafelek Azure Data Lake Storage Gen2 z listy i wybierz pozycję **Kontynuuj.**
1. Wprowadź nazwę utworzonego powyżej konta magazynu.
1. Wybierz pozycję **Utwórz**
1. Po odczekeniu kilku sekund powinno być widać, że utworzone łącze prywatne wymaga zatwierdzenia.

## <a name="private-link-approval"></a>Zatwierdzenie linku prywatnego
1. Wybierz utworzony powyżej prywatny punkt końcowy. Zostanie wyświetlony hiperlink, który umożliwia zatwierdzenie prywatnego punktu końcowego na poziomie konta magazynu. *Alternatywą jest bezpośrednie przejdź do konta Azure Portal Storage i przejdź do bloku Połączenia **prywatnego punktu końcowego.***
1. Zaznacz prywatny punkt końcowy utworzony w programie Studio i wybierz pozycję **Zatwierdź.**
1. Dodaj opis i wybierz pozycję **Tak**
1. Wstecz do Synapse Studio w sekcji **Zarządzane** sieci wirtualne na **karcie** Zarządzanie.
1. Zatwierdzenie prywatnego punktu końcowego powinno zająć około 1 minuty.

## <a name="check-the-connection-works"></a>Sprawdź, czy połączenie działa
1. Przejdź do karty **Zarządzanie** i wybierz utworzoną połączona usługę.
1. Upewnij się, **że tworzenie interakcyjne** jest aktywne.
1. Wybierz pozycję **Testuj połączenie**. Połączenie powinno zostać pomyślnie nawiązaniu.

Nawiązliśmy bezpieczne i prywatne połączenie między usługą Synapse i usługą połączona.

## <a name="next-steps"></a>Następne kroki


Aby lepiej zrozumieć zarządzany prywatny punkt końcowy w Azure Synapse Analytics, zobacz [Zarządzane prywatne punkty końcowe](../security/synapse-workspace-managed-private-endpoints.md).


Aby uzyskać więcej informacji na temat integracji Azure Synapse Analytics, zobacz artykuł [Inging data into a Data Lake](data-integration-data-lake.md) (Pozysowanie danych do data lake).