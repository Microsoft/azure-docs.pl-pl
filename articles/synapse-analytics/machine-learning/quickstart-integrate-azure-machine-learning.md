---
title: 'Szybki Start: łączenie obszaru roboczego Azure Machine Learning'
description: Łączenie obszaru roboczego Synapse z obszarem roboczym Azure Machine Learning
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 141cc47adb398cbd5730fbe33bcd90fece809ed3
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91543478"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>Szybki Start: Tworzenie nowej Azure Machine Learning połączonej usługi w programie Synapse

W tym przewodniku szybki start połączysz obszar roboczy Synapse Analytics z obszarem roboczym Azure Machine Learning. Łączenie tych obszarów roboczych umożliwia korzystanie z Azure Machine Learning z różnych środowisk w Synapse.

Na przykład to połączenie z obszarem roboczym Azure Machine Learning umożliwia korzystanie z następujących funkcji:

- Uruchom potoki Azure Machine Learning jako krok w potokach Synapse. Aby dowiedzieć się więcej, zobacz [wykonywanie potoków Azure Machine Learning](/azure/data-factory/transform-data-machine-learning-service).

- Wzbogacaj dane przy użyciu prognoz przez umieszczenie modelu uczenia maszynowego z rejestru modelu Azure Machine Learning model i Ocena modelu w Synapse pule SQL. Aby uzyskać więcej informacji, zobacz [Samouczek: Kreator oceniania modelu uczenia maszynowego dla pul Synapse SQL](tutorial-sql-pool-model-scoring-wizard.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
- [Obszar roboczy Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu ADLS Gen2 skonfigurowanym jako magazyn domyślny. Musisz być **współautorem danych obiektów blob magazynu** dla systemu plików ADLS Gen2, z którym pracujesz.
- [Obszar roboczy usługi Azure Machine Learning](/azure/machine-learning/how-to-manage-workspace).
- Musisz mieć uprawnienia (lub zażądać od kogoś, kto ma uprawnienia), aby utworzyć jednostkę usługi i klucz tajny, których można użyć do utworzenia połączonej usługi. Należy pamiętać, że ta jednostka usługi musi mieć przypisaną rolę współautor w Obszar roboczy usługi Azure Machine Learning.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Ten krok spowoduje utworzenie nowej nazwy głównej usługi. Jeśli chcesz użyć istniejącej nazwy głównej usługi, możesz pominąć ten krok.
1. Otwórz witrynę Azure Portal. 

1. Przejdź do **Azure Active Directory**  ->  **rejestracje aplikacji**Azure Active Directory.

1. Kliknij pozycję **Nowa rejestracja**. Następnie postępuj zgodnie z instrukcjami wyświetlanymi w interfejsie użytkownika, aby zarejestrować nową aplikację.

1. Po zarejestrowaniu aplikacji. Wygeneruj klucz tajny dla aplikacji. Przejdź do **Your application**  ->  **certyfikatu aplikacji & wpis tajny**. Kliknij pozycję **Dodaj klucz tajny klienta** , aby wygenerować klucz tajny. Tajne zabezpieczenie jest bezpieczne i będzie używane później.

   ![Generuj klucz tajny](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. Utwórz nazwę główną usługi dla aplikacji. Przejdź do **omówienia swojej aplikacji**  ->  **Overview** , a następnie kliknij pozycję **Utwórz nazwę główną usługi**. W niektórych przypadkach ta nazwa główna usługi jest tworzona automatycznie.

   ![Tworzenie jednostki usługi](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. Dodaj nazwę główną usługi jako "Współautor" Azure Machine Learning obszaru roboczego. Należy pamiętać, że będzie to wymagało właściciela grupy zasobów, do której należy obszar roboczy Azure Machine Learning.

   ![Przypisywanie roli współautor](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

## <a name="create-a-linked-service"></a>Tworzenie usługi połączonej

1. W obszarze roboczym Synapse, w którym chcesz utworzyć nową połączoną usługę Azure Machine Learning przejdź do pozycji **Zarządzanie**  ->  **połączonej**usługi, Utwórz nową połączoną usługę z typem "Azure Machine Learning".

   ![Utwórz połączoną usługę](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Wypełnij formularz:

   - Identyfikator jednostki usługi: jest to **Identyfikator aplikacji (klienta)** .
  
     > [!NOTE]
     > To nie jest nazwa aplikacji. Ten identyfikator można znaleźć na stronie Przegląd aplikacji. Powinien być to długi ciąg podobny do tego "81707eac-ab38-406u-8f6c-10ce76a568d5".

   - Klucz jednostki usługi: wpis tajny wygenerowany w poprzedniej sekcji.

3. Kliknij przycisk **Testuj połączenie** , aby sprawdzić, czy konfiguracja jest poprawna. Jeśli test połączenia przebiega pomyślnie, kliknij przycisk **Zapisz**.

   Jeśli test połączenia nie powiódł się, upewnij się, że identyfikator jednostki usługi i klucz tajny są poprawne, i spróbuj ponownie.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Kreator oceniania modelu uczenia maszynowego — Pula SQL](tutorial-sql-pool-model-scoring-wizard.md)
- [Możliwości Machine Learning na platformie Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)](what-is-machine-learning.md)
