---
title: Zarządzanie rejestracjami urządzeń dla IoT Hub Device Provisioning Service platformy Azure w Azure Portal
description: Jak zarządzać rejestracjami urządzeń dla usługi Device Provisioning Service (DPS) w Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: 06c355b913d5b786455cae20d1a57eb8c63c3ee1
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842966"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Jak zarządzać rejestracjami urządzeń za pomocą Azure Portal

*Rejestracja urządzenia* powoduje utworzenie rekordu jednego urządzenia lub grupy urządzeń, które mogą być w pewnym momencie zarejestrowane w usłudze Azure IoT Hub Device Provisioning Service. Rekord rejestracji zawiera początkową konfigurację urządzeń w ramach tej rejestracji. Uwzględniona w konfiguracji to centrum IoT, do którego zostanie przypisane urządzenie, lub zasada alokacji, która konfiguruje centrum na podstawie zestawu centrów. W tym artykule opisano sposób zarządzania rejestracjami urządzeń dla usługi aprowizacji.


## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń

Istnieją dwa sposoby rejestrowania urządzeń przy użyciu usługi aprowizacji:

* **Grupa rejestracji** to wpis dla grupy urządzeń, które korzystają ze wspólnego mechanizmu zaświadczania. Zalecamy używanie grupy rejestracji dla dużej liczby urządzeń, które udostępniają konfigurację początkową, lub dla urządzeń, które przechodzą do tej samej dzierżawy. Obsługiwane są urządzenia korzystające z [klucza symetrycznego](concepts-symmetric-key-attestation.md) lub [certyfikatu X. 509](concepts-x509-attestation.md) . 

    Instrukcje krok po kroku dotyczące tworzenia i używania grup rejestracji z kluczami symetrycznymi znajdują się w samouczku [Udostępnianie urządzeń przy użyciu kluczy symetrycznych](how-to-legacy-device-symm-key.md) .

    W portalu można utworzyć grupę rejestracji dla grupy urządzeń, wykonując następujące czynności:

    1. Zaloguj się do Azure Portal i kliknij pozycję **wszystkie zasoby** w menu po lewej stronie.  
    1. Kliknij usługę Device Provisioning, z której chcesz zarejestrować urządzenie na liście zasobów.  
    1. W usłudze aprowizacji kliknij pozycję **Zarządzaj rejestracjami**, a następnie kliknij przycisk **Dodaj grupę rejestracji** u góry.  
     
        ![Grupa rejestracji w portalu](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. Po wyświetleniu panelu "Dodaj grupę rejestracji" Wprowadź informacje dotyczące rejestracji i kliknij przycisk **Zapisz**.  
     
        [![Dodawanie grupy rejestracji przy użyciu portalu](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | Pole | Opis |
        | :--- | :--- |
        | **Nazwa grupy** | Wymagana nazwa grupy urządzeń. |
        | **Typ zaświadczania** | Kliknij pozycję **certyfikat** lub **klucz symetryczny** dla typu zaświadczania w zależności od metody zaświadczania, która będzie używana przez urządzenia. |
        | **Typ certyfikatu** | Dostępne w przypadku korzystania z zaświadczania o certyfikatach. Wybierz pozycję **certyfikat urzędu certyfikacji** lub **pośredni** na podstawie certyfikatu podpisanego przez certyfikat urządzenia. |
        | **Certyfikat podstawowy** | W przypadku podpisywania certyfikatów urządzeń z certyfikatem głównego urzędu certyfikacji ten certyfikat głównego urzędu certyfikacji musi mieć [potwierdzenie posiadania](how-to-verify-certificates.md) . Następnie można wybrać ten **certyfikat jako podstawowy** dla grupy urządzeń.<br><br>W przypadku podpisywania certyfikatów urządzeń za pomocą certyfikatu pośredniego zostanie udostępniony przycisk Przekaż, który umożliwi przekazanie certyfikatu pośredniego. Certyfikat, który podpisał pośredni, musi również mieć [potwierdzenie posiadania](how-to-verify-certificates.md) . |

        
    

* **Rejestracja indywidualna** to wpis dla pojedynczego urządzenia, które może zostać przypisane do centrum IoT Hub. Obsługiwane są urządzenia korzystające z [klucza symetrycznego](concepts-symmetric-key-attestation.md), [certyfikaty X. 509](concepts-x509-attestation.md)i [zaświadczania modułu TPM](concepts-tpm-attestation.md) . 

    Możesz utworzyć rejestrację indywidualną w portalu, wykonując następujące czynności:

    1. Zaloguj się do Azure Portal i kliknij pozycję **wszystkie zasoby** w menu po lewej stronie.
    1. Kliknij usługę Device Provisioning, z której chcesz zarejestrować urządzenie na liście zasobów.
    1. W usłudze aprowizacji kliknij pozycję **Zarządzanie rejestracjami**, a następnie kliknij przycisk **Dodaj rejestrację indywidualną** u góry.   

       [![Dodawanie rejestracji indywidualnej w portalu](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. Po wyświetleniu panelu "Dodaj rejestrację" Wprowadź informacje dotyczące rejestracji poszczególnych urządzeń i kliknij przycisk **Zapisz**. 
     
        [![Rejestracja indywidualna w portalu](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | Pole | Opis |
        | :--- | :--- |
        | **Ustanawia** | Wybierz pozycję **X. 509**, **moduł TPM** lub **klucz symetryczny** dla mechanizmu zaświadczania, który ma być używany, w zależności od metody zaświadczania, która będzie używana przez urządzenia. |
        | Ustawienia zaświadczania | Aby uzyskać instrukcje krok po kroku dotyczące tworzenia i używania indywidualnych rejestracji z kluczami symetrycznymi lub certyfikatami X. 509, zobacz jedno z [sekcji Udostępnianie urządzenia symetrycznego](quick-create-simulated-device-symmetric-key-java.md#create-a-device-enrollment) lub [udostępnianie certyfikatu x. 509](quick-create-simulated-device-x509-java.md#create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry) przewodników Szybki Start.<br><br>Aby uzyskać instrukcje krok po kroku dotyczące tworzenia indywidualnych rejestracji i korzystania z nich przy użyciu zaświadczania modułu TPM, zobacz jeden z przykładowych [zainicjowania obsługi symulowanych urządzeń TPM](quick-create-simulated-device-tpm-java.md#create-a-device-enrollment-entry) .|
        | **Identyfikator urządzenia IoT Hub** |  Ten identyfikator będzie reprezentować Twoje urządzenie. Musi być zgodna z regułami dotyczącymi identyfikatora urządzenia. Aby uzyskać więcej informacji, zobacz [właściwości tożsamości urządzenia](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).<br><br>W przypadku używania certyfikatów X. 509 ten tekst musi być nazwą podmiotu w certyfikacie urządzenia przekazywanym do rejestracji. Ta nazwa podmiotu musi być zgodna z regułami dotyczącymi identyfikatora urządzenia.|
            


## <a name="update-an-enrollment-entry"></a>Aktualizowanie wpisu rejestracji
Istniejący wpis rejestracji można zaktualizować w portalu, wykonując następujące czynności:

1. Otwórz usługę Device Provisioning w Azure Portal a następnie kliknij pozycję **Zarządzaj rejestracjami**. 
1. Przejdź do wpisu rejestracji, który chcesz zmodyfikować. Kliknij wpis, aby otworzyć podsumowanie informacji o rejestracji urządzenia. 
1. Na tej stronie można modyfikować elementy inne niż typ zabezpieczeń i poświadczenia, takie jak centrum IoT, z którym urządzenie powinno być połączone, oraz identyfikator urządzenia. Można również zmodyfikować stan początkowy dwuosiowy urządzenia. 
1. Po zakończeniu kliknij przycisk **Zapisz** , aby zaktualizować rejestrację urządzenia. 

    ![Aktualizowanie rejestracji w portalu](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Usuwanie rejestracji urządzenia
W przypadkach, gdy urządzenia nie muszą być obsługiwane w żadnym Centrum IoT Hub, można usunąć powiązane wpisy rejestracji w portalu, wykonując następujące czynności:

1. Otwórz usługę Device Provisioning w Azure Portal a następnie kliknij pozycję **Zarządzaj rejestracjami**. 
1. Przejdź do i wybierz wpis rejestracji, który chcesz usunąć. 
1. Kliknij przycisk **Usuń** u góry, a następnie wybierz pozycję **tak** po wyświetleniu monitu o potwierdzenie. 
1. Po zakończeniu akcji zobaczysz, że wpis został usunięty z listy rejestracji urządzeń. 
 
    ![Usuwanie rejestracji w portalu](./media/how-to-manage-enrollments/remove-enrollment.png)


