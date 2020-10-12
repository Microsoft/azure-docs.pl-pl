---
title: Szyfrowanie w Azure Data Lake Storage Gen1 | Microsoft Docs
description: Szyfrowanie w Azure Data Lake Storage Gen1 pomaga chronić dane, wdrażać zasady zabezpieczeń przedsiębiorstwa i spełniać wymagania dotyczące zgodności z przepisami. Ten artykuł zawiera omówienie projektu i niektórych technicznych aspektów implementacji.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a187b31657ec2a67c306d817a75150d19a5cf9b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86497186"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Szyfrowanie danych w Azure Data Lake Storage Gen1

Szyfrowanie w Azure Data Lake Storage Gen1 pomaga chronić dane, wdrażać zasady zabezpieczeń przedsiębiorstwa i spełniać wymagania dotyczące zgodności z przepisami. Ten artykuł zawiera omówienie projektu i niektórych technicznych aspektów implementacji.

Data Lake Storage Gen1 obsługuje szyfrowanie danych zarówno podczas przechowywania, jak i podczas przesyłania. W przypadku danych przechowywanych w Data Lake Storage Gen1 obsługuje "domyślnie włączone" szyfrowanie przezroczyste. Poniżej przedstawiono bardziej szczegółowe wyjaśnienie tych terminów:

* **Domyślnie włączone**: podczas tworzenia nowego konta Data Lake Storage Gen1, domyślne ustawienie włącza szyfrowanie. Następnie dane przechowywane w Data Lake Storage Gen1 są zawsze szyfrowane przed zapisaniem na nośniku trwałym. Takie działanie dotyczy wszystkich danych i nie można go zmienić po utworzeniu konta.
* **Przezroczysty**: Data Lake Storage Gen1 automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje dane przed pobraniem. Szyfrowanie jest konfigurowane i zarządzane na poziomie konta Data Lake Storage Gen1 przez administratora. W interfejsach API dostępu do danych nie są wprowadzane żadne zmiany. W związku z tym nie są wymagane żadne zmiany w aplikacjach i usługach, które współpracują z Data Lake Storage Gen1 ze względu na szyfrowanie.

Dane przesyłane (nazywane również danymi w ruchu) również są zawsze szyfrowane w Data Lake Storage Gen1. Oprócz tego, że dane są szyfrowane przed zapisaniem na nośniku trwałym, są również zawsze zabezpieczane podczas przesyłania przy użyciu protokołu HTTPS. HTTPS jest jedynym protokołem obsługiwanym przez interfejsy REST Data Lake Storage Gen1. Na poniższym diagramie pokazano, jak dane są szyfrowane w Data Lake Storage Gen1:

