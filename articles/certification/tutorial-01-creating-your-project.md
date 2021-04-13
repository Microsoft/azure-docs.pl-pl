---
title: Program certyfikowany na platformie Azure — samouczek — Tworzenie projektu
description: Przewodnik tworzenia projektu w portalu certyfikowanych urządzeń platformy Azure
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 11d72847209a2e706d4aa32d38af1b2c8af3dfa0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314001"
---
# <a name="tutorial-create-your-project"></a>Samouczek: Tworzenie projektu

Gratulujemy wybrania certyfikowania urządzenia za pomocą programu certyfikowanego urządzenia platformy Azure! Wybrano teraz odpowiedni program certyfikacyjny dla Twojego urządzenia i wszystko jest gotowe do rozpoczęcia pracy z portalem.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zaloguj się do [portalu urządzenia z certyfikatem platformy Azure](https://certify.azure.com/)
> * Tworzenie nowego projektu certyfikacji dla urządzenia
> * Określ podstawowe szczegóły urządzenia projektu

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć prawidłowe [konto Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)służbowego.
- Będziesz potrzebować zweryfikowanego konta Microsoft Partner Network (MPN). Jeśli nie masz konta MPN, [Dołącz do sieci partnerskiej](https://partner.microsoft.com/) przed rozpoczęciem.

## <a name="signing-into-the-azure-certified-device-portal"></a>Logowanie się do portalu certyfikowanych urządzeń platformy Azure

Aby rozpocząć, musisz zalogować się do portalu, w którym będą podawane informacje o urządzeniu, zakończeniu testowania certyfikacji i zarządzania publikacjami urządzeń w katalogu certyfikowanych urządzeń platformy Azure.

1. Przejdź do [portalu certyfikowanego urządzenia platformy Azure](https://certify.azure.com).
1. Zaznacz po `Company profile` lewej stronie i zaktualizuj informacje o producencie.
   ![Sekcja profilu firmy](./media/images/company-profile.png)
1. Zaakceptuj umowę programu, aby rozpocząć projekt.

## <a name="creating-your-project-on-the-portal"></a>Tworzenie projektu w portalu

Teraz, gdy wszystko jest skonfigurowane w portalu, możesz rozpocząć proces certyfikacji. Najpierw należy utworzyć projekt dla urządzenia.

1. Na ekranie głównym wybierz opcję `Create new project` . Spowoduje to otwarcie okna umożliwiającego dodanie podstawowych informacji o urządzeniu w następnej sekcji.

 ![Obraz przycisku Utwórz nowy projekt](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>Identyfikowanie podstawowych informacji o urządzeniu

Następnie należy podać podstawowe informacje o urządzeniu. Później można edytować te informacje.

1. Wypełnij pola wymagane w `Basics` sekcji. Zapoznaj się z poniższą tabelą, aby uzyskać wyjaśnienie dotyczące **wymaganych** pól:

    | Pola                  | Opis                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | Project name (Nazwa projektu)           | Nazwa wewnętrzna, która nie będzie widoczna w wykazie certyfikowanych urządzeń platformy Azure                                                        |
    | Nazwa urządzenia            | Nazwa publiczna urządzenia                                                                                                |
    | Typ urządzenia            | Specyfikacja gotowego produktu lub Solution-Ready Developer Kit.     Aby uzyskać więcej informacji na temat terminologii, zobacz [słownik certyfikacji](./resources-glossary.md).                                                                     |
    | Klasa urządzenia           | Brama, czujnik lub inne.  Aby uzyskać więcej informacji na temat terminologii, zobacz [słownik certyfikacji](./resources-glossary.md).                                                                    |
    | Adres URL kodu źródłowego urządzenia | Wymagane, jeśli certyfikat jest certyfikowany Solution-Ready dev Kit, opcjonalnie w przeciwnym razie. Adres URL musi być lokalizacją usługi GitHub dla kodu urządzenia. |
1. Wybierz `Next` przycisk, aby przejść do `Certifications` karty.

    ![Obraz przedstawiający formularz Utwórz nowy projekt na karcie Certyfikaty](./media/images/create-new-project-certificationswindow.png)

1. Określ, które certyfikaty chcesz uzyskać dla urządzenia.
1. Wybierz pozycję `Create` i nowy projekt zostanie zapisany i widoczny na stronie głównej portalu.

    ![Obraz tabeli projektu](./media/images/project-table.png)

1. Wybierz nazwę projektu w tabeli. Spowoduje to uruchomienie strony podsumowania projektu, na której można dodawać i wyświetlać inne szczegóły dotyczące Twojego urządzenia.

    ![Obraz strony szczegółów projektu](./media/images/device-details-section.png)

## <a name="next-steps"></a>Następne kroki

Teraz możesz przystąpić do dodawania szczegółów urządzenia i testowania urządzenia przy użyciu naszej usługi certyfikacji. Przejdź do następnego artykułu, aby dowiedzieć się, jak edytować szczegóły urządzenia.
> [!div class="nextstepaction"]
> [Samouczek: Dodawanie szczegółów urządzenia](tutorial-02-adding-device-details.md)
