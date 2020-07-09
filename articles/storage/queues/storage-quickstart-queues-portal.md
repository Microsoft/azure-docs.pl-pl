---
title: 'Szybki Start: Tworzenie kolejek usługi Azure Storage w portalu'
description: Użyj Azure Portal, aby utworzyć kolejkę. Następnie użyj Azure Portal, aby dodać komunikat, wyświetlić właściwości wiadomości i usunąć z kolejki komunikat.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/06/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: dineshm
ms.openlocfilehash: af3f812c8018f059f26c29cd373714e4ab3c8a49
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84808967"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Szybki Start: Tworzenie kolejki i Dodawanie komunikatu z Azure Portal

W tym przewodniku Szybki start dowiesz się, jak używać witryny [Azure Portal](https://portal.azure.com/) do tworzenia kolejki w usłudze Azure Storage oraz do dodawania komunikatów do kolejki i usuwania ich z kolejki.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Tworzenie kolejki

Aby utworzyć kolejkę w witrynie Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego nowego konta magazynu.
2. W menu po lewej stronie dla konta magazynu przewiń do sekcji **Usługa kolejki**, a następnie wybierz pozycję **Kolejki**.
3. Wybierz przycisk **+ Kolejka**.
4. Wpisz nazwę nowej kolejki. Nazwa kolejki musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).
6. Wybierz przycisk **OK**, aby utworzyć kolejkę.

    ![Zrzut ekranu przedstawiający sposób tworzenia kolejki w witrynie Azure Portal](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Dodawanie komunikatu

Następnie dodaj komunikat do nowej kolejki. Maksymalny rozmiar komunikatu wynosi 64 KB.

1. Wybierz nową kolejkę z listy kolejek na koncie magazynu.
1. Wybierz przycisk **+ Dodaj komunikat**, aby dodać komunikat do kolejki. Wprowadź komunikat w polu **Tekst komunikatu**. 
1. Określ termin wygaśnięcia ważności komunikatu. Maksymalny czas pozostawania komunikatu w kolejce wynosi 7 dni.
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
> [Co to są kolejki platformy Azure?](storage-queues-introduction.md)