![Diagram szyfrowania danych w Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Konfigurowanie szyfrowania przy użyciu Data Lake Storage Gen1

Szyfrowanie dla Data Lake Storage Gen1 jest konfigurowane podczas tworzenia konta i zawsze jest domyślnie włączone. Możesz samodzielnie zarządzać kluczami lub zezwalać Data Lake Storage Gen1 na zarządzanie nimi (jest to ustawienie domyślne).

Aby uzyskać więcej informacji, zobacz [Wprowadzenie](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Jak działa szyfrowanie w Data Lake Storage Gen1

Poniższe informacje dotyczą sposobu zarządzania głównymi kluczami szyfrowania i objaśniają trzy różne typy kluczy, których można użyć w szyfrowaniu danych dla Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Główne klucze szyfrowania

Data Lake Storage Gen1 oferuje dwa tryby zarządzania głównymi kluczami szyfrowania (głównymi kluczami szyfrowania). Na razie załóżmy, że główny klucz szyfrowania jest kluczem najwyższego poziomu. Aby można było odszyfrować wszystkie dane przechowywane w Data Lake Storage Gen1, wymagany jest dostęp do głównego klucza szyfrowania.

Oto dwa tryby zarządzania głównym kluczem szyfrowania:

*   Klucze zarządzane przez usługę
*   Klucze zarządzane przez klienta

W obu trybach główny klucz szyfrowania jest zabezpieczony dzięki przechowywaniu go w usłudze Azure Key Vault. Key Vault to w pełni zarządzana, wysoce bezpieczna usługa platformy Azure, która może służyć do ochrony kluczy kryptograficznych. Aby uzyskać więcej informacji, zobacz [Key Vault](https://azure.microsoft.com/services/key-vault).

Oto krótkie porównanie możliwości oferowanych przez dwa tryby zarządzania głównymi kluczami szyfrowania.

| Pytanie | Klucze zarządzane przez usługę | Klucze zarządzane przez klienta |
| -------- | -------------------- | --------------------- |
|W jaki sposób przechowywane są dane?|Są zawsze szyfrowane przed zapisaniem.|Są zawsze szyfrowane przed zapisaniem.|
|Gdzie jest przechowywany główny klucz szyfrowania?|Usługa Key Vault|Usługa Key Vault|
|Czy jakiekolwiek klucze szyfrowania są przechowywane poza usługą Key Vault? |Nie|Nie|
|Czy można pobrać główny klucz szyfrowania za pomocą usługi Key Vault?|Nie. Po umieszczeniu głównego klucza szyfrowania w usłudze Key Vault można go używać tylko do szyfrowania i odszyfrowywania.|Nie. Po umieszczeniu głównego klucza szyfrowania w usłudze Key Vault można go używać tylko do szyfrowania i odszyfrowywania.|
|Kto jest właścicielem wystąpienia usługi Key Vault i głównego klucza szyfrowania?|Usługa Data Lake Storage Gen1|Ty jesteś właścicielem wystąpienia usługi Key Vault, które znajduje się w Twojej subskrypcji platformy Azure. Głównym kluczem szyfrowania w usłudze Key Vault można zarządzać programowo lub sprzętowo.|
|Czy można odwołać dostęp do główny klucz szyfrowania usługi Data Lake Storage Gen1?|Nie|Tak. Można zarządzać listami kontroli dostępu w Key Vault i usuwać wpisy kontroli dostępu do tożsamości usługi dla usługi Data Lake Storage Gen1.|
|Czy można trwale usunąć główny klucz szyfrowania?|Nie|Tak. Po usunięciu główny klucz szyfrowania z Key Vault dane na koncie Data Lake Storage Gen1 nie mogą zostać odszyfrowane przez żadną osobę, w tym usługę Data Lake Storage Gen1. <br><br> Jeśli przed usunięciem głównego klucza szyfrowania z usługi Key Vault jawnie utworzono jego kopię zapasową, to można go przywrócić i odzyskać dane. Jednakże jeśli nie utworzono kopii zapasowej główny klucz szyfrowania przed usunięciem go z Key Vault, dane na koncie Data Lake Storage Gen1 nigdy nie będą później odszyfrowane.|


Poza różnicą dotyczącą sposobu zarządzania głównym kluczem szyfrowania i wystąpieniem usługi Key Vault, w którym ten klucz się znajduje, pozostałe elementy projektu są takie same dla obu trybów.

Podczas wybierania trybu głównych kluczy szyfrowania należy pamiętać o następujących kwestiach:

*   Podczas aprowizacji konta Data Lake Storage Gen1 można zdecydować, czy klucze zarządzane przez klienta czy klucze zarządzane przez usługę mają być używane.
*   Po zainicjowaniu obsługi konta Data Lake Storage Gen1 nie można zmienić trybu.

### <a name="encryption-and-decryption-of-data"></a>Szyfrowanie i odszyfrowywanie danych

W projekcie szyfrowania danych używane są trzy typy kluczy. Poniższa tabela zawiera podsumowanie:

| Klucz                   | Skrót | Skojarzony z | Lokalizacja magazynu                             | Type       | Uwagi                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Główny klucz szyfrowania | GKS          | Konto Data Lake Storage Gen1 | Usługa Key Vault                              | Asymetryczny | Może być zarządzany przez Data Lake Storage Gen1 lub użytkownika.                                                              |
| Klucz szyfrowania danych   | KSD          | Konto Data Lake Storage Gen1 | Magazyn trwały zarządzany przez usługę Data Lake Storage Gen1 | Symetryczny  | Klucz szyfrowania danych jest szyfrowany przy użyciu głównego klucza szyfrowania. Na nośniku trwałym jest zapisywany zaszyfrowany klucz szyfrowania danych. |
| Klucz szyfrowania bloków  | KSB          | Blok danych | Brak                                         | Symetryczny  | Klucz szyfrowania bloków jest tworzony na podstawie klucza szyfrowania danych i bloku danych.                                                      |

Poniższy diagram przedstawia te koncepcje:

![Klucze używane do szyfrowania danych](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudoalgorytm stosowany w przypadku odszyfrowywania pliku:
1.  Sprawdź, czy klucz szyfrowania danych dla konta Data Lake Storage Gen1 jest buforowany i gotowy do użycia.
    - Jeśli nie, odczytaj zaszyfrowany klucz szyfrowania danych z magazynu trwałego i prześlij go do usługi Key Vault w celu odszyfrowania. Odszyfrowany klucz szyfrowania danych zapisz w pamięci podręcznej. Jest on teraz gotowy do użycia.
2.  Dotyczy każdego bloku danych w pliku:
    - Odczytaj zaszyfrowany blok danych z magazynu trwałego.
    - Wygeneruj klucz szyfrowania bloków na podstawie klucza szyfrowania danych i zaszyfrowanego bloku danych.
    - Użyj klucza szyfrowania bloków w celu odszyfrowania danych.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudoalgorytm stosowany w przypadku szyfrowania bloku danych:
1.  Sprawdź, czy klucz szyfrowania danych dla konta Data Lake Storage Gen1 jest buforowany i gotowy do użycia.
    - Jeśli nie, odczytaj zaszyfrowany klucz szyfrowania danych z magazynu trwałego i prześlij go do usługi Key Vault w celu odszyfrowania. Odszyfrowany klucz szyfrowania danych zapisz w pamięci podręcznej. Jest on teraz gotowy do użycia.
2.  Wygeneruj unikatowy klucz szyfrowania bloków dla bloku danych na podstawie klucza szyfrowania danych.
3.  Zaszyfruj blok danych przy użyciu klucza szyfrowania bloków, korzystając z algorytmu AES-256.
4.  Zapisz zaszyfrowany blok danych w magazynie trwałym.

> [!NOTE] 
> Klucz szyfrowania danych jest zawsze przechowywany jako zaszyfrowany za pomocą głównego klucza szyfrowania na nośniku trwałym lub buforowany w pamięci.

## <a name="key-rotation"></a>Wymiana kluczy

W przypadku korzystania z kluczy zarządzanych przez klienta można wymienić główny klucz szyfrowania. Aby dowiedzieć się, jak skonfigurować konto Data Lake Storage Gen1 przy użyciu kluczy zarządzanych przez klienta, zobacz [wprowadzenie](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Wymagania wstępne

Po skonfigurowaniu konta Data Lake Storage Gen1 użytkownik zdecydował się korzystać z własnych kluczy. Po utworzeniu konta ta opcja nie może zostać zmieniona. W poniższych krokach przyjęto, że używasz kluczy zarządzanych przez klienta (tzn. wybrano własne klucze z usługi Key Vault).

Należy pamiętać, że w przypadku używania opcji domyślnych dla szyfrowania dane są zawsze szyfrowane przy użyciu kluczy zarządzanych przez Data Lake Storage Gen1. W przypadku tej opcji nie ma możliwości rotacji kluczy, ponieważ są one zarządzane przez Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Jak obrócić główny klucz szyfrowania w Data Lake Storage Gen1

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do wystąpienia Key Vault, w którym są przechowywane klucze skojarzone z Twoim kontem Data Lake Storage Gen1. Wybierz pozycję **Klucze**.

    ![Zrzut ekranu usługi Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Wybierz klucz skojarzony z kontem Data Lake Storage Gen1 i Utwórz nową wersję tego klucza. Należy pamiętać, że Data Lake Storage Gen1 obecnie obsługuje tylko rotację kluczy do nowej wersji klucza. Wymiana na inny klucz nie jest obsługiwana.

   ![Zrzut ekranu okna Klucze z wyróżnionym przyciskiem Nowa wersja](./media/data-lake-store-encryption/keynewversion.png)

4. Przejdź do konta Data Lake Storage Gen1 i wybierz pozycję **szyfrowanie**.

   ![Zrzut ekranu przedstawiający okno konta Data Lake Storage Gen1 z wyróżnionym szyfrowaniem](./media/data-lake-store-encryption/select-encryption.png)

5. Zostanie wyświetlony komunikat informujący o dostępności nowej wersji klucza. Kliknij pozycję **Wymień klucz**, aby zaktualizować klucz do nowej wersji.

   ![Zrzut ekranu okna Data Lake Storage Gen1 z wyróżnionym komunikatem i klawiszem Obróć](./media/data-lake-store-encryption/rotatekey.png)

Ta operacja powinna zająć mniej niż dwie minuty i nie powinna powodować żadnego przestoju. Po zakończeniu operacji jest używana nowa wersja klucza.

> [!IMPORTANT]
> Po zakończeniu operacji rotacji kluczy stara wersja klucza nie jest już aktywnie używana do szyfrowania danych.  Jednak w rzadkich przypadkach wystąpienia nieoczekiwanych awarii, które mają wpływ nawet na nadmiarowe kopie danych, dane można przywrócić z kopii zapasowej nadal używającej starego klucza. Aby zapewnić dostępność danych w tych rzadkich przypadkach, należy zachować kopię poprzedniej wersji klucza szyfrowania. Zobacz [wskazówki dotyczące odzyskiwania po awarii dla danych w Data Lake Storage Gen1](data-lake-store-disaster-recovery-guidance.md) , aby poznać najlepsze rozwiązania związane z planowaniem odzyskiwania po awarii. 