---
title: Zarządzanie rejestracjami urządzeń dla IoT Hub Device Provisioning Service platformy Azure w Azure Portal
description: Jak zarządzać rejestracjami urządzeń dla usługi Device Provisioning Service (DPS) w witrynie Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74974942"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Jak zarządzać rejestracjami urządzeń za pomocą witryny Azure Portal

*Rejestracja urządzenia* powoduje utworzenie rekordu jednego urządzenia lub grupy urządzeń, które mogą być w pewnym momencie zarejestrowane w usłudze Azure IoT Hub Device Provisioning Service. Rekord rejestracji zawiera początkową wymaganą konfigurację dla urządzeń w ramach rejestracji, łącznie z żądanym Centrum IoT Hub. W tym artykule opisano sposób zarządzania rejestracjami urządzeń dla usługi aprowizacji.


## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń

Istnieją dwa sposoby rejestrowania urządzeń przy użyciu usługi aprowizacji:

* **Grupa rejestracji** to wpis dla grupy urządzeń, który udostępnia wspólny mechanizm zaświadczania certyfikatów X. 509 podpisany przez ten sam certyfikat podpisywania, który może być [certyfikatem głównym](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) lub [certyfikatem pośrednim](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), używanym do tworzenia certyfikatu urządzenia na urządzeniu fizycznym. Zalecamy używanie grupy rejestracji dla dużej liczby urządzeń, które współużytkują żądaną konfigurację początkową, lub dla urządzeń, które przechodzą do tej samej dzierżawy. Należy pamiętać, że można rejestrować tylko urządzenia korzystające z mechanizmu zaświadczania X. 509 jako *grupy rejestracji*. 

    W portalu można utworzyć grupę rejestracji dla grupy urządzeń, wykonując następujące czynności:

  1. Zaloguj się do Azure Portal i kliknij pozycję **wszystkie zasoby** w menu po lewej stronie.  
  1. Kliknij usługę Device Provisioning, z której chcesz zarejestrować urządzenie na liście zasobów.  
  1. W usłudze aprowizacji:  
     a. Kliknij pozycję **Zarządzanie rejestracjami**, a następnie wybierz kartę **grupy rejestracji** .  
     b. Kliknij przycisk **Dodaj** znajdujący się u góry.  
     c. Po wyświetleniu panelu "Dodaj grupę rejestracji" Wprowadź informacje dotyczące wpisu listy rejestracji.  **Nazwa grupy** jest wymagana. W polu **Typ certyfikatu**wybierz również pozycję "ca lub pośredni" i przekaż główny **certyfikat podstawowy** dla grupy urządzeń.  
     d. Kliknij przycisk **Zapisz**. Po pomyślnym utworzeniu grupy rejestracji powinna zostać wyświetlona nazwa grupy wyświetlana na karcie **grupy rejestracji** .  

     [![Grupa rejestracji w portalu](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* **Rejestracja indywidualna** to wpis dla jednego urządzenia, które może się zarejestrować. Rejestracje indywidualne mogą używać certyfikatów x509 lub tokenów sygnatury dostępu współdzielonego (z poziomu fizycznego lub wirtualnego modułu TPM) jako mechanizmów zaświadczania. Zalecamy używanie indywidualnych rejestracji dla urządzeń, które wymagają unikatowej konfiguracji początkowej, lub dla urządzeń, które mogą używać tylko tokenów SAS za pośrednictwem modułu TPM lub wirtualnego modułu TPM jako mechanizmu zaświadczania. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

    Możesz utworzyć rejestrację indywidualną w portalu, wykonując następujące czynności:

    1. Zaloguj się do Azure Portal i kliknij pozycję **wszystkie zasoby** w menu po lewej stronie.
    1. Kliknij usługę Device Provisioning, z której chcesz zarejestrować urządzenie na liście zasobów.
    1. W usłudze aprowizacji:  
       a. Kliknij pozycję **Zarządzanie rejestracjami**, a następnie wybierz kartę **rejestracje indywidualne** .  
       b. Kliknij przycisk **Dodaj** znajdujący się u góry.   
       c. Po wyświetleniu panelu "Dodaj rejestrację" Wprowadź informacje dotyczące wpisu listy rejestracji. Najpierw wybierz **mechanizm** zaświadczania dla urządzenia (X. 509 lub moduł TPM). Zaświadczanie X. 509 wymaga przekazania **podstawowego certyfikatu** liścia dla urządzenia. Moduł TPM wymaga wprowadzenia **klucza zaświadczania** i **identyfikatora rejestracji** dla urządzenia.  
       d. Kliknij przycisk **Zapisz**. Po pomyślnym utworzeniu grupy rejestracji urządzenie zostanie wyświetlone na karcie **indywidualne rejestracje** .  

       [![Rejestracja indywidualna w portalu](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Aktualizowanie wpisu rejestracji
Istniejący wpis rejestracji można zaktualizować w portalu, wykonując następujące czynności:

1. Otwórz usługę Device Provisioning w Azure Portal a następnie kliknij pozycję **Zarządzaj rejestracjami**. 
1. Przejdź do wpisu rejestracji, który chcesz zmodyfikować. Kliknij wpis, aby otworzyć podsumowanie informacji o rejestracji urządzenia. 
1. Na tej stronie można modyfikować elementy inne niż typ zabezpieczeń i poświadczenia, takie jak centrum IoT, z którym urządzenie powinno być połączone, a także identyfikator urządzenia. Można również zmodyfikować stan początkowy dwuosiowy urządzenia. 
1. Po zakończeniu kliknij przycisk **Zapisz** , aby zaktualizować rejestrację urządzenia. 

    ![Aktualizowanie rejestracji w portalu](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Usuwanie rejestracji urządzenia
W przypadkach, gdy urządzenia nie muszą być obsługiwane w żadnym Centrum IoT Hub, można usunąć powiązane wpisy rejestracji w portalu, wykonując następujące czynności:

1. Otwórz usługę Device Provisioning w Azure Portal a następnie kliknij pozycję **Zarządzaj rejestracjami**. 
1. Przejdź do i wybierz wpis rejestracji, który chcesz usunąć. 
1. Kliknij przycisk **Usuń** u góry, a następnie wybierz pozycję **tak** po wyświetleniu monitu o potwierdzenie. 
1. Po zakończeniu akcji zobaczysz, że wpis został usunięty z listy rejestracji urządzeń. 
 
    ![Usuwanie rejestracji w portalu](./media/how-to-manage-enrollments/remove-enrollment.png)


