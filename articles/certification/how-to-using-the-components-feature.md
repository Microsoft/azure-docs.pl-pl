---
title: Jak korzystać z funkcji składniki w portalu certyfikowanych urządzeń platformy Azure
description: Przewodnik po najlepszym użyciu funkcji Components sekcji Szczegóły urządzenia, aby precyzyjnie opisać urządzenie
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 091a666026853d677f1e9ed7e5e1736307cecbe8
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969453"
---
# <a name="add-components-on-the-portal"></a>Dodaj składniki w portalu

Podczas wykonywania kroków opisanych w tym [samouczku, aby dodać szczegóły urządzenia](tutorial-02-adding-device-details.md) do projektu certyfikacji, należy opisać specyfikacje sprzętu urządzenia. W tym celu użytkownicy mogą wyróżnić wiele różnych produktów sprzętowych (nazywanych **składnikami**) tworzących Twoje urządzenie. Dzięki temu można lepiej promować urządzenia, które są dostarczane z dodatkowym sprzętem, i umożliwia klientom znalezienie odpowiedniego produktu przez wyszukiwanie w katalogu na podstawie tych funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Należy się zalogować i utworzyć projekt dla swojego urządzenia w [portalu certyfikowanego urządzenia platformy Azure](https://certify.azure.com). Aby uzyskać więcej informacji, zobacz [samouczek](tutorial-01-creating-your-project.md).

## <a name="how-to-add-components"></a>Jak dodać składniki

Każdy projekt przesłany do certyfikacji będzie zawierać jeden składnik **produktu gotowego klienta** (w wielu przypadkach będzie reprezentował całościowy produkt). Aby lepiej zrozumieć rozróżnienie typu składnika produktu gotowego dla klienta, zapoznaj się z naszym [słownikiem certyfikacji](./resources-glossary.md). Wszystkie dodatkowe składniki są według własnego uznania, aby dołączyć do dokładnego przechwycenia urządzenia.

1. Wybierz pozycję `Add a component` na karcie Szczegóły produktu.

    ![Dodawanie linku do składnika](./media/images/add-a-component-link.png)

1. Wypełnij odpowiednie pola formularza dla składnika.

    ![Sekcja szczegółów składnika](./media/images/component-details-section.png)

1. Zapisz informacje przy użyciu `Save Product Details` przycisku w dolnej części strony:  

    ![Przycisk Zapisz szczegóły produktu](./media/images/save-product-details-button.png)

1. Po zapisaniu składnika można dodatkowo dostosować możliwości sprzętowe, które obsługuje. Wybierz `Edit` łącze według nazwy składnika.  

    ![Przycisk Edytuj składnik](./media/images/component-edit.png)

1. W razie potrzeby podaj odpowiednie informacje o możliwościach sprzętowych.  

    ![Obraz sekcji edytowalnych składników](./media/images/component-selection-area.png)  

    Edytowalne pola składnika (pokazane powyżej) obejmują:

    - **Ogólne**: szczegóły sprzętu, takie jak procesory i bezpieczny sprzęt
    - **Łączność**: opcje łączności, protokoły i interfejsy, takie jak radio (s) i GPIO
    - **Akceleratory**: Określ przyspieszenie sprzętowe, takie jak GPU i VPU
    - **Czujniki**: Określ dostępne czujniki, takie jak GPS i wibracje
    - **Dodatkowe dane techniczne**: dodatkowe informacje o urządzeniu, takie jak wymiary fizyczne i informacje o magazynie/baterii

1. Wybierz `Save Product Details` w dolnej części strony Szczegóły produktu.

## <a name="component-use-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące użycia składników

Możesz mieć pytania dotyczące liczby składników do uwzględnienia lub typu składnika, który ma być używany. Poniżej przedstawiono przykłady kilku przykładowych scenariuszy urządzeń, które mogą być certyfikowane i jak można użyć funkcji składniki.

| Typ produktu                                       | Nie. Składniki | Typ składnika 1/załącznika      | Składniki 2 +/typ załącznika                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| Gotowy produkt                                   | 1          | Produkt gotowy do sprzedaży, dyskretny | Nie dotyczy                                              |
| Gotowy produkt z **odłączalnymi urządzeniami peryferyjnymi** | 2 lub więcej  | Produkt gotowy do sprzedaży, dyskretny | Urządzenia peryferyjne/dyskretne lub zintegrowane              |
| Gotowy produkt ze **zintegrowanymi składnikami**  | 2 lub więcej  | Produkt gotowy do sprzedaży, dyskretny | Wybierz odpowiedni typ/dyskretny lub zintegrowany |
| Solution-Ready dev Kit                             | 2 lub więcej  | Produkt gotowy do sprzedaży, dyskretny | Wybierz odpowiedni typ/dyskretny lub zintegrowany |

## <a name="example-component-usage"></a>Przykładowe użycie składników

Poniżej przedstawiono przykłady sposobu, w jaki producent OEM o nazwie contoso użyje funkcji Components do certyfikowania produktów o nazwie Falcon.

1. Falcon to kompletne urządzenie autonomiczne, które nie integruje się z większym produktem.
    1. Nie. składników: 1
    1. Typ urządzenia składnika: produkt gotowy do klienta
    1. Typ załącznika: dyskretny

     ![Obraz produktu gotowego do użycia przez klienta](./media/images/customer-ready-product.png)

1. Falcon to urządzenie z wbudowanym modułem kamer peryferyjnych produkowanym przez elektronikę INC, który łączy się za pośrednictwem portu USB z Falcon.
    1. Nie. składników: 2
    1. Typ urządzenia składnika: produkt gotowy do klienta, urządzenie peryferyjne
    1. Typ załącznika: dyskretny, zintegrowany
    
    > [!Note]
    > Składnik zewnętrzny jest uznawany za zintegrowany, ponieważ nie jest wymienny.

     ![Obraz składnika przykładowego urządzenia peryferyjnego](./media/images/peripheral.png)

1. Falcon to urządzenie, które obejmuje zintegrowany system w module z firmy Electronic Electronics, który używa wbudowanego procesora Apollo52 z Espressif firmy i ma architekturę ARM64.
    1. Nie. składników: 2
    1. Typ urządzenia składnikowego: produkt gotowy do klienta, system w module
    1. Typ załącznika: dyskretny, zintegrowany

    > [!Note]
    > Składnik zewnętrzny jest uznawany za zintegrowany, ponieważ nie jest wymienny. Składnik SoM również zawiera informacje o procesorach.

     ![Obraz składnika system w przykładowym module ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>Dodatkowe porady

Podano poniżej więcej wyjaśnień dotyczących zasad użycia składników. Jeśli masz pytania dotyczące odpowiedniego użycia składników, skontaktuj się z naszym zespołem w [iotcert@microsoft.com](mailto:iotcert@microsoft.com) usłudze i będziemy mogli uzyskać więcej niż szczęśliwej pomocy!

1. Projekt musi zawierać **tylko** jeden składnik produktu gotowego dla klienta. W przypadku certyfikowania projektu z dwoma niezależnymi urządzeniami te urządzenia powinny być certyfikowane osobno.
1. Przede wszystkim można używać składników (lub nie używać ich) do promowania możliwości urządzenia dla potencjalnych klientów.
1. Podczas naszego przeglądu urządzenia zespół certyfikacji platformy Azure będzie wymagał tylko jednego składnika produktu gotowego do użycia przez klienta. Jednakże możemy zażądać modyfikacji informacji o składnikach, jeśli szczegóły nie są jasne lub nie są niewyraźne (na przykład producent składnika nie jest dostarczany do typu produktu gotowego dla klienta).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wszystko jest gotowe do korzystania z funkcji składników, możesz teraz przejść do szczegółów urządzenia lub edytować projekt w celu zwiększenia przejrzystości.

- [Samouczek: Dodawanie szczegółów urządzenia](tutorial-02-adding-device-details.md)
- [Edytowanie opublikowanego urządzenia](how-to-edit-published-device.md)

