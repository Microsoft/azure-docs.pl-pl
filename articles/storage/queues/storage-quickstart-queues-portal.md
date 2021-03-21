---
title: 'Szybki Start: Tworzenie kolejek usługi Azure Storage w portalu'
description: Użyj Azure Portal, aby utworzyć kolejkę. Następnie użyj Azure Portal, aby dodać komunikat, wyświetlić właściwości wiadomości i usunąć z kolejki komunikat.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 08/13/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 24b1d16ab5f13a9aef1faa5bc7a3579ad1a5aa5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585551"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Szybki Start: Tworzenie kolejki i Dodawanie komunikatu z Azure Portal

W tym przewodniku Szybki start dowiesz się, jak używać witryny [Azure Portal](https://portal.azure.com/) do tworzenia kolejki w usłudze Azure Storage oraz do dodawania komunikatów do kolejki i usuwania ich z kolejki.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Tworzenie kolejki

Aby utworzyć kolejkę w witrynie Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego nowego konta magazynu.
2. W menu po lewej stronie konta magazynu przewiń do sekcji **queue storage** , a następnie wybierz **kolejno pozycje kolejki**.
3. Wybierz przycisk **+ Kolejka**.
4. Wpisz nazwę nowej kolejki. Nazwa kolejki musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).
6. Wybierz przycisk **OK**, aby utworzyć kolejkę.

    ![Zrzut ekranu przedstawiający sposób tworzenia kolejki w witrynie Azure Portal](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Dodawanie komunikatu

Następnie dodaj komunikat do nowej kolejki. Maksymalny rozmiar komunikatu wynosi 64 KB.

1. Wybierz nową kolejkę z listy kolejek na koncie magazynu.
1. Wybierz przycisk **+ Dodaj komunikat**, aby dodać komunikat do kolejki. Wprowadź komunikat w polu **Tekst komunikatu**.
1. Określ termin wygaśnięcia ważności komunikatu. Prawidłowe wartości, które można wprowadzić w polu **wygasa w** , to od 1 sekundy do 7 dni. Wybierz opcję **komunikat nigdy nie wygasa** , aby wskazać komunikat, który pozostanie w kolejce, dopóki nie zostanie on jawnie usunięty.
1. Wskaż, czy komunikat ma być kodowany przy użyciu schematu Base64. Kodowanie danych binarnych jest zalecane.
1. Wybierz przycisk **OK**, aby dodać komunikat.

    ![Zrzut ekranu przedstawiający sposób dodawania komunikatu do kolejki](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Wyświetlanie właściwości komunikatu

Po dodaniu komunikatu w witrynie Azure Portal zostanie wyświetlona lista wszystkich komunikatów w kolejce. Można wyświetlić identyfikator komunikatu, zawartość komunikatu, czas wstawienia komunikatu i czas wygaśnięcia ważności komunikatu. Można również sprawdzić, ile razy dany komunikat został usunięty z kolejki.

![Zrzut ekranu przedstawiający właściwości komunikatu](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Usuwanie komunikatu z kolejki

Komunikat można usunąć z początku kolejki w witrynie Azure Portal. Wycofanie komunikatu z kolejki oznacza jego usunięcie.

Usuwanie z kolejki zawsze dotyczy najstarszego komunikatu.

![Zrzut ekranu przedstawiający sposób usuwania komunikatu z kolejki z poziomu portalu](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób tworzenia kolejki, dodawania komunikatu, wyświetlania właściwości komunikatu i usuwania komunikatu z kolejki w witrynie Azure Portal.

> [!div class="nextstepaction"]
> [Co to jest Azure Queue Storage?](storage-queues-introduction.md)
